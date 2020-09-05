---
title: 웹앱 자습서 - Azure API for FHIR에 대한 연결 테스트
description: 이 자습서에서는 간단한 웹 애플리케이션을 배포하는 예제를 안내합니다. 자습서의 이 섹션에서는 Postman을 사용하여 FHIR 서버에 연결하는 테스트를 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852925"
---
# <a name="testing-the-fhir-api"></a>FHIR API 테스트
이전 두 단계에서 Azure API for FHIR을 배포하고 클라이언트 애플리케이션을 등록했습니다. 이제 클라이언트 애플리케이션을 사용하여 Azure API for FHIR이 설정되었는지 테스트할 준비가 되었습니다. 

## <a name="retrieve-capability-statement"></a>Retrieve 기능 문
먼저 Azure API for FHIR에 대한 기능 문을 가져옵니다. 
1. Postman 열기
1. GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata를 수행하여 기능 문을 검색합니다. 아래 이미지에서 FHIR 서버 이름은 **fhirserver**입니다.

![기능 문](media/tutorial-web-app/postman-capability-statement.png)

다음으로 환자를 검색하려고 합니다. 환자를 검색하려면 GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient를 입력합니다. 401 권한 없음 오류가 표시됩니다. 이 오류는 환자 데이터에 액세스할 수 있어야 한다는 것을 입증하지 않았기 때문입니다.

## <a name="get-patient-from-fhir-server"></a>FHIR 서버에서 환자 받기
![실패한 환자](media/tutorial-web-app/postman-patient-authorization-failed.png)

액세스 권한을 얻으려면 액세스 토큰이 필요합니다.
1. Postman에서 **권한 부여**를 선택하고 유형을 **OAuth2.0**으로 설정합니다.
1. **새 액세스 토큰 가져오기** 선택
1. 필드를 입력하고 **요청 토큰**을 선택합니다. 아래에서 이 자습서의 각 필드에 대한 값을 확인할 수 있습니다.

|필드                |값                                                               |
|---------------------|--------------------------------------------------------------------|
|토큰 이름           |토큰에 대한 이름                                               |
|권한 부여 유형           |인증 코드                                                  |
|콜백 URL         |https://www.getpostman.com/oauth2/callback                          |
|인증 URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|액세스 토큰 URL     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|클라이언트 ID            |이전 단계에서 복사한 클라이언트 ID             |
|클라이언트 암호        |\<BLANK>                                                            |
|범위                |\<BLANK>                                                            |
|시스템 상태                |1234                                                                |
|클라이언트 인증|본문에서 클라이언트 자격 증명 보내기                                     |

4. 자격 증명을 사용하여 로그인하고 **수락**을 선택합니다.
1. 결과에서 아래로 스크롤하고 **토큰 사용**을 선택합니다.
1. 맨 위에서 **보내기**를 다시 선택하고 이번에는 ![성공 환자](media/tutorial-web-app/postman-patient-authorization-success.png) 결과를 얻어야 합니다.

## <a name="post-patient-into-fhir-server"></a>FHIR 서버에 환자 게시
이제 액세스할 수 있으므로 새 환자를 만들 수 있습니다. 다음은 FHIR 서버에 추가할 수 있는 간단한 환자 샘플입니다. Postman의 **본문** 섹션에 아래 코드를 입력합니다.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
이 POST에서는 Tiberious Kirk 이름으로 FHIR 서버에 새 환자를 만듭니다.
![환자 게시](media/tutorial-web-app/postman-post-patient.png)

위의 GET 단계를 수행하여 환자를 다시 검색하는 경우 James Tiberious Kirk가 출력에 표시됩니다.

## <a name="troubleshooting-access-issues"></a>액세스 문제 해결
이러한 단계를 수행하는 동안 문제가 발생한 경우 Azure Active Directory 및 Azure API for FHIR에 대해 함께 제공된 문서를 검토하세요. 

* [Azure AD 및 Azure API for FHIR](azure-ad-hcapi.md) - 이 문서에서는 Azure Active Directory의 몇 가지 기본 원리와 Azure API for FHIR과 상호 작용하는 방법을 간략하게 설명합니다.
* [액세스 토큰 유효성 검사](azure-ad-hcapi-token-validation.md) - 이 방법 가이드는 액세스 토큰 유효성 검사 및 액세스 문제를 해결하기 위해 수행해야 하는 단계에 대한 구체적인 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계
이제 클라이언트 애플리케이션에 성공적으로 연결할 수 있으므로 웹 애플리케이션을 작성할 준비가 되었습니다.

>[!div class="nextstepaction"]
>[웹 애플리케이션 작성](tutorial-web-app-write-web-app.md)



