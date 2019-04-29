---
title: Azure의 OpenShift 필수 조건 | Microsoft Docs
description: Azure에서 OpenShift 배포에 대한 필수 조건입니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473888"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Azure에서 OpenShift를 배포하기 위한 일반적인 필수 조건

이 문서는 Azure에서 OpenShift Container Platform 또는 OKD를 배포하기 위한 일반적인 필수 조건을 설명합니다.

OpenShift 설치에는 Ansible 플레이북이 사용됩니다. Ansible은 SSH(Secure Shell)를 사용하여 설치 단계를 완료하는 모든 클러스터 호스트에 연결합니다.

Ansible의 원격 호스트에 SSH 연결 하면 암호를 들어갈 수 없습니다. 따라서 개인 키에는 연결된 암호를 사용할 수 없으며, 사용할 경우 배포에 실패합니다.

VM(가상 머신)은 Azure Resource Manager 템플릿을 통해 배포되기 때문에 동일한 공개 키가 모든 VM에 액세스하는 데 사용됩니다. 모든 마찬가지로 플레이 북을 실행 중인 VM에 해당 개인 키가 있어야 합니다. 이 작업을 안전 하 게 수행 하려면 VM에 개인 키를 전달 하는 Azure key vault 사용 됩니다.

컨테이너에 대한 영구 저장소가 필요한 경우에는 영구적 볼륨이 필요합니다. OpenShift는 영구적 볼륨에 대 한 Azure 가상 하드 디스크 (Vhd)를 지원 하지만 Azure는 클라우드 공급자로 먼저 구성 해야 합니다.

이 모델에서 OpenShift는 다음을 수행합니다.

- Azure storage 계정 또는 관리 되는 디스크에 VHD 개체를 만듭니다.
- VM에 VHD를 탑재하고 볼륨을 포맷합니다.
- Pod에 볼륨을 탑재합니다.

이 구성이 작동하려면 OpenShift에 Azure에서 이러한 작업을 수행할 수 있는 권한이 필요합니다. 서비스 주체는이 목적을 위해 사용 됩니다. SP(서비스 주체)는 리소스에 대한 권한이 부여된 Azure Active Directory의 보안 계정입니다.

서비스 주체는 클러스터를 구성하는 VM 및 저장소 계정에 대한 액세스 권한이 있어야 합니다. 모든 OpenShift 클러스터 리소스가 단일 리소스 그룹에 배포된 경우 서비스 주체는 해당 리소스 그룹에 대한 권한을 부여 받을 수 있습니다.

이 가이드에서는 필수 조건과 연결된 아티팩트를 만드는 방법을 설명합니다.

> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 키 자격 증명 모음을 만듭니다.
> * Azure 클라우드 공급자가 사용할 서비스 주체를 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 
[az login](/cli/azure/reference-index) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따르거나 **시도**를 클릭하여 Cloud Shell을 사용합니다.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Key vault를 호스트 하는 전용된 리소스 그룹을 사용 해야 합니다. 이 그룹은 OpenShift 클러스터 리소스가 배포되는 리소스 그룹과는 다릅니다.

다음 예제에서는 *eastus* 위치에 *keyvaultrg*이라는 리소스 그룹을 만듭니다.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기
[az keyvault create](/cli/azure/keyvault) 명령을 사용하여 클러스터에 대한 SSH 키를 저장할 키 자격 증명 모음을 만듭니다. 키 자격 증명 모음 이름은 전역적으로 고유해야 합니다.

다음 예제에서는 *keyvaultrg* 리소스 그룹에 *keyvault*라는 키 자격 증명 모음을 만듭니다.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH 키 만들기 
SSH 키는 OpenShift 클러스터에 대한 액세스를 보호하기 위해 필요합니다. `ssh-keygen` 명령(Linux 또는 macOS에서)을 사용하여 SSH 키 쌍을 만듭니다.
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH 키 쌍에는 암호를 포함할 수 없습니다.

