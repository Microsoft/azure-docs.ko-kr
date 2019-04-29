---
title: Azure Time Series Insights에서 API로 인증하고 권한을 부여하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 66a3c40bf1e1e1dc6253520a555e19ebf011297c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761631"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에서 사용되는 인증 및 권한 부여를 구성하는 방법을 설명합니다.

## <a name="service-principal"></a>서비스 주체

이 섹션에서는 애플리케이션을 대신해 Time Series Insights API에 액세스하도록 애플리케이션을 구성하는 방법을 설명합니다. 애플리케이션은 사용자 자격 증명이 아니라 애플리케이션 자격 증명으로 Time Series Insights 환경에서 데이터를 쿼리하거나 참조 데이터를 게시할 수 있습니다.

Time Series Insights에 액세스해야 하는 애플리케이션이 있는 경우 Azure Active Directory 애플리케이션을 설정하고 Time Series Insights 환경에서 데이터 액세스 정책을 할당해야 합니다. 이 방법은 다음의 이유로 사용자 고유의 자격 증명을 사용하여 앱을 실행하는 데 좋습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어 앱이 Time Series Insights 환경에서만 데이터를 읽을 수 있도록 허용할 수 있습니다.
* 책임이 변경되면 앱의 자격 증명을 변경할 필요가 없습니다.
* 무인 스크립트를 실행할 때 인증서 또는 애플리케이션 키를 사용하여 인증을 자동화할 수 있습니다.

이 문서에서는 Azure Portal을 통해 이러한 단계를 수행하는 방법을 보여 줍니다. 여기서는 애플리케이션을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 애플리케이션을 중점적으로 다룹니다. 일반적으로 단일 조직에서 실행되는 LOB(기간 업무) 애플리케이션에 대해 단일 테넌트 애플리케이션을 사용하게 됩니다.

설정 흐름은 크게 세 단계로 구성됩니다.

1. Azure Active Directory에서 애플리케이션을 만듭니다.
2. 이 애플리케이션에 Time Series Insights 환경에 액세스할 수 있는 권한을 부여합니다.
3. 애플리케이션 ID 및 키를 사용하여 `"https://api.timeseries.azure.com/"` 대상 그룹 또는 리소스에 대한 토큰을 가져옵니다. 그런 다음 토큰을 사용하여 Time Series Insights API를 호출할 수 있습니다.

세부 단계는 다음과 같습니다.

1. Azure Portal에서 **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

   ![Azure Active Directory에서 새 애플리케이션 등록](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. 애플리케이션의 이름을 지정하고, **웹앱/API** 형식을 선택하고, **로그온 URL**에 대한 유효한 URI를 선택하고, **만들기**를 클릭합니다.

   ![Azure Active Directory에서 애플리케이션 만들기](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. 새로 만든 애플리케이션을 선택하고 해당 애플리케이션 ID를 원하는 텍스트 편집기에 복사합니다.

   ![애플리케이션 ID 복사](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. **키**를 선택하고 키 이름을 입력한 후 만료를 선택하고 **저장**을 클릭합니다.

   ![애플리케이션 키 선택](media/authentication-and-authorization/active-directory-application-keys.png)

   ![키 이름 및 만료를 입력하고 저장을 클릭합니다.](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. 원하는 텍스트 편집기에 키를 복사합니다.

   ![애플리케이션 키 복사](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Time Series Insights 환경에 대해 **데이터 액세스 정책**을 선택하고 **추가**를 클릭합니다.

   ![Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. **사용자 선택** 대화 상자에서 애플리케이션 이름(2단계) 또는 애플리케이션 ID(3단계)를 붙여 넣습니다.

   ![사용자 선택 대화 상자에서 애플리케이션 찾기](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. 역할(데이터를 쿼리하는 경우 **읽기 권한자**, 데이터를 쿼리하고 참조 데이터를 변경하는 경우 **참가자**)을 선택하고 **확인**을 클릭합니다.

   ![역할 선택 대화 상자에서 읽기 권한자 또는 참가자 선택](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. **확인**을 클릭하여 정책을 저장합니다.

10. 애플리케이션 ID(3단계)와 애플리케이션 키(5단계)를 사용하여 애플리케이션 대신 토큰을 가져옵니다. 애플리케이션이 Time Series Insights API를 호출할 때 `Authorization` 헤더에서 이 토큰을 전달할 수 있습니다.

    C#을 사용하는 경우 다음 코드를 사용하여 애플리케이션 대신 토큰을 가져올 수 있습니다. 전체 샘플은 [C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)를 참조하세요.

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

애플리케이션에서 애플리케이션 ID 및 키를 사용하여 Azure Time Series Insight에서 인증을 받습니다. 

## <a name="next-steps"></a>다음 단계

- Time Series Insights API를 호출하는 샘플 코드는 [C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)를 참조하세요.

- API 참조 정보에 대해서는 [쿼리 API 참조](/rest/api/time-series-insights/ga-query-api)를 참조하세요.

- 설명 하는 방법 [서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)합니다.