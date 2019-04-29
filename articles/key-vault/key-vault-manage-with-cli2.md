---
title: CLI를 사용하여 Azure Key Vault 관리 - Azure Key Vault | Microsoft Docs
description: 이 문서를 참조하여 Azure CLI를 사용해 Key Vault에서 일반 작업을 자동화할 수 있습니다.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: barclayn
ms.openlocfilehash: 1679fbe0dedc88ca3e8293512f9a79bb7da69790
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641027"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Azure CLI를 사용하여 Key Vault 관리 

이 문서에서는 Azure CLI를 사용하여 Azure Key Vault 관련 작업을 시작하는 방법을 설명합니다.  다음과 같은 정보를 확인할 수 있습니다.

- Azure에서 확정된 컨테이너(자격 증명 모음)를 만드는 방법
- 키 자격 증명 모음에 키, 비밀 또는 인증서 추가
- Azure Active Directory에 애플리케이션 등록
- 키 또는 비밀을 사용하여 애플리케이션에 권한 부여
- 키 자격 증명 모음 고급 액세스 정책 설정
- HSM(하드웨어 보안 모듈) 작업
- 키 자격 증명 모음 및 연결된 키와 비밀 삭제
- 다양한 Azure 플랫폼 간 명령줄 인터페이스 명령


Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

> [!NOTE]
> 이 문서에서는 단계 중 하나에 포함된 Azure 애플리케이션을 작성하는 방법에 대한 지침을 포함하고 있지 않지만, 키 자격 증명 모음에서 키 또는 비밀을 사용할 수 있도록 애플리케이션에 권한을 부여하는 방법을 보여 줍니다.
>

Azure Key Vault에 대한 개요는 [Azure Key Vault란?](key-vault-whatis.md)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서 Azure CLI 명령을 사용하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure 명령줄 인터페이스 버전 2.0 이상. 최신 버전을 설치하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* 이 문서에서 만드는 키 또는 암호를 사용하도록 구성되는 애플리케이션. 샘플 애플리케이션은 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45343)에서 사용할 수 있습니다. 지침은 포함된 추가 정보 파일을 참조하세요.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure 플랫폼 간 명령줄 인터페이스 도움말 보기

이 문서에서는 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에 익숙하다고 가정합니다.

--help 또는 -h 매개 변수를 사용하여 특정 명령에 대한 도움말을 볼 수 있습니다. 또는 Azure help [명령] [옵션] 형식도 사용할 수 있습니다. 명령에 필요한 매개 변수가 확실하지 않은 경우 도움말을 참조하세요. 예를 들어 다음 명령은 모두 동일한 정보를 반환합니다.

```azurecli
az account set --help
az account set -h
```

다음 문서를 참조하여 Azure 플랫폼 간 명령줄 인터페이스에서 Azure Resource Manager를 익힐 수 있습니다.

