---
title: Azure Lsv2 시리즈 가상 머신에서 성능 최적화 - 스토리지
description: Lsv2 시리즈 가상 머신에서 솔루션의 성능을 최적화 하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: cd9e539e01e8e11d866186552ab3b8dde7e03f91
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654772"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>Lsv2 시리즈 Linux 가상 머신에서 성능 최적화

Lsv2 시리즈 가상 머신은 광범위한 애플리케이션과 업계에서 로컬 스토리지에 대한 높은 I/O와 처리량이 필요한 다양한 워크로드를 지원합니다.  Lsv2 시리즈는 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 Cassandra, MongoDB, Cloudera, Redis 등의 대규모 트랜잭션 데이터베이스에 적합합니다.

Lsv2 시리즈 Virtual Machines(VM)의 디자인은 AMD EPYC™ 7551 프로세서를 최대화하여 프로세서, 메모리, NVMe 디바이스 및 VM 간에 최고의 성능을 제공합니다. Linux에서 파트너를 사용 하는 경우 Lsv2 시리즈 성능에 최적화 되어 있고 현재 다음을 포함 하는 Azure Marketplace 몇 가지 빌드를 사용할 수 있습니다.

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

이 문서에서는 워크로드와 애플리케이션이 VM에 설계된 최대 성능을 얻을 수 있는 팁과 제안을 제공합니다. Lsv2 최적화 이미지가 Microsoft Azure Marketplace에 더 추가됨에 따라 이 페이지의 정보는 계속 업데이트될 예정입니다.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 칩셋 아키텍처

Lsv2 시리즈 VM은 Zen 마이크로아키텍처를 기반으로 하는 AMD EYPC™ 서버 프로세서를 사용합니다. AMD는 온다이, 온패키지 및 멀티패키지 통신에 사용할 수 있는 NUMA 모델을 위한 확장 가능한 상호 연결로 Infinity Fabric(IF) for EYPC™를 개발했습니다. Intel의 최신 모놀리식 다이 프로세서에 사용되는 QPI(Quick-Path Interconnect) 및 UP(Ultra-Path Interconnect)와 비교하여, AMD의 “NUMA는 많고 다이는 작은” 아키텍처는 성능상의 이점과 문제를 모두 가져올 수 있습니다. 메모리 대역폭과 대기 시간 제약 조건의 실제 영향은 실행 중인 워크로드의 유형에 따라 달라질 수 있습니다.

## <a name="tips-to-maximize-performance"></a>성능 최대화 팁

* 워크 로드에 대 한 사용자 지정 Linux GuestOS을 업로드 하는 경우 가속화 된 네트워킹은 기본적으로 **해제** 됩니다. 가속화 된 네트워킹을 사용 하도록 설정 하려는 경우 최적의 성능을 위해 VM을 만들 때 사용 하도록 설정 합니다.

* Lsv2 시리즈 VM을 구동하는 하드웨어는 8개의 I/O QP(큐 쌍)가 있는 NVMe 디바이스를 활용합니다. 모든 NVMe 디바이스 I/O 큐는 실제로는 한 쌍(제출 큐와 완료 큐)입니다. NVMe 드라이버는 라운드 로빈 일정으로 I/O를 배포하여 이 8개의 I/O QP 사용률을 최적화하도록 설정되었습니다. 최대 성능을 얻으려면 이에 맞게 디바이스당 8개의 작업을 실행합니다.

* 활성 워크로드 중에 NVMe 관리자 명령(예: NVMe SMART info query 등)과 NVMe I/O 명령을 섞어서 사용하지 마세요. Lsv2 NVMe 디바이스는 NVMe 관리자 명령이 보류될 때마다 "저속 모드"로 전환되는 Hyper-V NVMe Direct 기술로 지원됩니다. 이 경우 Lsv2 사용자는 극적인 NVMe I/O 성능 저하를 경험할 수 있습니다.

