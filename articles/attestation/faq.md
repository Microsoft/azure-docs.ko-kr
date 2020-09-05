---
title: 질문과 대답
description: Microsoft Azure 증명에 대 한 자주 묻는 질문에 대 한 답변
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236648"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure 증명에 대 한 질문과 대답

이 문서에서는 [Azure 증명](overview.md)에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 제공 합니다.

Azure 문제가이 문서에서 해결 되지 않은 경우 azure [지원 페이지](https://azure.microsoft.com/support/options/)에서 azure 지원 요청을 제출할 수도 있습니다.

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Enclave 증명의 Azure PCK 캐싱 서비스 및 해당 역할 이란?

Azure PCK 캐싱 서비스는 Intel의 [Azure 기밀 컴퓨팅 (ACC)](../confidential-computing/overview.md) 노드에 대 한 azure 보안 기준을 정의 하 고 데이터를 캐시 합니다. 캐시 된 정보는 Azure 증명에서 TEEs (신뢰할 수 있는 실행 환경)의 유효성을 검사 하는 데 추가로 사용 됩니다.  

Azure PCK 캐싱 서비스:
   - 고가용성 제공 
   - 외부에서 호스팅된 서비스와 인터넷 연결에 대 한 종속성을 줄입니다.
   - Intel에서 최신 버전의 Intel 인증서, Crl, 신뢰할 수 있는 컴퓨팅 기반 (TCB) 정보를 가져오고, Enclave에서 ACC 노드의 id를 인용 합니다. 따라서이 서비스는 TEEs의 유효성을 검사 하는 동안 azure 증명에서 참조할 Azure 보안 기준을 확인 하 고, Intel 인증서의 무효화 또는 해지로 인 한 증명 오류를 크게 줄입니다.  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>비 Azure 환경에서 Azure 증명을 통해 지원 되는 SGX 증명

Azure 증명은 TEEs의 유효성을 검사 하기 위해 Azure PCK 캐싱 서비스에 명시 된 보안 기준에 따라 결정 됩니다. Azure PCK 캐싱 서비스는 현재 Azure 기밀 컴퓨팅 노드만 지원 하도록 설계 되었습니다. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>증명 SGX enclaves에 대해 Azure 증명에서 수행 하는 유효성 검사는 무엇 인가요?

Azure 증명은 원격으로 다양 한 유형의 TEEs을 증명 하는 통합 프레임 워크입니다. Azure 증명:

   - 서명 된 enclave 견적의 신뢰할 수 있는 루트가 Intel에 속하는지 확인 합니다.
   - Enclave 견적이 Azure PCK 캐싱 서비스에 정의 된 Azure 보안 기준을 충족 하는지 확인 합니다.
   - 증명 요청 개체에서 Enclave 소유 데이터 (EHD)의 SHA256 해시가 Enclave 따옴표의 reportData 필드의 처음 32 바이트와 일치 하는지 확인 합니다.
   - 고객은 증명 공급자를 만들고 사용자 지정 정책을 구성할 수 있습니다. 위의 유효성 검사 외에도 Azure 증명은 정책에 대해 enclave 견적을 평가 합니다. 정책은 enclave에 대 한 권한 부여 규칙을 정의 하 고 증명 토큰을 생성 하기 위한 발급 규칙도 지시 합니다. 의도 한 소프트웨어가 enclave에서 실행 되 고 있는지 확인 하기 위해 고객은 권한 부여 규칙을 추가 하 여 enclave 인용의 **mrsigner** 및 **mrenclave** 필드가 고객 바이너리의 값과 일치 하는지 확인할 수 있습니다.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>검증 도구에서 Azure 증명으로 지원 되는 SGX 증명에 대 한 참고 자료를 가져오는 방법

일반적으로 Intel을 신뢰의 루트로 사용 하는 증명 모델의 경우 증명 클라이언트는 enclave Api에 통신 하 여 enclave 증거를 인출 합니다. Enclave Api는 내부적으로 Intel PCK 캐싱 서비스를 호출 하 여 노드의 Intel 인증서를 증명 된로 가져옵니다. 인증서는 enclave 증명 정보에 서명 하는 데 사용 되므로 원격 attestable 참고 자료를 생성 합니다.  

Azure 증명에 대해 동일한 프로세스를 구현할 수 있습니다. 그러나 Azure PCK 캐싱 서비스에서 제공 하는 혜택을 활용 하려면 가상 컴퓨터를 설치 하 고 나 서 [azure](https://www.nuget.org/packages/Microsoft.Azure.DCAP)를 설치 하는 것이 좋습니다. Intel와의 규약에 따라 Azure ECAP library가 설치 되 면 enclave 증명을 생성 하는 요청이 Intel PCK 캐싱 서비스에서 Azure PCK 캐싱 서비스로 리디렉션됩니다. Azure ECAP 라이브러리는 Windows 및 Linux 기반 환경에서 지원 됩니다.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>다른 증명 모델에서 Azure 증명으로 전환 하는 방법

- Azure 기밀 컴퓨팅 가상 머신을 설치한 후 azure PCK 캐싱 서비스에서 제공 하는 혜택을 활용 하기 위해 Azure dblibrary ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/))를 설치 합니다.
- Enclave 증명 정보를 검색 하 고 Azure 증명에 요청을 보낼 수 있는 원격 증명 클라이언트를 작성 해야 합니다. 참조는 [코드 샘플](/samples/browse/?expanded=azure&terms=attestation) 을 참조 하세요. 
- 증명 요청을 기본 공급자나 사용자 지정 증명 공급자의 REST API 끝점으로 보낼 수 있습니다. 
- Azure 증명 Api는 Azure AD 인증에 의해 보호 됩니다. 따라서 증명 Api를 호출 하는 클라이언트는 증명 요청에서 유효한 Azure AD 액세스 토큰을 얻고 전달할 수 있어야 합니다. 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>신뢰 당사자가 증명 토큰의 무결성을 확인 하는 방법

