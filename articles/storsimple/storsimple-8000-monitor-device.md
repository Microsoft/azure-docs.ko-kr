---
title: StorSimple 8000 시리즈 디바이스 모니터링 | Microsoft Docs
description: StorSimple 디바이스 관리자 서비스를 사용하여 사용량, I/O 성능 및 수용작업량 사용률을 모니터링하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634565"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 모니터링합니다.

## <a name="overview"></a>개요
StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 솔루션 내에서 특정 디바이스를 모니터링할 수 있습니다. I/O 성능, 수용작업량 사용률, 네트워크 처리량 및 디바이스 성능 메트릭을 기준으로 사용자 지정 차트를 만들고 해당 항목을 대시보드에 고정할 수 있습니다. 자세한 내용은 [포털 대시보드 사용자 지정](../azure-portal/azure-portal-dashboards.md)으로 이동합니다.

특정 디바이스에 대한 모니터링 정보를 확인하려면, Azure Portal에서 StorSimple 디바이스 관리자 서비스를 선택합니다. 디바이스 목록에서 디바이스를 선택한 다음, **모니터링**으로 이동합니다. 선택한 디바이스에 대한 **수용작업량**, **사용량** 및 **성능** 차트를 확인할 수 있습니다.

## <a name="capacity"></a>용량
**수용작업량**은 프로비전된 공간 및 디바이스에 남은 공간을 추적합니다. 나머지 수용작업량은 로컬로 고정되었거나 계층화 것으로 표시됩니다.

프로비전된 수용작업량 및 나머지 수용작업량은 계층화된 볼륨 및 로컬 고정 볼륨으로 분류됩니다. 각 볼륨의 경우 프로비전된 용량 및 디바이스의 나머지 용량이 표시됩니다.