* Lsv2 사용자는 데이터 드라이브에 대해 VM 내에서 보고되는 디바이스 NUMA 정보(모두 0)를 바탕으로 앱의 NUMA 선호도를 결정하면 안 됩니다. 성능 향상을 위해 가능한 경우 CPU 간에 워크로드를 분산하는 것이 좋습니다.

* Lsv2 VM NVMe 디바이스의 I/O 큐 쌍당 지원되는 최대 큐 용량은 1024입니다(Amazon i3 QD 32 제한 대비). Lsv2 사용자는 가상 벤치마킹 워크로드를 큐 깊이 1024 이하로 제한하여 성능 저하를 일으킬 수 있는 큐 전체 조건 트리거를 피해야 합니다.

## <a name="utilizing-local-nvme-storage"></a>로컬 NVMe 스토리지 활용

모든 Lsv2 VM의 1.92TB NVMe 디스크에 있는 로컬 스토리지는 사용 후 삭제됩니다. VM의 표준 재부팅에 성공하면 로컬 NVMe 디스크의 데이터가 유지됩니다. VM이 재배포, 할당 취소 또는 삭제되면 NVMe의 데이터가 유지되지 않습니다. 다른 문제로 인해 VM 또는 VM이 실행 중인 하드웨어가 비정상 상태가 되면 데이터가 유지되지 않습니다. 이 경우 이전 호스트의 모든 데이터가 안전하게 지워집니다.

계획된 유지 관리 작업을 수행하는 경우와 같이 VM을 다른 호스트 머신으로 이동해야 하는 경우도 있습니다. [Scheduled Events](scheduled-events.md)으로 계획된 유지 관리 작업과 일부 하드웨어 오류를 예상할 수 있습니다. 예측된 유지 관리 및 복구 작업에 대한 최신 정보를 확인하려면 Scheduled Events를 사용해야 합니다.

계획된 유지 관리 이벤트를 위해 빈 로컬 디스크가 있는 새 호스트에 VM을 다시 만들어야 하는 경우 데이터를 다시 동기화해야 합니다. 이 경우 이전 호스트의 모든 데이터는 안전하게 지워집니다. 이는 Lsv2 시리즈 VM이 현재 로컬 NVMe 디스크에서 라이브 마이그레이션을 지원하지 않기 때문입니다.

계획된 유지 관리에는 두 가지 모드가 있습니다.

### <a name="standard-vm-customer-controlled-maintenance"></a>표준 VM 고객 제어 유지 관리

- 30일 동안 VM이 업데이트된 호스트로 이동됩니다.
- Lsv2 로컬 스토리지 데이터가 손실될 수 있으므로 이벤트 전에 데이터를 백업하는 것이 좋습니다.

### <a name="automatic-maintenance"></a>자동 유지 관리

- 고객이 고객 제어 유지 관리를 실행하지 않거나 보안 제로 데이 이벤트와 같은 긴급 절차가 발생하는 경우 수행됩니다.
- 고객 데이터 유지가 목적이지만 VM 중지 또는 재부팅의 위험이 약간 있습니다.
- Lsv2 로컬 스토리지 데이터가 손실될 수 있으므로 이벤트 전에 데이터를 백업하는 것이 좋습니다.

예정된 서비스 이벤트의 경우 제어된 유지 관리 프로세스를 사용하여 업데이트에 가장 편리한 시간을 선택합니다. 이벤트 전에 프리미엄 스토리지에 데이터를 백업할 수 있습니다. 유지 관리 이벤트가 완료된 후 새로 고친 Lsv2 VM 로컬 NVMe 스토리지로 데이터를 반환할 수 있습니다.

다음과 같은 경우 로컬 NVMe 디스크에 데이터가 유지됩니다.

- VM이 실행 중이고 정상 상태입니다.
- VM이 사용자나 Azure에 의해 재부팅됩니다.
- VM이 일시 중지되었습니다(할당 취소 없이 중지됨).
- 대부분의 계획된 유지 관리가 작업을 처리하고 있습니다.

