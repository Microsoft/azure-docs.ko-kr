---
title: Verizon의 사용자 지정 보고서 | Microsoft Docs
description: 다음과 같은 보고서를 사용 하 여 cdn 사용 패턴을 볼 수 있습니다. 대역폭, 전송 데이터, 적중 횟수, 캐시 상태, 캐시 적중률, 전송 되는 IPV4/i p v 6 데이터입니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 15f17ac6556c4ff731372dc7f738d0f58bdc3e31
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593315"
---
# <a name="custom-reports-from-verizon"></a>Verizon의 사용자 지정 보고서

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon 프로필에 대한 관리 포털을 통해 Verizon 사용자 지정 보고서를 사용하여 에지 CNAME 보고서에 대해 수집할 데이터 유형을 정의할 수 있습니다.


## <a name="accessing-verizon-custom-reports"></a>Verizon 사용자 지정 보고서 액세스
1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 관리 단추](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **분석** 탭을 마우스로 가리킨 후 **핵심 보고서** 플라이아웃을 마우스로 가리킵니다. **에지 CNAME**을 클릭합니다.
   
    ![CDN 관리 포털 - 사용자 지정 보고서 메뉴](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>에지 CNAME 사용자 지정 보고서
에지 CNAME 사용자 지정 보고서는 사용자 지정 보고서 로깅을 사용하도록 설정된 에지 CNAME에 대한 적중 횟수 및 데이터 전송 통계를 제공합니다. 에지 CNAME은 Azure CDN 엔드포인트 호스트 이름 및 모든 관련 사용자 지정 도메인 호스트 이름으로 구성됩니다. 

사용자 지정 보고서 데이터 로깅은 에지 CNAME의 사용자 지정 보고 기능을 사용하도록 설정한 후 1시간 후에 시작됩니다. 특정 플랫폼 또는 모든 플랫폼에 대한 에지 CNAME 보고서를 생성하여 보고서 데이터를 볼 수 있습니다. 이 보고서의 적용 범위는 지정된 기간 동안 사용자 지정 보고서 데이터가 수집된 에지 CNAME으로 제한됩니다. 에지 CNAME 보고서는 메트릭 옵션에 정의된 메트릭에 따라 상위 10개 에지 CNAME에 대한 그래프 및 데이터 테이블로 구성됩니다. 

다음 보고서 옵션을 정의하여 사용자 지정 보고서를 생성합니다.

- 메트릭: 다음과 같은 옵션이 지원됩니다.

   - 적중 횟수: 사용자 지정 보고 기능이 사용 되는 지 CNAME로 전달 되는 요청의 총 수를 나타냅니다. 이 메트릭에는 클라이언트에 반환된 상태 코드가 포함되지 않습니다.

   - 전송 된 데이터: 사용자 지정 보고 기능이 사용 되는 지 CNAME로 전달 되는 요청에 대한 HTTP 클라이언트 (예: 웹 브라우저)에 지 서버에서 전송 되는 데이터의 총량을 나타냅니다. 전송된 데이터의 양은 응답 본문에 HTTP 응답 헤더를 추가하여 계산됩니다. 결과적으로 각 자산에 대해 전송된 데이터의 양은 실제 파일 크기보다 큽니다.

- 그룹화: 가로 막대형 차트 아래 표시 되는 통계의 유형을 결정 합니다. 다음과 같은 옵션이 지원됩니다.

   - HTTP 응답 코드: HTTP 응답 코드 별로 통계 (예: 200, 403 등) 클라이언트에 반환 합니다. 

   - 캐시 상태: 캐시 상태별 통계를 구성합니다.


보고서의 날짜 범위를 설정하려면 드롭다운 목록에서 **오늘** 또는 **이번 주**와 같은 사전 정의된 날짜 범위를 선택하거나, **사용자 지정**을 선택하고 달력 아이콘을 클릭하여 날짜 범위를 수동으로 입력합니다. 

날짜 범위를 선택한 후 **이동**을 클릭하여 보고서를 생성합니다.

**이동** 단추 오른쪽의 Excel 기호를 클릭하여 Excel 형식으로 데이터를 내보낼 수 있습니다.

![CNAME 보고서](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>에지 CNAME 사용자 지정 보고서 필드

| 필드                     | Description   |
|---------------------------|---------------|
| 2xx                       | 상태 코드가 2xx HTTP(예: 200 OK)인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. |
| 3xx                       | 상태 코드가 3xx HTTP(예: 302 있음 또는 304 수정되지 않음)인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. |
| 4xx                       | 상태 코드가 4xx HTTP(예: 400 잘못된 요청, 403 사용 권한 없음 또는 404 찾을 수 없음)인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. |
| 5xx                       | 상태 코드가 5xx HTTP(예: 500 내부 서버 오류 또는 502 잘못된 게이트웨이)인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. |
| 캐시 적중률               | 캐시에서 요청자에게 직접 제공된 캐시 가능한 요청의 비율을 나타냅니다. |
| 캐시 적중                | 캐시 적중(예: TCP_EXPIRED_HIT, TCP_HIT 또는 TCP_PARTIAL_HIT)을 초래하는 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. 요청된 콘텐츠의 캐시된 버전이 발견되면 캐시 적중이 발생합니다. |
| 전송되는 데이터(MB)     | 에지 CNAME에 대해 에지 서버에서 HTTP 클라이언트(웹 브라우저)로 전송된 총 데이터(MB)를 나타냅니다. 전송된 데이터의 양은 응답 본문에 HTTP 응답 헤더를 추가하여 계산됩니다. 결과적으로 각 자산에 대해 전송된 데이터의 양은 실제 파일 크기보다 큽니다. |
| Description               | CNAME을 호스트 이름으로 식별합니다. |
| 적중 횟수                      | 에지 CNAME에 대한 총 요청 수를 나타냅니다. |
| 누락                    | 캐시 누락(예: TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS 또는 TCP_MISS)을 초래하는 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. 캐시 누락은 요청된 콘텐츠가 요청을 받은 에지 서버에 캐시되지 않은 경우 발생합니다. | 
| 캐시 없음                  | 캐시 상태 코드가 CONFIG_NOCACHE인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다.  |
| 기타                     | HTTP 상태 코드가 2xx - 5xx 범위를 벗어나는 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다. |
| 플랫폼                  | 에지 CNAME의 트래픽을 처리하는 플랫폼을 나타냅니다. |
| 할당되지 않음               | 캐시 상태 코드 또는 HTTP 상태 코드 정보가 기록되지 않은 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다.  |
| 캐시 불가능               | 캐시 상태 코드가 UNCACHEABLE인 에지 CNAME의 총 요청 수 또는 전송된 데이터(MB)를 나타냅니다.  |


## <a name="considerations"></a>고려 사항
보고서는 최근 18개월 내에서만 생성할 수 있습니다.

