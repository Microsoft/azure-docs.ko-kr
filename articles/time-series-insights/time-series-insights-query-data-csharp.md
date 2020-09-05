---
title: 'C # 코드를 사용 하 여 Gen1 환경에서 데이터 쿼리-Azure Time Series Insights Gen1 | Microsoft Docs'
description: 'C #으로 작성 된 사용자 지정 앱을 사용 하 여 Azure Time Series Insights Gen1 환경에서 데이터를 쿼리 하는 방법에 대해 알아봅니다.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: e7f5750d89c661cad7616cf1c56157c6e92e1175
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654466"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>C #을 사용 하 여 Azure Time Series Insights Gen1 환경에서 데이터 쿼리

이 c # 예제에서는 [Gen1 쿼리 api](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query) 를 사용 하 여 Azure Time Series Insights Gen1 환경에서 데이터를 쿼리 하는 방법을 보여 줍니다.

> [!TIP]
> 에서 Gen1 c # 코드 샘플을 봅니다 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)를 사용하여 Azure Active Directory를 통해 액세스 토큰을 획득하는 방법

* 이후 쿼리 API 요청의 헤더에서 획득 한 액세스 토큰을 전달 하는 방법입니다 `Authorization` .

* 이 샘플은에 대 한 HTTP 요청을 수행 하는 방법을 보여 주는 각 Gen1 쿼리 Api를 호출 합니다.
  * 사용자가 액세스할 수 있는 환경을 반환 하는 [환경 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environments-api)
  * [환경 가용성 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * 환경 메타 데이터를 검색 하는 [환경 메타 데이터 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api)
  * [환경 이벤트 가져오기 API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [환경 집계 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* WSS를 사용 하 여 Gen1 쿼리 Api와 상호 작용 하 여를 메시지에 포함 하는 방법:

  * [환경 이벤트 스트리밍 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [환경 집계 스트리밍 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>필수 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [Gen1 Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) 합니다.
1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다.
1. 필요한 프로젝트 종속성을 설치합니다.
1. 각 **#DUMMY #** 을 적절 한 환경 식별자로 바꿔서 아래 샘플 코드를 편집 합니다.
1. Visual Studio 내에서 코드를 실행 합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+

샘플 코드에는 다음과 같은 두 가지 필수 종속성이 있습니다.

* [System.identitymodel. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9로 package.
* 온-9.0.1 패키지 [를Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json) 합니다.

**빌드** > **솔루션 빌드** 옵션을 선택하여 Visual Studio 2019에서 패키지를 다운로드합니다.

또는 [NuGet 2.12 +](https://www.nuget.org/)를 사용 하 여 패키지를 추가 합니다.

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 샘플 코드

C # 샘플 코드에 액세스 하려면 [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] 리포지토리를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* 쿼리에 대해 자세히 알아보려면 [Query API 참조](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api)를 참조하세요.

* [클라이언트 SDK를 사용하여 JavaScript 앱을](https://github.com/microsoft/tsiclient) Time Series Insights에 연결하는 방법을 읽어보세요.
Azure-Samples/Azure-Time 시리즈-Insights/gen1-sample/csharp-gen1-sample/Program .cs