Azure 증명에 의해 생성 된 증명 토큰은 자체 서명 된 인증서를 사용 하 여 서명 됩니다. 인증서는 [openid connect 메타 데이터 끝점](/rest/api/attestation/metadataconfiguration/get)을 통해 노출 됩니다. 신뢰 당사자는이 끝점에서 서명 인증서를 검색 하 고 증명 토큰의 서명 확인을 수행할 수 있습니다. 증명 토큰의 유효 시간은 8 시간입니다. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Openid connect 메타 데이터 끝점에서 서명 확인에 사용할 인증서를 확인 하는 방법

Openid connect 메타 데이터 끝점에 노출 되는 여러 인증서는 Azure 증명에서 지 원하는 여러 사용 사례 (예: SGX 증명)에 해당 합니다. [RFC 7515](https://tools.ietf.org/html/rfc7515)에 지정 된 표준에 따라 증명 토큰 헤더의 *kid* 매개 변수와 일치 하는 키 ID (kid)가 있는 인증서는 서명 확인에 사용 됩니다. 일치 하는 **kid** 가 없으면 openid connect metadata 끝점에서 노출 하는 모든 인증서를 사용해 볼 수 있습니다.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>신뢰 당사자가 검증 된 신뢰할 수 있는 실행 환경 (TEEs)과 암호를 공유할 수 있습니다.

Enclave 내에서 생성 되는 공개 키는 클라이언트에서 Azure 증명으로 보낸 증명 요청 개체의 Enclave 보관 데이터 (EHD) 속성에 표시 될 수 있습니다. EHD의 SHA256 해시가 견적의 reportData 필드에 표시 되는지 확인 한 후 Azure 증명은 증명 토큰에 EHD를 포함 합니다. 신뢰 당사자는 확인 된 증명 응답에서 EHD를 사용 하 여 암호를 암호화 하 고 enclave와 공유할 수 있습니다. 자세한 내용은 [Azure 증명 기본 개념](basic-concepts.md) 을 참조 하세요.
