---
title: 권장 되는 성능 벤치 마크 테스트-Azure NetApp Files
description: Azure NetApp Files 사용 하는 볼륨 성능 및 메트릭에 대한 벤치 마크 테스트 권장 사항에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551525"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 벤치마크 테스트 추천 사항

이 문서에서는 Azure NetApp Files를 사용 하 여 볼륨 성능 및 메트릭에 대한 벤치 마크 테스트 권장 사항을 제공 합니다.

## <a name="overview"></a>개요

Azure NetApp Files 볼륨의 성능 특징을 이해 하려면 오픈 소스 도구 [Fio](https://github.com/axboe/fio) 를 사용 하 여 다양 한 작업을 시뮬레이트하는 일련의 벤치 마크를 실행할 수 있습니다. FIO는 Linux 및 Windows 기반 운영 체제 모두에 설치할 수 있습니다.  이 도구는 볼륨에 대한 IOPS 및 처리량의 신속한 스냅숏을 얻기 위한 훌륭한 도구입니다.

### <a name="vm-instance-sizing"></a>VM 인스턴스 크기 조정

최상의 결과를 위해서는 테스트를 수행 하도록 적절 한 크기를 지정 하는 VM (가상 머신) 인스턴스를 사용 하 고 있는지 확인 합니다. 다음 예에서는 Standard_D32s_v3 인스턴스를 사용 합니다. VM 인스턴스 크기에 대한 자세한 내용은 Windows 기반 Vm에 대한 [azure의 windows 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 Linux 기반 vm에 대한 [azure의 linux 가상 머신에](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 대한 크기를 참조 하세요.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files 볼륨 크기 조정

예상 되는 성능 수준에 맞는 올바른 서비스 수준 및 볼륨 할당량 크기를 선택 했는지 확인 합니다. 자세한 내용은 [Azure NetApp Files의 서비스 수준](azure-netapp-files-service-levels.md) 을 참조 하세요.

### <a name="virtual-network-vnet-recommendations"></a>VNet (가상 네트워크) 권장 사항

Azure NetApp Files와 동일한 VNet에서 벤치 마크 테스트를 수행 해야 합니다. 아래 예제에서는 권장 사항을 보여 줍니다.

![VNet 권장 사항](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 설치

FIO는 Linux 및 Windows 모두에서 이진 형식으로 사용할 수 있습니다. 선택한 플랫폼용으로 설치 하려면 [Fio](https://github.com/axboe/fio) 의 이진 패키지 섹션을 따릅니다.

## <a name="fio-examples-for-iops"></a>IOPS의 FIO 예제 

이 섹션의 FIO 예제는 다음 설정을 사용 합니다.
* VM 인스턴스 크기: D32s_v3
* 용량 풀 서비스 수준 및 크기: 프리미엄/50 TiB
* 볼륨 할당량 크기: 48 TiB

다음 예제에서는 FIO 임의 읽기 및 쓰기를 보여 줍니다.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k 블록 크기 100% 임의 읽기

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>출력: 68k 읽기 IOPS가 표시 됨

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k 블록 크기 100% 임의 쓰기

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>출력: 73k write IOPS 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>대역폭에 대한 FIO 예제

이 단원의 예제에서는 FIO 순차 읽기 및 쓰기를 보여 줍니다.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k 블록 크기 100% 순차 읽기

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>출력: 11.8 1Gb/s 처리량이 표시 됩니다.

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k 블록 크기 100% 순차 쓰기

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>출력: 12.2 1Gb/s 처리량이 표시 됩니다.

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>볼륨 메트릭

Azure NetApp Files 성능 데이터는 Azure Monitor 카운터를 통해 사용할 수 있습니다. 카운터는 Azure Portal 및 REST API GET 요청을 통해 사용할 수 있습니다. 

다음 정보에 대한 기록 데이터를 볼 수 있습니다.
* 평균 읽기 대기 시간 
* 평균 쓰기 대기 시간 
* 읽기 IOPS (평균)
* 쓰기 IOPS (평균)
* 볼륨 논리적 크기 (평균)
* 볼륨 스냅숏 크기 (평균)

### <a name="using-azure-monitor"></a>Azure Monitor 사용 

아래와 같이 메트릭 페이지에서 볼륨 별로 Azure NetApp Files 카운터에 액세스할 수 있습니다.

![Azure Monitor 메트릭](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

메트릭 페이지로 이동 하 고, NetApp에 대해 필터링 하 고, 관심 있는 볼륨 카운터를 지정 하 여 Azure NetApp Files에 대한 Azure Monitor에서 대시보드를 만들 수도 있습니다. 

![Azure Monitor 대시보드](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API 액세스

REST API 호출을 사용 하 여 Azure NetApp Files 카운터에 액세스할 수 있습니다. 용량 풀 및 볼륨에 대한 카운터는 [Azure Monitor에서 지원 되는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) 을 참조 하세요.

다음 예제에서는 논리 볼륨 크기를 보기 위한 GET URL을 보여 줍니다.

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files에 대한 성능 벤치 마크](azure-netapp-files-performance-benchmarks.md)