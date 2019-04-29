---
title: Azure에서 OpenShift 컨테이너 플랫폼 자체 관리 되는 Marketplace 제품 배포 | Microsoft Docs
description: Azure에서 OpenShift 컨테이너 플랫폼 자체 관리 되는 Marketplace 제품을 배포 합니다.
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
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 1228c770799de37c85b8a48b1dc923ac8294eeca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773557"
---
# <a name="configure-prerequisites"></a>필수 조건 구성

Azure에서 자체 관리 되는 OpenShift Container Platform 클러스터를 배포 하는 Marketplace 제품을 사용 하기 전에 몇 가지 필수 구성 요소 구성 되어야 합니다.  읽기를 [OpenShift 필수 구성 요소](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) 를 만드는 지침에 대 한 문서는 ssh 키 (암호) 없이, Azure 주요 자격 증명 모음, 키 자격 증명 모음 암호 및 서비스 주체.

 
## <a name="deploy-using-the-marketplace-offer"></a>Marketplace 제품을 사용 하 여 배포

사용 하는 가장 간단한 방법은 Azure에 자체 관리 되는 OpenShift Container Platform 클러스터를 배포 하는 것은 [Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview)합니다.

이 옵션은 가장 간단 하 고 있지만 사용자 지정 기능이 제한 했습니다. OpenShift Container Platform 3.11.82를 배포 하 고 다음 구성 옵션을 포함 하는 Marketplace 제품:

- **마스터 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 마스터 노드입니다.
- **인프라 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 인프라 노드입니다.
- **노드**: 노드 수 (1부터 9) 및 인스턴스 유형을 구성할 수 있습니다.
- **디스크 유형**: Managed Disks가 사용됩니다.
- **네트워킹**: 신규 또는 기존 네트워크와 CIDR 범위를 사용자 지정을 지원 합니다.
- **CNS**: CNS를 사용하도록 설정할 수 있습니다.
- **메트릭**: 메트릭을 사용하도록 설정할 수 있습니다.
- **로깅**: 로깅을 사용하도록 설정할 수 있습니다.
- **Azure 클라우드 공급 기업**: 기본적으로 사용을 비활성화할 수 있습니다.

