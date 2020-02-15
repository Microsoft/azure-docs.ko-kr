---
title: Azure HPC 캐시 관리 및 업데이트
description: Azure Portal를 사용 하 여 Azure HPC 캐시를 관리 하 고 업데이트 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252045"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Azure Portal에서 캐시 관리

Azure Portal의 캐시 개요 페이지에는 캐시에 대 한 프로젝트 세부 정보, 캐시 상태 및 기본 통계가 표시 됩니다. 캐시를 중지 또는 시작 하 고, 캐시를 삭제 하 고, 장기 저장소로 데이터를 플러시하고, 소프트웨어를 업데이트 하는 컨트롤도 있습니다.

개요 페이지를 열려면 Azure Portal에서 캐시 리소스를 선택 합니다. 예를 들어 **모든 리소스** 페이지를 로드 하 고 캐시 이름을 클릭 합니다.

![Azure HPC 캐시 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-overview.png)

페이지 맨 위에 있는 단추를 통해 캐시를 관리할 수 있습니다.

* **시작** 및 [**중지**](#stop-the-cache) -캐시 작업 일시 중단
* [**플러시**](#flush-cached-data) -변경 된 데이터를 저장소 대상에 기록 합니다.
* [**업그레이드**](#upgrade-cache-software) -캐시 소프트웨어를 업데이트 합니다.
* **새로 고침** -개요 페이지를 다시 로드 합니다.
* [**삭제**](#delete-the-cache) -캐시를 영구적으로 삭제 합니다.

아래에서 이러한 옵션에 대해 자세히 알아보세요.

## <a name="stop-the-cache"></a>캐시 중지

비활성 기간 동안 캐시를 중지 하 여 비용을 줄일 수 있습니다. 캐시를 중지 하는 동안에는 작동 시간에 대 한 요금이 부과 되지 않지만 캐시의 할당 된 디스크 저장소에 대 한 요금이 청구 됩니다. 자세한 내용은 [가격 책정](https://aka.ms/hpc-cache-pricing) 페이지를 참조 하세요.

중지 된 캐시는 클라이언트 요청에 응답 하지 않습니다. 캐시를 중지 하기 전에 클라이언트를 분리 해야 합니다.

**중지** 단추를 클릭 하면 활성 캐시가 일시 중단 됩니다. **중지** 단추는 캐시의 상태가 **정상** 이거나 **저하**된 경우에 사용할 수 있습니다.

![중지를 강조 표시 한 위쪽 단추의 스크린샷 및 중지 작업을 설명 하 고 ' 계속 하 시겠습니까? ' 라는 팝업 메시지 예 (기본값) 및 아니요 단추](media/stop-cache.png)

예를 클릭 하 여 캐시 중지를 확인 한 후 캐시에서 해당 콘텐츠를 저장소 대상에 자동으로 플러시합니다. 이 프로세스는 다소 시간이 걸릴 수 있지만 데이터 일관성을 보장 합니다. 마지막으로 캐시 상태가 **중지 됨**으로 변경 됩니다.

중지 된 캐시를 다시 활성화 하려면 **시작** 단추를 클릭 합니다. 확인은 필요 하지 않습니다.

![시작이 강조 표시 된 위쪽 단추의 스크린샷](media/start-cache.png)

## <a name="flush-cached-data"></a>캐시 된 데이터 플러시

개요 페이지의 **플러시** 단추는 캐시에 저장 된 변경 된 모든 데이터를 백 엔드 저장소 대상에 즉시 쓰도록 캐시에 지시 합니다. 캐시는 데이터를 저장소 대상에 정기적으로 저장 하므로 백 엔드 저장소 시스템이 최신 상태 인지 확인 하지 않는 한이 작업을 수동으로 수행할 필요가 없습니다. 예를 들어 저장소 스냅숏을 만들거나 데이터 집합 크기를 확인 하기 전에 **Flush** 를 사용할 수 있습니다.

> [!NOTE]
> 플러시 프로세스 중에 캐시는 클라이언트 요청을 처리할 수 없습니다. 작업이 완료 된 후에 캐시 액세스가 일시 중단 되 고 다시 시작 됩니다.

![플러시가 강조 표시 된 위쪽 단추의 스크린샷 및 플러시 작업을 설명 하 고 ' 계속 할까요? ' 라는 팝업 메시지가 표시 됩니다. 예 (기본값) 및 아니요 단추](media/hpc-cache-flush.png)

캐시 플러시 작업을 시작 하면 캐시에서 클라이언트 요청 수락이 중지 되 고 개요 페이지의 캐시 상태가 **플러시하**로 변경 됩니다.

캐시의 데이터는 적절 한 저장소 대상에 저장 됩니다. 플러시된 데이터 양에 따라 프로세스는 몇 분 정도 걸릴 수 있습니다.

모든 데이터가 저장소 대상에 저장 되 고 나면 캐시가 자동으로 클라이언트 요청을 다시 가져오기 시작 합니다. 캐시 상태가 **정상**으로 돌아갑니다.

## <a name="upgrade-cache-software"></a>캐시 소프트웨어 업그레이드

새 소프트웨어 버전을 사용할 수 있는 경우 **업그레이드** 단추가 활성화 됩니다. 또한 페이지 맨 위에 소프트웨어 업데이트에 대 한 메시지가 표시 됩니다.

![업그레이드 단추가 활성화 된 단추 위쪽 행의 스크린샷](media/hpc-cache-upgrade-button.png)

클라이언트 액세스는 소프트웨어를 업그레이드 하는 동안 중단 되지 않지만 캐시 성능이 느려집니다. 사용량이 많지 않은 사용 시간 또는 계획 된 유지 관리 기간 동안 소프트웨어를 업그레이드 하도록 계획 합니다.

소프트웨어 업데이트에는 몇 시간이 걸릴 수 있습니다. 높은 처리량으로 구성 된 캐시는 최대 처리량 값이 적은 캐시 보다 업그레이드 하는 데 더 많은 시간이 걸립니다.

소프트웨어 업그레이드를 사용할 수 있는 경우 일주일 또는 수동으로 적용 해야 합니다. 종료 날짜는 업그레이드 메시지에 나열 됩니다. 이 시간 동안 업그레이드 하지 않으면 Azure에서 자동으로 캐시에 업데이트를 적용 합니다. 자동 업그레이드 타이밍은 구성할 수 없습니다. 캐시 성능 영향에 대해 염려 하는 경우 기간이 만료 되기 전에 소프트웨어를 직접 업그레이드 해야 합니다.

종료 날짜가 전달 될 때 캐시가 중지 되 면 다음에 시작 될 때 캐시가 자동으로 소프트웨어를 업그레이드 합니다. 업데이트는 즉시 시작 되지 않을 수도 있지만 처음부터 시작 됩니다.

**업그레이드** 단추를 클릭 하 여 소프트웨어 업데이트를 시작 합니다. 작업이 완료 될 때까지 캐시 상태가 **업그레이드** 중으로 변경 됩니다.

## <a name="delete-the-cache"></a>캐시 삭제

삭제 단추를 클릭 하면 캐시가 **삭제** 됩니다. 캐시를 삭제 하면 모든 리소스가 삭제 되 고 더 이상 계정 요금이 발생 하지 않습니다.

저장소 대상으로 사용 되는 백 엔드 저장소 볼륨은 캐시를 삭제할 때 영향을 받지 않습니다. 나중에 나중에 캐시에 추가 하거나 개별적으로 서비스를 해제할 수 있습니다.

> [!NOTE]
> Azure HPC 캐시는 캐시를 삭제 하기 전에 캐시에서 자동으로 변경 된 데이터를 백 엔드 저장소 시스템에 기록 하지 않습니다.
>
> 캐시의 모든 데이터가 장기 저장소에 기록 되었는지 확인 하려면 삭제 하기 전에 [캐시를 중지](#stop-the-cache) 합니다. 삭제 단추를 클릭 하기 전에 상태가 **중지 됨** 으로 표시 되는지 확인 합니다.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>캐시 메트릭 및 모니터링

개요 페이지에는 몇 가지 기본 캐시 통계, 캐시 처리량, 초당 작업 및 대기 시간에 대 한 그래프가 표시 됩니다.

![샘플 캐시에 대해 위에서 언급 한 통계를 보여 주는 세 개의 선 그래프 스크린샷](media/hpc-cache-overview-stats.png)

이러한 차트는 Azure의 기본 제공 모니터링 및 분석 도구에 포함 됩니다. 추가 도구 및 경고는 포털 사이드바의 **모니터링** 제목 아래에 있는 페이지에서 사용할 수 있습니다. [Azure 모니터링 설명서](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)의 포털 섹션에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

<!-- * Learn more about metrics and statistics for hpc cache -->
* [Azure 메트릭 및 통계 도구](../azure-monitor/index.yml) 에 대해 자세히 알아보기
* [AZURE HPC 캐시로 도움](hpc-cache-support-ticket.md) 받기
