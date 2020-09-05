---
title: Marketplace 정보-Microsoft 상업적 marketplace, Microsoft AppSource 및 Azure Marketplace
description: Microsoft AppSource 및 Azure Marketplace에서 고객 참여를 측정할 수 있게 해 주는 Marketplace 웹 분석의 요약에 액세스합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1a645a333db9b24005639f4adbb2913a2b887b66
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055671"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>파트너 센터의 Marketplace 인사이트 대시보드

이 문서에서는 파트너 센터의 Marketplace 인사이트 대시보드에 대한 정보를 제공합니다. 이 대시보드에는 상업적 marketplace 온라인 상점: Microsoft AppSource 및 Azure Marketplace에 나열 된 해당 제품 세부 정보 페이지에 대 한 고객 engagement를 게시자가 측정할 수 있는 marketplace 웹 분석 요약이 표시 됩니다.

## <a name="marketplace-insights-dashboard"></a>Marketplace 인사이트 대시보드

파트너 센터에서 **Marketplace 인사이트 대시보드**에 액세스하려면 상업용 마켓플레이스에서 **[분석 탭](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 을 엽니다.

다음 항목에 대한 그래픽 표현을 볼 수 있습니다.  

- [Marketplace 인사이트 요약](#marketplace-insights-summary)
- [지역별 페이지 방문 횟수](#page-visits-by-geography)  
- [페이지 방문 횟수 대 고유 방문자 수 추세](#page-visits-versus-unique-visitors-trend)
- [CTA (작업 호출) 및 Cta의 고유 방문자 수](#call-to-action-versus-unique-visitors-with-ctas)
- [제품별 페이지 방문 횟수 및 작업에 대한 호출](#page-visits-and-calls-to-action-by-offers)
- [작업에 대한 호출 백분율 추세](#call-to-action-percentage-trend)
- [조회 도메인별 페이지 방문 횟수 및 작업에 대한 호출](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace 인사이트 세부 정보 테이블](#marketplace-insights-details-table)

Azure Marketplace 또는 AppSource에서 제품을 방문한 사용자와 파트너 센터의 보고 간 최대 대기 시간은 48 시간입니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 마켓플레이스 분석에 대한 자주 묻는 질문과 용어](./faq-terminology.md)를 참조하세요.

### <a name="insights-dashboard-layout"></a>인사이트 대시보드 레이아웃

다양 한 방법으로 상용 마켓플레이스 메트릭을 봅니다.

- 온라인 상점 탭
- 페이지 필터
- 날짜 필터

**온라인 스토어 탭**: appsource & Azure Marketplace 탭을 통해 제품의 메트릭을 별도로 볼 수 있습니다. 오른쪽의 제품 드롭다운 목록에서 제품을 선택하여 선택한 제품에 대한 메트릭의 시각화를 볼 수 있습니다. 기본적으로 모든 제품이 선택되어 있습니다.

![파트너 센터 인사이트 대시보드 제품 드롭다운 목록](./media/insights-offer-dropdown.png)

**인사이트 페이지 필터**: 이러한 필터는 제품(제품 세부 정보 페이지) 수준에서 적용됩니다. 보려는 조건에 대해 여러 필터를 선택할 수 있습니다. 이 필터는 차트 및 세부 정보를 비롯한 전체 Marketplace 인사이트 섹션에 적용됩니다.

![파트너 센터 인사이트 대시보드 페이지 필터](./media/insights-page-filter.png)

- 제품 이름은 선택한 날짜 범위에서 페이지 방문이 있는 제품에 대해서만 나열됩니다.  
- 각 필터 옵션에 대한 기본 선택 항목은 ‘모두’입니다.
- 적용된 필터는 선택 항목에 대한 선택 항목 수를 표시합니다. 적용된 필터는 기본 ‘모두’ 선택에 대해 표시되지 않습니다.

![파트너 센터 인사이트 필터 적용됨](./media/insights-page-filter-two.png)

**인사이트 날짜 필터**: 이 필터는 전체 Marketplace 인사이트 섹션에 적용됩니다. 필터에는 미리 결정된 날짜 범위 또는 사용자 지정 날짜 범위가 포함될 수 있습니다.

![파트너 센터 인사이트 날짜 필터](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace 인사이트 요약

Marketplace Insights 요약 섹션에는 선택한 날짜 범위에 대 한 **페이지 방문**횟수, **작업 호출**및 **고유 방문자** 수가 표시 됩니다.

>[!NOTE]
>Marketplace Insights 대시보드는 클릭 스트림 데이터를 제공 하며,이는 리드 대상 끝점에서 생성 된 잠재 고객과 상관 되어서는 안 됩니다.

### <a name="page-visits"></a>페이지 방문 횟수

이 숫자는 선택된 날짜 범위에 대한 제품 페이지(제품 세부 정보 페이지)의 고유한 사용자 세션 수를 나타냅니다. 빨강/녹색 백분율 지표는 페이지 방문 횟수의 증가 비율(%)을 나타냅니다. 추세 차트는 월간 페이지 방문 횟수를 나타냅니다.

### <a name="unique-visitors"></a>고유 방문자 수

이 숫자는 페이지 필터에서 선택한 제품에 대해 선택한 날짜 범위 동안 고유 방문자 수를 나타냅니다. 하나 이상의 제품 세부 정보 페이지를 방문한 방문자는 하나의 고유 방문자로 계산됩니다.

### <a name="call-to-action"></a>활용 방안

이 숫자는 제품 페이지(제품 정보 페이지)에서 **작업에 대한 호출** 단추 클릭 완료 횟수를 나타냅니다. 사용자가 **지금 가져오기**, **무료 평가판**, **나에 게 연락**또는 **드라이브 테스트** 단추를 선택 하면 **작업에 대 한 호출이** 카운트 됩니다.

![파트너 센터 인사이트 작업에 대한 호출 요약](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>지역별 페이지 방문 횟수

아래 열 지도에는 **페이지 방문 횟수**, **작업에 대한 호출** 및 **고객 국가에 따른 고유한 방문자**가 표시됩니다. 페이지 방문 횟수가 높으면 더 짙은 지도 색으로 표시되고 페이지 방문 횟수가 낮으면 더 밝은 지도 색으로 표시됩니다.

![파트너 센터 인사이트 지리적 확산](./media/insights-geography.png)

열 지도에는 다음과 같은 기능이 포함되어 있습니다.

- 열 지도에는 **페이지 방문 횟수**, **작업에 대한 호출** 및 특정 위치의 **고유한 방문자**에 대한 세부 정보를 확인하기 위한 보조 표가 있습니다. 원하는 경우 특정 위치로 확대할 수 있습니다.  
- **국가/지역 확대**는 선택한 날짜 범위 동안 고객이 페이지 방문을 보고한 모든 국가/지역의 수입니다.
- 표에서 국가를 검색하여 선택하면 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈**을 선택하여 원래 보기로 되돌립니다.

## <a name="page-visits-versus-unique-visitors-trend"></a>페이지 방문 횟수 대 고유 방문자 수 추세

아래 열은 Y축(차트 왼쪽에 있는 축)에 표시되는 월간 페이지 방문 횟수를 나타냅니다. 추세 선은 온라인 상점에 게시 된 제품에 대 한 보조 Y 축 (차트 오른쪽의 축)에 표시 되는 고유 방문자의 월별 추세를 나타냅니다. Azure Marketplace 및 AppSource.

![파트너 센터 인사이트 페이지 방문 횟수 대 고유 방문자 수 추세](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>CTA를 사용한 작업에 대한 호출 대 고유 방문자 수

누적 열은 월간 CTA(작업에 대한 호출)를 나타냅니다. 이는 CTA 유형(**지금 받기**, **담당자** 및 **평가판**)으로 분류되고 Y축(페이지 왼쪽에 있는 축)에 그려집니다. 추세 선은 Azure Marketplace 및 AppSource에 게시된 제품에 대한 보조 Y축(차트 오른쪽에 있는 축)에 표시되는 CTA를 사용한 고유 방문자의 월별 추세를 나타냅니다.

![파트너 센터 인사이트 CTA를 사용한 작업에 대한 호출 대 고유 방문자 수](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>제품별 페이지 방문 횟수 및 작업에 대한 호출

바깥쪽 원형 차트는 Marketplace에 게시하고 필터에서 선택한 제품을 기준으로 **페이지 방문 횟수** 분석을 나타냅니다. 안쪽 차트는 동일한 제품의 **작업에 대한 호출** 분석을 나타냅니다.

![파트너 센터 인사이트 제품별 페이지 방문 횟수 및 작업에 대한 호출](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>작업에 대한 호출 백분율 추세

**작업에 대한 호출 백분율 추세**는 호출은 Marketplace에 게시된 제품의 CTA 비율을 나타냅니다. CTA % = (CTA/페이지 방문 횟수) * 100.

![파트너 센터 인사이트 작업에 대한 호출 백분율 추세](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>조회 도메인별 페이지 방문 횟수 및 작업에 대한 호출

아래 그래프는 상위 50개 조회 도메인을 표시합니다. 특정 조회 도메인을 선택하면 오른쪽 차트에 페이지 방문 횟수 및 작업에 대한 호출의 월별 추세가 표시됩니다.

![파트너 센터 인사이트 조회 도메인 및 캠페인별 페이지 방문 횟수 및 작업에 대한 호출](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace 인사이트 세부 정보 테이블

이 표에서는 선택한 제품의 페이지 방문 횟수 및 작업에 대한 호출의 목록 보기를 날짜별로 정렬하여 보여 줍니다.

![파트너 센터 인사이트 세부 정보 테이블](./media/insights-details-page.png)

- 레코드 수가 1000보다 작은 경우 데이터를 CSV 파일로 추출할 수 있습니다.
- 레코드 수가 1000을 초과하는 경우 내보낸 데이터는 다음 30일 동안 비동기적으로 다운로드 페이지에 배치됩니다.
- 원하는 데이터를 표시 하기 위해 제품 이름 및 캠페인 이름을 기준으로 데이터를 필터링 합니다.

## <a name="next-steps"></a>다음 단계

- 파트너 센터 상업용 마켓플레이스에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터의 상업용 마켓플레이스에 대한 분석](./analytics.md)을 참조하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 마켓플레이스 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](./orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 마켓플레이스 분석의 사용량 대시보드](./usage-dashboard.md)를 참조하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 마켓플레이스 분석의 고객 대시보드](./customer-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 마켓플레이스 분석의 다운로드 대시보드](./downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대한 고객 의견의 통합 보기를 확인하려면 [상업용 마켓플레이스 분석의 평가 및 검토 대시보드](./ratings-reviews.md)를 참조하세요.
- 상업용 마켓플레이스 분석 및 데이터 용어의 종합 사전에 대한 자주 묻는 질문은 [상업용 마켓플레이스 분석에 대한 자주 묻는 질문과 용어](./faq-terminology.md)를 참조하세요.
