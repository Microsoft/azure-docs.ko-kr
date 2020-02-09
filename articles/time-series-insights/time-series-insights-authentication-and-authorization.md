---
title: API 인증 및 권한 부여-Azure Time Series Insights | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984605"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에서는 새 Azure Active Directory 블레이드를 사용 하 여 Azure Active Directory에 앱을 등록 하는 방법을 설명 합니다. Azure Active Directory에 등록 된 앱은 사용자가에 인증 하 고 Time Series Insights 환경에 연결 된 Azure Time Series insights API를 사용할 수 있도록 권한을 부여 받습니다.

> [!IMPORTANT]
> Azure Time Series Insights는 다음 인증 라이브러리를 모두 지원 합니다.
> * 최신 [MSAL (Microsoft 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [ADAL (Azure Active Directory 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>서비스 주체

다음 섹션에서는 앱을 대신 하 여 Time Series Insights API에 액세스 하도록 응용 프로그램을 구성 하는 방법을 설명 합니다. 그러면 응용 프로그램은 Azure Active Directory를 통해 자체 응용 프로그램 자격 증명을 사용 하 여 Time Series Insights 환경에서 참조 데이터를 쿼리하거나 게시할 수 있습니다.

## <a name="summary-and-best-practices"></a>요약 및 모범 사례

Azure Active Directory 앱 등록 흐름에는 세 가지 주요 단계가 포함 됩니다.

1. Azure Active Directory에 [응용 프로그램을 등록](#azure-active-directory-app-registration) 합니다.
1. [Time Series Insights 환경에 대 한 데이터 액세스](#granting-data-access)권한을 응용 프로그램에 부여 합니다.
1. **응용 프로그램 ID** 및 **클라이언트 암호** 를 사용 하 여 [클라이언트 앱](#client-app-initialization)의 `https://api.timeseries.azure.com/`에서 토큰을 가져옵니다. 그런 다음 토큰을 사용하여 Time Series Insights API를 호출할 수 있습니다.

**3 단계**에 따라 응용 프로그램 및 사용자 자격 증명을 분리 하면 다음 작업을 수행할 수 있습니다.

* 사용자 고유의 사용 권한과는 다른 앱 id에 대 한 사용 권한을 할당 합니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어 특정 Time Series Insights 환경의 데이터만 읽도록 앱을 허용할 수 있습니다.
* **클라이언트 암호** 또는 보안 인증서를 사용 하 여 사용자의 인증 자격 증명을 만드는 것과 응용 프로그램의 보안을 격리 합니다. 따라서 응용 프로그램의 자격 증명은 특정 사용자의 자격 증명에 종속 되지 않습니다. 사용자의 역할이 변경 되 면 응용 프로그램은 새 자격 증명이 나 추가 구성이 필요 하지 않습니다. 사용자가 암호를 변경 하는 경우 응용 프로그램에 대 한 모든 액세스에는 새 자격 증명이 나 키가 필요 하지 않습니다.
* 특정 사용자의 자격 증명이 아닌 **클라이언트 암호** 또는 보안 인증서를 사용 하 여 무인 스크립트를 실행 합니다 (있어야 합니다).
* 암호 대신 보안 인증서를 사용 하 여 Azure Time Series Insights API에 대 한 액세스를 보호 합니다.

> [!IMPORTANT]
> Azure Time Series Insights 보안 정책을 구성할 때 문제 (위의 시나리오에 대해 설명)의 **분리** 원칙을 따릅니다.

> [!NOTE]
> * 이 문서는 응용 프로그램이 한 조직 에서만 실행 되도록 하는 단일 테넌트 응용 프로그램에 중점을 두는 것입니다.
> * 일반적으로 조직에서 실행 되는 lob (기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용 합니다.

## <a name="detailed-setup"></a>자세한 설정

### <a name="azure-active-directory-app-registration"></a>앱 등록 Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>데이터 액세스 권한 부여

1. Time Series Insights 환경의 경우 **데이터 액세스 정책** 을 선택 하 고 **추가**를 선택 합니다.

   [![Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **사용자 선택** 대화 상자에서 **응용 프로그램 이름** 또는 응용 프로그램 **ID** Azure Active Directory 앱 등록 섹션에서 붙여넣습니다.

   [사용자 선택 대화 상자에서 응용 프로그램을 찾을 ![](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 역할을 선택 합니다. 데이터 또는 **참가자** 를 쿼리하여 데이터를 쿼리하고 참조 데이터를 변경 하려면 **Reader** 를 선택 합니다. **확인**을 선택합니다.

   [사용자 역할 선택 대화 상자에서 판독기 또는 참가자를 선택 ![.](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **확인을**선택 하 여 정책을 저장 합니다.

   > [!TIP]
   > 고급 데이터 액세스 옵션에 대 한 자세한 내용은 [데이터 액세스 권한 부여](./time-series-insights-data-access.md)를 참조 하세요.

### <a name="client-app-initialization"></a>클라이언트 앱 초기화

* 개발자는 [MSAL (Microsoft 인증 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ) 또는 [ADAL (Azure Active Directory authentication library)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 을 사용 하 여 Azure Time Series Insights 인증할 수 있습니다.

* 예를 들어 ADAL을 사용 하 여 인증 하려면:

   1. 응용 프로그램을 대신 하 여 토큰을 획득 하려면 Azure Active Directory 앱 등록 섹션에서 **응용 프로그램 ID** 및 **클라이언트 암호** (응용 프로그램 키)를 사용 합니다.

   1. 에서 C#다음 코드는 응용 프로그램 대신 토큰을 가져올 수 있습니다. 전체 샘플을 보려면를 [사용 하 여 C#쿼리 데이터 ](time-series-insights-query-data-csharp.md)를 참조 하세요.

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. 애플리케이션이 Time Series Insights API를 호출할 때 `Authorization` 헤더에서 이 토큰을 전달할 수 있습니다.

* 또는 개발자가 MSAL을 사용 하 여 인증 하도록 선택할 수 있습니다. [Msal으로 마이그레이션](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) 하는 방법에 대 한 자세한 내용을 확인 하 고 자세한 내용은 문서를 [사용 하 여 C# Azure Time Series Insights 환경의 GA 참조 데이터 관리](time-series-insights-manage-reference-data-csharp.md) 를 참조 하세요. 

## <a name="common-headers-and-parameters"></a>공용 헤더 및 매개 변수

이 섹션에서는 Time Series Insights GA 및 Preview Api에 대 한 쿼리를 수행 하는 데 사용 되는 일반적인 HTTP 요청 헤더 및 매개 변수에 대해 설명 합니다. API 관련 요구 사항은 [Time Series Insights REST API 참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/)에 자세히 설명 되어 있습니다.

> [!TIP]
> REST Api를 사용 하 고 HTTP 요청을 수행 하 고 HTTP 응답을 처리 하는 방법에 대해 자세히 알아보려면 [Azure REST API 참조를 참조](https://docs.microsoft.com/rest/api/azure/) 하세요.

### <a name="authentication"></a>인증

[TIME SERIES INSIGHTS Rest api](https://docs.microsoft.com/rest/api/time-series-insights/)에 대해 인증 된 쿼리를 수행 하려면 선택한 rest 클라이언트 (Postman, JavaScript, C#)를 사용 하 여 유효한 OAuth 2.0 전달자 토큰을 [인증 헤더](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) 에 전달 해야 합니다. 

> [!TIP]
> 호스팅된 Azure Time Series Insights [CLIENT sdk 샘플 시각화](https://tsiclientsample.azurewebsites.net/) 를 읽고 차트 및 그래프와 함께 [JavaScript 클라이언트 sdk](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 를 사용 하 여 프로그래밍 방식으로 Time Series Insights api를 사용 하 여 인증 하는 방법을 알아봅니다.

### <a name="http-headers"></a>HTTP 헤더

필요한 요청 헤더는 아래에 설명 되어 있습니다.

| 필요한 요청 헤더 | Description |
| --- | --- |
| 권한 부여 | Time Series Insights 인증 하려면 유효한 OAuth 2.0 전달자 토큰을 **인증** 헤더에 전달 해야 합니다. | 

> [!IMPORTANT]
> 토큰은 토큰의 "대상"이 라고도 하는 `https://api.timeseries.azure.com/` 리소스에 대해 정확 하 게 발급 되어야 합니다.
> * 따라서 [Postman](https://www.getpostman.com/) **authurl** 은 다음과 같습니다. `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` 올바르지만 `https://api.timeseries.azure.com` 되지 않습니다.

선택적 요청 헤더는 아래에 설명 되어 있습니다.

| 선택적 요청 헤더입니다. | Description |
| --- | --- |
| Content-type | `application/json`만 지원 됩니다. |
| x-ms-client-request-id | 클라이언트 요청 ID입니다. 서비스는이 값을 기록 합니다. 서비스에서 서비스에 대 한 작업을 추적할 수 있습니다. |
| x-y-세션-id | 클라이언트 세션 ID입니다. 서비스는이 값을 기록 합니다. 서비스에서 서비스 간 관련 작업 그룹을 추적할 수 있습니다. |
| x-y-클라이언트-응용 프로그램 이름 | 이 요청을 생성 한 응용 프로그램의 이름입니다. 서비스는이 값을 기록 합니다. |

선택 사항 이지만 권장 되는 응답 헤더는 아래에 설명 되어 있습니다.

| 응답 헤더 | Description |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-y (요청 id) | 서버에서 생성 된 요청 ID입니다. Microsoft에 문의 하 여 요청을 조사 하는 데 사용할 수 있습니다. |
| x-m 속성-찾을 수 없음-동작 | GA API 선택적 응답 헤더입니다. 가능한 값은 `ThrowError` (기본값) 또는 `UseNull`입니다. |

### <a name="http-parameters"></a>HTTP 매개 변수

> [!TIP]
> [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/)에서 필수 및 선택적 쿼리 정보에 대 한 자세한 내용을 확인 합니다.

필수 URL 쿼리 문자열 매개 변수는 API 버전에 따라 달라 집니다.

| 릴리스 | 가능한 API 버전 값 |
| --- |  --- |
| 일반 공급 | `api-version=2016-12-12`|
| 미리 보기 | `api-version=2018-11-01-preview` |
| 미리 보기 | `api-version=2018-08-15-preview` |

선택적 URL 쿼리 문자열 매개 변수에는 HTTP 요청 실행 시간에 대 한 시간 제한을 설정 하는 작업이 포함 됩니다.

| 선택적 쿼리 매개 변수 | Description | 버전 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 요청 실행을 위한 서버 쪽 시간 제한입니다. [환경 이벤트 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) 및 [환경 집계](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) api에만 적용 됩니다. 제한 시간 값은 ISO 8601 기간 형식 이어야 합니다. 예를 들어 `"PT20S"` `1-30 s`범위에 있어야 합니다. 기본값은 `30 s`입니다. | GA |
| `storeType=<storeType>` | 웜 저장소가 활성화 된 미리 보기 환경의 경우 `WarmStore` 또는 `ColdStore`에서 쿼리를 실행할 수 있습니다. 쿼리의이 매개 변수는 쿼리를 실행 해야 하는 저장소를 정의 합니다. 정의 되지 않은 경우 콜드 스토어에서 쿼리가 실행 됩니다. 웜 저장소를 쿼리하려면 **Storetype** 을 `WarmStore`설정 해야 합니다. 정의 되지 않은 경우 콜드 스토어에 대해 쿼리가 실행 됩니다. | 미리 보기 |

## <a name="next-steps"></a>다음 단계

- GA Time Series Insights API를 호출 하는 샘플 코드의 경우를 [사용 하 C#여 쿼리 데이터 ](./time-series-insights-query-data-csharp.md)를 읽습니다.

- 미리 보기 Time Series Insights API 코드 샘플은를 [사용 하 여 C#쿼리 미리 보기 데이터 ](./time-series-insights-update-query-data-csharp.md)를 참조 하세요.

- API 참조 정보는 [쿼리 api 참조](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) 설명서를 참조 하세요.

- [서비스 주체를 만드는](../active-directory/develop/howto-create-service-principal-portal.md)방법에 대해 알아봅니다.
