---
title: Azure Attestation 기본 개념
description: Azure Attestation에 대한 기본 개념입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 3cd7d2541cb980fc5ca6a1a9c42a430eac1ecb1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99429282"
---
# <a name="basic-concepts"></a>기본 개념

Microsoft Azure Attestation과 관련된 몇 가지 기본 개념은 다음과 같습니다.

## <a name="json-web-token-jwt"></a>JWT(JSON 웹 토큰)

[JWT(JSON Web Token)](https://jwt.io/)는 당사자 간에 정보를 JSON(JavaScript Object Notation) 개체로 안전하게 전송하기 위한 개방형 표준 [RFC7519](https://tools.ietf.org/html/rfc7519) 방법입니다. 이 정보는 디지털 서명되어 있으므로 확인하고 신뢰할 수 있습니다. JWT는 비밀 또는 공개/프라이빗 키 쌍을 사용하여 서명할 수 있습니다.

## <a name="json-web-key-jwk"></a>JWK(JSON 웹 키)

[JWK(JSON Web Key)](https://tools.ietf.org/html/rfc7517)는 암호화 키를 나타내는 JSON 데이터 구조입니다. 또한 이 사양은 JWK 세트를 나타내는 JWK 세트 JSON 데이터 구조도 정의합니다.

## <a name="attestation-provider"></a>증명 공급자

증명 공급자는 Microsoft.Attestation이라는 Azure 리소스 공급자에 속합니다. 리소스 공급자는 Azure Attestation REST 계약을 제공하고 [Azure Resource Manager](../azure-resource-manager/management/overview.md)를 사용하여 배포되는 서비스 엔드포인트입니다. 각 증명 공급자는 검색 가능한 특정 정책을 적용합니다. 증명 공급자는 각 증명 형식에 대한 기본 정책을 사용하여 만들어집니다(VBS enclave에는 기본 정책이 없음). SGX에 대한 기본 정책에 대한 자세한 내용은 [증명 정책 예제](policy-examples.md)를 참조하세요.

### <a name="regional-shared-provider"></a>지역 공유 공급자

Azure Attestation은 사용 가능한 모든 지역에서 지역 공유 공급자를 제공합니다. 고객은 증명에 지역 공유 공급자를 사용하거나 사용자 지정 정책을 사용하여 자체 공급자를 만들 수 있습니다. 공유 공급자는 Azure AD 사용자가 액세스할 수 있으며, 공유 공급자와 연결된 정책은 변경할 수 없습니다.

| 지역 | 증명 URI | 
|--|--|
| 미국 동부 | `https://sharedeus.eus.attest.azure.net` | 
| 미국 서부 | `https://sharedwus.wus.attest.azure.net` | 
| 영국 남부 | `https://shareduks.uks.attest.azure.net` | 
| 영국 서부| `https://sharedukw.ukw.attest.azure.net  ` | 
| 캐나다 동부 | `https://sharedcae.cae.attest.azure.net` | 
| 캐나다 중부 | `https://sharedcac.cac.attest.azure.net` | 
| 북유럽 | `https://sharedneu.neu.attest.azure.net` | 
| 서유럽| `https://sharedweu.weu.attest.azure.net` | 
| 미국 동부 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| 미국 중부 | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>증명 요청

증명 요청은 클라이언트 애플리케이션에서 증명 공급자에게 보낸 직렬화된 JSON 개체입니다. SGX enclave에 대한 요청 개체에는 다음 두 가지 속성이 있습니다. 
- "Quote" – 이 속성의 값은 Base64URL로 인코딩된 증명 따옴표의 표현이 포함된 문자열입니다.
- "EnclaveHeldData" – 이 "EnclaveHeldData" 속성의 값은 Base64URL로 인코딩된 enclave 보유 데이터의 표현이 포함된 문자열입니다.

Azure Attestation은 제공된 "Quote"의 유효성을 검사한 다음, 제공된 Enclave 보유 데이터의 SHA256 해시가 따옴표에 있는 reportData 필드의 처음 32바이트에 표시되어 있는지 확인합니다. 

## <a name="attestation-policy"></a>증명 정책

증명 정책은 증명 증거를 처리하는 데 사용되며 고객이 구성할 수 있습니다. Azure Attestation의 핵심에는 증거를 구성하는 클레임을 처리하는 정책 엔진이 있습니다. 정책은 Azure Attestation에서 증거를 기반으로 하여 증명 토큰을 발급하는지 여부를 결정하고 이에 따라 증명자를 보증하는지 여부를 결정하는 데 사용됩니다. 따라서 모든 정책을 통과하지 못하면 JWT 토큰이 발급되지 않습니다.

증명 공급자의 기본 정책이 요구 사항을 충족하지 않는 경우 고객은 Azure Attestation에서 지원되는 모든 지역에서 사용자 지정 정책을 만들 수 있습니다. 정책 관리는 Azure Attestation에서 고객에게 제공하는 주요 기능입니다. 정책은 증명 형식에 따라 다르며, enclave를 식별하거나 클레임을 출력 토큰에 추가하거나 출력 토큰에서 클레임을 수정하는 데 사용할 수 있습니다. 

정책 샘플은 [증명 정책의 예](policy-examples.md)를 참조하세요.

## <a name="benefits-of-policy-signing"></a>정책 서명의 이점

증명 정책은 궁극적으로 Azure Attestation에서 증명 토큰을 발급하는지 여부를 결정합니다. 또한 정책은 증명 토큰에서 생성될 클레임을 결정합니다. 따라서 서비스에서 평가한 정책은 실제로 관리자가 작성한 정책이며, 외부 엔터티에서 이를 변조하거나 수정하지 않은 것이 가장 중요합니다. 

신뢰 모델은 정책을 정의하고 업데이트하는 증명 공급자의 권한 부여 모델을 정의합니다.  두 가지 모델이 지원됩니다. 하나는 Azure AD 권한 부여를 기반으로 하고, 다른 하나는 고객 관리형 암호화 키 소유를 기반으로 합니다(격리된 모델이라고도 함).  격리된 모델을 사용하면 Azure Attestation에서 고객이 제출한 정책이 변조되지 않도록 할 수 있습니다.

격리된 모델에서 관리자는 신뢰할 수 있는 서명 X.509 인증서 세트를 지정하는 증명 공급자를 파일에 만듭니다. 그런 다음, 관리자는 서명된 정책을 증명 공급자에 추가할 수 있습니다. 증명 요청을 처리하는 동안 Azure Attestation은 헤더의 "jwk" 또는 "x5c" 매개 변수로 표시되는 공개 키를 사용하여 정책 서명의 유효성을 검사합니다.  또한 Azure Attestation은 요청 헤더의 공개 키가 증명 공급자와 연결된 신뢰할 수 있는 서명 인증서 목록에 있는지 확인합니다. 신뢰 당사자(Azure Attestation)는 알고 있는 X.509 인증서를 사용하여 서명된 정책을 이러한 방식으로 신뢰할 수 있습니다. 

샘플은 [정책 서명자 인증서 예제](policy-signer-examples.md)를 참조하세요.

## <a name="attestation-token"></a>증명 토큰

Azure Attestation 응답은 JWT가 해당 값에 포함된 JSON 문자열입니다. Azure Attestation은 클레임을 패키지하고 서명된 JWT를 생성합니다. 서명 작업은 증명 공급자의 AttestUri 요소와 일치하는 주체 이름이 있는 자체 서명된 인증서를 사용하여 수행됩니다.

OpenID 메타데이터 가져오기 API는 [OpenID Connect 검색 프로토콜](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)에서 지정한 대로 OpenID 구성 응답을 반환합니다. API는 Azure Attestation에서 사용하는 서명 인증서에 대한 메타데이터를 검색합니다.

SGX enclave에 대해 생성된 JWT의 예제는 다음과 같습니다.

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
위에서 사용된 클레임 중 일부는 사용되지 않는 것으로 간주되지만 완전히 지원됩니다.  이후 모든 코드 및 도구는 더 이상 사용되지 않는 클레임 이름을 사용하는 것이 좋습니다. 자세한 내용은 [Azure Attestation을 통해 발급된 클레임](claim-sets.md)을 참조하세요.

## <a name="encryption-of-data-at-rest"></a>저장소 데이터 암호화

고객 데이터를 보호하기 위해 Azure Attestation은 해당 데이터를 Azure Storage에 유지합니다. Azure 스토리지는 데이터 센터에 기록되는 대로 미사용 데이터의 암호화를 제공하고, 고객이 액세스하도록 암호를 해독합니다. 이 암호화는 Microsoft 관리형 암호화 키를 사용하여 수행됩니다. 

Azure Attestation은 Azure 스토리지의 데이터를 보호하는 것 외에도 ADE(Azure Disk Encryption)를 활용하여 서비스 VM을 암호화합니다. Azure 기밀 컴퓨팅 환경의 enclave에서 실행되는 Azure Attestation의 경우 현재 ADE 확장이 지원되지 않습니다. 이러한 시나리오에서는 데이터가 메모리 내에 저장되지 않도록 페이지 파일을 사용할 수 없습니다. 

Azure Attestation 인스턴스 로컬 하드 디스크 드라이브에는 고객 데이터가 유지되지 않습니다.


## <a name="next-steps"></a>다음 단계

- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