Azure portal의 왼쪽 위를 클릭 **리소스 만들기**'openshift container platform' 검색 상자에 입력 하 고 Enter 키를 누릅니다.

   ![새 리소스 검색](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

결과 페이지를 사용 하 여 열립니다 **Red Hat OpenShift 컨테이너 플랫폼 Self-Managed** 목록에서입니다. 

   ![새 리소스 검색 결과](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

제품의 세부 정보를 보려면 제품을 클릭 합니다. 이 제품을 배포 하려면 클릭 **만들기**합니다. 필요한 매개 변수를 입력 하는 UI가 표시 됩니다. 첫 번째 화면의 합니다 **기본 사항** 블레이드입니다.

   ![제품 제목 페이지](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**기본 사항**

마우스로 입력된 매개 변수 중 하나에서 도움말을 보려면 합니다 ***있습니까*** 매개 변수 이름 옆에 있는 합니다.

입력된 매개 변수의 값을 입력 하 고 클릭 **확인**합니다.

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| VM 관리자 사용자 이름 | 모든 VM 인스턴스에서 만들어질 관리자 사용자 |
| SSH 공개 키 관리 사용자에 대 한 | VM에 로그인 하는 데 SSH 공개 키 암호가 없어야 합니다. |
| 구독 | Azure 구독에 클러스터를 배포 하려면 |
| 리소스 그룹 | 새 리소스 그룹을 만들거나 클러스터 리소스에 대 한 기존 빈 리소스 그룹 선택 |
| Location | 클러스터에 배포할 azure 지역 |

   ![기본 사항 블레이드를 제공 합니다.](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**인프라 설정**

입력된 매개 변수의 값을 입력 하 고 클릭 **확인**합니다.

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| OCP 클러스터 이름 접두사 | 모든 VM 인스턴스에서 만들어질 관리자 사용자 |
| 마스터 노드 크기 | 기본 VM 크기를 그대로 사용 하거나 클릭 **크기를 변경** 다른 VM 크기를 선택 합니다.  작업 부하에 대 한 적절 한 VM 크기 선택 |
| 인프라 노드 크기 | 기본 VM 크기를 그대로 사용 하거나 클릭 **크기를 변경** 다른 VM 크기를 선택 합니다.  작업 부하에 대 한 적절 한 VM 크기 선택 |
| 응용 프로그램 노드 수 | 기본 VM 크기를 그대로 사용 하거나 클릭 **크기를 변경** 다른 VM 크기를 선택 합니다.  작업 부하에 대 한 적절 한 VM 크기 선택 |
| 응용 프로그램 노드 크기 | 기본 VM 크기를 그대로 사용 하거나 클릭 **크기를 변경** 다른 VM 크기를 선택 합니다.  작업 부하에 대 한 적절 한 VM 크기 선택 |
| 요새 호스트 크기 | 기본 VM 크기를 그대로 사용 하거나 클릭 **크기를 변경** 다른 VM 크기를 선택 합니다.  작업 부하에 대 한 적절 한 VM 크기 선택 |
| 새 또는 기존 가상 네트워크 | (기본값) 새 vNet을 만들거나 기존 vNet 사용 |
| CIDR 기본 설정을 선택 하거나 IP 범위 (CIDR)를 사용자 지정 | 기본 CIDR 범위 또는 선택 허용 **사용자 지정 IP 범위** 사용자 지정 CIDR 정보를 입력 합니다.  10.2.0.0/16, 서브넷 및 10.3.0.0/16 사용 하 여 계산 및 cn 서브넷 기본 설정을 10.0.0.0/14, 10.1.0.0/16, 인프라를 사용 하 여 마스터 서브넷의 CIDR을 사용 하 여 vNet 만들기는 |
| 키 자격 증명 모음 리소스 그룹 이름 | Key Vault를 포함 하는 리소스 그룹의 이름 |
| Key Vault 이름 | 사용 하 여 암호를 포함 하는 키 자격 증명 모음의 이름을 ssh 개인 키입니다.  허용 및 3 자에서 24 자 사이 여야 하며 영숫자 문자와 대시만 |
| 비밀 이름 | 이름을 포함 하는 암호는 ssh 개인 키입니다.  영숫자 및 대시만 허용 됩니다. |

   ![제품 인프라 블레이드에서](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**크기 변경**

다른 VM 크기를 선택 하려면 클릭 ***크기를 변경***합니다.  VM 선택 창이 열립니다.  누르고 원하는 VM 크기 선택 **선택**합니다.

   ![VM 크기 선택](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**기존 가상 네트워크**

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| 기존 가상 네트워크 이름 | 기존 vNet의 이름 |
| 마스터 노드에 대 한 서브넷 이름 | 마스터 노드에 대 한 기존 서브넷의 이름입니다.  RFC 1918에 따라 최소 16 개의 IP 주소가 포함 해야 |
| 서브넷 이름에 대 한 인프라 노드 | 이름을 기존 서브넷에 대 한 인프라 노드.  RFC 1918에 따라 최소한 32 IP 주소를 포함 해야 |
| 계산 및 cn 노드에 대 한 서브넷 이름 | 계산 및 cn 노드에 대 한 기존 서브넷의 이름입니다.  RFC 1918에 따라 최소한 32 IP 주소를 포함 해야 |
| 기존 가상 네트워크에 대 한 리소스 그룹 | 기존 vNet을 포함 하는 리소스 그룹의 이름 |

   ![기존 vnet 인프라를 제공 합니다.](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**사용자 지정 IP 범위**

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| 가상 네트워크의 주소 범위 | VNet에 대 한 사용자 지정 CIDR |
| 마스터 노드를 포함 하는 서브넷의 주소 범위 | 마스터 서브넷에 대 한 사용자 지정 CIDR |
| 인프라 노드를 포함 하는 서브넷의 주소 범위 | 인프라 서브넷용 CIDR 사용자 지정 |
| 계산 및 cn 노드를 포함 하는 서브넷에 대 한 주소 범위 | 계산 및 cn 노드에 대 한 사용자 지정 CIDR |

   ![IP 범위를 사용자 지정 하는 인프라를 제공 합니다.](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

입력 매개 변수 값을 입력 하 고 클릭 **확인**

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| OpenShift 관리자 사용자 암호 | 초기 OpenShift 사용자 암호입니다.  이 사용자는 클러스터 관리자 수도 |
| OpenShift 관리자 사용자 암호를 확인 합니다. | OpenShift 관리자 사용자 암호를 다시 입력 |
| Red Hat 구독 관리자 사용자 이름 | Red Hat 구독 또는 조직 id입니다. 액세스 하려면 사용자 이름  이 자격 증명은 구독에 RHEL 인스턴스를 등록 하는 데 사용 되며 Microsoft 또는 Red Hat에서 저장 되지 않습니다. |
| Red Hat 구독 관리자 사용자 암호 | Red Hat 구독 또는 정품 인증 키에 액세스 하려면 암호를 제공 합니다.  이 자격 증명은 구독에 RHEL 인스턴스를 등록 하는 데 사용 되며 Microsoft 또는 Red Hat에서 저장 되지 않습니다. |
| Red Hat Subscription Manager OpenShift 풀 ID | OpenShift Container Platform 자격을 포함 하는 풀의 ID입니다. OpenShift Container Platform 클러스터 설치에 대 한 충분 한 자격 확인 |
| Broker에 대 한 Red Hat Subscription Manager OpenShift 풀 ID / 마스터 노드 | Broker에 대 한 OpenShift Container Platform 권리를 포함 하는 ID를 풀 / 마스터 노드. OpenShift Container Platform 클러스터 설치에 대 한 충분 한 자격 확인 합니다. Broker를 사용 하지 않는 경우 / 마스터 풀 ID, 응용 프로그램 노드에 대 한 풀 ID를 입력 합니다. |
| Azure 클라우드 공급자를 구성 합니다. | Azure Cloud Provider를 사용 하도록 OpenShift를 구성 합니다. 영구적 볼륨에 대 한 Azure 디스크를 사용 하 여 연결 하는 경우 필요 합니다.  기본값은 Yes |
| Azure AD 서비스 주체 클라이언트 ID GUID | Azure AD 서비스 주체 클라이언트 ID GUID-AppID 라고도 합니다. Azure Cloud Provider 구성으로 설정 하는 경우에 필요 **예** |
| Azure AD 서비스 주체 클라이언트 ID 암호 | Azure AD 서비스 주체 클라이언트 ID 암호입니다. Azure Cloud Provider 구성으로 설정 하는 경우에 필요 **예** |
 
   ![OpenShift 블레이드를 제공 합니다.](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**추가 설정**

추가 설정 블레이드에서 glusterfs 저장소에 대 한 CN의 구성을 허용 로깅, 메트릭 및 라우터 하위 도메인입니다.  이러한 옵션 중 하나를 설치 하지 않습니다 기본값과 테스트용 nip.io 라우터 하위 도메인으로 사용 됩니다. CN을 사용 하도록 설정 하면 glusterfs pod를 호스트 하는 세 개의 추가 연결 된 디스크를 사용 하 여 세 개의 추가 계산 노드를 설치 합니다.  

입력 매개 변수 값을 입력 하 고 클릭 **확인**

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| 기본 저장소 컨테이너 (CN) 구성 | CN 설치는 OpenShift 클러스터 및 저장소로 사용 하도록 설정 합니다. Azure 공급자를 사용 하지 않도록 설정 하는 경우 기본 언어가 됩니다. |
| 클러스터 로깅 구성 | 클러스터에 EFK 로깅 기능을 설치합니다.  노드 호스트 EFK pod 적절 하 게 인프라 크기 조정 |
| 클러스터에 대 한 메트릭 구성 | OpenShift 클러스터에 Hawkular 메트릭을 설치합니다.  노드 호스트 Hawkular 메트릭 pod 적절 하 게 인프라 크기 조정 |
| 기본 라우터 하위 도메인 | Nipio 테스트 또는 프로덕션에 대 한 고유한 하위 도메인을 입력 하는 사용자 지정 선택 |
 
   ![제품 추가 블레이드](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**추가 설정-추가 매개 변수**

| 매개 변수를 입력 합니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| (CN) 노드 크기 | 기본 노드 크기를 그대로 사용 하거나 선택 **크기를 변경** 새 VM 크기를 선택 합니다. |
| 사용자 지정 하위 도메인 입력 | OpenShift 클러스터에서 라우터를 통해 응용 프로그램을 노출 하는 데 사용할 사용자 지정 라우팅 도메인.  적절 한 와일드 카드 DNS 항목을 만들고 해야] |
 
   ![추가 cn 제품 설치](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**요약**

유효성 검사는 선택한 클러스터에 대 한 Vm의 총 수를 배포 하려면 코어 할당량 부족을 확인 하려면이 단계에서 발생 합니다.  입력 된 모든 매개 변수를 검토 합니다.  입력을 허용 가능한 경우 클릭 **확인** 를 계속 합니다.

   ![제품 요약 블레이드](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**구입**

구매 페이지에서 연락처 정보를 확인 하 고 클릭 **구매** 사용 약관에 동의 하 여 OpenShift Container Platform 클러스터의 배포를 시작 합니다.

   ![제품 구매 블레이드](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. 사용 하 여 브라우저를 사용 하 여 OpenShift 콘솔에 연결 합니다 **OpenShift 콘솔 URL**합니다. 요새 호스트에 SSH 수도 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com/container-platform)

