---
title: SAP HANA Azure 가상 머신 스토리지 구성 | Microsoft Docs
description: SAP HANA가 배포된 VM에 대한 스토리지 권장 사항입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa6aba12af08e2b5e044eaeb299ec6090ab6d750
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650471"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 가상 머신 스토리지 구성

Azure에서는 SAP HANA를 실행하는 Azure VM에 적합한 다양한 스토리지 유형을 제공합니다. SAP HANA 배포에 고려할 수 있는 **SAP HANA 인증 Azure 스토리지 유형**은 다음과 같습니다. 

- Azure premium SSD 또는 premium storage 
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

이러한 디스크 유형에 대 한 자세한 내용은 [SAP 워크 로드에 대 한 유형 Azure Storage](./planning-guide-storage.md) 및 [디스크 유형 선택](../../disks-types.md) 문서를 참조 하세요.

Azure는 Azure Standard 및 premium storage에서 Vhd에 대 한 두 가지 배포 방법을 제공 합니다. Azure 블록 저장소 배포에 [azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/) 를 활용 하는 것이 좋습니다. 

IOPS 및 스토리지 처리량에서 스토리지 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

> [!IMPORTANT]
> 선택한 Azure 스토리지 유형과는 독립적으로 해당 스토리지에 사용되는 파일 시스템을 SAP에서 특정 운영 체제 및 DBMS에 대해 지원해야 합니다. [SAP Support Note #405827](https://launchpad.support.sap.com/#/notes/405827)에는 SAP HANA를 포함하여 다양한 운영 체제 및 데이터베이스에 대해 지원되는 파일 시스템이 나와 있습니다. 이는 SAP HANA가 어떤 작업이든 읽기 및 쓰기를 위해 액세스할 수 모든 볼륨에 적용됩니다. 특히 SAP HANA용 Azure에서 NFS를 사용하는 경우 이 문서의 뒷부분에서 설명한 대로 NFS 버전의 추가 제한 사항이 적용됩니다. 


다양한 스토리지 유형에 대한 최소 SAP HANA 인증 조건은 다음과 같습니다. 

- Azure [쓰기 가속기](../../how-to-enable-write-accelerator.md)에서 지원 되려면 azure premium storage- **/hana/log** 가 필요 합니다. **/Hana/data** 볼륨은 Azure 쓰기 가속기 또는 Ultra disk 없이 premium storage에 배치 될 수 있습니다.
- 최소 **/hana/log** 볼륨에 대 한 Azure Ultra disk. **/Hana/data** 볼륨은 Azure 쓰기 가속기 없는 premium storage에 배치 하거나 Ultra disk를 더 빠르게 다시 시작 하는 데 사용할 수 있습니다.
- **/Hana/log 및/hana/data**에 대 한 Azure NetApp Files 위에 있는 **NFS v 4.1** 볼륨 /Hana/shared 볼륨은 NFS v3 또는 NFS v 4.1 프로토콜을 사용할 수 있습니다.

일부 스토리지 유형은 결합할 수 있습니다. 예를 들어 **/hana/data** 를 premium storage에 배치 하 고 필요한 짧은 대기 시간을 얻기 위해 **/hana/log** 를 울트라 디스크 저장소에 배치할 수 있습니다. **/Hana/data**에 대해 anf를 기반으로 하는 볼륨을 사용 하는 경우 **/hana/log** 볼륨은 anf의 맨 위에 있는 NFS를 기반으로 해야 합니다. 볼륨 중 하나 (예:/hana/data) 및 Azure premium storage 또는 다른 볼륨에 대 한 Ultra disk (예: **/hana/log**)에 대해 ANF 위에 NFS를 사용 하는 것은 **지원 되지 않습니다**.

온-프레미스 환경의 경우 I/O 하위 시스템 및 해당 성능은 신경 쓸 필요가 거의 없었습니다. SAP HANA에 대한 최소 스토리지 요구 사항을 충족하는 것은 어플라이언스 공급업체의 몫이었기 때문입니다. 자체적으로 Azure 인프라를 빌드하는 동안에는 그러한 SAP 발행 요구 사항 몇 가지에 대해 알아야 합니다. SAP에서 권장 하는 최소 처리량 특성 중 일부는 다음과 같습니다.

- 1mb i/o 크기를 사용 하는 250 m b/초의 **/hana/log** 읽기/쓰기
- 16mb 및 64 MB i/o 크기에 대 한 **/hana/data** 에 대 한 최소 400 m b/초의 읽기 작업
- 16mb 및 64 MB i/o 크기를 사용 하는 **/hana/data** 에 대해 최소 250 m b/초의 쓰기 활동

낮은 스토리지 대기 시간이 DBMS 시스템에 중요하므로 SAP HANA와 같이 DBMS의 경우에도 데이터를 메모리 내에 유지합니다. 스토리지에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 **/hana/data** 및 **/hana/log** 볼륨에 대해 Azure premium storage, ULTRA disk 또는 anf를 활용 하는 것이 **필수** 입니다. 


HANA에 대 한 저장소 구성을 선택 하는 몇 가지 지침 원칙을 다음과 같이 나열할 수 있습니다.

- [SAP 워크 로드에 대 한 Azure Storage 유형을](./planning-guide-storage.md) 기반으로 저장소 유형을 결정 하 고 [디스크 유형을 선택](../../disks-types.md) 합니다.
- VM의 크기를 조정 하거나 결정할 때 전체 VM i/o 처리량 및 IOPS 제한에 유의 합니다. 전체 VM 저장소 처리량은 [메모리 최적화 가상 머신 크기](../../sizes-memory.md) 문서에 설명 되어 있습니다.
- 저장소 구성을 결정할 때 **/hana/data** 볼륨 구성을 사용 하 여 VM의 전체 처리량을 미만으로 유지 해 보세요. 저장점을 작성 하는 SAP HANA는 i/o가 적극적으로 실행 될 수 있습니다. 저장 점을 쓸 때 **/hana/data** 볼륨의 최대 처리량 제한을 푸시할 수 있습니다. **/Hana/data** 볼륨을 작성 하는 디스크의 처리량이 VM에서 허용 하는 것 보다 높은 경우 저장점 쓰기에서 사용 된 처리량이 다시 실행 로그 쓰기의 처리량 요구를 방해 하는 상황이 발생할 수 있습니다. 응용 프로그램 처리량에 영향을 줄 수 있는 상황
- Azure premium storage를 사용 하는 경우 가장 저렴 한 구성은 논리 볼륨 관리자를 사용 하 여 **/hana/data** 및 **/hana/log** 볼륨을 빌드하는 스트라이프 집합을 빌드하는 것입니다.

> [!IMPORTANT]
> 저장소 구성에 대 한 제안 사항은부터 시작 하기 위한 지침을 의미 합니다. 워크 로드를 실행 하 고 저장소 사용률 패턴을 분석 하면 제공 된 모든 저장소 대역폭 또는 IOPS를 활용 하지 않는 것을 알 수 있습니다. 저장소에 대해 다운 크기 조정을 고려할 수 있습니다. 반대로, 워크 로드에는 이러한 구성으로 제안 된 것 보다 더 많은 저장소 처리량이 필요할 수 있습니다. 따라서 용량, IOPS 또는 처리량을 더 많이 배포 해야 할 수 있습니다. 필요한 저장소 용량, 저장소 대기 시간, 저장소 처리량 및 IOPS가 필요 하 고 비용이 많이 드는 구성의 필드에서 Azure는 다양 한 기능을 갖춘 다양 한 저장소 유형과 사용자의 HANA 워크 로드에 대 한 올바른 손상에 대 한 적절 한 손상에 대 한 다양 한 가격 점수를 제공 합니다.

## <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP를 통한 일반적인 권장 사항은 디스크 볼륨에 대한 I/O 스케줄러 모드를 **mq-deadline** 또는 **kyber** 모드에서 **noop**(non-multiqueue) 또는 **없음**(multiqueue) 모드로 다시 구성하는 것입니다. 자세한 내용은 [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)에서 참조했습니다. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 가상 머신에 대 한 premium storage 및 Azure 쓰기 가속기 솔루션
Azure 쓰기 가속기는 Azure M 시리즈 VM 전용 기능입니다. 이름에 따라 기능의 목적은 Azure premium storage에 대 한 쓰기의 i/o 대기 시간을 개선 하는 것입니다. SAP HANA의 경우 Write Accelerator는 **/hana/log** 볼륨에 대해서만 사용해야 합니다. 따라서 **/hana/data**와 **/hana/log**는 별개의 볼륨입니다(Azure 쓰기 가속기가 **/hana/log** 볼륨만 지원하기 때문). 

> [!IMPORTANT]
> Azure premium storage를 사용 하는 경우 **/hana/log** 볼륨에 대 한 azure [쓰기 가속기](../../how-to-enable-write-accelerator.md) 를 사용 하는 것은 필수입니다. 쓰기 가속기는 premium storage 및 M 시리즈 및 Mv2 시리즈 Vm에만 사용할 수 있습니다. 쓰기 가속기 Esv3 또는 Edsv4와 같은 다른 Azure VM 제품군과 함께 작동 하지 않습니다.

아래의 Azure premium 디스크에 대 한 캐싱 권장 사항은 다음과 같이 SAP HANA에 대 한 i/o 특성을 가정 합니다.

- HANA 데이터 파일에 대한 읽기 워크로드가 거의 없습니다. 예외는 HANA 인스턴스를 다시 시작한 후 또는 HANA에 데이터를 로드할 때 발생하는 대규모 I/O입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 대부분의 경우 모든 데이터 파일 볼륨을 완전히 읽어야 하므로 결과적으로 읽기 캐싱은 의미가 없습니다. 
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 저장점 쓰기는 비동기이며 사용자 트랜잭션을 유지하지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 그러나 크래시 복구는 예외적인 상황이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구를 수행하는 경우 또는 HANA 인스턴스를 다시 시작하는 단계의 대규모 I/O는 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대한 기본 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O가 있거나, 다른 경우에 1MB 이상의 I/O 크기가 있을 수 있습니다. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에서 유지되어야 합니다.

**권장 사항: 이러한 관찰 된 i/o SAP HANA 패턴의 결과로 Azure premium storage를 사용 하는 다른 볼륨에 대 한 캐싱은 다음과 같이 설정 해야 합니다.**

- **/hana/data** -캐싱 또는 읽기 캐싱 없음
- **/hana/log** - 캐싱 없음 - 읽기 캐싱 없이 쓰기 가속기를 사용하도록 설정해야 하는 M/Mv2 시리즈는 예외입니다. 
- **/hana/shared** - 읽기 캐싱
- **OS 디스크** -VM을 만들 때 Azure에서 설정 하는 기본 캐싱을 변경 하지 않습니다.


LVM 또는 mdadm을 사용 하 여 여러 Azure premium 디스크에 스트라이프 세트를 빌드하려면 스트라이프 크기를 정의 해야 합니다. 이러한 크기는 **/hana/data** 과 **/hana/log**사이에 차이가 있습니다. **권장 사항: 스트라이프 크기를 사용 하는 것이 좋습니다.**

- **/hana/data**에 대해 256KB
- **/hana/log** 의 경우 64 KB

> [!NOTE]
> **/Hana/data** 에 대 한 스트라이프 크기가 최신 Linux 버전을 사용 하는 고객 환경에 따라 64 kb 또는 128 kb ~ 256 KB를 호출 하는 이전 권장 사항에서 변경 되었습니다. 256 KB 크기는 약간 더 나은 성능을 제공 합니다. 또한 더 큰 i/o 크기를 사용 하 여 충분 한 처리량을 얻기 위해 **/hana/log** 의 스트라이프 크기에 대 한 권장 사항을 32 kb에서 64 kb로 변경 했습니다.

> [!NOTE]
> Azure 블록 저장소는 VHD의 3 개 이미지를 유지 하므로 RAID 볼륨을 사용 하 여 중복성 수준을 구성할 필요가 없습니다. Azure premium 디스크에 스트라이프 세트를 사용 하는 것은 전적으로 충분 한 IOPS 및/또는 i/o 처리량을 제공 하는 볼륨을 구성 하는 것입니다.

스트라이프 세트 아래에 많은 Azure Vhd를 누적 하는 것은 IOPS 및 저장소 처리량 측면에서 누적 됩니다. 따라서 3 x P30 Azure premium storage 디스크에 스트라이프 세트를 배치 하는 경우 IOPS의 3 배 및 단일 Azure premium Storage P30 디스크의 저장소 처리량 3 배를 제공 해야 합니다.

> [!IMPORTANT]
> 여러 Azure premium 디스크에 스트라이프 세트를 만들기 위해 LVM 또는 mdadm을 볼륨 관리자로 사용 하는 경우 3 개의 SAP HANA 파일 시스템/data,/log 및/shared를 기본 또는 루트 볼륨 그룹에 배치 해서는 안 됩니다. 일반적으로 /data, /log 및 /shared에 개별 볼륨 그룹을 만드는 Linux 공급 업체 지침을 따르는 것이 좋습니다.


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium storage에 대 한 Azure 버스트 기능
Azure premium storage 디스크의 용량을 512 GiB 하는 경우 버스트 기능이 제공 됩니다. 디스크 버스트 작동 방식에 대 한 정확한 방법은 [디스크 버스트](../../linux/disk-bursting.md)문서에 설명 되어 있습니다. 이 문서를 읽으면 i/o 워크 로드가 디스크의 공칭 IOPS 및 처리량 보다 낮은 시간에 발생 IOPS 및 처리량의 개념을 이해 하 게 됩니다 (명목상 처리량에 대 한 자세한 내용은 [관리 되는 디스크 가격](https://azure.microsoft.com/pricing/details/managed-disks/)참조). 현재 사용량과 디스크의 명목상 값 사이에 IOPS 및 처리량의 변화량을 계산 하려고 합니다. 버스트는 최대 30 분으로 제한 됩니다.

이 버스트 기능이 계획 될 수 있는 이상적인 사례는 다른 DBMS에 대 한 데이터 파일을 포함 하는 볼륨이 나 디스크가 될 가능성이 높습니다. 이러한 볼륨에 대해 예상 되는 i/o 워크 로드, 특히 중소 규모의 시스템은 다음과 같습니다.

- 데이터를 메모리에 캐시 하는 것이 가장 좋지만 HANA의 경우와 마찬가지로 메모리에 완전히 있어야 하기 때문에 낮은 수준의 읽기 작업
- 정기적으로 실행 되는 데이터베이스 검사점 또는 저장점에 의해 트리거되는 쓰기 버스트
- 저장소 스냅숏을 통해 백업이 실행 되지 않는 경우 연속 스트림에서 읽는 백업 워크 로드
- SAP HANA 인스턴스를 다시 시작한 후에 메모리에 데이터를 로드 합니다.

특히 워크 로드에서 초당 몇 백 개의 트랜잭션을 처리 하는 소규모 DBMS 시스템에서 트랜잭션 또는 다시 실행 로그를 저장 하는 디스크 또는 볼륨에 대해 버스트 기능을 사용할 수 있습니다. 이러한 디스크 또는 볼륨에 대해 예상 되는 작업은 다음과 같습니다.

- 응용 프로그램에서 발급 한 모든 커밋이 i/o 작업을 트리거할 가능성이 있으므로 작업 및 워크 로드의 특성에 따라 달라 지는 디스크에 대 한 일반 쓰기
- 인덱스를 만들거나 다시 작성 하는 것과 같은 운영 작업의 사례에 대 한 처리량의 높은 작업
- 트랜잭션 로그 또는 다시 실행 로그 백업을 수행할 때의 읽기 버스트


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Azure premium storage를 기반으로 하는 프로덕션 권장 저장소 솔루션

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> Azure premium storage를 포함 하는 시나리오에서는 구성에 버스트 기능을 구현 합니다. 어떤 모양이 나 폼의 저장소 테스트 도구를 사용 하는 경우 [Azure premium disk 버스트의 작동](../../linux/disk-bursting.md) 방식을 염두에 두십시오. SAP HWCCT 또는 HCMT 도구를 통해 전달 된 저장소 테스트를 실행 하는 경우 일부 테스트는 축적 된 버스트 크레딧을 초과 하므로 모든 테스트에서 조건을 전달 하는 것은 아닙니다. 특히 모든 테스트가 중단 없이 순차적으로 실행 되는 경우입니다.


> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

**권장 사항: 프로덕션 시나리오에 대해 Azure premium storage에 권장 되는 구성은 다음과 같습니다.**

SAP **/hana/data** 볼륨에 대 한 구성:

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192GiB | 500MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M32ls | 256GiB | 500MBps | 4 x P6 | 680 MBps | 960 | 14,000 |
| M64ls | 512GiB | 1,000MBps | 4 x P10 |  680 MBps | 2,000 | 14,000 |
| M64s | 1,000GiB | 1,000MBps | 4 x P15 | 680 MBps | 4400 | 14,000 |
| M64ms | 1,750GiB | 1,000MBps | 4 x P20 | 680 MBps | 9200 | 14,000 |  
| M128s | 2,000GiB | 2000 MBps | 4 x P20 | 680 MBps | 9200| 14,000 | 
| M128ms | 3,800GiB | 2000 MBps | 4 x P30 | 800 MBps (프로 비전 됨) | 20,000 | 버스트 없음 | 
| M208s_v2 | 2,850GiB | 1,000MBps | 4 x P30 | 800 MBps (프로 비전 됨) | 20,000| 버스트 없음 | 
| M208ms_v2 | 5,700GiB | 1,000MBps | 4 x P40 | 1000 MBps (프로 비전 됨) | 25,000 | 버스트 없음 |
| M416s_v2 | 5,700GiB | 2000 MBps | 4 x P40 | 1000 MBps (프로 비전 됨) | 25,000 | 버스트 없음 |
| M416ms_v2 | 11,400GiB | 2000 MBps | 4 x P50 | 2000 MBps (프로 비전 됨) | 25,000 | 버스트 없음 |


**/Hana/log** 볼륨의 경우. 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | **/hana/log** 볼륨 | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192GiB | 500MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M32ls | 256GiB | 500MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64ls | 512GiB | 1,000MBps | 3 x P10 | 510 MBps | 1,500 | 10500 | 
| M64s | 1,000GiB | 1,000MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M64ms | 1,750GiB | 1,000MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M128s | 2,000GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500|  
| M128ms | 3,800GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 
| M208s_v2 | 2,850GiB | 1,000MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M208ms_v2 | 5,700GiB | 1,000MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416s_v2 | 5,700GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 |  
| M416ms_v2 | 11,400GiB | 2000 MBps | 3 x P15 | 510 MBps | 3300 | 10500 | 


다른 볼륨에 대 한 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32ls | 256GiB | 500MBps |  1 x P20 | 1 x P6 | 1 x P6 |
| M64ls | 512GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1,000GiB | 1,000MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1,750GiB | 1,000MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2,000GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3,800GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850GiB | 1,000MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700GiB | 1,000MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700GiB | 2000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


제안된 다른 볼륨에 대한 스토리지 처리량이 실행하려는 워크로드를 충족하는지 여부를 확인합니다. 워크 로드에 **/hana/data** 및 **/hana/log**에 대 한 더 높은 볼륨이 필요한 경우 Azure premium storage vhd의 수를 늘려야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 따라서 최소한 **/hana/log** 볼륨을 형성 하는 Azure premium storage 디스크를 관리 디스크로 배포 해야 합니다. Azure 쓰기 가속기에 대 한 자세한 지침 및 제한은 [쓰기 가속기](../../how-to-enable-write-accelerator.md)문서에서 찾을 수 있습니다.

Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) 제품군 및 [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)의 HANA 인증 vm에 대해 **/hana/data** 및 **/hana/log** 볼륨에 대해 anf를 수행 해야 합니다. 또는 **/hana/log** 볼륨에 대해서만 azure premium Storage 대신 azure Ultra disk storage를 활용 해야 합니다. 결과적으로, Azure premium storage의 **/hana/data** 볼륨에 대 한 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 510 MBps | 1,500 | 10500 |
| E32ds_v4 | 256GiB | 768MBps | 3 x P10 |  510 MBps | 1,500 | 10500|
| E48ds_v4 | 384 GiB | 1152 MBps | 3 x P15 |  510 MBps | 3300  | 10500 | 
| E64ds_v4 | 504 GiB | 1,200MBps | 3 x P15 |  510 MBps | 3300 | 10500 | 
| E64s_v3 | 432GiB | 1,200MB/s | 3 x P15 |  510 MBps | 3300 | 10500 | 

울트라 디스크의 **/hana/log** 를 비롯 한 다른 볼륨의 경우 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/log 볼륨 | /hana/log I/O 처리량 | /hana/log IOPS | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80GB | 250MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256GiB | 768MBps | 128GB | 250MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1152 MBps | 192 GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200MBps | 256GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432GiB | 1,200MBps | 220 GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA용 Azure Ultra Disk 스토리지 구성
다른 Azure storage 유형을 [Azure Ultra disk](../../disks-types.md#ultra-disk)라고 합니다. 이제까지 제공된 Azure 스토리지와 Ultra Disk의 중요한 차이점은 디스크 기능이 더 이상 디스크 크기에 구속되지 않는다는 것입니다. 고객은 Ultra Disk에 대해 다음과 같은 기능을 정의할 수 있습니다.

- 4GiB에서 65,536GiB까지의 디스크 크기
- 100IOPS에서 160,000IOPS까지의 IOPS 범위(최대값은 VM 유형에 따라 다름)
- 300MB/초에서 2,000MB/초까지의 스토리지 처리량

Ultra Disk를 사용하면 원하는 크기, IOPS 및 디스크 처리량 범위를 충족하는 단일 디스크를 정의할 수 있습니다. IOPS 및 저장소 처리량 요구 사항을 충족 하는 볼륨을 구성 하기 위해 Azure premium storage 위에 LVM 또는 MDADM과 같은 논리 볼륨 관리자를 사용 하는 대신 Ultra disk와 premium storage 간에 구성 조합을 실행할 수 있습니다. 결과적으로 Ultra disk의 사용량을 **/hana/data** 및 **/hana/log** 볼륨의 성능으로 제한 하 고 Azure premium storage를 사용 하 여 다른 볼륨을 처리할 수 있습니다.

울트라 디스크의 다른 이점은 premium storage와 비교할 때 읽기 대기 시간이 더 좋을 수 있습니다. 더 빠른 읽기 대기 시간은 HANA 시작 시간과 이후 데이터를 메모리로 로드하는 시간을 단축하려는 경우에 이점이 될 수 있습니다. HANA가 저장점을 작성하는 경우에도 Ultra Disk 스토리지의 이점을 느낄 수 있습니다. 

> [!NOTE]
> Ultra Disk는 아직 모든 Azure 지역에 출시되지 않았고 아직 아래 나열된 모든 VM 유형을 지원하지 않습니다. Ultra Disk를 사용할 수 있는 지역 및 지원되는 VM 제품군에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../../windows/disks-types.md#ultra-disk) 문서를 참조하세요.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>순수 Ultra Disk 구성을 사용하는 프로덕션 권장 스토리지 솔루션
이 구성에서는 **/hana/data** 및 **/hana/log** 볼륨을 별도로 유지 합니다. 제안된 값은 SAP가 [SAP TDI 스토리지 백서](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)에서 권장하는 대로 SAP HANA 및 스토리지 구성에 대한 VM 유형을 인증하는 KPI에서 도출된 것입니다.

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 볼륨 | /hana/data I/O 처리량 | /hana/data IOPS | /hana/log 볼륨 | /hana/log I/O 처리량 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480MB/s | 200GB | 400MBps | 2,500 | 80GB | 250MB | 1,800 |
| E32ds_v4 | 256GiB | 768MB/s | 300GB | 400MBps | 2,500 | 128GB | 250MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 m b/초 | 460 GB | 400MBps | 3,000 | 192 GB | 250MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200MB/s | 610 GB | 400MBps | 3,500 |  256GB | 250MBps | 1,800 |
| E64s_v3 | 432GiB | 1,200MB/s | 610 GB | 400MBps | 3,500 | 220 GB | 250MB | 1,800 |
| M32ts | 192GiB | 500MB/s | 250GB | 400MBps | 2,500 | 96 GB | 250MBps  | 1,800 |
| M32ls | 256GiB | 500MB/s | 300GB | 400MBps | 2,500 | 256GB | 250MBps  | 1,800 |
| M64ls | 512GiB | 1,000MB/s | 620 GB | 400MBps | 3,500 | 256GB | 250MBps  | 1,800 |
| M64s | 1,000GiB | 1,000MB/s |  1,200GB | 600MBps | 5,000 | 512 GB | 250MBps  | 2,500 |
| M64ms | 1,750GiB | 1,000MB/s | 2,100GB | 600MBps | 5,000 | 512 GB | 250MBps  | 2,500 |
| M128s | 2,000GiB | 2,000MB/s |2,400GB | 750 MBps | 7,000 | 512 GB | 250MBps  | 2,500 | 
| M128ms | 3,800GiB | 2,000MB/s | 4,800GB | 750 MBps |7,000 | 512 GB | 250MBps  | 2,500 | 
| M208s_v2 | 2,850GiB | 1,000MB/s | 3,500GB | 750 MBps | 7,000 | 512 GB | 250MBps  | 2,500 | 
| M208ms_v2 | 5,700GiB | 1,000MB/s | 7,200GB | 750 MBps | 7,000 | 512 GB | 250MBps  | 2,500 | 
| M416s_v2 | 5,700GiB | 2,000MB/s | 7,200GB | 1,000MBps | 9,000 | 512 GB | 400MBps  | 4,000 | 
| M416ms_v2 | 11,400GiB | 2,000MB/s | 14,400GB | 1,500MBps | 9,000 | 512 GB | 400MBps  | 4,000 |   

**나열된 값은 시작점으로 사용되며 실제 수요를 기준으로 평가해야 합니다.** Azure Ultra Disk의 장점은 VM을 종료하거나 시스템에 적용되는 워크로드를 중지하지 않고도 IOPS 및 처리량 값을 조정할 수 있다는 것입니다.   

> [!NOTE]
> 이제까지 Ultra Disk 스토리지를 사용하는 스토리지 스냅샷은 사용할 수 없습니다. 따라서 Azure Backup 서비스에서 VM 스냅샷을 사용할 수 없습니다.


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 기반 NFS v 4.1 볼륨
Azure NetApp Files은 **/hana/shared**, **/hana/data**및 **/hana/log** 볼륨에 사용할 수 있는 네이티브 NFS 공유를 제공 합니다. **/Hana/data** 및 **/hana/log** 볼륨에 대해 anf 기반 nfs 공유를 사용 하려면 v 4.1 nfs 프로토콜을 사용 해야 합니다. NFS 프로토콜 v3은 ANF의 공유를 기반으로 하는 경우 **/hana/data** 및 **/hana/log** 볼륨의 사용에 대해 지원 되지 않습니다. 

> [!IMPORTANT]
> Azure NetApp Files에서 구현 된 NFS v3 프로토콜은 **/hana/data** 및 **/hana/log**에 사용할 수 **없습니다** . NFS 4.1 사용은 기능 관점에서 **/hana/data** 및 **/hana/log** 볼륨에 대해 필수입니다. **/Hana/shared** 볼륨의 경우 기능 관점에서 nfs V3 또는 nfs v 4.1 프로토콜을 사용할 수 있습니다.

### <a name="important-considerations"></a>중요 고려 사항
SAP Netweaver 및 SAP HANA에 Azure NetApp Files를 고려하는 경우 다음과 같은 중요한 사항을 고려해야 합니다.

- 최소 용량 풀은 4TiB입니다.  
- 최소 볼륨 크기는 100GiB입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재되는 모든 가상 머신은 동일한 Azure Virtual Network 또는 동일한 지역의 [피어링된 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md)에 있어야 합니다.  
- 선택한 가상 네트워크에는 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨을 크기 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족해야 합니다.  
- Azure NetApp Files는 [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 제공합니다. 사용자는 허용되는 클라이언트, 액세스 유형(읽기 및 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 지역에서 대기 시간이 미칠지도 모르는 영향을 염두에 두어야 합니다.  
- 짧은 대기 시간을 위해 Azure NetApp 스토리지와 근접하게 가상 머신을 배포하는 것이 중요합니다. 
- 가상 머신의 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID는 Azure NetApp Files의 구성과 일치해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

> [!IMPORTANT]
> 가상 머신과 Azure NetApp 구성 간에 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID가 일치하지 않는 경우 가상 머신에 탑재된 Azure NetApp 볼륨의 파일에 대한 권한이 `nobody`로 표시됩니다. Azure NetApp Files에 [새 시스템을 온보딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)할 때 <b>sidadm</b> 사용자 ID 및 `sapsys` 그룹 ID를 올바로 지정해야 합니다.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다. 

Azure에서 SAP용 인프라를 설계할 때 다음의 최소 처리량 특성으로 변환되는 SAP의 최소 스토리지 처리량 요구 사항에 대해 알고 있어야 합니다.

- 1MB I/O 크기의 **/hana/log**에 대해 250MB/초의 읽기/쓰기 작업을 사용하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 400MB/초의 읽기 작업을 사용하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 **/hana/data**에 대해 최소 250MB/초의 쓰기 작업을 사용하도록 설정합니다.  

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](../../../azure-netapp-files/azure-netapp-files-service-levels.md)는 다음과 같습니다.
- Premium storage 계층-64 MiB/s  
- Ultra Storage 계층 - 128MiB/s  

> [!IMPORTANT]
> 단일 NFS 볼륨에 배포하는 용량에 관계없이 처리량은 가상 머신의 소비자가 활용하는 1.2~1.4GB/초 대역폭의 범위에서 안정될 것으로 예상됩니다. 이는 ANF 제품의 기본 아키텍처와 NFS 관련 Linux 세션 제한과 관련이 있습니다. [Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과](../../../azure-netapp-files/performance-benchmarks-linux.md) 문서에 설명된 성능 및 처리량 수치는 여러 클라이언트 VM이 있는 한 공유 NFS 볼륨에서 여러 세션에 걸쳐 테스트한 결과입니다. 이 시나리오는 SAP에서 측정하는 시나리오와 다릅니다. 즉, ANF에서 호스트되는 단일 NFS 볼륨에 대해 단일 VM에서 처리량을 측정합니다.

데이터 및 로그에 대한 SAP 최소 처리량 요구 사항을 충족하기 위해 또한 `/hana/shared`에 대한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared | 4개 작업자 노드당 최대(512GB, 1xRAM) | 4개 작업자 노드당 최대(512GB, 1xRAM) | v3 또는 v4.1 |


> [!NOTE]
> 여기에 명시된 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자에게 표명하는 최소 요구 사항을 충족하는 것을 목표로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

따라서 Ultra Disk 스토리지에 대해 나열된 것처럼 ANF 볼륨에 비슷한 처리량을 배포하는 것을 고려할 수 있습니다. 또한 이미 Ultra Disk 테이블에서 수행한 것처럼 다른 VM SKU의 볼륨에 대해 나열된 크기에 대해서도 크기를 고려합니다.

> [!TIP]
> 볼륨을 `unmount`하거나 가상 머신을 중지하거나 SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 그러므로 애플리케이션이 예상된 처리량 수요와 예측하지 못한 처리량 수요를 모두 충족할 수 있습니다.

ANF에서 호스트되는 NFS v4.1 볼륨을 사용하는 대기 노드로 SAP HANA 스케일 아웃 구성을 배포하는 방법에 대한 설명서는 [SUSE Linux Enterprise Server의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md)에 게시되어 있습니다.


## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure premium storage를 사용 하는 비용에 민감한 솔루션
지금까지이 문서에 설명 된 azure premium storage 솔루션은 [premium storage를 사용 하는 솔루션 섹션의 Azure M 시리즈 가상 컴퓨터에 대 한 azure 쓰기 가속기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) 에 설명 되어 SAP HANA 프로덕션 지원 시나리오를 위한 것입니다. 프로덕션 지원 가능 구성의 특성 중 하나는 SAP HANA 데이터에 대 한 볼륨을 분리 하 고 두 개의 서로 다른 볼륨으로 다시 실행 하는 것입니다. 이러한 분리의 이유는 볼륨의 워크 로드 특성이 서로 다르기 때문입니다. 그리고 제안 된 프로덕션 구성을 사용 하는 경우 다양 한 유형의 캐싱 또는 다양 한 유형의 Azure 블록 저장소가 필요할 수 있습니다. Azure 블록 저장소 대상을 사용 하는 프로덕션 지원 구성은 [azure Virtual Machines에 대 한 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 를 준수 합니다.  비프로덕션 시나리오의 경우 프로덕션 시스템에 대해 수행 되는 몇 가지 고려 사항은 더 낮은 프로덕션 이외의 시스템에 적용 되지 않을 수 있습니다. 따라서 HANA 데이터 및 로그 볼륨을 결합할 수 있습니다. 궁극적으로는 궁극적으로는 프로덕션 시스템에 필요한 특정 처리량 또는 대기 시간 Kpi를 충족 하지 원인 합니다. 이러한 환경에서 비용을 절감 하는 또 다른 측면은 [Azure 표준 SSD storage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage)를 사용 하는 것입니다. 그러나 [Azure Virtual Machines에 대 한 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 무효화 하는 선택이 있습니다. 

이러한 구성에 대 한 비용이 저렴 한 대안은 다음과 같습니다.


| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| E16v3 | 128GiB | 384MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM 유형 HANA 인증 안 됨 <br /> 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| M32ts | 192GiB | 500MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 5000<sup>2</sup> 로 제한 됩니다. |
| E20ds_v4 | 160 GiB | 480MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| E32v3 | 256GiB | 768MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM 유형 HANA 인증 안 됨 <br /> 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| E32ds_v4 | 256GiB | 768MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| M32ls | 256GiB | 500MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 5000<sup>2</sup> 로 제한 됩니다. |
| E48ds_v4 | 384 GiB | 1152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| E64v3 | 432GiB | 1,200MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| E64ds_v4 | 504 GiB | 1200MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 저장소 대기 시간<sup>1</sup> 보다 더 작은 값을 얻지 못합니다. |
| M64ls | 512GiB | 1,000MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 1만<sup>2</sup> 로 제한 됩니다. |
| M64s | 1,000GiB | 1,000MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 1만<sup>2</sup> 로 제한 됩니다. |
| M64ms | 1,750GiB | 1,000MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 1만<sup>2</sup> 로 제한 됩니다. |
| M128s | 2,000GiB | 2,000MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 2만<sup>2</sup> 로 제한 됩니다. |
| M208s_v2 | 2,850GiB | 1,000MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 1만<sup>2</sup> 로 제한 됩니다. |
| M128ms | 3,800GiB | 2,000MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 2만<sup>2</sup> 로 제한 됩니다. |
| M208ms_v2 | 5,700GiB | 1,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 1만<sup>2</sup> 로 제한 됩니다. |
| M416s_v2 | 5,700GiB | 2,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 2만<sup>2</sup> 로 제한 됩니다. |
| M416ms_v2 | 11400GiB | 2,000MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 결합 된 데이터 및 로그 볼륨에 쓰기 가속기를 사용 하면 IOPS 비율이 2만<sup>2</sup> 로 제한 됩니다. |


<sup>1</sup> [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md) 는 Ev4 및 Ev4 VM 제품군에서 사용할 수 없습니다. Azure premium storage를 사용 하면 i/o 대기 시간이 1ms 미만이 됩니다.

<sup>2</sup> VM 제품군은 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md)을 지원 하지만 Write Accelerator의 iops 제한으로 인해 디스크 구성 iops 기능이 제한 될 가능성이 있습니다.

SAP HANA에 대 한 데이터 및 로그 볼륨을 결합 하는 경우 스트라이프 볼륨을 구성 하는 디스크에서 읽기 캐시 또는 읽기/쓰기 캐시를 사용 하도록 설정 하지 않아야 합니다.

SAP를 사용 하 여 인증 되지 않은 VM 유형 및 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나열 되지 않은 것 처럼 나열 된 VM 유형이 있습니다. 고객의 의견은 나열 되지 않은 VM 유형이 일부 비프로덕션 작업에 성공적으로 사용 된 것 이었습니다.


## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

- [Azure Virtual Machines의 SAP HANA 고가용성 가이드](./sap-hana-availability-overview.md)
