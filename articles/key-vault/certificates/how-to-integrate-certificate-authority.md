---
title: DigiCert 인증 기관과 Key Vault 통합
description: DigiCert 인증 기관과 Key Vault를 통합하는 방법
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 01383acad9f221e376f814ecf99794eb0431d0cd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588928"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>DigiCert 인증 기관과 Key Vault 통합

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 디지털 인증서는 전자 자격 증명으로 전자 트랜잭션에서 ID 증명을 설정합니다. 

Azure 키 자격 증명 모음 사용자는 Key Vault에서 직접 DigiCert 인증서를 생성할 수 있습니다. Key Vault는 DigiCert 인증 기관과 Key Vault의 트러스트된 파트너 관계를 통해 DigiCert에서 발급한 인증서에 대한 엔드투엔드 인증서 수명 주기 관리를 보장합니다.

인증서에 대한 일반적인 정보는 [Azure Key Vault 인증서](/azure/key-vault/certificates/about-certificates)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 완료하려면 다음 리소스가 필요합니다.
* 키 자격 증명 모음. 다음 빠른 시작 중 하나의 단계에 따라 기존 키 자격 증명 모음을 사용하거나 새로 만들 수 있습니다.
   - [Azure CLI를 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-cli.md)
   - [Azure PowerShell을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-powershell.md)
   - [Azure Portal을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-portal.md)
*   DigiCert CertCentral 계정을 활성화해야 합니다. CertCentral 계정에 [가입](https://www.digicert.com/account/signup/)합니다.
*   계정에서 관리자 수준 권한


### <a name="before-you-begin"></a>시작하기 전에

DigiCert CertCentral 계정에서 다음과 같은 정보를 사용할 수 있는지 확인합니다.
-   CertCentral 계정 ID
-   조직 ID
-   API 키

## <a name="adding-certificate-authority-in-key-vault"></a>Key Vault에서 인증 기관 추가 
DigiCert CertCentral 계정에서 위의 정보를 수집한 후에는 이제 키 자격 증명 모음의 인증 기관 목록에 DigiCert를 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1.  DigiCert 인증 기관을 추가하려면 DigiCert를 추가하려는 키 자격 증명 모음으로 이동합니다. 
2.  Key Vault 속성 페이지에서 **인증서**를 선택합니다.
3.  **인증 기관** 탭을 선택합니다. ![인증서 속성](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  **추가** 옵션을 선택합니다.
 ![인증서 속성](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  **인증서 기관 만들기** 화면에서 다음 값을 선택합니다.
    -   **Name**: 식별 가능한 발급자 이름을 추가합니다. 예제 DigicertCA
    -   **공급자**: 메뉴에서 DigiCert를 선택합니다.
    -   **계정 ID**: DigiCert CertCentral 계정 ID를 입력합니다.
    -   **계정 암호**: DigiCert CertCentral 계정에서 생성한 API 키를 입력합니다.
    -   **조직 ID**: DigiCert CertCentral 계정에서 수집된 OrgID 입력 
    -   **만들기**를 클릭합니다.
   
6.  이제 DigicertCA가 인증 기관 목록에 추가된 것을 볼 수 있습니다.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 명령 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. Azure는 브라우저 자체에서 Azure Portal을 통해 사용할 수 있는 대화형 셸 환경인 Azure Cloud Shell을 호스트합니다.

로컬에서 PowerShell을 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Login-AzAccount
```

1.  **리소스 그룹** 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. **Key Vault** 만들기

키 자격 증명 모음에 고유한 이름을 사용해야 합니다. 여기서 "Contoso-Vaultname"은 이 가이드 전체에서 Key Vault의 이름입니다.

- **자격 증명 모음 이름**은 Contoso-Vaultname입니다.
- **리소스 그룹 이름**은 ContosoResourceGroup입니다.
- **위치**는 EastUS입니다.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. DigiCert CertCentral 계정에서 수집된 정보에 대한 변수를 정의합니다.

- **계정 ID** 변수 정의
- **조직 ID** 변수 정의
- **API 키** 변수 정의
- **발급자 이름** 변수 정의

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. **발급자** 설정 키 자격 증명 모음에 Digicert를 인증 기관으로 추가합니다.
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. Key Vault 내의 DigiCert에서 직접 **인증서에 대한 정책을 설정하고 인증서를 발급**합니다.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

이제 이 통합을 통해 지정된 Key Vault 내의 Digicert CA에서 인증서가 성공적으로 발급되었습니다.

## <a name="troubleshoot"></a>문제 해결

발급된 인증서가 Azure Portal의 '사용 안 함' 상태이면 **인증서 작업** 보기를 계속 진행하여 해당 인증서에 대한 DigiCert 오류 메시지를 검토합니다.

 ![인증서 속성](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

- KeyVault를 통해 digicert 와일드 카드 인증서를 생성할 수 있나요? 
   예. digicert 계정을 구성한 방법에 따라 달라집니다.
- EV 인증서를 만들어야 하는 경우 어떻게 지정하나요? 
   인증서를 만들 때 [고급 정책 구성]을 클릭한 다음, 인증서 유형을 지정합니다. 지원되는 값은 다음과 같습니다. OV-SSL, EV-SSL
- 통합을 통해 digicert 인증서를 만드는 방법과 digicert를 통해 직접 인증서를 획득하는 방법 간에 시간 차이가 있나요?
   아니요. 인증서를 만들 때 확인 프로세스를 완료하는 데 시간이 걸릴 수 있으며 확인은 DigiCert가 따르는 프로세스에 따라 달라집니다.


## <a name="next-steps"></a>다음 단계

- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
