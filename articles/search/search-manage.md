---
title: 포털에서 Azure Search에 대한 서비스 관리 - Azure Search
description: Azure Portal을 사용하여 Microsoft Azure에 호스트된 클라우드 검색 서비스인 Azure Search 서비스를 관리합니다.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565452"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Azure 포털에서 Azure Search에 대한 서비스 관리
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [포털](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search는 사용자 지정 앱에 풍부한 검색 환경을 구축하기 위해 사용되는 완전히 관리되는 클라우드 기반 검색 서비스입니다. 이 문서에서는 이미 프로비전된 검색 서비스를 위한 [Azure Portal](https://portal.azure.com)에서 수행할 수 있는 서비스 관리 작업에 대해 설명합니다. 서비스 관리는 의도적으로 간단하게 작성되었으며 다음 작업으로 제한됩니다.

> [!div class="checklist"]
> * 서비스에 대한 읽기 또는 쓰기 액세스에 사용되는 *api-key*에 대한 액세스를 관리합니다.
> * 파티션 및 복제본의 할당을 변경하여 서비스 용량을 조정합니다.
> * 서비스 계층의 최대 한계를 기준으로 리소스 사용을 모니터링합니다.

*업그레이드*가 관리 작업으로 나열되지 않습니다. 서비스가 프로비저닝될 때 자원이 할당되기 때문에 다른 계층으로 이동하려면 새 서비스가 필요합니다. 자세한 내용은 [Azure Search 서비스 만들기](search-create-service-portal.md)를 참조하세요.

> [!Tip]
> 검색 트래픽 또는 쿼리 성능을 분석하는 방법에 대한 도움말이 필요하십니까? 쿼리 볼륨, 사용자가 검색하는 용어 및 인덱스의 특정 문서로 고객을 안내하는 경우 검색 결과의 성공 정도를 모니터링합니다. 자세한 내용은 [Azure Search에 대한 검색 트래픽 분석](search-traffic-analytics.md), [사용량 및 쿼리 메트릭 모니터링](search-monitor-usage.md) 및 [성능 및 최적화](search-performance-optimization.md)를 참조하세요.

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>관리자 권한
Azure 구독 관리자 또는 공동 관리자는 서비스 자체를 프로비전 또는 프로비전 해제할 수 있습니다.

서비스 내에서 서비스 URL 및 관리자 API 키에 액세스 권한이 있는 사람은 서비스에 대한 읽기/쓰기 권한이 있습니다. 읽기/쓰기 액세스는 [RBAC 정의 역할](search-security-rbac.md)을 통해 구현된 API 키, 인덱스, 인덱서, 데이터 소스, 일정 및 역할 할당을 비롯한 서버 개체를 추가, 삭제 또는 수정하는 권한을 제공합니다.

Azure Search와 상호 작용하는 모든 사용자는 서비스에 대한 읽기-쓰기 권한(관리자 권한) 또는 서비스에 대한 읽기 전용 권한(쿼리 권한) 모드 중 하나에 속합니다. 자세한 내용은 [api-key 관리](search-security-api-keys.md)를 참조하세요.

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>로깅 및 시스템 정보
Azure Search에서는 포털 또는 프로그래밍 방식 인터페이스를 통해 개별 서비스에 대한 로그 파일을 노출하지 않습니다. 기본 계층 이상에서 Microsoft는 Service Level Agreement(서비스 수준 약정)당 99.9% 가용성에 대해 모든 Azure Search 서비스를 모니터링합니다. 서비스 속도가 느리거나 요청 처리량이 SLA 임계값 미만으로 떨어질 경우 지원 팀은 사용할 수 있는 로그 파일을 검토하고 문제를 해결합니다.

서비스에 대한 일반 정보를 기준으로 다음과 같은 방법으로 정보를 얻을 수 있습니다.

* 포털의 서비스 대시보드에서 알림, 속성 및 상태 메시지를 통해 정보를 얻습니다.
* [PowerShell](search-manage-powershell.md) 또는 [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 사용하여 [서비스 속성](https://docs.microsoft.com/rest/api/searchmanagement/services) 또는 인덱스 리소스 사용 현황에 대한 상태를 가져옵니다.
* 이전에 설명한 것처럼 [검색 트래픽 분석](search-traffic-analytics.md)을 통해 정보를 얻습니다.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>리소스 사용 모니터링
대시보드에서는 리소스 모니터링이 서비스 대시보드에 표시되는 정보 및 서비스를 쿼리하여 얻을 수 있는 몇 개의 메트릭으로 제한됩니다. 서비스 대시보드의 사용 섹션에서 파티션 리소스 수준이 애플리케이션에 적합한지 신속하게 확인할 수 있습니다. 캡처 및 기록된 된 이벤트를 유지 하려는 경우 Azure 모니터링 하는 등의 외부 리소스를 제공할 수 있습니다. 자세한 내용은 [Azure Search 모니터링](search-monitor-usage.md)합니다.

Search Service REST API를 사용하여 프로그래밍 방식으로 문서 및 인덱스 수를 가져올 수 있습니다. 

* [인덱스 통계 가져오기](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [문서 수 계산](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>재해 복구 및 서비스 중단

데이터를 복원할 수 있지만 Azure Search는 클러스터 또는 데이터 센터 수준에서 작동이 중단될 경우 서비스의 즉각적인 장애 조치(failover)를 제공하지 않습니다. 데이터 센터에서 클러스터가 실패하면 운영 팀에서는 서비스를 검색하고 복원하기 위해 작업합니다. 서비스를 복원하는 동안 가동 중지 시간이 발생하지만 [SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에 따라 서비스 사용 불가 상황을 보장하기 위해 서비스 크레딧을 요청할 수 있습니다. 

Microsoft의 통제 범위를 벗어나는 치명적인 장애가 발생하더라도 지속적인 서비스가 필요한 경우 다른 하위 지역에서 [추가 서비스를 프로비전](search-create-service-portal.md)하고 지역에서 복제 전략을 구현하여 인덱스가 모든 서비스에서 완전히 중복되도록 해야 합니다.

[인덱서](search-indexer-overview.md)를 사용하여 인덱스를 채우고 새로 고치는 고객은 동일한 데이터 원본을 활용하여 지역별 인덱서를 통해 재해 복구를 처리할 수 있습니다. 인덱서를 실행하는 서로 다른 지역의 두 서비스는 동일한 데이터 소스에서 인덱싱하여 지리적 중복을 적용할 수 있습니다. 지역 중복 데이터 원본에서 인덱싱하는 경우 Azure Search 인덱서는 주 복제본에서만 증분 인덱싱을 수행할 수 있습니다. 장애 조치(failover) 이벤트에서 인덱서를 새로운 주 복제본으로 다시 지정하십시오. 

인덱서를 사용하지 않는 경우, 애플리케이션 코드를 사용하여 개체 및 데이터를 여러 서비스에 동시에 푸시합니다. 자세한 내용은 [Azure Search에서 성능 및 최적화](search-performance-optimization.md)를 참조하세요.

## <a name="backup-and-restore"></a>Backup 및 복원

Azure Search는 기본 데이터 저장소 솔루션이 아니므로 셀프 서비스 백업 및 복원에 대한 공식적인 메커니즘을 제공하지 않습니다. 인덱스를 만들고 채우는 데 사용되는 애플리케이션 코드는 인덱스를 실수로 삭제하는 경우에 사실상 복원 옵션으로 사용됩니다. 

인덱스를 다시 작성하려면 삭제(있다고 가정)하고, 서비스에서 인덱스를 다시 만들고, 기본 데이터 저장소에서 데이터를 검색하여 다시 로드합니다.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>확장 또는 축소
모든 검색 서비스는 최소 복제본 한 개와 파티션 한 개로 시작됩니다. [전용 리소스를 제공하는 계층](search-limits-quotas-capacity.md)에 등록한 경우 서비스 대시보드에서 **확장** 타일을 클릭하여 리소스 사용량을 조정합니다.

어느 하나의 리소스를 통해 용량을 추가하면 서비스에서 자동으로 사용합니다. 추가적인 조치가 필요하지 않지만 새 리소스의 영향이 나타나기 전에 잠시 지연이 있습니다. 추가 리소스를 프로비전하는 데 15분 이상 걸릴 수 있습니다.

 ![][10]

### <a name="add-replicas"></a>복제본 추가
QPS(초당 쿼리 수)를 높이거나 고가용성을 구현하려면 복제본을 추가합니다. 각 복제본에 하나의 인덱스 사본이 있으므로 복제본을 하나 이상 추가하면 서비스 쿼리 요청을 처리하는 데 사용할 수 있는 인덱스도 하나 이상 증가합니다. 고가용성을 위해서는 복제본이 최소 3개 필요합니다(자세한 내용은 [용량 계획](search-capacity-planning.md) 을 참조하세요).

더 많은 복제본을 포함하는 검색 서비스는 쿼리 요청의 부하를 다수의 인덱스에 분산할 수 있습니다. 쿼리 볼륨 수준을 감안할 때 요청 처리에 사용할 수 있는 인덱스 사본 수가 더 많으면 쿼리 처리량이 늘어납니다. 쿼리 대기 시간이 발생하는 경우 추가 복제본이 온라인에 있으면 성능이 향상될 수 있습니다.

복제본을 추가하면 쿼리 처리량이 증가하지만 서비스에 복제본을 추가한 만큼 정확히 두 배나 세 배로 증가하는 것은 아닙니다. 모든 검색 애플리케이션에 적용되는 외부 요인이 쿼리 성능에 영향을 줄 수 있습니다. 복잡한 쿼리와 네트워크 대기 시간은 쿼리 응답 시간 변동에 기여하는 두 가지 요인입니다.

### <a name="add-partitions"></a>파티션 추가
대부분의 서비스 애플리케이션에는 기본적으로 파티션보다 더 많은 수의 복제본이 필요합니다. 문서 수를 늘려야 하는 경우 표준 서비스에 등록했으면 파티션을 추가할 수 있습니다. 기본 계층은 추가 파티션을 제공하지 않습니다.

표준 계층에서 파티션은 12의 배수(특히, 1, 2, 3, 4, 6 또는 12)로 추가됩니다. 이는 분할의 아티팩트입니다. 인덱스는 12개 분할로 만들어지며 모두 하나의 파티션에 저장되거나 2, 3, 4, 6 또는 12개 파티션(파티션당 하나의 분할)에 똑같이 나뉠 수 있습니다.

### <a name="remove-replicas"></a>복제본 제거
쿼리 볼륨이 많은 기간이 지나고 검색 쿼리 부하가 정규화되면(예: 휴일 할인이 종료된 후) 슬라이더를 사용하여 복제본을 줄일 수 있습니다. 필요한 추가 단계는 없습니다. 복제본 수를 줄이면 데이터 센터의 가상 머신이 해제됩니다. 이제 이전보다 적은 VM에서 쿼리 및 데이터 수집 작업이 실행됩니다. 최소 요구 사항은 복제본 하나입니다.

### <a name="remove-partitions"></a>파티션 제거
추가 조치가 필요 없는 복제본 제거와 달리, 줄일 수 있는 것보다 많은 저장소를 사용하는 경우 몇 가지 작업이 필요할 수 있습니다. 예를 들어 솔루션에서 파티션 세 개를 사용하며 한 개 또는 두 개의 파티션으로 다운사이징하는 경우 인덱스를 호스팅하는 데 새 저장소 공간이 필요한 것보다 적으면 오류가 생성됩니다. 예상처럼, 연결된 인덱스 내의 문서 또는 인덱스를 삭제하여 공간을 확보하거나 현재 구성을 유지할 수 있습니다.

특정 파티션에 저장되는 인덱스 분할을 알려주는 검색 방법은 없습니다. 각 파티션에서 제공하는 저장소는 대략 25GB이므로 보유한 개수의 파티션에 수용할 수 있는 크기로 저장소를 줄여야 합니다. 파티션 한 개로 되돌리려면 12개 분할이 모두 들어가야 합니다.

미래 계획에 도움이 되도록 저장소를 검사( [인덱스 통계 가져오기](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)사용)하여 실제로 사용한 크기를 확인하는 것이 좋습니다. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>규모 및 배포에 대한 모범 사례
이 30분 분량의 비디오는 지역으로 분산된 워크로드를 포함한 고급 배포 시나리오에 대한 모범 사례를 검토합니다. 동일한 내용을 다루는 도움말 페이지는 [Azure Search에서 성능 및 최적화](search-performance-optimization.md) 를 참조할 수도 있습니다.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>다음 단계
서비스 관리의 개념을 이해한 후에는 [PowerShell](search-manage-powershell.md)을 사용하여 작업을 자동화하는 것이 좋습니다.

또한 [성능 및 최적화 문서](search-performance-optimization.md)를 검토하는 것이 좋습니다.

이전 섹션에 언급된 비디오를 보는 것도 좋습니다. 이 섹션에 언급된 기술에 대해 보다 심층적인 설명이 제공됩니다.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



