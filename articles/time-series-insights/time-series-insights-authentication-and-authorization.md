---
title: API 인증 및 권한 부여 - Azure Time Series Insights | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: bbec605f25c2e74178bdb5c28d0a7995e4e265f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690394"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에서는 새 Azure Active Directory 블레이드를 사용하여 Azure Active Directory에 앱을 등록하는 방법을 설명합니다. Azure Active Directory에 등록 된 앱은 사용자가에 인증 하 고 Azure Time Series Insights 환경에 연결 된 Azure Time Series insights API를 사용할 수 있도록 권한을 부여 받습니다.

## <a name="service-principal"></a>서비스 주체

다음 섹션에서는 앱을 대신 하 여 Azure Time Series Insights API에 액세스 하도록 응용 프로그램을 구성 하는 방법을 설명 합니다. 그러면 응용 프로그램은 Azure Active Directory를 통해 자체 응용 프로그램 자격 증명을 사용 하 여 Azure Time Series Insights 환경에서 참조 데이터를 쿼리하거나 게시할 수 있습니다.

## <a name="summary-and-best-practices"></a>요약 및 모범 사례

Azure Active Directory 앱 등록 흐름에는 세 가지 주요 단계가 포함됩니다.

1. Azure Active Directory에서 [애플리케이션을 등록](#azure-active-directory-app-registration)합니다.
1. [Azure Time Series Insights 환경에 대 한 데이터 액세스](#granting-data-access)권한을 응용 프로그램에 부여 합니다.
1. **애플리케이션 ID** 및 **클라이언트 암호**를 사용하여 [클라이언트 앱](#client-app-initialization)의 `https://api.timeseries.azure.com/`에서 토큰을 획득합니다. 그런 다음 토큰을 사용 하 여 Azure Time Series Insights API를 호출할 수 있습니다.

**3단계**에 따라 애플리케이션 및 사용자 자격 증명을 분리하면 다음 작업을 수행할 수 있습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당합니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어 특정 Azure Time Series Insights 환경의 데이터만 읽도록 앱을 허용할 수 있습니다.
* **클라이언트 암호** 또는 보안 인증서를 사용하여 사용자의 인증 자격 증명을 만드는 것과 애플리케이션의 보안을 격리합니다. 따라서 애플리케이션의 자격 증명은 특정 사용자의 자격 증명에 종속되지 않습니다. 사용자의 역할이 변경되어도 애플리케이션에 새 자격 증명이나 추가 구성이 반드시 필요한 것은 아닙니다. 사용자가 암호를 변경하는 경우 애플리케이션에 대한 모든 액세스에 새 자격 증명이나 키가 필요하지는 않습니다.
* 특정 사용자의 자격 증명이 아닌 **클라이언트 암호** 또는 보안 인증서(존재해야 함)를 사용하여 무인 스크립트를 실행합니다.
* 암호 대신 보안 인증서를 사용하여 Azure Time Series Insights API에 대한 액세스를 보호합니다.

> [!IMPORTANT]
> Azure Time Series Insights 보안 정책을 구성하는 경우 **문제의 분리**(위의 시나리오에 설명됨) 원칙을 따릅니다.

> [!NOTE]

> * 이 문서에서는 애플리케이션을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 애플리케이션을 중점적으로 다룹니다.
> * 일반적으로 단일 조직에서 실행되는 LOB(기간 업무) 애플리케이션에 대해 단일 테넌트 애플리케이션을 사용하게 됩니다.

## <a name="detailed-setup"></a>자세한 설정

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>데이터 액세스 권한 부여

1. Azure Time Series Insights 환경의 경우 **데이터 액세스 정책** 을 선택 하 고 **추가**를 선택 합니다.

   [![Azure Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **사용자 선택** 대화 상자의 Azure Active Directory 앱 등록 섹션에서 **애플리케이션 이름** 또는 **애플리케이션 ID**를 붙여넣습니다.

   [![사용자 선택 대화 상자에서 애플리케이션 찾기](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 역할을 선택합니다. **읽기 권한자**를 선택하여 데이터를 쿼리하거나 **기여자**를 선택하여 데이터를 쿼리하고 참조 데이터를 변경합니다. **확인**을 선택합니다.

   [![사용자 역할 선택 대화 상자에서 읽기 권한자 또는 기여자 선택](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **확인**을 선택하여 정책을 저장합니다.

   > [!TIP]
   > 고급 데이터 액세스 옵션에 대한 내용은 [데이터 액세스 권한 부여](./time-series-insights-data-access.md)를 읽어보세요.

### <a name="client-app-initialization"></a>클라이언트 앱 초기화

* 개발자는 MSAL (Microsoft Authentication Library)을 사용 하 여 Azure Time Series Insights 인증할 수 있습니다.

* MSAL을 사용 하 여 인증 하려면:

   1. 애플리케이션을 대신하여 토큰을 획득하려면 Azure Active Directory 앱 등록 섹션에서 **애플리케이션 ID** 및 **클라이언트 암호**(애플리케이션 키)를 사용합니다.

   1. C#에서 다음 코드는 애플리케이션 대신 토큰을 가져올 수 있습니다. Gen1 환경에서 데이터를 쿼리 하는 방법에 대 한 전체 예제는 [c #을 사용 하 여 쿼리 데이터](time-series-insights-query-data-csharp.md)를 참조 하세요.

        C # 코드에 액세스 하려면 [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] 리포지토리를 참조 하세요. 

   1. 그런 다음 `Authorization` 응용 프로그램에서 AZURE TIME SERIES INSIGHTS API를 호출할 때 헤더에서 토큰을 전달할 수 있습니다.

> [!IMPORTANT]
> [ADAL (Azure Active Directory Authentication Library)](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) 을 사용 하는 경우 [msal으로의 마이그레이션](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)에 대해 읽어 보십시오.

## <a name="common-headers-and-parameters"></a>일반 헤더 및 매개 변수

이 섹션에서는 Azure Time Series Insights Gen1 및 Gen2 Api에 대 한 쿼리를 수행 하는 데 사용 되는 일반적인 HTTP 요청 헤더 및 매개 변수에 대해 설명 합니다. API 관련 요구 사항은 [Azure Time Series Insights REST API 참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/)에 자세히 설명 되어 있습니다.

> [!TIP]
> REST API를 사용하고, HTTP 요청을 수행하고, HTTP 응답을 처리하는 방법에 대해 자세히 알아보려면 [Azure REST API 참조](https://docs.microsoft.com/rest/api/azure/)를 읽어보세요.

### <a name="authentication"></a>인증

[AZURE TIME SERIES INSIGHTS Rest api](https://docs.microsoft.com/rest/api/time-series-insights/)에 대해 인증 된 쿼리를 수행 하려면 선택한 rest 클라이언트 (Postman, JavaScript, c #)를 사용 하 여 유효한 OAuth 2.0 전달자 토큰을 [인증 헤더](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) 에 전달 해야 합니다.

> [!TIP]
> 호스팅된 Azure Time Series Insights [CLIENT sdk 샘플 시각화](https://tsiclientsample.azurewebsites.net/) 를 읽고 차트 및 그래프와 함께 [JavaScript 클라이언트 sdk](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 를 사용 하 여 프로그래밍 방식으로 Azure Time Series Insights api를 사용 하 여 인증 하는 방법을 알아봅니다.

### <a name="http-headers"></a>HTTP 헤더

필요한 요청 헤더는 아래에 설명되어 있습니다.

| 필수 요청 헤더 | Description |
| --- | --- |
| 권한 부여 | Azure Time Series Insights 인증 하려면 유효한 OAuth 2.0 전달자 토큰을 **인증** 헤더에 전달 해야 합니다. |

> [!IMPORTANT]
> 토큰은 토큰의 "대상"이라고도 하는 `https://api.timeseries.azure.com/` 리소스로 정확히 발급되어야 합니다.

> * 따라서 [Postman](https://www.getpostman.com/) **AuthURL**은 다음과 같습니다. `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com/.default`
> * `https://api.timeseries.azure.com/`은 유효하지만 `https://api.timeseries.azure.com`은 유효하지 않습니다.

선택적 요청 헤더는 아래에 설명되어 있습니다.

| 선택적 요청 헤더입니다. | Description |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-ms-client-request-id | 클라이언트 요청 ID입니다. 서비스는 이 값을 기록합니다. 이 서비스는 서비스 간 작업을 추적할 수 있습니다. |
| x-ms-client-session-id | 클라이언트 세션 ID입니다. 서비스는 이 값을 기록합니다. 이 서비스는 서비스 간 관련 작업 그룹을 추적할 수 있습니다. |
| x-ms-client-application-name | 이 요청을 생성한 애플리케이션의 이름입니다. 서비스는 이 값을 기록합니다. |

선택 사항이지만 권장되는 응답 헤더는 아래에 설명되어 있습니다.

| 응답 헤더 | Description |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-ms-request-id | 서버에서 생성된 요청 ID입니다. Microsoft에 문의하여 요청을 조사하는 데 사용할 수 있습니다. |
| x-ms-property-not-found-behavior | GA API의 선택적 응답 헤더입니다. 가능한 값은 `ThrowError`(기본값) 또는 `UseNull`입니다. |

### <a name="http-parameters"></a>HTTP 매개 변수

> [!TIP]
> [참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/)에서 필수 및 선택적 쿼리 정보에 대한 자세한 내용을 확인하세요.

필수 URL 쿼리 문자열 매개 변수는 API 버전에 따라 달라집니다.

| 해제 | 가능한 API 버전 값 |
| --- |  --- |
| 1세대 | `api-version=2016-12-12`|
| 2세대 | `api-version=2020-07-31` 및 `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview`버전은 곧 사용 되지 않습니다. 사용자가 최신 버전으로 전환 하는 것이 좋습니다.

선택적 URL 쿼리 문자열 매개 변수에는 HTTP 요청 실행 시간에 대한 시간 제한을 설정하는 작업이 포함됩니다.

| 선택적 쿼리 매개 변수 | Description | 버전 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 요청 실행을 위한 서버 쪽 시간 제한입니다. [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) 미 [Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API에만 적용됩니다. 제한 시간 값은 ISO 8601 기간 형식(예: `"PT20S"`)이어야 하며 `1-30 s` 범위에 있어야 합니다. 기본값은 `30 s`입니다. | 1세대 |
| `storeType=<storeType>` | 웜 저장소가 활성화 된 Gen2 환경의 경우 또는에서 쿼리를 실행할 수 있습니다 `WarmStore` `ColdStore` . 쿼리의 이 매개 변수는 쿼리를 실행해야 하는 저장소를 정의합니다. 정의되지 않은 경우 쿼리는 콜드 저장소에서 실행됩니다. 웜 저장소를 쿼리하려면 **storeType**을 `WarmStore`로 설정해야 합니다. 정의되지 않은 경우 콜드 저장소에 대해 쿼리가 실행됩니다. | 2세대 |

## <a name="next-steps"></a>다음 단계

* Gen1 Azure Time Series Insights API를 호출 하는 샘플 코드는 [c #을 사용 하 여 Query Gen1 data](./time-series-insights-query-data-csharp.md)를 읽습니다.

* Gen2 Azure Time Series Insights API 코드 샘플을 호출 하는 샘플 코드는 [c #을 사용 하 여 Query Gen2 data](./time-series-insights-update-query-data-csharp.md)를 참조 하세요.

* API 참조 정보를 보려면 [쿼리 API 참조](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api) 설명서를 참조하세요.

* [서비스 주체를 만드는](../active-directory/develop/howto-create-service-principal-portal.md) 방법을 알아봅니다.
