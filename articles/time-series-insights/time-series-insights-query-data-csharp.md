---
title: 코드 Azure Time Series Insights를 사용 하 여 C# GA 환경에서 데이터 쿼리 | Microsoft Docs
description: 에서 C#작성 한 사용자 지정 앱을 사용 하 여 Azure Time Series Insights 환경에서 데이터를 쿼리 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987974"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>를 사용 하 여 Azure Time Series Insights GA 환경에서 데이터 쿼리C#

이 C# 예제에서는 [ga 쿼리 api](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) 를 사용 하 여 Azure Time Series Insights ga 환경에서 데이터를 쿼리 하는 방법을 보여 줍니다.

> [!TIP]
> 에서 C# GA 코드 샘플 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)을 봅니다.

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* System.identitymodel를 사용 하 여 액세스 토큰을 가져오는 방법 Azure Active Directory [ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* 이후 쿼리 API 요청의 `Authorization` 헤더에서 획득 한 액세스 토큰을 전달 하는 방법입니다. 

* 이 샘플은에 대 한 HTTP 요청을 수행 하는 방법을 보여 주는 각 GA 쿼리 Api를 호출 합니다.
    * 사용자가 액세스할 수 있는 환경을 반환 하는 [환경 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api)
    * [환경 가용성 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * 환경 메타 데이터를 검색 하는 [환경 메타 데이터 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api)
    * [환경 이벤트 가져오기 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [환경 집계 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* WSS를 사용 하 여 GA 쿼리 Api와 상호 작용 하 여 메시지를 메시지에 포함 하는 방법:

   * [환경 이벤트 스트리밍 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [환경 집계 스트리밍 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>필수 구성 요소 및 설치

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [GA Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) 합니다.
1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명 된 대로 Azure Active Directory에 대 한 Azure Time Series Insights 환경을 구성 합니다. 
1. 필요한 프로젝트 종속성을 설치 합니다.
1. 각 **#DUMMY #** 을 적절 한 환경 식별자로 바꿔서 아래 샘플 코드를 편집 합니다.
1. Visual Studio 내에서 코드를 실행 합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용 하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -버전 16.4.2 +

샘플 코드에는 두 가지 필수 종속성이 있습니다.

* [System.identitymodel. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9로 package.
* [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1 package.

**빌드** > **솔루션 빌드** 옵션을 선택 하 여 Visual Studio 2019에서 패키지를 다운로드 합니다.

또는 [NuGet 2.12 +](https://www.nuget.org/)를 사용 하 여 패키지를 추가 합니다.

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 샘플 코드

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>다음 단계

- 쿼리에 대 한 자세한 내용은 [쿼리 API 참조를 참조](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)하세요.

- [클라이언트 SDK를 사용 하 여 JavaScript 앱을 연결 하 여](https://github.com/microsoft/tsiclient) Time Series Insights 하는 방법을 참조 하세요.