![IO 수용작업량](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>사용 현황
**사용량**은 볼륨, 볼륨 컨테이너 또는 디바이스에서 사용되는 데이터 저장소 공간의 양과 관련된 메트릭을 추적합니다. 기본 저장소, 클라우드 저장소 또는 디바이스 저장소 용량 사용률을 기반으로 보고서를 만들 수 있습니다. 용량 사용률은 특정 볼륨, 특정 볼륨 컨테이너 또는 모든 볼륨 컨테이너에 대해 측정될 수 있습니다.
기본적으로 지난 24시간 동안 사용량이 보고됩니다. 다음을 선택하여 보고되는 사용량에 대한 기간을 변경하기 위해 차트를 편집할 수 있습니다.
* 지난 24시간
* 지난 7일간
* 지난 30일간
* 지난 90일간
* 지난해

두 가지 주요 메트릭인 증가 및 범위는 사용량 차트에서 보고됩니다. 범위는 선택한 기간(예: 최근 7일) 동안 보고된 사용량의 최대값 및 최소값을 의미합니다.

증가는 선택한 기간 동안 첫 번째 날부터 마지막 날까지 사용량의 증가를 의미합니다. 

다음과 같은 수식으로 증가 및 범위를 나타낼 수도 있습니다.

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

사용된 기본, 클라우드 및 로컬 저장소는 다음과 같이 설명할 수 있습니다.

### <a name="primary-storage-usage"></a>기본 저장소 사용량
이 차트는 데이터가 중복 제거 및 압축되기 전에 StorSimple 볼륨에 기록된 데이터의 양을 나타냅니다. 볼륨 컨테이너의 모든 볼륨 또는 단일 볼륨에 사용된 기본 저장소를 볼 수 있습니다. 사용된 Tiered Storage는 사용된 기본 계층화 스토리지 및 기본 로컬 고정 스토리지로 분류됩니다.

다음 차트에서는 클라우드 스냅숏을 생성하기 전과 생성한 후의 StorSimple 디바이스에 사용된 기본 저장소를 보여줍니다. 볼륨 데이터만 표시되기 때문에 클라우드 스냅숏으로 인해 기본 저장소가 변경되어서는 안 됩니다. 볼 수 있듯이 차트에서는 클라우드 스냅숏을 생성한 결과로 사용된 기본 계층화 저장소 또는 로컬 고정 저장소에 아무런 차이가 표시되지 않습니다. 해당 디바이스에서 클라우드 스냅숏은 오후 11시 50분쯤에 시작되었습니다.

![클라우드 스냅숏 후 기본 용량 사용률](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

이제 StorSimple 장치에 연결된 호스트에서 IO를 실행하는 경우 데이터를 작성한 볼륨(계층화된 볼륨 또는 로컬 고정 볼륨)에 따라 사용된 기본 계층화 스토리지 또는 로컬 고정 Tiered Storage의 증가를 확인할 수 있습니다. StorSimple 디바이스의 기본 저장소 사용량 차트는 다음과 같습니다. 이 디바이스에서 StorSimple 호스트는 디바이스의 계층화된 볼륨에서 오후 2시 30분쯤에 쓰기를 제공하기 시작했습니다. 호스트에서 실행 중인 IO에 해당하는 쓰기 바이트에서 최대치를 확인할 수 있습니다.

![계층화된 볼륨에서 IO를 실행 중인 경우 성능](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

사용된 기본 계층화 스토리지를 보면 장치에서 로컬 고정 볼륨에 쓰기가 제공되지 않을 경우 기본 로컬 고정 사용량은 변하지 않는 반면 사용된 기본 계층화 스토리지 사용량은 증가합니다.

![계층화된 볼륨에서 IO를 실행 중인 경우 기본 수용작업량 사용률](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

업데이트 3 이상을 실행하는 경우 아래 나와 있는 것처럼 기본 저장소 수용작업량 사용률을 개별 볼륨, 모든 볼륨, 모든 계층화된 볼륨 및 모든 로컬 고정 볼륨으로 구분할 수 있습니다. 모든 로컬 고정 볼륨별로 분석하면 로컬 계층이 얼마나 사용되는지 빠르게 확인할 수 있습니다.

![모든 계층화된 볼륨에 대한 기본 수용작업량 사용률](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![모든 로컬 고정 볼륨에 대한 기본 용량 사용률](./media/storsimple-8000-monitor-device/monitor-usage4.png)

목록에서 각각의 볼륨을 클릭하고 해당 사용량을 확인할 수 있습니다.

![모든 로컬 고정 볼륨에 대한 기본 용량 사용률](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>클라우드 저장소 사용량
이 차트는 사용한 클라우드 저장소의 크기를 표시합니다. 이 데이터는 중복 제거되고 압축됩니다. 이 양에는 모든 기본 볼륨에 반영되지 않으며 레거시 또는 필수 보존 목적을 위해 유지되는 데이터를 포함할 수 있는 클라우드 스냅숏이 포함되어 있습니다. 기본 및 클라우드 저장소 소비 수치를 비교하여 정확한 숫자는 아니지만 데이터 축소 비율을 파악할 수 있습니다.

다음 차트에서는 클라우드 스냅숏을 생성하는 경우 StorSimple 디바이스 클라우드 저장소 사용률을 보여줍니다.

* 클라우드 스냅숏이 오전 11시 50분쯤에 해당 디바이스에서 시작하면 클라우드 스냅숏 전에 클라우드 저장소가 사용되지 않았다는 것을 확인할 수 있습니다. 
* 클라우드 스냅숏을 완료하면 클라우드 저장소 사용률은 0.89GB에 도달합니다. 
* 클라우드 스냅숏이 진행 중인 동안 디바이스에서 클라우드로 IO에 해당하는 최대치가 발생합니다.

    ![클라우드 스냅숏 이전 클라우드 저장소 사용률](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![클라우드 스냅숏 이후 클라우드 저장소 사용률](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![클라우드 스냅숏 중에 디바이스에서 클라우드로의 IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>로컬 저장소 사용량
SSD 선형 계층을 포함하므로 기본 저장소 사용량 이상인 디바이스에 대한 총 사용률을 보여 줍니다. 이 계층에는 디바이스의 다른 계층에 있는 데이터의 양이 포함되어 있습니다. SSD 선형 계층의 용량은 순환되어 새로운 데이터가 들어오면 이전 데이터가 HDD 계층(이때 중복 제거되고 압축됨) 및 이후에 클라우드로 이동됩니다.

시간이 지남에 따라 사용된 기본 저장소 및 로컬 저장소는 데이터가 클라우드로 티어링되기 시작할 때까지 함께 증가할 가능성이 높습니다. 해당 시점에 사용된 로컬 저장소가 안정 상태를 유지하기 시작하지만 사용된 기본 저장소는 더 많은 데이터가 기록될수록 증가합니다.

다음 차트에서는 클라우드 스냅숏을 생성하는 경우 StorSimple 디바이스에 사용된 기본 저장소를 보여줍니다. 클라우드 스냅숏은 오전 11시 50분에 시작했고, 해당 시점에 로컬 저장소가 줄기 시작했습니다. 사용되는 로컬 저장소는 1.445GB에서 1.09GB로 감소되었습니다. 이는 선영 SSD 계층의 압축되지 않은 데이터가 중복 제거, 압축되어 HDD 계층으로 이동한 것을 나타낼 가능성이 큽니다. 디바이스의 SSD와 HDD 계층에 이미 많은 양의 데이터가 있으면 이러한 감소가 보이지 않을 수도 있습니다. 이 예에서는 디바이스에는 적은 양의 데이터가 있습니다.

![클라우드 스냅숏 이후 로컬 저장소 사용률](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>성능
**성능**은 호스트 서버의 iSCSI 초기자 인터페이스와 디바이스 또는 디바이스와 클라우드 사이의 읽기 및 쓰기 작업의 수와 관련된 메트릭을 추적합니다. 이 성능은 특정 볼륨, 특정 볼륨 컨테이너 또는 모든 볼륨 컨테이너에 대해 측정될 수 있습니다. 성능은 디바이스의 다양한 네트워크 인터페이스에 대한 CPU 사용률 및 네트워크 처리량도 포함합니다.

### <a name="io-performance-for-initiator-to-device"></a>초기자에서 디바이스로의 I/O 성능
아래 차트에서는 프로덕션 디바이스의 모든 볼륨에 대한 디바이스 개시자의 I/O를 보여 줍니다. 그려진 메트릭은 초당 바이트를 읽고 작성합니다. 또한 읽기, 쓰기 및 미해결 IO 또는 읽기 및 쓰기 대기 시간을 차트로 만들 수 있습니다.

![초기자에서 디바이스로의 IO 성능](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>디바이스에서 클라우드로의 I/O 성능
동일한 디바이스에 대한 I/O 작업은 디바이스에서 모든 볼륨 컨테이너에 대한 클라우드로의 데이터에 대해 그려집니다. 이 디바이스에서 데이터는 선형 계층에만 있으며 클라우드로는 아무것도 넘어가지 않습니다. 디바이스에서 클라우드로 가는 읽기/쓰기 작업은 없습니다. 따라서 디바이스와 서비스 사이에서 하트비트를 검사하는 빈도인 5분 간격으로 차트에 최대치가 표시됩니다.

동일한 디바이스에 대해 오전 11시 50분부터 볼륨 데이터에 대한 클라우드 스냅숏이 생성됩니다. 그 결과 디바이스에서 클라우드로 데이터 흐름이 발생합니다. 읽기는 이 기간 동안 클라우드에 제공됩니다. IO 차트에는 스냅숏이 생성된 시간에 해당하는 쓰기 바이트의 최대값이 표시됩니다.

![클라우드 스냅숏 중에 디바이스에서 클라우드로의 IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>디바이스 네트워크 인터페이스의 네트워크 처리량
**네트워크 처리량** 은 호스트 서버 및 디바이스의 iSCSI 초기자 네트워크 인터페이스와 디바이스와 클라우드 사이에서 전송되는 데이터의 양과 관련된 메트릭을 추적합니다. 디바이스의 각 iSCSI 네트워크 인터페이스에 대한 해당 메트릭을 모니터링할 수 있습니다.

다음 차트는 디바이스에서 Data 0, 1 1GbE 네트워크의 처리량을 보여줍니다. 여기서는 클라우드(기본값) 및 iSCSI를 모두 사용합니다. 6월 14일 오후 9시쯤에 이 디바이스에서 데이터를 클라우드로 계층화하고 클라우드에 제공되는 IO에서 발생합니다. (해당 시점에 클라우드 스냅숏을 사용하지 않았습니다. 즉, 계층화가 클라우드로 데이터를 이동하는 메커니즘을 가리키지 않았습니다.) 동시에 네트워크 처리량 그래프에서 해당 최대치가 발생하고 대부분의 네트워크 트래픽이 클라우드로 아웃바운드됩니다.

![Data 0에 대한 네트워크 처리량](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

iSCSI만을 사용하는 다른 1개의 GbE 네트워크 인터페이스인 Data 1: 인터페이스 처리량 차트를 보면 이 기간 동안 네트워크 트래픽이 거의 없었습니다.

![Data 1에 대한 네트워크 처리량](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>디바이스의 CPU 사용률
**CPU 사용률**은 디바이스에서 사용된 CPU에 관련된 메트릭을 추적합니다. 다음 차트에서는 프로덕션 환경에서 디바이스의 CPU 사용률 통계를 보여 줍니다.

![디바이스의 CPU 사용률](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>다음 단계
* [StorSimple 디바이스 관리자 서비스 디바이스 대시보드를 사용](storsimple-device-dashboard.md)하는 방법을 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.

