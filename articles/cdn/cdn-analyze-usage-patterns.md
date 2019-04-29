---
title: Verizon의 코어 보고서 | Microsoft Docs
description: 다음과 같은 보고서를 사용 하 여 cdn 사용 패턴을 볼 수 있습니다. 대역폭, 전송 데이터, 적중 횟수, 캐시 상태, 캐시 적중률, 전송 되는 IPV4/i p v 6 데이터입니다.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6eb0fe592196466f7f49c21ce38afdf13b254d86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061540"
---
# <a name="core-reports-from-verizon"></a>Verizon의 코어 보고서

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon 프로필에 대한 관리 포털을 통해 Verizon 코어 보고서를 사용하면 다음과 같은 보고서와 함께 CDN에 대한 사용 패턴을 볼 수 있습니다.

* 대역폭
* 전송되는 데이터
* 적중 횟수
* 캐시 상태
* 캐시 적중률
* 전송되는 IPv4/IPv6 데이터

## <a name="accessing-verizon-core-reports"></a>Verizon 코어 보고서 액세스
1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 관리 단추](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **분석** 탭을 마우스로 가리킨 후 **핵심 보고서** 플라이아웃을 마우스로 가리킵니다. 메뉴에서 보고서를 클릭합니다.
   
    ![CDN 관리 포털 - 핵심 보고서 메뉴](./media/cdn-reports/cdn-core-reports.png)

3. 각 보고서에 대해 **날짜 범위** 목록에서 날짜 범위를 선택합니다. **오늘** 또는 **이번 주**와 같은 사전 정의된 날짜 범위를 선택하거나, **사용자 지정**을 선택하고 달력 아이콘을 클릭하여 날짜 범위를 수동으로 입력할 수 있습니다. 

4. 날짜 범위를 선택한 후 **이동**을 클릭하여 보고서를 생성합니다. 

4. 데이터를 Excel 형식으로 내보내려면 **이동** 단추 위의 Excel 아이콘을 클릭합니다.

## <a name="bandwidth"></a>대역폭
대역폭 보고서는 특정 기간의 HTTP 및 HTTPS에 대한 CDN 대역폭 사용량(Mbps)을 나타내는 그래프와 데이터 테이블로 구성됩니다. 모든 POP 또는 특정 POP의 대역폭 사용량을 볼 수 있습니다. 이 보고서를 통해 POP에 대한 트래픽 급증 및 분산을 GB 단위로 볼 수 있습니다.

**에지 노드** 목록에서 **모든 에지 노드**를 선택하여 모든 노드의 트래픽을 확인하거나 특정 지역을 선택합니다.

보고서는 5분마다 업데이트됩니다.

![대역폭 보고서](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>전송되는 데이터
이 보고서는 특정 기간의 HTTP 및 HTTPS에 대한 CDN 트래픽 사용량(GB)을 나타내는 그래프와 데이터 테이블로 구성됩니다. 모든 POP 또는 특정 POP의 트래픽 사용량을 볼 수 있습니다. 이 보고서를 통해 POP 간에 트래픽 급증 및 분산을 볼 수 있습니다.

**에지 노드** 목록에서 **모든 에지 노드**를 선택하여 모든 노드의 트래픽을 확인하거나 특정 지역을 선택합니다.

보고서는 5분마다 업데이트됩니다.

![전송되는 데이터 보고서](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>적중 횟수(상태 코드)
이 보고서는 콘텐츠에 대한 요청 상태 코드의 분포를 설명합니다. 각 콘텐츠 요청에서 HTTP 상태 코드를 생성합니다. 상태 코드는 에지 POP가 요청을 처리한 방식을 설명합니다. 예를 들어 2xx 상태 코드는 요청이 클라이언트로 성공적으로 처리되었음을 나타내는 반면, 4xx 상태 코드는 오류가 발생했음을 나타냅니다. HTTP 상태 코드에 대한 자세한 내용은 [HTTP 상태 코드 목록](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)을 참조하세요.

![적중 횟수 보고서](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>캐시 상태
이 보고서는 클라이언트 요청에 대한 캐시 적중 횟수 및 캐시 누락 수 분포를 설명합니다. 캐시 적중 횟수에서 가장 빠른 성능 결과가 제공되므로 캐시 누락 수를 최소화하여 데이터 전달 속도를 최적화할 수 있습니다. 

캐시 누락 수를 줄이려면 다음의 사용을 최소화하도록 원본 서버를 구성합니다. 
 * `no-cache` 응답 헤더
 * 쿼리 문자열 캐싱(반드시 필요하지는 않은 경우)  
 * 캐시할 수 없는 응답 코드

만료된 캐시 적중 횟수를 줄이려면 자산의 `max-age`를 좀 더 긴 기간으로 설정하여 원본 서버에 대한 요청 수를 최소화합니다.

![캐시 상태 보고서](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>기본 캐시 상태는 다음과 같습니다.
* TCP_HIT: 에 지 서버에서 처리 됩니다. 개체가 캐시에 있었으며 max-age를 초과하지 않았습니다.
* TCP_MISS: 원본 서버에서 처리 됩니다. 개체가 캐시에 없었으며 응답이 다시 원본으로 전송되었습니다.
* TCP_EXPIRED _MISS: 사용 하 여 유효성을 재검사 한 후 원본 서버에서 처리 합니다. 개체가 캐시에 있었지만 max-age를 초과했습니다. 원본을 사용하여 유효성을 재검사한 후 캐시 개체가 원본의 새 응답으로 대체되었습니다.
* TCP_EXPIRED _HIT: 사용 하 여 유효성을 재검사 한 후에 지에서 처리 합니다. 개체가 캐시에 있었지만 max-age를 초과했습니다. 원본 서버를 사용하여 유효성을 재검사한 후 캐시 개체가 수정되지 않았습니다.

### <a name="full-list-of-cache-statuses"></a>캐시 상태 전체 목록
* TCP_HIT - 요청이 POP에서 클라이언트로 직접 처리된 경우 이 상태가 보고됩니다. 클라이언트에 가장 가까운 POP에 캐시되고 활성 시간이 만료된 유효한 TTL(Time to Live)이 있는 경우 POP에서 즉시 자산이 제공됩니다. TTL은 다음 응답 헤더에 의해 결정됩니다.
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * 만료
* TCP_MISS: 이 상태는 클라이언트에 가장 가까운 POP에서 요청 된 자산의 캐시 된 버전을 찾을 수 없습니다 나타냅니다. 원본 서버 또는 원본 보호 서버의 자산이 요청됩니다. 원본 서버 또는 원본 보호 서버가 자산을 반환하면 클라이언트에 제공되고 클라이언트와 에지 서버 둘 다에 캐시됩니다. 그렇지 않으면 200 이외의 상태 코드(예를 들어 403 사용할 수 없음 또는 404 찾을 수 없음)가 반환됩니다.
* TCP_EXPIRED_HIT: TTL이 만료 된 자산을 대상으로 하는 요청이 클라이언트로 POP에서 직접 처리 된 경우이 상태가 보고 됩니다. 예를 들어 자산의 max-age 기간이 만료되었습니다. 
  
   요청이 만료된 경우 일반적으로 원본 서버에 대한 유효성 재검사 요청이 발생합니다. TCP_EXPIRED_HIT 상태가 발생하려면 원본 서버에서 자산의 최신 버전이 없음을 표시해야 합니다. 이 경우는 일반적으로 자산의 Cache-Control 및 Expires 헤더를 업데이트하게 됩니다.
* TCP_EXPIRED_MISS: 만료 된 캐시 된 자산의 최신 버전은 클라이언트에 POP에서 제공 하는 경우이 상태가 보고 됩니다. 이 상태는 캐시된 자산의 TTL이 만료되었으며(예: 만료된 max-age) 원본 서버가 해당 자산의 최신 버전을 반환하는 경우에 발생합니다. 캐시된 버전 대신 이 새로운 버전의 자산이 클라이언트에 제공됩니다. 또한 에지 서버와 클라이언트에 캐시됩니다.
* CONFIG_NOCACHE: 이 상태 했음을 나타냅니다 고객별 구성은에 지 POP 자산을 캐시할.
* NONE - 이 상태는 캐시 콘텐츠 새로 고침 검사가 수행되지 않았음을 나타냅니다.
* TCP_CLIENT_REFRESH_MISS: 브라우저와 같은 HTTP 클라이언트를에 지 POP가 원본 서버에서 부실 자산의 새 버전을 검색 하려면 강제 하는 경우이 상태가 보고 됩니다. 기본적으로 서버는 HTTP 클라이언트에서 에지 서버가 원본 서버에서 자산의 새 버전을 검색하도록 강제할 수 없게 합니다.
* TCP_PARTIAL_HIT: 바이트 범위 요청을 하면 부분적으로 캐시 된 자산이 적중 하는 경우이 상태가 보고 됩니다. 요청된 바이트 범위는 POP에서 클라이언트에 즉시 제공됩니다.
* UNCACHEABLE: 자산의 경우이 상태가 보고 됩니다 `Cache-Control` 고 `Expires` 헤더는 것은 캐시 되지 POP에서 또는 HTTP 클라이언트에 의해 표시 됩니다. 이러한 유형의 요청은 원본 서버에서 처리됩니다.

## <a name="cache-hit-ratio"></a>캐시 적중률
이 보고서는 캐시에서 직접 처리된 캐시된 요청 비율을 나타냅니다.

보고서는 다음과 같은 세부 정보를 제공합니다.

* 요청한 콘텐츠가 요청자에게 가장 가까운 POP에 캐시되었습니다.
* 네트워크 에지에서 직접 요청이 처리되었습니다.
* 요청에 원본 서버를 사용한 유효성 재검사가 필요하지 않았습니다.

보고서에 포함되지 않는 정보는 다음과 같습니다.

* 국가 필터링 옵션으로 인해 거부된 요청
* 헤더에 캐시하지 않도록 표시된 자산에 대한 요청 예를 들어 `Cache-Control: private`, `Cache-Control: no-cache` 또는 `Pragma: no-cache` 헤더는 자산이 캐시되지 않도록 합니다.
* 부분적으로 캐시된 콘텐츠에 대한 바이트 범위 요청.

수식은 다음과 같습니다. (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![캐시 적중률 보고서](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>전송되는 IPv4/IPv6 데이터
이 보고서는 IPv4 및 IPv6의 트래픽 사용량 분포를 보여 줍니다.

![전송되는 IPv4/IPv6 데이터](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>고려 사항
보고서는 최근 18개월 내에서만 생성할 수 있습니다.

