---
title: Azure Key Vault에 인증
description: Azure Key Vault에 인증하는 방법을 알아봅니다.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6336a0d4d8aa9c781befed0470d9a190af5aa9eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930862"
---
# <a name="authenticate-to-azure-key-vault"></a>Azure Key Vault에 인증

## <a name="overview"></a>개요

Azure Key Vault는 애플리케이션 비밀의 스토리지를 중앙 집중화하고 해당 배포를 제어할 수 있는 비밀 관리 솔루션입니다. Azure Key Vault를 사용하면 자격 증명을 애플리케이션에 저장할 필요가 없습니다. 애플리케이션에서 키 자격 증명 모음에 인증하여 필요한 자격 증명을 검색할 수 있습니다. 이 문서에서는 키 자격 증명 모음에 대한 인증의 기본 사항에 대해 설명합니다.

이 문서는 키 자격 증명 모음 인증의 작동 방식을 이해하는 데 도움이 됩니다. 이 문서에서는 인증 흐름에 대해 설명하고, 키 자격 증명 모음에 대한 액세스 권한을 부여하는 방법을 보여 주며, python 애플리케이션 샘플에서 키 자격 증명 모음에 저장된 비밀을 검색하는 자습서가 포함되어 있습니다.

이 문서에서 다루는 내용은 다음과 같습니다.

* 주요 개념
* 보안 주체 등록
* Key Vault 인증 흐름 이해
* Key Vault에 대한 서비스 주체 액세스 권한 부여
* 자습서(Python)

## <a name="key-concepts"></a>주요 개념

### <a name="azure-active-directory-concepts"></a>Azure Active Directory 개념

* AAD(Azure Active Directory) - Azure AD(Azure Active Directory)는 직원이 로그인하여 리소스에 액세스하는 데 도움이 되는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.

* 역할 정의 - 역할 정의는 권한 컬렉션입니다.  AAD에는 Azure 리소스에서 읽기, 쓰기 및 삭제와 같은 작업을 수행할 수 있는 권한 수준이 포함된 표준 역할(소유자, 기여자 또는 읽기 권한자)이 있습니다. 또한 역할은 세분화된 특정 권한이 있는 사용자가 만든 사용자 지정 정의일 수 있습니다.

* 애플리케이션 등록 - Azure AD 애플리케이션을 등록하면 애플리케이션 개체와 서비스 주체 개체라는 두 개의 개체가 Azure AD 테넌트에 만들어집니다. 애플리케이션 개체는 모든 테넌트에서 사용하기 위한 애플리케이션에 대한 글로벌 표현으로 간주되고, 서비스 주체는 특정 테넌트에서 사용하기 위한 로컬 표현으로 간주됩니다.

### <a name="security-principal-concepts"></a>보안 주체 개념

* 보안 주체 - 보안 주체는 Azure 리소스에 대한 액세스를 요청하는 사용자, 그룹, 서비스 주체 또는 관리 ID를 나타내는 개체입니다.

* 사용자 - Azure Active Directory에 프로필이 있는 개인.

* 그룹 - Azure Active Directory에서 만든 사용자 집합. 그룹에 역할을 할당하면 해당 그룹의 모든 사용자가 같은 역할을 갖습니다.

* 서비스 주체 - 애플리케이션 또는 서비스에서 특정 Azure 리소스에 액세스하기 위해 사용하는 보안 ID. 이는 애플리케이션에 대한 사용자 ID(사용자 이름 및 암호 또는 인증서)로 간주할 수 있습니다.

* 관리 ID - Azure에서 자동으로 관리하는 Azure Active Directory의 ID입니다.

* 개체 ID(클라이언트 ID) - 초기 프로비저닝 중에 Azure AD에서 생성하여 서비스 주체에 연결되는 고유 식별자입니다.

## <a name="security-principal-registration"></a>보안 주체 등록

1. 관리자가 사용자 또는 애플리케이션(서비스 주체)을 Azure Active Directory에 등록합니다.

2. 관리자가 Azure Key Vault를 만들고, 액세스 정책(ACL)을 구성합니다.

