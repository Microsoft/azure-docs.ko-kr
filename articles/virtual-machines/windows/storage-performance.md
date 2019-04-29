---
title: Azure Lsv2-시리즈 virtual machines-저장소의 성능 최적화 | Microsoft Docs
description: Lsv2-시리즈 virtual machines에서 솔루션의 성능을 최적화 하는 방법을 알아봅니다.
services: virtual-machines-windows
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: f84e81a5a9e9c9cf6f477adefa0869d776f7dd71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487344"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Lsv2-시리즈 virtual machines에서 성능 최적화

Lsv2-시리즈 virtual machines는 다양 한 광범위 한 응용 프로그램 및 산업 높은 I/O 및 로컬 저장소에 대 한 처리량을 필요로 하는 워크 로드를 지원 합니다.  Lsv2 시리즈는 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징, 대규모 트랜잭션 데이터베이스, Cassandra, MongoDB, Cloudera, 포함 하는 데 적합 하며 Redis

Lsv2-시리즈 Virtual Machines (Vm)의 디자인 프로세서, 메모리, NVMe 장치 및 Vm 간에 최상의 성능을 제공 하기 위해 AMD EPYC™ 7551 프로세서를 최대화 합니다. 하드웨어 성능을 최대화 하는 것 외에도 Lsv2 시리즈 Vm은 하드웨어 및 소프트웨어를 사용 하 여 성능 향상을 위해 Windows 및 Linux 운영 체제의 요구에 따라 작동 하도록 설계 되었습니다.

최적화 된 버전에서 발생 한 소프트웨어 및 하드웨어 튜닝 [Windows Server 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview)Lsv2 시리즈 NVMe 장치에서 최대 성능을 지 원하는 Azure Marketplace에 12 월 2018 년 초에 출시 Vm입니다.

이 문서에서는 팁 및 워크 로드 및 응용 프로그램을 확인 하기 위한 제안을 Vm에 최대 성능을 얻을 수 있습니다. Azure Marketplace에 자세한 Lsv2 액세스에 최적화 된 이미지를 추가할 때이 페이지의 정보는 지속적으로 업데이트 됩니다.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 칩셋 아키텍처

Lsv2 시리즈 Vm Zen 마이크로 아키텍처에 기반을 둔 AMD EYPC™ 서버 프로세서를 사용 합니다. AMD EYPC™에 대 한 무한대 패브릭 (IF)으로 확장 가능한 개발 다 고 패키지에 다중 패키지 통신에 사용할 수 있는 해당 NUMA 모델에 대 한 상호 연결 합니다. QPI (빠른 경로 Interconnect) 및 Intel 최신 모놀리식 주사위 프로세서에서 사용 되는 (크게 경로 Interconnect) UPI에 비해 AMD의 여러 NUMA 작은 주사위 아키텍처 수 이점을 모두 성능 문제 뿐 아니라 합니다. 메모리 대역폭 및 대기 시간 제한의 실제 영향은 실행 중인 워크 로드 유형에 따라 달라질 수 있습니다.

## <a name="tips-for-maximizing-performance"></a>성능을 극대화 하기 위한 팁

* Lsv2 시리즈 Vm을 구동 하는 하드웨어는 8 개 I/O 큐 쌍 (QP) s 사용 하 여 NVMe 장치를 활용 합니다. 모든 NVMe 장치 I/O 큐가 실제로 쌍: 전송 큐 및 큐를 완료 합니다. NVMe 드라이버 필자를 배포 하 여 이러한 8 I/O QPs의 사용률을 최적화할 수 있도록 설정 된/O의 라운드 로빈에서 예약 합니다. 최대 성능을 위해, 일치 하는 장치 당 8 개 작업을 실행 합니다.

* NVMe 관리자 명령 (예를 들어, NVMe 스마트 정보 쿼리, 등 NVMe I/O 명령 사용 하 여) 활성 작업 중에 혼합 하지 마세요. Lsv2 NVMe 장치 모드로 전환 "저속" 보류 중인 모든 NVMe 관리 명령을 때마다 Hyper-v NVMe 직접 기술에 의해 지원 됩니다. Lsv2 사용자는 이런 경우 NVMe I/O 성능에서 삭제 하는 성능이 크게 볼 수 있습니다.

* Lsv2 사용자가 앱에 대 한 NUMA 선호도 결정 합니다. 데이터 드라이브에 대 한 VM 내에서 보고 장치 NUMA 정보 (모두 0) 따를 필요가 없습니다. 더 나은 성능을 위해 가능 하면 Cpu 간에 워크 로드를 분산 하는 것이 좋습니다. 

* Lsv2 VM NVMe 장치에 대 한 I/O 큐 쌍 당 지원 되는 최대 큐 깊이 1024 (vs. Amazon i3 QD 32 제한)입니다. Lsv2 사용자 (가상) 벤치 마크 워크 로드 성능이 저하 될 수 있는 전체 조건에서 큐 트리거를 방지 하려면 1024 이하로 큐 깊이를 제한 해야 합니다.

## <a name="utilizing-local-nvme-storage"></a>로컬 NVMe 저장소를 활용 하 여

1.92 TB NVMe 디스크에 모든 Lsv2 Vm 로컬 저장소는 사용 후 삭제 합니다. VM의 성공적인 표준 재부팅을 하는 동안 로컬 NVMe 디스크의 데이터가 유지 됩니다. VM 다시 배포, 할당 취소 하거나 삭제 한 경우 데이터는 NVMe에서 유지 되지 않습니다. 다른 문제로 인해 VM 또는 실행 중인 하드웨어에에 비정상 상태가 되는 경우에 데이터 유지 되지 않습니다. 이 경우 이전 호스트의 모든 데이터가 안전 하 게 지워집니다.

