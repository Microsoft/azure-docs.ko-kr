---
title: -Azure Time Series Insights을 사용 하 여 C# 미리 보기 환경에서 데이터 쿼리 Microsoft Docs
description: 에서 C#작성 한 앱을 사용 하 여 Azure Time Series Insights 환경에서 데이터를 쿼리 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980990"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>을 사용 하 여 Azure Time Series Insights 미리 보기 환경에서 데이터 쿼리C#

이 C# 예제에서는 Azure Time Series Insights 미리 보기 환경에서 데이터 [액세스 api 미리 보기](https://docs.microsoft.com/rest/api/time-series-insights/preview) 에서 데이터를 쿼리 하는 방법을 보여 줍니다.

> [!TIP]
> 에서 C# 미리 보기 코드 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)샘플을 봅니다.

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [Azure AutoRest](https://github.com/Azure/AutoRest)에서 SDK 자동 생성에 대 한 지원.
* System.identitymodel를 사용 하 여 액세스 토큰을 가져오는 방법 Azure Active Directory [ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* 이후 데이터 액세스 API 요청의 `Authorization` 헤더에서 획득 한 액세스 토큰을 전달 하는 방법입니다. 
* 이 샘플에서는에 대 한 HTTP 요청을 수행 하는 방법을 보여 주는 콘솔 인터페이스를 제공 합니다.

    * [환경 미리 보기 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [환경 가용성 Api 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) 및 [이벤트 스키마 가져오기 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [쿼리 API 미리 보기](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [이벤트 Api 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [series Api 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)및 [집계 시리즈 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries) 가져오기
    * [시계열 모델 Api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [계층 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) 및 [계층 구조 가져오기 Batch api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Get TYPES api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) 및 [types Batch api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Get INSTANCES api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) 및 [instances Batch api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 고급 [검색](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) 및 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) 기능.

## <a name="prerequisites-and-setup"></a>필수 구성 요소 및 설치

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [미리 보기 Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) 합니다.
1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명 된 대로 Azure Active Directory에 대 한 Azure Time Series Insights 환경을 구성 합니다. 
1. [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) 에 지정 된 대로 [GenerateCode](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) 를 실행 하 Time Series Insights 미리 보기 클라이언트 종속성을 생성 합니다.
1. `TSIPreviewDataPlaneclient.sln` 솔루션을 열고 `DataPlaneClientSampleApp`를 Visual Studio의 기본 프로젝트로 설정 합니다.
1. [아래](#project-dependencies) 에 설명 된 단계를 사용 하 여 필요한 프로젝트 종속성을 설치 하 고 예제를 실행 파일 `.exe` 파일로 컴파일합니다.
1. `.exe` 파일을 두 번 클릭 하 여 실행 합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용 하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -버전 16.4.2 +

예제 코드에는 [패키지 .config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) 파일에서 볼 수 있는 몇 가지 필수 종속성이 있습니다.

**빌드** > **솔루션 빌드** 옵션을 선택 하 여 Visual Studio 2019에서 패키지를 다운로드 합니다. 

또는 [NuGet 2.12 +](https://www.nuget.org/)를 사용 하 여 각 패키지를 추가 합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 샘플 코드

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 기본 환경 변수를 변경 하지 않고 코드 샘플을 실행할 수 있습니다.
> * 코드 샘플은 .NET 실행 가능 콘솔 앱으로 컴파일됩니다.

## <a name="next-steps"></a>다음 단계

- 쿼리에 대 한 자세한 내용은 [쿼리 API 참조를 참조](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)하세요.

- [클라이언트 SDK를 사용 하 여 JavaScript 앱을 연결 하 여](https://github.com/microsoft/tsiclient) Time Series Insights 하는 방법을 참조 하세요.
