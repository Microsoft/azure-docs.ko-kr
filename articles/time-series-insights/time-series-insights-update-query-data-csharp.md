---
title: 'C #을 사용 하 여 Gen2 환경에서 데이터 쿼리-Azure Time Series Insights | Microsoft Docs'
description: 'C #으로 작성 된 앱을 사용 하 여 Azure Time Series Insights Gen2 환경에서 데이터를 쿼리 하는 방법에 대해 알아봅니다.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: fcb38f761a72bf2c6e5dee8bb993cec9143e7241
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998954"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>C #을 사용 하 여 Azure Time Series Insights Gen2 환경에서 데이터 쿼리

이 c # 예제에서는 Azure Time Series Insights Gen2 환경에서 [Gen2 Data Access api](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) 의 데이터를 쿼리 하는 방법을 보여 줍니다.

> [!TIP]
> 에서 Gen2 c # 코드 샘플을 봅니다 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [Azure AutoRest](https://github.com/Azure/AutoRest)의 SDK 자동 생성 지원
* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)를 사용하여 Azure Active Directory를 통해 액세스 토큰을 획득하는 방법
* 후속 데이터 액세스 API 요청의 `Authorization` 헤더에서 획득한 액세스 토큰을 전달하는 방법
* 이 샘플에서는 다음에 대 한 HTTP 요청을 수행 하는 방법을 보여 주는 콘솔 인터페이스를 제공 합니다.
  * [Gen2 환경 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [Get Environments Availability API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) 및 [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2 쿼리 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) 및 [Get Aggregate Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Time Series Model API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Get Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) 및 [Hierarchies Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Get Types API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) 및 [Types Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Get Instances API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) 및 [Instances Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* 고급 [Search](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) 및 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 기능

## <a name="prerequisites-and-setup"></a>필수 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [Gen2 Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) 합니다.
1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다.
1. [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) 에 지정 된 대로 [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) 를 실행 하 여 Azure Time Series Insights Gen2 client 종속성을 생성 합니다.
1. `TSIPreviewDataPlaneclient.sln` 솔루션을 열고 `DataPlaneClientSampleApp`를 Visual Studio의 기본 프로젝트로 설정합니다.
1. [아래](#project-dependencies)에 설명된 단계를 사용하여 필요한 프로젝트 종속성을 설치하고 예제를 `.exe` 실행 파일로 컴파일합니다.
1. `.exe` 파일을 두 번 클릭하여 실행합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio를 사용하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+

예제 코드에는 [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) 파일에서 볼 수 있는 몇 가지 필수 종속성이 있습니다.

**빌드** > **솔루션 빌드** 옵션을 선택하여 Visual Studio 2019에서 패키지를 다운로드합니다.

또는 [NuGet 2.12+](https://www.nuget.org/)를 사용하여 각 패키지를 추가합니다. 예를 들면 다음과 같습니다.

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 샘플 코드

C # 샘플 코드에 액세스 하려면 [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) 리포지토리를 참조 하세요. 

> [!NOTE]
>
> * 기본 환경 변수를 변경하지 않고 코드 샘플을 실행할 수 있습니다.
> * 코드 샘플은 .NET 실행 가능 콘솔 앱으로 컴파일됩니다.

## <a name="next-steps"></a>다음 단계

* 쿼리에 대해 자세히 알아보려면 [Query API 참조](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)를 참조하세요.

* [클라이언트 SDK를 사용 하 여 JavaScript 앱을 연결 하 여](https://github.com/microsoft/tsiclient) Azure Time Series Insights 하는 방법을 참조 하세요.