VM 계획 된 유지 관리 작업을 하는 동안 예를 들어, 다른 호스트 컴퓨터로 이동 해야 하는 경우 사례도 됩니다. 계획 된 유지 관리 작업 및 일부 하드웨어 오류를 사용 하 여 예측할 수 있습니다 [예약 된 이벤트](scheduled-events.md)합니다. 모든 예측된 유지 관리 및 복구 작업에서 업데이트를 유지할 예약 된 이벤트를 사용 해야 합니다.

계획 된 유지 관리 이벤트를 VM에 빈 로컬 디스크를 사용 하 여 새 호스트를 다시 생성 해야 하는 경우 데이터 (마찬가지로 안전 하 게 삭제 되 고 이전 호스트의 모든 데이터)과 다시 동기화 해야 합니다. 이 Lsv2 시리즈 Vm 실시간 마이그레이션 로컬 NVMe 디스크에 현재 지원 하지 않기 때문에 발생 합니다.

계획 된 유지 관리에 대 한 두 가지 모드가 있습니다.

### <a name="standard-vm-customer-controlled-maintenance"></a>표준 VM 고객 제어 유지 관리

- VM이 30 일 기간 동안 업데이트 된 호스트에 이동 합니다.
- Lsv2 로컬 저장소 데이터가 손실 되어 이므로 이벤트 이전에 백업 데이터를 사용 하는 것이 좋습니다.

### <a name="automatic-maintenance"></a>자동 유지 관리

- 고객이 고객 제어 유지 관리, 실행 되지 않을 경우 또는 보안 제로 이벤트와 같은 응급 절차 시 발생 합니다.
- 고객 데이터를 유지 하기 위한 것 이지만 VM 중지 또는 다시 부팅 하는 작은 위험이 있습니다.
- Lsv2 로컬 저장소 데이터가 손실 되어 이므로 이벤트 이전에 백업 데이터를 사용 하는 것이 좋습니다.

모든 향후 서비스 이벤트에 대 한 제어 된 유지 관리 프로세스를 사용 업데이트에 가장 편리한 시간을 선택 합니다. 이벤트를 하기 전에 premium storage에서 데이터를 백업할 수 있습니다. 유지 관리 이벤트에는 다음이 완료 되 면 새로 고친된 Lsv2 Vm 로컬 NVMe 저장소에 데이터를 반환할 수 있습니다.

로컬 NVMe 디스크의 데이터를 유지 관리 하는 시나리오에는 다음이 포함 됩니다.

- VM 실행 중이 고 정상 상태입니다.
- VM (또는 Azure)에서 진행에서 재부팅 됩니다.
- VM 일시 중지 (할당 취소 없이 중지) 합니다.
- 계획된 된 유지 관리 작업을 서비스의 과반수입니다.

고객을 보호 하기 위해 데이터를 안전 하 게 지워서는 시나리오에는 다음이 포함 됩니다.

- VM 다시 배포 되 중지 (할당 취소), 또는 사용자에 의해 삭제 됩니다.
- VM 비정상 상태가 되 고 서비스에 하드웨어 문제로 인해 다른 노드로 복구 합니다.
- 계획된 된 유지 관리 작업을 서비스 수가 적은 VM이 서비스에 대 한 다른 호스트에 재할당 될 필요 합니다.

로컬 저장소에 데이터 백업에 대 한 옵션에 대 한 자세한 내용은 참조 하세요 [Azure IaaS 디스크에 대 한 백업 및 재해 복구](backup-and-disaster-recovery-for-azure-iaas-disks.md)합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **Lsv2 시리즈 Vm을 배포 시작 하려면 어떻게 해야 하나요?**  
   훨씬 다른 VM과 같은 사용를 [포털](quick-create-portal.md)를 [Azure CLI](quick-create-cli.md), 또는 [PowerShell](quick-create-powershell.md) VM을 만듭니다.

* **단일 NVMe 디스크 오류가 발생 하면 모든 Vm에 있는 호스트에 오류를?**  
   하드웨어 노드의 디스크 오류가 감지 되 면 하드웨어는 실패 한 상태입니다. 이 문제가 발생 하면 노드의 모든 Vm 자동으로 할당 취소 되어 정상 노드로 이동 합니다. Lsv2 시리즈 vm의 경우 즉, 실패 한 노드에서 고객 데이터가 안전 하 게 지워집니다 고 고객이 새 노드에 다시 생성 해야 합니다. 에서 설명한 대로 실시간 마이그레이션 Lsv2에서 사용할 수 있게 되기까지, 실패 한 노드의 데이터 사전에 이동 됩니다 Vm과 다른 노드로 전송 될 때.

* **Windows Server 2012에서 Windows 또는 Windows Server 2016에 대 한 폴링 조정 해야 합니까?**  
   NVMe 폴링만 Azure에서 Windows Server 2019에 제공 됩니다.  

* **기존의 인터럽트 서비스 루틴 (ISR) 모델로 전환할 수 있나요?**  
   Lsv2 시리즈 Vm NVMe에 최적화 된 폴링. 업데이트 폴링 성능을 향상 시키기 위해 지속적으로 제공 됩니다.

* **Windows Server 2019 폴링 설정을 조정할 수 있나요?**  
   폴링 설정은 사용자가 조정할 수 없습니다.
   
## <a name="next-steps"></a>다음 단계

* 모든 사양을 참조 하세요 [Vm 저장소 성능을 위해 최적화](sizes-storage.md) Azure에서
