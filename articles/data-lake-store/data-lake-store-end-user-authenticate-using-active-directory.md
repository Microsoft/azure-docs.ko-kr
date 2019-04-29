---
title: '최종 사용자 인증: Azure Active Directory 사용 하 여 azure Data Lake Storage Gen1 | Microsoft Docs'
description: Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증을 수행하는 방법을 알아봅니다.
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
ms.openlocfilehash: c0fe63e395ee08cb65e9bbbadc4ce1f03032ce95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878253"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1은 인증을 위해 Azure Active Directory를 사용합니다. Data Lake Storage Gen1 또는 Azure Data Lake Analytics와 함께 작동하는 애플리케이션을 제작하기 전에 Azure AD(Azure Active Directory)를 사용하여 애플리케이션을 인증하는 방법을 결정해야 합니다. 사용할 수 있는 두 가지 주요 옵션은 다음과 같습니다.

* 최종 사용자 인증(이 문서)
* 서비스 간 인증(위의 드롭다운에서 이 옵션을 선택)

둘 중 어떤 옵션을 사용하든 애플리케이션에는 OAuth 2.0 토큰이 제공됩니다. 이 토큰은 Data Lake Storage Gen1 또는 Azure Data Lake Analytics에 대한 각 요청에 연결됩니다.

이 문서에서는 최종 사용자 인증을 위한 **Microsoft Azure Active Directory 네이티브 애플리케이션**을 만드는 방법에 대해 설명합니다. 서비스 간 인증용 Azure AD 애플리케이션 구성 관련 지침은 [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* 구독 ID. Azure Portal에서 검색할 수 있습니다. 예를 들어 Data Lake Storage Gen1 계정 블레이드에서 사용할 수 있습니다.
  
    ![구독 ID 가져오기](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD 도메인 이름. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. 아래 스크린샷에서 도메인 이름은 **contoso.onmicrosoft.com**이며 괄호 안의 GUID는 테넌트 ID입니다. 
  
    ![AAD 도메인 가져오기](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Azure 테넌트 ID입니다. 테넌트 ID를 검색하는 방법에 대한 지침은 [테넌트 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id)를 참조하세요.

## <a name="end-user-authentication"></a>최종 사용자 인증
이 인증 메커니즘은 최종 사용자가 Microsoft Azure Active Directory를 통해 애플리케이션에 로그인하기를 원하는 경우에 권장되는 방법입니다. 애플리케이션은 로그인한 최종 사용자와 동일한 수준의 액세스로 Azure 리소스에 액세스할 수 있게 됩니다. 최종 사용자는 애플리케이션이 액세스를 유지할 수 있도록 주기적으로 자격 증명을 제공해야 합니다.

최종 사용자의 로그인으로 인해 애플리케이션에 액세스 토큰 및 새로 고침 토큰이 제공됩니다. 액세스 토큰은 Data Lake Storage Gen1 또는 Data Lake Analytics에 대한 각 요청에 연결되며, 기본적으로 1시간 동안 유효합니다. 새로 고침 토큰은 새 액세스 토큰을 가져오는 데 사용할 수 있고 기본적으로 최대 2주 동안 유효합니다. 최종 사용자 로그인에 두 가지 방법을 사용할 수 있습니다.

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2.0 팝업 사용
애플리케이션은 최종 사용자가 자격 증명을 입력할 수 있는 OAuth 2.0 권한 부여 팝업을 트리거할 수 있습니다. 이 팝업은 필요한 경우 Azure AD 2단계 인증(2FA) 프로세스와도 작동합니다. 

> [!NOTE]
> 이 방법은 Python 또는 Java용 Azure ADAL(AD 인증 라이브러리)에서 아직 지원되지 않습니다.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>사용자 자격 증명에서 직접 전달
애플리케이션은 Azure AD에 사용자 자격 증명을 직접 제공할 수 있습니다. 이 방법은 조직 ID 사용자 계정과만 작동합니다. @outlook.com 또는 @live.com으로 끝나는 계정을 포함한 개인/"live ID" 사용자 계정과 호환되지 않습니다. 또한 이 방법은 Azure AD 2단계 인증(2FA)이 필요한 사용자 계정과 호환되지 않습니다.

### <a name="what-do-i-need-for-this-approach"></a>이 방법에는 무엇이 필요한가요?
* Azure AD 도메인 이름. 이 요구 사항은 이 문서의 필수 구성 요소에 이미 나열되어 있습니다.
* Microsoft Azure Active Directory 테넌트 ID. 이 요구 사항은 이 문서의 필수 구성 요소에 이미 나열되어 있습니다.
* Azure AD **네이티브 애플리케이션**
* Azure AD 네이티브 애플리케이션에 대한 애플리케이션 ID
* Azure AD 네이티브 애플리케이션에 대한 리디렉션 URI
* 위임된 권한 설정


## <a name="step-1-create-an-active-directory-native-application"></a>1단계: Active Directory 네이티브 응용 프로그램 만들기

Azure Active Directory를 사용하여 Data Lake Storage Gen1로 최종 사용자를 인증하기 위한 Azure AD 네이티브 애플리케이션을 만들고 구성합니다. 지침에 대해서는 [Azure AD 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

이 링크에 있는 지침을 수행하는 동안 다음 스크린샷과 같이 애플리케이션 유형으로 **네이티브**를 선택해야 합니다.

![웹앱 만들기](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "네이티브 앱 만들기")

## <a name="step-2-get-application-id-and-redirect-uri"></a>2단계: 응용 프로그램 ID 및 리디렉션 URI 가져오기

애플리케이션 ID를 검색하려면 [애플리케이션 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key)를 참조하세요.

리디렉션 URI를 검색하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 다음, 만든 Azure AD 네이티브 애플리케이션을 찾아서 클릭합니다.

2. 애플리케이션에 대한 **설정** 블레이드에서 **리디렉션 URI**를 클릭합니다.

    ![리디렉션 URI 가져오기](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. 표시되는 값을 복사합니다.


## <a name="step-3-set-permissions"></a>3단계: 권한 설정

1. Azure Portal에서 **Azure Active Directory**를 선택하고 **앱 등록**을 클릭한 다음, 만든 Azure AD 네이티브 애플리케이션을 찾아서 클릭합니다.

2. 애플리케이션에 대한 **설정** 블레이드에서 **필요한 사용 권한**을 클릭한 다음, **추가**를 클릭합니다.

    ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. **API 액세스 추가** 블레이드에서 **API 선택**을 클릭하고 **Azure Data Lake**를 클릭한 후 **선택**을 클릭합니다.

    ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  **API 액세스 추가** 블레이드에서 **사용 권한 선택**을 클릭한 후 **Data Lake Store에 대한 모든 권한**을 부여하기 위한 확인란을 선택하고 **선택**을 클릭합니다.

    ![클라이언트 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    **Done**을 클릭합니다.

5. 마지막 두 단계를 반복하여 **Microsoft Azure Service Management API**에 대한 권한도 부여합니다.
   
## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure AD 네이티브 애플리케이션을 만들고 .NET SDK, Java SDK, REST API 등을 사용하여 만든 클라이언트 애플리케이션에 필요한 정보를 수집했습니다. 계속해서 Azure AD 웹 애플리케이션을 사용해 Data Lake Storage Gen1에 먼저 인증한 후 저장소에서 다른 작업을 수행하는 방법에 대해 설명하는 다음 문서를 확인할 수 있습니다.

* [Java SDK를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-java-sdk.md)
* [.NET SDK를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)
* [Python을 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-python.md)
* [REST API를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-rest-api.md)