* [Azure CLI 설치](/cli/azure/install-azure-cli)
* [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Azure에서 확정된 컨테이너(자격 증명 모음)를 만드는 방법

자격 증명 모음은 하드웨어 보안 모듈의 지원을 받는 보안 컨테이너입니다. 자격 증명 모음은 애플리케이션 비밀을 중앙 집중식으로 저장하여 보안 정보의 우발적인 손실 가능성을 줄이는 데 도움이 됩니다. Key Vault는 또한 저장된 모든 것에 대한 액세스를 제어하고 기록합니다. Azure Key Vault는 TLS(전송 계층 보안) 인증서의 요청 및 갱신을 처리할 수 있으므로, 강력한 인증서 수명 주기 관리 솔루션에 필요한 기능을 제공합니다. 다음 단계에서는 자격 증명 모음을 만듭니다.

### <a name="connect-to-your-subscriptions"></a>구독에 연결

대화형으로 로그인하려면 다음 명령을 사용합니다.

```azurecli
az login
```
조직 계정을 사용하여 로그인하려면 사용자 이름과 암호를 입력해야 합니다.

```azurecli
az login -u username@domain.com -p password
```

둘 이상의 구독이 있고 사용할 구독을 지정해야 하는 경우 다음을 입력하여 계정에 대한 구독을 확인합니다.

```azurecli
az account list
```

subscription 매개 변수를 사용하여 구독을 지정합니다.

```azurecli
az account set --subscription <subscription name or ID>
```

Azure 플랫폼 간 명령줄 인터페이스를 구성하는 방법에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="create-a-new-resource-group"></a>새 리소스 그룹 만들기

Azure 리소스 관리자를 사용하면 관련된 모든 리소스는 리소스 그룹의 내부에 만들어집니다. 기존 리소스 그룹에 키 자격 증명 모음을 만들 수 있습니다. 새 리소스 그룹을 사용하려는 경우 새 리소스 그룹을 만들 수 있습니다.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

첫 번째 매개 변수는 리소스 그룹 이름이고, 두 번째 매개 변수 위치입니다. 가능한 위치 형식의 전체 목록을 가져오려면:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>키 자격 증명 모음 리소스 공급자 등록

 새 키 자격 증명 모음을 만들려고 하면 "구독이 'Microsoft.KeyVault' 네임스페이스를 사용하도록 등록되지 않았습니다"라는 오류 메시지가 나타날 수 있습니다. 이 오류 메시지가 나타나면 Key Vault 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

`az keyvault create` 명령을 사용하여 키 자격 증명 모음을 만듭니다. 이 스크립트에는 3개의 필수 매개 변수, 리소스 그룹 이름, 키 자격 증명 모음 이름 및 지리적 위치가 있습니다.

**동아시아 지역**에 있는 **ContosoResourceGroup** 리소스 그룹에 이름이 **ContosoKeyVault**인 새 자격 증명 모음을 만들려면 다음을 입력합니다. 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

이 명령의 출력에는 만든 자격 증명 모음의 속성이 표시됩니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

* **name**: 이 예제에서 이름은 ContosoKeyVault입니다. 이 이름은 다른 Key Vault 명령에 사용됩니다.
* **vaultUri**: 이 예제에서 URI는 https://contosokeyvault.vault.azure.net입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

Azure 계정은 이제 이 키 자격 증명 모음에서 모든 작업을 수행할 권한을 가지게 됩니다. 아직까지는 권한이 부여된 사용자가 없습니다.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>키 자격 증명 모음에 키, 비밀 또는 인증서 추가

Azure Key Vault에서 소프트웨어 보호 키를 만들도록 하려면 `az key create` 명령을 사용합니다.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

.pem 파일에 기존 키가 있는 경우 Azure Key Vault에 업로드할 수 있습니다. 소프트웨어 또는 HSM을 사용하여 키를 보호하도록 선택할 수 있습니다. 이 예제에서는 .pem 파일에서 키를 가져와서 소프트웨어에서 "hVFkk965BuUv"라는 암호를 사용하여 보호합니다.

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

이제 해당 URI를 사용하여 Azure Key Vault를 만들거나 업로드하는 이 키를 참조할 수 있습니다. 항상 현재 버전을 가져오려면 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**를 사용합니다. 이 특정 버전을 가져오려면 https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id]를 사용합니다. 예를 들어 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**입니다. 

자격 증명 모음에 암호가 SQLPassword이고 Azure Key Vault에 대한 "hVFkk965BuUv" 값이 있는 비밀을 추가합니다. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

해당 URI를 사용하여 이 암호를 참조합니다. **https://ContosoVault.vault.azure.net/secrets/SQLPassword**를 사용하여 항상 현재 버전을 가져오고, https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id]를 사용하여 이 특정 버전을 가져옵니다. 예를 들어 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**입니다.

.pem 또는 .pfx를 사용하여 인증서를 자격 증명 모음으로 가져옵니다.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

만든 키, 암호 또는 인증서를 살펴보겠습니다.

* 키를 보려면 다음을 입력합니다. 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* 비밀을 보려면 다음을 입력합니다. 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* 인증서를 보려면 다음을 입력합니다. 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Azure Active Directory에 애플리케이션 등록

이 단계는 일반적으로 별도의 컴퓨터에서 개발자가 수행할 수 있습니다. Azure Key Vault에만 한정되지는 않지만 이해를 돕기 위해 여기에 포함되었습니다. 애플리케이션 등록을 완료하려면 계정, 자격 증명 모음 및 애플리케이션이 동일한 Azure 디렉터리에 있어야 합니다.

자격 증명 모음 키를 사용하는 애플리케이션은 Azure Active Directory에서 토큰을 사용하여 인증해야 합니다.  애플리케이션 소유자가 먼저 Azure Active Directory에 등록해야 합니다. 등록 끝에 애플리케이션 소유자는 다음 값을 가져옵니다.

- **애플리케이션 ID**(AAD 클라이언트 ID 또는 appID라고도 함)
- **인증 키**(또한 공유 암호라고도 함) 

애플리케이션은 토큰을 가져올 Azure Active Directory에 이 두 값 모두가 있어야 합니다. 토큰을 가져오도록 애플리케이션을 구성하는 방법은 애플리케이션에 따라 다릅니다. [Key Vault 샘플 애플리케이션](https://www.microsoft.com/download/details.aspx?id=45343)의 경우, 애플리케이션 소유자는 app.config 파일에서 이러한 값을 설정합니다.

Azure Active Directory에 애플리케이션을 등록하는 방법에 대한 자세한 단계를 알아보려면 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/active-directory-integrating-applications.md), [포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure CLI를 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli) 문서를 검토해야 합니다.

