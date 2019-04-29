---
title: '최종 사용자 인증: Azure Active Directory를 사용 하 여 Azure 데이터 레이크 저장소 Gen1를 사용 하 여 REST API | Microsoft Docs'
description: Azure Active Directory와 REST API를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877825"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>REST API를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK 사용](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-end-user-authenticate-python.md)
> * [REST API 사용](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

이 문서에서는 REST API를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증을 수행하는 방법을 배웁니다. REST API를 사용하여 Data Lake Storage Gen1에서 서비스 간 인증을 수행하려면 [REST API를 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-rest-api.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "네이티브" 애플리케이션을 만듭니다**. [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

* **[cURL](https://curl.haxx.se/)**. 이 문서에서는 cURL을 사용하여 Data Lake Storage Gen1 계정에 대해 REST API 호출을 수행하는 방법을 설명합니다.

## <a name="end-user-authentication"></a>최종 사용자 인증
최종 사용자 인증은 사용자가 Microsoft Azure Active Directory를 사용하여 애플리케이션에 로그인하기를 원하는 경우에 권장되는 방법입니다. 애플리케이션은 로그인한 사용자와 동일한 수준의 액세스 권한으로 Azure 리소스에 액세스할 수 있습니다. 사용자는 애플리케이션이 액세스를 유지할 수 있도록 주기적으로 자격 증명을 입력해야 합니다.

최종 사용자의 로그인으로 인해 애플리케이션에 액세스 토큰 및 새로 고침 토큰이 제공됩니다. 액세스 토큰은 Data Lake Storage Gen1 또는 Data Lake Analytics에 대해 만들어진 각 요청에 연결하며 기본적으로 1시간 동안 유효합니다. 새로 고침 토큰은 새 액세스 토큰을 가져오는 데 사용할 수 있고 정기적으로 사용되는 경우 기본적으로 최대 2주 동안 유효합니다. 최종 사용자 로그인에 두 가지 방법을 사용할 수 있습니다.

이 시나리오에서 애플리케이션은 로그인하라는 메시지를 표시하고 모든 작업은 사용자의 컨텍스트에서 수행됩니다. 다음 단계를 수행합니다.

1. 애플리케이션을 통해 다음 URL로 사용자를 리디렉션합니다.

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI>는 URL에서 사용하도록 인코딩되어야 합니다. 따라서 https://localhost의 경우 `https%3A%2F%2Flocalhost`를 사용합니다.

    이 자습서에서는 위의 URL에 있는 자리 표시자 값을 바꿀 수 있으며 이를 웹 브라우저의 주소 표시줄에 붙여 넣습니다. Azure 로그인을 사용하여 인증하도록 리디렉션됩니다. 성공적으로 로그인되면 응답은 브라우저의 주소 표시줄에 표시됩니다. 응답은 다음 형식으로 되어 있습니다.

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. 응답에서 인증 코드를 캡처합니다. 이 자습서에서는 웹 브라우저의 주소 표시줄에서 권한 부여 코드를 복사하여 아래 코드 조각처럼 토큰 엔드포인트에 대한 POST 요청에 전달할 수 있습니다.

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > 이 경우에 \<REDIRECT-URI>는 인코딩되지 않아야 합니다.
   > 
   > 

3. 응답은 액세스 토큰(예: `"access_token": "<ACCESS_TOKEN>"`) 및 새로 고침 토큰(예: `"refresh_token": "<REFRESH_TOKEN>"`)을 포함하는 JSON 개체입니다. 응용 프로그램은 Azure Data Lake Storage Gen1에 액세스할 때 액세스 토큰을 사용하고 액세스 토큰이 만료되면 다른 액세스 토큰을 가져오는 새로 고침 토큰을 사용합니다.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. 액세스 토큰이 만료되면 다음 코드 조각처럼 새로 고침 토큰을 사용하여 새 액세스 토큰을 요청할 수 있습니다.

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

대화형 사용자 인증에 대한 자세한 내용은 [인증 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 REST API를 사용하여 Azure Data Lake Storage Gen1에서 인증하는 서비스 간 인증을 사용하는 방법을 배웠습니다. 이제 다음 문서를 통해 REST API를 Azure Data Lake Storage Gen1과 함께 사용하는 방법을 살펴볼 수 있습니다.

* [REST API를 사용한 Data Lake Storage Gen1에서의 계정 관리 작업](data-lake-store-get-started-rest-api.md)
* [REST API를 사용한 Data Lake Storage Gen1에서의 데이터 작업](data-lake-store-data-operations-rest-api.md)

