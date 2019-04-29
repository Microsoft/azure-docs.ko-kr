---
title: Azure Application Insights에서 Power BI로 내보내기 | Microsoft Docs
description: 분석 쿼리를 Power BI에서 표시할 수 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: a57393918992019844e2ff4ccc13d671f0b90ed5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900396"
---
# <a name="feed-power-bi-from-application-insights"></a>Application Insights에서 Power BI 공급
[Power BI](https://www.powerbi.com/)는 데이터 분석과 통찰력 공유에 도움이 되는 비즈니스 도구 제품군입니다. 모든 디바이스에서 풍부한 대시보드를 사용할 수 있습니다. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)의 Analytics 쿼리를 포함하여 다양한 원본의 데이터를 포함할 수 있습니다.

Power BI에 Application Insights 데이터를 내보내는 세 가지 방법이 있습니다.

* [**Analytics 쿼리 내보내기**](#export-analytics-queries). 이는 선호되는 방법입니다. 원하는 쿼리를 작성하여 Power BI에 내보냅니다. 이 쿼리를 다른 데이터와 함께 대시보드에 배치할 수 있습니다.
* [**연속 내보내기 및 Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). 이 방법은 데이터를 오랜 기간 저장하려는 경우에 유용합니다. 데이터를 오랜 기간 보존해야 할 필요가 없다면 분석 쿼리를 내보내는 방법을 사용하세요. 연속 내보내기 및 Stream Analytics의 경우 추가 설정 작업이 필요하고 저장 오버헤드가 추가로 발생합니다.
* **Power BI 어댑터**. 일련의 차트가 미리 정의되어 있으나, 다른 원본에서 직접 쿼리를 추가할 수 있습니다.

> [!NOTE]
> Power BI 어댑터는 이제 **사용되지 않습니다**. 이 솔루션의 미리 정의된 파트는 정적이고 편집할 수 없는 쿼리로 채워집니다. 이러한 쿼리는 편집할 수 없으며, 데이터의 특정 속성에 따라 Power BI에 대한 연결이 성공할 수 있지만 데이터는 채워지지 않습니다. 이는 하드 코딩된 쿼리 내에 설정된 제외 기준 때문입니다. 이 솔루션은 일부 고객에게 여전히 효과적일 수 있지만 어댑터 유연성이 결여되어 있기 때문에 [**Analytics 쿼리 내보내기**](#export-analytics-queries) 기능을 사용하는 것이 좋습니다.

## <a name="export-analytics-queries"></a>Analytics 쿼리 내보내기
이 경로를 사용하면 원하는 모든 Analytics 쿼리를 작성하거나 Usage Funnels에서 내보낸 다음 Power BI 대시보드로 내보낼 수 있습니다. (어댑터에서 만든 대시보드를 추가할 수 있습니다.)

### <a name="one-time-install-power-bi-desktop"></a>한 번: Power BI Desktop을 설치합니다.
Application Insights 쿼리를 가져오려면 데스크톱 버전의 Power BI를 사용합니다. 그런 다음 또 웹이나 사용자의 Power BI 클라우드 작업 영역으로 게시할 수 있습니다. 

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)을 설치합니다.

### <a name="export-an-analytics-query"></a>Analytics 쿼리 내보내기
1. [Analytics 열기 및 쿼리 작성](../../azure-monitor/log-query/get-started-portal.md).
2. 결과에 만족할 때까지 쿼리를 테스트하고 수정합니다. 내보내기 전에 Analytics에서 쿼리가 제대로 실행되는지 확인합니다.
3. **내보내기** 메뉴에서 **Power BI(M)** 를 선택합니다. 텍스트 파일을 저장합니다.
   
    ![내보내기 메뉴가 강조 표시된 Analytics 스크린샷](./media/export-power-bi/analytics-export-power-bi.png)
4. Power BI Desktop을 열고 **데이터 가져오기** > **빈 쿼리**를 선택합니다. 그런 다음 쿼리 편집기의 **보기**에서 **고급 편집기**를 선택합니다.

    내보낸 M 언어 스크립트를 고급 편집기에 붙여넣습니다.

    ![고급 편집기가 강조 표시된 Power BI Desktop 스크린샷](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Power BI가 Azure에 액세스할 수 있도록 자격 증명을 제공해야 할 수 있습니다. **조직 계정**을 사용하여 Microsoft 계정에 로그인합니다.
   
    ![Power BI 쿼리 설정 대화 상자 스크린샷](./media/export-power-bi/power-bi-import-sign-in.png)

    자격 증명을 확인해야 하는 경우 쿼리 편집기에서 **데이터 원본 설정** 메뉴 명령을 사용합니다. Azure에 사용할 자격 증명은 Power BI용 자격 증명과 다를 수 있습니다.
6. 쿼리에 대한 시각화를 선택하고 x축, y축 및 차원 분할에 대한 필드를 선택합니다.
   
    ![Power BI Desktop 시각화 옵션 스크린샷](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Power BI 클라우드 작업 영역에 보고서를 게시합니다. 여기에서 다른 웹 페이지에 동기화된 버전을 포함할 수 있습니다.
   
    ![게시 단추가 강조 표시된 Power BI Desktop 스크린샷](./media/export-power-bi/publish-power-bi.png)
8. 간격을 두고 보고서를 수동으로 새로 고치거나 옵션 페이지에서 예약된 새로 고침을 설정합니다.

### <a name="export-a-funnel"></a>깔때기 내보내기
1. [깔때기를 만듭니다](../../azure-monitor/app/usage-funnels.md).
2. **Power BI**를 선택합니다.

   ![Power BI 단추 스크린샷](./media/export-power-bi/button.png)

3. Power BI Desktop을 열고 **데이터 가져오기** > **빈 쿼리**를 선택합니다. 그런 다음 쿼리 편집기의 **보기**에서 **고급 편집기**를 선택합니다.

   ![쿼리 단추가 강조 표시된 Power BI Desktop 스크린샷](./media/export-power-bi/blankquery.png)

   내보낸 M 언어 스크립트를 고급 편집기에 붙여넣습니다. 

   ![고급 편집기가 강조 표시된 Power BI Desktop 스크린샷](./media/export-power-bi/advancedquery.png)

4. 쿼리에서 항목을 선택하고 깔때기 시각화를 선택합니다.

   ![Power BI Desktop 시각화 옵션 스크린샷](./media/export-power-bi/selectsequence.png)

5. 제목을 더 의미 있게 변경하고 Power BI 클라우드 작업 영역에 보고서를 게시합니다. 

   ![제목 변경이 강조 표시된 Power BI Desktop 스크린샷](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>문제 해결

자격 증명이나 데이터 세트 크기와 관련한 오류가 발생할 수 잇습니다. 이러한 오류에 대해 수행할 작업에 대한 일부 내용은 다음과 같습니다.

### <a name="unauthorized-401-or-403"></a>권한 없음(401 또는 403) 
새로 고침 토큰이 업데이트되지 않은 경우에 발생할 수 있습니다. 액세스 권한이 있는지 확인하려면 다음 단계를 시도합니다.

1. Azure Portal에 로그인하고 리소스에 액세스할 수 있는지 확인합니다.
2. 대시보드에 대한 자격 증명 새로 고침을 시도합니다.

   액세스 권한이 있고 자격 증명 새로 고침이 작동하지 않는 경우 지원 티켓을 여세요.

### <a name="bad-gateway-502"></a>잘못된 게이트웨이(502)
일반적으로 너무 많은 데이터를 반환하는 분석 쿼리가 원인입니다. 쿼리에 대해 시간 범위를 좁혀서 사용합니다. 

분석 쿼리에서 들어오는 데이터 세트 감소가 요구 사항을 충족하지 않는 경우 더 큰 데이터 세트를 가져오도록 [API](https://dev.applicationinsights.io/documentation/overview) 사용을 고려해야 합니다. M 쿼리 내보내기를 변환하여 API를 사용하는 방법은 다음과 같습니다.

1. [API 키](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)를 만듭니다.
2. Azure Resource Manager URL을 Application Insights API로 대체하여 Analytics에서 내보낸 Power BI M 스크립트를 업데이트합니다.
   * 바꿉니다 **https:\//management.azure.com/subscriptions/...**
   * 를 사용 하 여 **https:\//api.applicationinsights.io/beta/apps/...**
3. 마지막으로 자격 증명을 기본으로 업데이트하고 API 키를 사용합니다.

**기존 스크립트**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**업데이트된 스크립트**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>샘플링 정보
애플리케이션에서 보낸 데이터의 양에 따라 원격 분석의 백분율만 보내는 적응 샘플링 기능을 사용할 수 있습니다. 이는 SDK 또는 수집에서 샘플링을 수동으로 설정한 경우에도 마찬가지입니다. [샘플링에 대해 자세히 알아봅니다](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI 어댑터(사용되지 않음)
이 방법은 원격 분석의 전체 대시보드를 만듭니다. 초기 데이터 세트는 미리 정의되어 있으나, 더 많은 데이터를 추가할 수 있습니다.

### <a name="get-the-adapter"></a>어댑터 가져오기
1. [Power BI](https://app.powerbi.com/)에 로그인합니다.
2. **데이터 가져오기** ![왼쪽 하단 모서리의 데이터 가져오기 아이콘 스크린샷](./media/export-power-bi/001.png), **서비스**를 엽니다.

    ![Application Insights 데이터 원본에서 가져오기 스크린샷](./media/export-power-bi/002.png)

3. Application Insights 아래에서 **지금 가져오기**를 선택합니다.

   ![Application Insights 데이터 원본에서 가져오기 스크린샷](./media/export-power-bi/003.png)
4. Application Insights 리소스의 세부 정보를 제공한 다음, **로그인**합니다.

    ![Application Insights 데이터 원본에서 가져오기 스크린샷](./media/export-power-bi/005.png)

     이 정보는 Application Insights 개요 창에서 확인할 수 있습니다.

     ![Application Insights 데이터 원본에서 가져오기 스크린샷](./media/export-power-bi/004.png)

5. 새로 생성된 Application Insights Power BI 앱을 엽니다.

6. 데이터를 가져오는 데1, 2분 정도 기다립니다.

    ![Power BI 어댑터 스크린샷](./media/export-power-bi/010.png)

Application Insights 차트를 다른 원본의 차트 및 Analytics 쿼리와 결합하여 대시보드를 편집할 수 있습니다. 시각화 갤러리에서 다른 차트를 가져올 수 있고, 각 차트에는 설정할 수 있는 매개 변수가 있습니다.

초기 가져오기 후에는 대시보드와 보고서가 지속적으로 매일 업데이트됩니다. 데이터 세트에 대한 새로 고침 일정을 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Power BI - 알아보기](https://www.powerbi.com/learning/)
* [Analytics 자습서](../../azure-monitor/log-query/get-started-portal.md)