Windows의 SSH 키에 대한 자세한 내용은 [Windows에서 SSH 키를 만드는 방법](/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요. OpenSSH 형식으로 개인 키를 내보내야 합니다.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Azure Key Vault에 SSH 개인 키 저장
OpenShift 배포는 사용자가 만든 SSH 키를 사용하여 OpenShift 마스터에 대한 액세스를 보호합니다. SSH 키를 안전하게 검색하기 위해 배포를 사용하도록 설정하려면 다음 명령을 사용하여 Key Vault에 키를 저장합니다.

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기 
OpenShift는 사용자 이름 및 암호 또는 서비스 주체를 사용하여 Azure와 통신합니다. Azure 서비스 주체는 앱, 서비스 및 OpenShift와 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 주체가 Azure에서 수행할 수 있는 작업에 대한 권한은 사용자가 제어하고 정의합니다. 전체 구독 대신 서비스 특정 리소스 그룹에 사용자의 사용 권한 범위를 지정 하는 것이 좋습니다.

[az ad sp create-for-rbac](/cli/azure/ad/sp)를 사용하여 서비스 주체를 만들고 OpenShift가 필요로 하는 자격 증명을 출력합니다.

다음 예제에서는 서비스 주체를 만들고 openshiftrg라는 리소스 그룹에 대한 contributor 권한을 할당합니다.

먼저 openshiftrg라는 리소스 그룹을 만듭니다.

```azurecli
az group create -l eastus -n openshiftrg
```

서비스 주체 만들기:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Windows를 사용하는 경우 ```az group show --name openshiftrg --query id```를 실행하고 $scope 대신 출력을 사용합니다.

명령에서 반환되는 appId 속성을 기록해 둡니다.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 보안 암호를 만들어야 합니다. [Azure AD 암호 규칙 및 제한 사항](/azure/active-directory/active-directory-passwords-policy) 지침을 따르세요.

서비스 주체에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 서비스 주체 만들기](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Resource Manager 템플릿을 적용할 필수 구성 요소

암호는 SSH 개인 키에 대해 생성 될 필요 합니다 (**sshPrivateKey**)를 Azure AD 클라이언트 암호 (**aadClientSecret**), OpenShift 관리자 암호 (**openshiftPassword** ), 및 Red Hat 구독 관리자 암호 또는 정품 인증 키 (**rhsmPasswordOrActivationKey**).  또한 사용자 지정 SSL 인증서를 사용 하는 경우 다음 6 추가 암호를 만들어야 할- **routingcafile**, **routingcertfile**하십시오 **routingkeyfile**를 **mastercafile**하십시오 **mastercertfile**, 및 **masterkeyfile**합니다.  이러한 매개 변수를 자세히 설명 되어 있습니다.

템플릿을 참조 특정 비밀 이름 이므로 있습니다 **해야 합니다** (대/소문자 구분) 위에 나열 된 굵게 표시 된 이름을 사용 합니다.

### <a name="custom-certificates"></a>사용자 지정 인증서

기본적으로 템플릿은 OpenShift 웹 콘솔 및 라우팅 도메인에 대 한 자체 서명 된 인증서를 사용 하 여 OpenShift 클러스터를 배포 합니다. 사용자 지정 SSL 인증서를 사용 하려는 경우 '사용자 지정'으로 ' routingCertType' 및 'masterCertType' '사용자 지정'으로 설정 합니다.  인증서.pem 형식으로 CA, 인증서 및 키 파일을 해야 합니다.  하나 있지만 다른 사용자 지정 인증서를 사용 하는 것이 가능 합니다.

Key Vault 암호에 이러한 파일을 저장 해야 합니다.  개인 키에 사용 되는 것과 동일한 키 자격 증명 모음을 사용 합니다.  비밀 이름에 대 한 6 추가 입력이 필요 합니다 아닌 템플릿을 각 SSL 인증서 파일의 특정 비밀 이름을 사용 하도록 하드 코딩 됩니다.  다음 표의 정보를에서 사용 하 여 인증서 데이터를 저장 합니다.

| 비밀 이름      | 인증서 파일   |
|------------------|--------------------|
| mastercafile     | 마스터 CA 파일     |
| mastercertfile   | 마스터 인증서 파일   |
| masterkeyfile    | master 키 파일이    |
| routingcafile    | 라우팅 CA 파일    |
| routingcertfile  | 라우팅 인증서 파일  |
| routingkeyfile   | 라우팅 키 파일   |

Azure CLI를 사용 하 여 비밀을 만듭니다. 예제는 다음과 같습니다.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>다음 단계

이 문서는 다음 항목을 설명합니다.
> [!div class="checklist"]
> * OpenShift 클러스터에 대한 SSH 키를 관리하는 키 자격 증명 모음을 만듭니다.
> * Azure 클라우드 솔루션 공급자가 사용할 서비스 주체를 만듭니다.

다음으로 OpenShift 클러스터를 배포합니다.

- [OpenShift Container Platform 배포](./openshift-container-platform.md)
- [OpenShift 컨테이너 플랫폼 자체 관리 되는 Marketplace 제품을 배포](./openshift-marketplace-self-managed.md)