3. (선택 사항) 관리자가 Azure Key Vault 방화벽을 구성합니다.

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Key Vault 인증 흐름 이해

1. 서비스 주체에서 AAD에 인증하는 호출을 수행합니다. 이는 다음과 같이 여러 가지 방법으로 수행할 수 있습니다.
    * 사용자가 사용자 이름과 암호를 사용하여 Azure Portal에 로그인할 수 있습니다.
    * 애플리케이션에서 클라이언트 ID를 사용하고, 클라이언트 암호 또는 클라이언트 인증서를 AAD에 제공합니다.
    * 가상 머신과 같은 Azure 리소스에서 할당된 MSI를 사용하고 IMDS REST 엔드포인트에 연결하여 액세스 토큰을 가져옵니다.

2. AAD에 대한 인증이 성공하면 OAuth 토큰이 서비스 주체에 부여됩니다.
3. 서비스 주체에서 Key Vault를 호출합니다.
4. Azure Key Vault 방화벽에서 호출을 허용할지 여부를 결정합니다.
    * 시나리오 1: Key Vault 방화벽이 사용하지 않도록 설정되어 있고, 퍼블릭 인터넷에서 키 자격 증명 모음의 퍼블릭 엔드포인트(URI)에 연결할 수 있습니다. 호출이 허용됩니다.
    * 시나리오 2: 호출자가 Azure Key Vault 신뢰할 수 있는 서비스입니다. 이 옵션을 선택하면 특정 Azure 서비스에서 키 자격 증명 모음 방화벽을 무시할 수 있습니다. [Key Vault 신뢰할 수 있는 서비스 목록](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * 시나리오 3: 호출자가 IP 주소, 가상 네트워크 또는 서비스 엔드포인트별로 Azure Key Vault 방화벽에 나열됩니다.
    * 시나리오 4: 호출자가 구성된 프라이빗 링크 연결을 통해 Azure Key Vault에 연결할 수 있습니다.
    * 시나리오 5: 호출자에 권한이 부여되지 않고, 금지됨 응답이 반환됩니다.
5. Key Vault에서 AAD를 호출하여 서비스 주체의 액세스 토큰을 확인합니다.
6. Key Vault에서 요청된 작업을 수행하는 데 충분한 액세스 정책 권한이 서비스 주체에 있는지 확인합니다. 이 예에서 작업은 비밀 가져오기입니다.
7. Key Vault에서 서비스 주체에 대한 비밀을 제공합니다.

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Key Vault에 대한 서비스 주체 액세스 권한 부여

1. 서비스 주체가 아직 없는 경우 이를 만듭니다. [서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Azure Key Vault IAM 설정에서 서비스 주체에 대한 역할 할당을 추가합니다. 미리 할당된 소유자, 기여자 또는 읽기 권한자 역할을 추가할 수 있습니다. 서비스 주체에 대한 사용자 지정 역할을 만들 수도 있습니다. 최소 권한의 원칙을 따르고, 서비스 주체에 필요한 최소한의 액세스 권한만 제공해야 합니다. 
3.  키 자격 증명 모음 방화벽을 구성합니다. 키 자격 증명 모음 방화벽을 사용하지 않도록 설정된 상태를 유지하고, 퍼블릭 인터넷에서 액세스를 허용할 수 있습니다(보안 수준 낮음, 쉽게 구성할 수 있음). 또한 특정 IP 범위, 서비스 엔드포인트, 가상 네트워크 또는 프라이빗 엔드포인트에 대한 액세스를 제한할 수 있습니다(보안 수준 높음).
4.  서비스 주체에 대한 액세스 정책을 추가합니다. 이는 서비스 주체에서 키 자격 증명 모음에 대해 수행할 수 있는 작업의 목록입니다. 최소 권한의 원칙을 사용하고, 서비스 주체에서 수행할 수 있는 작업을 제한해야 합니다. 그러나 충분한 권한을 제공하지 않으면 서비스 주체의 액세스가 거부됩니다.

## <a name="tutorial"></a>자습서

이 자습서에서는 키 자격 증명 모음에 인증하고 비밀을 검색하도록 서비스 주체를 설정하는 방법에 대해 알아봅니다. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>1부:  Azure Portal에서 서비스 주체 만들기

1. Azure Portal에 로그인
1. Azure Active Directory 검색
1. "앱 등록" 탭 클릭
1. "+ 새 등록" 클릭
1. 서비스 주체 이름 만들기
1. 등록 선택

이 시점에서 등록된 서비스 주체가 있습니다. "앱 등록"을 선택하면 볼 수 있습니다. 이제 서비스 주체에 클라이언트 ID GUID가 할당됩니다. 이는 서비스 주체에 대한 "사용자 이름"으로 간주됩니다. 이제 서비스 주체에 대한 "암호"를 만들어야 합니다. 클라이언트 암호 또는 클라이언트 인증서를 사용할 수 있습니다. 클라이언트 암호를 인증에 사용하는 것은 안전하지 않으며 테스트 목적으로만 사용해야 합니다. 이 자습서에서는 클라이언트 인증서를 사용하는 방법을 보여 줍니다.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>2부: 서비스 주체에 대한 클라이언트 인증서 만들기

1. 인증서 만들기

    * 옵션 1: [OpenSSL](https://www.openssl.org/)을 사용하여 인증서를 만듭니다(테스트 목적으로만 사용, 프로덕션에서 자체 서명된 인증서 사용 안 함).
    * 옵션 2: 키 자격 증명 모음을 사용하여 인증서 만듭니다. [Azure Key Vault에서 인증서 만들기](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. PEM/PFX 형식으로 인증서 다운로드
1. Azure Portal에 로그인 및 Azure Active Directory로 이동
1. "앱 등록" 클릭
1. 1부에서 만든 서비스 주체 선택
1. 서비스 주체의 "인증서 및 비밀" 탭 클릭
1. "인증서 업로드" 단추를 사용하여 인증서 업로드

### <a name="part-3-configure-an-azure-key-vault"></a>3부: Azure Key Vault 구성

1. Azure Key Vault [링크](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) 만들기

2. Key Vault IAM 권한 구성
    1. 키 자격 증명 모음으로 이동
    1. "액세스 제어(IAM)" 탭 선택
    1. 역할 할당 추가 클릭
    1. 드롭다운에서 "기여자" 역할 선택
    1. 만든 서비스 주체의 이름 또는 클라이언트 ID 입력
    1. "역할 할당 보기"를 클릭하여 서비스 주체가 나열되는지 확인

3. Key Vault 액세스 정책 권한 구성
    1. 키 자격 증명 모음으로 이동
    1. "설정" 아래에서 "액세스 정책" 탭 선택
    1. "+ 액세스 정책 추가" 링크 선택
    1. [비밀 권한] 드롭다운 아래에서 "가져오기" 및 "목록" 권한 확인
    1. 이름 또는 클라이언트 ID로 서비스 주체 선택
    1. "추가" 선택
    1. "저장" 선택

4. 키 자격 증명 모음에 비밀 만들기
    1. 키 자격 증명 모음으로 이동
    1. "설정" 아래에서 "비밀" 탭 클릭
    1. "+ 생성/가져오기" 클릭
    1. 비밀 이름 만들기(이 예에서는 비밀 이름을 "test"로 지정함)
    1. 비밀 값 만들기(이 예에서는 값을 "password123"으로 설정함)

이제 로컬 컴퓨터에서 코드를 실행하면 클라이언트 ID와 인증서의 경로를 제공하여 액세스 토큰을 가져와서 키 자격 증명 모음에 인증할 수 있습니다.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>4부. 애플리케이션(Python)의 Azure Key Vault에서 비밀 검색

다음 코드 샘플을 사용하여 애플리케이션에서 구성한 서비스 주체를 사용하여 키 자격 증명 모음에서 비밀을 검색할 수 있는지 여부를 테스트합니다. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>다음 단계

1. 키 자격 증명 모음 인증 오류 문제를 해결하는 방법을 알아봅니다. [Key Vault 문제 해결 가이드](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