다음과 같은 경우 고객 보호를 위해 데이터가 안전하게 지워집니다.

- VM이 사용자에 의해 재배포, 중지(할당 취소됨) 또는 삭제됩니다.
- VM이 비정상 상태가 되고 하드웨어 문제로 인해 다른 노드로 서비스 치료가 필요합니다.
- 소수의 계획된 유지 관리가 서비스를 위해 다른 호스트에 VM을 할당해야 하는 작업을 처리하고 있습니다.

로컬 스토리지에 데이터를 백업하는 옵션에 대한 자세한 내용은 [Azure IaaS 디스크의 백업 및 재해 복구](../backup-and-disaster-recovery-for-azure-iaas-disks.md)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

* **Lsv2 시리즈 VM 배포를 시작하려면 어떻게 할까요?**  
   다른 VM과 마찬가지로 [포털](quick-create-portal.md), [Azure CLI](quick-create-cli.md) 또는 [PowerShell](quick-create-powershell.md)을 사용하여 VM을 만듭니다.

* **단일 NVMe 디스크 오류로 인해 호스트의 모든 VM이 실패하나요?**  
   하드웨어 노드에서 디스크 오류가 감지되면 하드웨어가 실패 상태입니다. 이 경우 노드의 모든 VM이 자동으로 할당 취소되고 정상 노드로 이동됩니다. Lsv2 시리즈 VM의 경우 오류가 발생한 노드의 고객 데이터도 안전하게 지워지며 고객이 새 노드에 해당 데이터를 다시 만들어야 합니다. 앞에서 언급한 바와 같이 실패한 노드의 데이터가 다른 노드로 전송될 때 VM과 함께 사전에 이동된 후 Lsv2에서 라이브 마이그레이션을 사용할 수 있습니다.

* **성능을 위해 rq_affinity를 조정 해야 하나요?**  
   Rq_affinity 설정은 최대 IOPS (초당 입력/출력 작업 수)를 사용 하는 경우 약간 조정 됩니다. 다른 모든 항목이 제대로 작동 하는 경우에는 rq_affinity를 0으로 설정 하 여 차이가 있는지 확인 하십시오.

* **Blk_mq 설정을 변경 해야 하나요?**  
   RHEL/CentOS 7.x는 NVMe 장치에 대해 blk-mq를 자동으로 사용 합니다. 구성 변경 이나 설정은 필요 하지 않습니다. Scsi_mod. use_blk_mq 설정은 vm에만 사용 되며 NVMe 장치가 게스트 Vm에 SCSI 장치로 표시 되기 때문에 Lsv2 Preview 중에 사용 되었습니다. 현재 NVMe 장치는 NVMe 장치로 표시 되므로 SCSI blk-mq 설정은 관련이 없습니다.

* **"Fio"를 변경 해야 하나요?**  
   L64v2 및 L80v2 VM 크기에서 ' fio '와 같은 성능 측정 도구를 사용 하 여 최대 IOPS를 얻으려면 각 NVMe 장치에서 "rq_affinity"을 0으로 설정 합니다.  예를 들어 다음 명령줄은 L80v2 VM의 모든 10 개 NVMe 장치에 대해 "rq_affinity"을 0으로 설정 합니다.

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   또한 i/o가 분할 안 함, 파일 시스템, RAID 0 구성 등을 포함 하지 않는 각 원시 NVMe 장치에 직접 수행 되는 경우 최상의 성능을 얻을 수 있습니다. 테스트 세션을 시작 하기 전에 `blkdiscard` 각 NVMe 장치에서를 실행 하 여 구성이 알려진 최신/정리 상태에 있는지 확인 합니다.
   
## <a name="next-steps"></a>다음 단계

* Azure의 [스토리지 성능에 최적화된 모든 VM](../sizes-storage.md) 사양을 참조하세요.