Azure Active Directory에서 애플리케이션을 등록하려면 다음을 수행합니다.

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>키 또는 비밀을 사용하여 애플리케이션에 권한 부여

애플리케이션이 자격 증명 모음의 키 또는 암호에 대한 액세스를 인증하려면 `az keyvault set-policy` 명령을 사용합니다.

예를 들어 자격 증명 모음 이름이 ContosoKeyVault이고, 애플리케이션의 appID가 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed이며, 자격 증명 모음의 키를 사용하여 암호를 해독하고 서명하도록 애플리케이션에 권한을 부여하려면 다음 명령을 사용합니다.

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

자격 증명 모음에서 비밀을 읽도록 동일한 애플리케이션에 권한을 부여하려면 다음을 실행합니다.

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> 키 자격 증명 모음 고급 액세스 정책 설정

키 자격 증명 모음에 대해 고급 정책을 사용하도록 설정하려면 [az keyvault update](/cli/azure/keyvault#az-keyvault-update)를 사용합니다.

 배포에 Key Vault 사용: Virtual Machines에서 자격 증명 모음으로부터 비밀로 저장된 인증서를 검색할 수 있도록 허용합니다.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

디스크 암호화에 Key Vault 사용: Azure Disk Encryption에 대해 자격 증명 모음을 사용하는 경우에 필요합니다.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

템플릿 배포에 Key Vault 사용: Resource Manager가 자격 증명 모음에서 비밀을 검색할 수 있습니다.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>HSM(하드웨어 보안 모듈) 작업

추가된 보증을 위해 HSM(하드웨어 보안 모듈) 경계를 벗어나지 않는 HSM에서 키를 가져오거나 생성할 수 있습니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. 이 요구 사항이 사용자에게 적용되지 않는 경우, 이 섹션을 건너뛰고 키 자격 증명 모음 및 연결된 키와 비밀 삭제로 이동합니다.

이러한 HSM 보호되는 키를 만들려면, HSM 보호되는 키를 지원하는 자격 증명 모음 구독이 있어야 합니다.

keyvault를 만들 때 'sku' 매개 변수를 추가합니다.

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

이 자격 증명이 모음에 소프트웨어 보호 키(이전에 표시된 대로)와 HSM 보호되는 키를 추가할 수 있습니다. HSM으로 보호되는 키를 만들려면, 대상 매개 변수를 'HSM'으로 설정합니다.

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

다음 명령을 사용하여 사용자 컴퓨터의 .pem 파일에서 키를 가져올 수 있습니다. 이 명령은 키 자격 증명 모음 서비스에서 HSM으로 키를 가져옵니다.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

다음 명령은 "bring your own key"(BYOK) 패키지를 가져옵니다. 이렇게 하면 로컬 HSM에 키를 생성하고 키가 HSM 경계를 벗어나지 않고 이를 키 자격 증명 모음 서비스의 HSM에 전달합니다.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

이 BYOK 패키지를 생성하는 방법에 대한 자세한 내용은 [Azure Key Vault와 HSM-보호된 키를 사용하는 방법](key-vault-hsm-protected-keys.md)을 참조하세요.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>키 자격 증명 모음 및 연결된 키와 비밀 삭제

키 자격 증명 모음 및 해당 키 또는 비밀이 더 이상 필요하지 않은 경우, `az keyvault delete` 명령을 사용하여 키 자격 증명 모음을 삭제할 수 있습니다.

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

또는 키 자격 증명 모음 및 해당 그룹에 포함된 다른 모든 리소스 및 키를 포함하는 전체 Azure 리소스 그룹을 삭제할 수 있습니다.

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>다양한 Azure 플랫폼 간 명령줄 인터페이스 명령

Azure Key Vault를 관리하는 데 유용한 다른 명령도 다음과 같습니다.

이 명령은 모든 키와 선택한 속성을 테이블 형식으로 나열합니다.

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

이 명령은 지정된 키에 대한 속성의 전체 목록을 표시합니다.

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

이 명령은 모든 비밀 이름과 선택한 속성을 테이블 형식으로 표시합니다.

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

다음은 특정 키를 제거하는 방법에 대한 예제입니다.

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

다음은 특정 암호를 제거하는 방법에 대한 예제입니다.

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>다음 단계

- Key Vault 명령에 대한 전체 Azure CLI 참조에 대해서는 [Key Vault CLI 참조](/cli/azure/keyvault)를 참조하세요.

- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

- Azure Key Vault 및 HSM에 대한 내용은 [Azure Key Vault에 HSM으로 보호되는 키 사용](key-vault-hsm-protected-keys.md)을 참조하세요.
