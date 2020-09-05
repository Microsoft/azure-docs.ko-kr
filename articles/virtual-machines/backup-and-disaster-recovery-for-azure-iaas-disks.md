---
title: Azure Vm의 IaaS 디스크에 대 한 백업 및 재해 복구
description: 이 문서에서는 Azure에서 IaaS 가상 머신 및 디스크의 백업 및 재해 복구를 계획하는 방법을 설명합니다. Managed Disks 및 Unmanaged Disks를 모두 다루고 있습니다.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 28a46ad9e53a90c25c239278ee57ea368af395a5
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754976"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Azure IaaS 디스크에 대한 백업 및 재해 복구

이 문서에서는 Azure에서 IaaS VM(가상 머신) 및 디스크의 백업 및 DR(재해 복구)을 계획하는 방법을 설명합니다. Managed Disks 및 Unmanaged Disks를 모두 다루고 있습니다.

먼저 로컬 오류로부터 보호하는 데 도움이 되는 Azure 플랫폼의 기본 제공 내결함성 기능을 다룹니다. 기본 제공 기능에 완전히 포함되는 재해 시나리오를 설명합니다. 또한 다른 백업 및 DR 고려 사항이 적용될 수 있는 워크로드 시나리오의 몇 가지 예도 보여 줍니다. 그런 다음 IaaS 디스크의 DR에 가능한 솔루션을 검토합니다.

## <a name="introduction"></a>소개

Azure 플랫폼은 중복성 및 내결함성을 위해 다양한 방법을 사용하여 지역화된 하드웨어 오류로부터 고객을 보호합니다. 로컬 오류 문제에는 가상 디스크의 데이터 일부 또는 해당 서버의 SSD 또는 HDD 오류를 저장하는 Azure Storage 서버 컴퓨터와 관련된 문제가 포함될 수 있습니다. 이러한 격리된 하드웨어 구성 요소 오류는 정상 작업 도중에 발생할 수 있습니다.

Azure 플랫폼은 이러한 오류에 탄력적으로 대처하도록 설계되었습니다. 중요한 재해로 인해 많은 스토리지 서버 또는 전체 데이터 센터에 오류가 발생하거나 액세스할 수 없게 될 수 있습니다. VM과 디스크는 일반적으로 지역화된 오류로부터 보호되지만, VM 및 디스크에 영향을 줄 수 있는 지역 전체의 치명적인 오류(예: 주요 재해)로부터 작업을 보호하려면 추가 단계가 필요합니다.

플랫폼 오류 가능성 외에도 고객 애플리케이션 또는 데이터와 관련된 문제가 발생할 수 있습니다. 예를 들어 새 버전의 애플리케이션에서 실수로 데이터를 심각하게 변경시켜 중단이 발생할 수 있습니다. 이 경우 애플리케이션과 데이터를 마지막으로 알려진 양호한 상태의 이전 버전으로 되돌리려고 할 수 있습니다. 이렇게 하려면 정기적으로 백업해야 합니다.

지역적 재해 복구의 경우 IaaS VM 디스크를 다른 지역에 백업해야 합니다.

백업 및 DR 옵션을 살펴보기 전에 지역화된 오류를 처리하는 데 사용할 수 있는 몇 가지 방법을 요약해 보겠습니다.

### <a name="azure-iaas-resiliency"></a>Azure IaaS 복원력

*복원력*은 하드웨어 구성 요소에서 발생하는 정상적인 오류에 대한 허용 오차를 나타냅니다. 복원력은 오류로부터 복구하고 계속 작동할 수 있는 능력입니다. 오류를 방지하는 것이 아니라 가동 중지 또는 데이터 손실을 방지하는 방법으로 오류에 대응하는 것입니다. 복원력의 목표는 오류가 발생한 후 애플리케이션을 완전히 작동하는 상태로 되돌리기 위한 것입니다. Azure 가상 머신 및 디스크는 일반적인 하드웨어 결함에 탄력적으로 대처할 수 있도록 설계되었습니다. Azure IaaS 플랫폼에서 이러한 복원력을 어떻게 제공하는지 살펴보겠습니다.

가상 머신은 컴퓨팅 서버 및 영구 디스크와 같이 크게 두 부분로 구성됩니다. 둘 다 가상 머신의 내결함성에 영향을 줍니다.

VM을 저장하는 Azure 컴퓨팅 호스트 서버에 자주 발생하지 않는 하드웨어 오류가 발생하는 경우 Azure는 다른 서버에서 해당 VM을 자동으로 복원하도록 설계되었습니다. 이 시나리오의 경우, 컴퓨터가 다시 부팅되고 일정 시간 후에 VM이 다시 시작됩니다. Azure는 자동으로 이러한 하드웨어 오류를 감지하고 복구를 실행하여 가능한 한 빨리 고객의 VM을 사용할 수 있도록 합니다.

IaaS 디스크의 경우 데이터의 내구성이 영구 스토리지 플랫폼에 중요합니다. Azure 고객은 IaaS에서 실행되는 중요한 비즈니스 애플리케이션을 가지고 있으며 데이터의 지속성에 의존합니다. Azure는 로컬로 저장된 3개의 중복 데이터 복사본을 사용하여 이러한 IaaS 디스크를 보호하도록 설계되었습니다. 로컬 오류에 대한 내구성 향상을 위해 이러한 복사본을 제공합니다. 디스크를 보유하는 하드웨어 구성 요소 중 하나에 오류가 발생하면 디스크 요청을 지원하기 위해 두 개의 추가 사본이 있으므로 VM에 영향을 주지 않습니다. 디스크를 지원하는 두 개의 다른 하드웨어 구성 요소가 동시에 실패하는 경우에도 작동합니다(드문 경우). 

항상 세 개의 복제본을 유지할 수 있도록 하기 위해 세 개의 복사본 중 하나를 사용할 수 없게 되면 Azure Storage는 백그라운드에서 새 데이터 복사본을 자동으로 만듭니다. 따라서 내결함성을 위해 Azure 디스크와 함께 RAID를 사용할 필요는 없습니다. 필요한 경우 더 큰 볼륨을 만들기 위해 디스크를 스트라이핑하는 데에는 간단한 RAID 0 구성으로 충분합니다.

이러한 아키텍처에 따라 Azure는 업계 최고의 0% [연간 실패율](https://en.wikipedia.org/wiki/Annualized_failure_rate)로 IaaS 디스크에 엔터프라이즈급 내구성을 일관되게 제공하고 있습니다.

컴퓨팅 호스트 또는 Storage 플랫폼에서 지역화된 하드웨어 오류로 인해 때때로 VM 가용성에 대한 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)가 적용되는 VM을 일시적으로 사용할 수 없게 될 수 있습니다. 또한 Azure는 Azure 프리미엄 SSD를 사용하는 단일 VM 인스턴스에 대해 업계 최고의 SLA를 제공합니다.

디스크 또는 VM의 일시적인 사용 불가로 인한 가동 중지로부터 애플리케이션 워크로드를 보호하기 위해 고객은 [가용성 세트](windows/manage-availability.md)을 사용할 수 있습니다. 가용성 세트에 있는 둘 이상의 가상 머신은 애플리케이션에 대한 중복성을 제공합니다. 그런 다음 Azure는 서로 다른 전원, 네트워크 및 서버 구성 요소를 사용하여 별도의 장애 도메인에 이러한 VM 및 디스크를 만듭니다.

이러한 별도의 장애 도메인 때문에 지역화된 하드웨어 오류는 일반적으로 집합에 있는 여러 VM에 동시에 영향을 주지 않습니다. 별도의 오류 도메인이 있으면 애플리케이션에 대한 고가용성을 제공합니다. 고가용성이 필요한 경우에는 가용성 집합을 사용하는 것이 좋습니다. 다음 섹션에서는 재해 복구 측면을 다룹니다.

### <a name="backup-and-disaster-recovery"></a>백업 및 재해 복구

재해 복구는 드물지만 주요한 인시던트로부터 복구하는 기능입니다. 이러한 인시던트에는 전체 지역에 영향을 주는 서비스 중단과 같이 일시적이지 않은 대규모 오류가 포함됩니다. 재해 복구에는 데이터 백업 및 보관이 포함되며, 백업에서 데이터베이스를 복원하는 것과 같은 수동 작업이 포함될 수 있습니다.

주요 재해로 인해 대규모 중단이 발생하는 경우 Azure 플랫폼의 지역화된 오류에 대한 기본 제공 보호 기능은 VM/디스크를 완전히 보호하지 못할 수 있습니다. 이러한 대규모 중단에는 데이터 센터가 태풍, 지진, 화재로 인해 큰 피해를 입거나 대규모 하드웨어 단위 오류가 발생한 경우와 같이 치명적인 상황이 포함됩니다. 또한 애플리케이션 또는 데이터 문제로 인해 오류가 발생할 수도 있습니다.

중단으로부터 IaaS 작업을 보호하려면 복구할 수 있도록 중복성과 백업을 계획해야 합니다. 재해 복구의 경우 기본 사이트에서 떨어진 다른 지리적 위치에 백업해야 합니다. 이렇게 하면 원래 VM이나 디스크에 영향을 미친 동일한 이벤트로 영향받지 않도록 백업을 보호할 수 있습니다. 자세한 내용은 [Azure 애플리케이션의 재해 복구](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요.

DR 고려 사항에는 다음과 같은 측면이 포함될 수 있습니다.

- 고가용성: 애플리케이션에서 상당한 가동 중지 시간 없이 정상 상태로 계속 실행할 수 있는 기능입니다. *정상 상태*는 애플리케이션에서 응답하고 사용자가 애플리케이션에 연결하여 애플리케이션과 상호 작용할 수 있음을 의미합니다. 특정 중요 업무용 애플리케이션 및 데이터베이스는 플랫폼에 오류가 발생하더라도 항상 사용할 수 있어야 합니다. 이러한 작업의 경우 애플리케이션과 데이터에 대한 중복성을 계획해야 합니다.

- 데이터 내구성: 어떤 경우에 주요 고려 사항은 재해가 발생하더라도 데이터가 보존되도록 하는 것입니다. 따라서 다른 사이트에 데이터를 백업해야 할 수 있습니다. 이러한 작업의 경우 애플리케이션에 대한 전체 중복성이 필요하지는 않지만 디스크를 정기적으로 백업해야 합니다.

## <a name="backup-and-dr-scenarios"></a>백업 및 DR 시나리오

애플리케이션 워크로드 시나리오의 몇 가지 일반적인 예와 재해 복구 계획에 대한 고려 사항을 살펴보겠습니다.

### <a name="scenario-1-major-database-solutions"></a>시나리오 1: 주요 데이터베이스 솔루션

고가용성을 지원할 수 있는 SQL Server 또는 Oracle과 같은 프로덕션 데이터베이스 서버를 살펴보겠습니다. 이 데이터베이스는 중요한 프로덕션 애플리케이션 및 사용자가 사용합니다. 이 시스템에 대한 재해 복구 계획에서 지원해야 하는 요구 사항은 다음과 같습니다.

- 데이터는 보호되고 복구할 수 있어야 합니다.
- 서버를 사용할 수 있어야 합니다.

재해 복구 계획은 다른 지역에 있는 데이터베이스의 복제본을 백업으로 유지 관리해야 할 수 있습니다. 서버 가용성 및 데이터 복구에 대한 요구 사항에 따라 솔루션은 활성-활성 또는 활성-수동 복제본 사이트에서 정기적인 오프라인 데이터 백업에 이르기까지 다양할 수 있습니다. SQL Server 및 Oracle과 같은 관계형 데이터베이스는 다양한 복제 옵션을 제공합니다. SQL Server의 경우 고가용성을 위해 [SQL Server Always On 가용성 그룹](https://msdn.microsoft.com/library/hh510230.aspx)을 사용할 수 있습니다.

MongoDB와 같은 NoSQL 데이터베이스는 중복성을 위한 [복제본](https://docs.mongodb.com/manual/replication/)도 지원하므로 고가용성을 위한 복제본을 사용합니다.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>시나리오 2: 중복된 VM 클러스터

중복성 및 부하 분산을 제공하는 VM 클러스터에서 처리하는 작업을 살펴보겠습니다. 예로 한 지역에 배포된 Cassandra 클러스터가 있습니다. 이러한 유형의 아키텍처는 이미 해당 지역에서 높은 수준의 중복성을 제공합니다. 그러나 지역 수준에서 작업을 보호하려면 클러스터를 두 지역에 분산하거나 다른 지역에 정기적으로 백업하는 것이 좋습니다.

### <a name="scenario-3-iaas-application-workload"></a>시나리오 3: IaaS 애플리케이션 워크로드

IaaS 애플리케이션 워크로드를 살펴보겠습니다. 예를 들어 이 애플리케이션은 Azure VM에서 실행되는 일반적인 프로덕션 워크로드일 수 있습니다. 사이트의 콘텐츠 및 다른 리소스를 보유하고 있는 웹 서버 또는 파일 서버일 수 있습니다. 또한 VM 디스크에 대한 데이터, 리소스 및 애플리케이션 상태를 저장한 VM에서 실행되는 사용자가 지정하여 빌드된 비즈니스 애플리케이션일 수도 있습니다. 이 경우에 백업을 정기적으로 수행하는지 확인해야 합니다. Backup 빈도는 VM 워크로드의 특성을 기준으로 해야 합니다. 예를 들어 애플리케이션이 매일 실행되고 데이터가 수정되는 경우 백업은 매 시간마다 수행되어야 합니다.

또 다른 예로 다른 원본의 데이터를 끌어와서 집계된 보고서를 생성하는 보고 서버가 있습니다. 이 VM 또는 디스크가 손실되면 보고서가 손실될 수 있습니다. 그러나 보고 프로세스를 다시 실행하여 해당 출력을 다시 생성할 수도 있습니다. 이 경우에 보고서 서버가 재해에 피해를 입는 경우에도 실제로 데이터가 손실되지 않습니다. 결과적으로 보고서 서버에서 데이터의 일부를 손실하는 더 높은 수준의 내결함성이 있을 수 있습니다. 이 경우에 백업 빈도를 줄이면 비용을 절감할 수 있는 옵션입니다.

### <a name="scenario-4-iaas-application-data-issues"></a>시나리오 4: IaaS 애플리케이션 데이터 문제

IaaS 애플리케이션 데이터 문제도 발생할 수 있습니다. 애플리케이션이 가격 정보와 같이 중요한 상용 데이터를 계산, 유지 관리 및 제공한다고 가정합니다. 새 버전의 애플리케이션에는 가격을 잘못 계산하고 플랫폼에서 제공하는 기존 상거래 데이터가 손상되는 소프트웨어 버그가 있었습니다. 여기서 가장 좋은 해결 방법은 이전 버전의 애플리케이션과 데이터로 되돌리는 것입니다. 이렇게 하려면 시스템을 정기적으로 백업해야 합니다.

## <a name="disaster-recovery-solution-azure-backup"></a>재해 복구 솔루션: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/)은 백업 및 DR에 사용되며 [관리 디스크](managed-disks-overview.md) 및 비관리 디스크에서 작동합니다. 시간 기반 백업, 손쉬운 VM 복원 및 백업 보존 정책을 사용하여 백업 작업을 만들 수 있습니다.

[프리미엄 SSD](disks-types.md), [관리 디스크](managed-disks-overview.md) 또는 [로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage) 옵션이 적용된 다른 디스크 유형을 사용하는 경우 정기적인 DR 백업을 만드는 것이 특히 중요합니다. Azure Backup은 장기 보존을 위해 복구 서비스 자격 증명 모음에 데이터를 저장합니다. 백업 복구 서비스 자격 증명 모음에 대해 [지역 중복 스토리지](../storage/common/storage-redundancy.md#geo-redundant-storage) 옵션을 선택합니다. 이 옵션은 지역 재해로부터 보호하기 위해 백업을 다른 Azure 지역에 복제하도록 합니다.

비관리 디스크의 경우 IaaS 디스크에 대해 로컬 중복 스토리지 유형을 사용할 수 있지만, 복구 서비스 자격 증명 모음에 대해 Azure Backup이 지역 중복 스토리지 옵션과 함께 사용되는지 확인합니다.

> [!NOTE]
> 관리 되지 않는 디스크에 대해 [지역 중복 저장소](../storage/common/storage-redundancy.md#geo-redundant-storage) 또는 [읽기 액세스 지역 중복 저장소](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  옵션을 사용 하는 경우 여전히 백업 및 DR에 대 한 일관 된 스냅숏이 필요 합니다. [Azure Backup](https://azure.microsoft.com/services/backup/) 또는 [일관성 있는 스냅샷](#alternative-solution-consistent-snapshots) 중 하나를 사용합니다.

 다음 표는 DR에 사용할 수 있는 솔루션의 요약입니다.

| 시나리오 | 자동 복제 | DR 솔루션 |
| --- | --- | --- |
| 프리미엄 SSD 디스크 | 로컬([로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| 관리 디스크 | 로컬([로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| 관리되지 않은 로컬 중복 스토리지 디스크 | 로컬([로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| 관리되지 않은 지역 중복 스토리지 디스크 | 지역 간([지역 중복 스토리지](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[일관성 있는 스냅샷](#alternative-solution-consistent-snapshots) |
| 관리되지 않은 읽기 액세스 지역 중복 스토리지 디스크 | 지역 간([읽기 액세스 지역 중복 스토리지](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[일관성 있는 스냅샷](#alternative-solution-consistent-snapshots) |

Azure Backup과 함께 가용성 집합에서 Managed Disks를 사용할 경우에 가장 고가용성이 충족됩니다. Unmanaged Disks를 사용하는 경우 DR에 Azure Backup을 계속 사용할 수 있습니다. Azure Backup을 사용할 수 없는 경우 백업 및 DR을 위한 대안 솔루션은 이후의 섹션에서 설명하는 [일관성 있는 스냅샷](#alternative-solution-consistent-snapshots)을 사용하는 것입니다.

애플리케이션 또는 인프라 수준에서 고가용성, 백업 및 DR을 선택하는 경우 다음과 같이 나타낼 수 있습니다.

| Level |   고가용성   | 백업 또는 DR |
| --- | --- | --- |
| 애플리케이션 | SQL Server AlwaysOn | Azure Backup |
| 인프라    | 가용성 집합  | 일관된 스냅샷을 사용하는 지역 중복 스토리지 |

### <a name="using-azure-backup"></a>Azure Backup 사용 

[Azure Backup](../backup/backup-azure-vms-introduction.md)은 Windows 또는 Linux를 실행 중인 VM을 Azure 복구 서비스 자격 증명 모음으로 백업할 수 있습니다. 데이터를 생성하는 애플리케이션이 실행되는 동안 중요 업무용 데이터를 백업해야 하므로 이러한 데이터를 백업하고 복원하는 것은 복잡합니다. 

이 문제를 해결하기 위해 Azure Backup은 Microsoft 워크로드에 대해 애플리케이션에 일관된 백업을 제공합니다. 데이터가 스토리지에 올바르게 작성되었는지를 확인하는 데 볼륨 섀도 서비스를 사용합니다. Linux VM의 경우 기본 백업 일괄성 모드는 Windows의 경우와 같이 Linux에는 볼륨 섀도 서비스에 해당하는 기능이 없기 때문에 파일 일치 백업입니다. Linux 머신의 경우 [Azure Linux VM의 애플리케이션 일치 백업](../backup/backup-azure-linux-app-consistent.md)을 참조하세요.

![Azure Backup 흐름][1]

Azure Backup은 예약된 시간에 백업 작업을 시작할 때 VM에 설치된 백업 확장을 트리거하여 특정 시점의 스냅샷을 만듭니다. 이 스냅샷은 가상 머신을 종료하지 않고도 가상 머신의 디스크에 대한 일관된 스냅샷을 가져오도록 볼륨 섀도 서비스와 조정되어 생성됩니다. VM의 백업 확장은 모든 디스크에 대한 일관성 있는 스냅샷을 만들기 전에 모든 쓰기를 플러시합니다. 스냅샷을 만든 후에는 Azure Backup에서 백업 자격 증명 모음으로 데이터를 전송합니다. 백업 프로세스의 효율성을 높이기 위해 Azure 백업 서비스는 마지막 백업 후에 변경된 데이터 블록만 식별하여 전송합니다.

복원하려면 Azure Backup을 통해 사용할 수 있는 백업을 확인한 다음 복원을 시작할 수 있습니다. [Azure Portal](https://portal.azure.com/), [PowerShell](../backup/backup-azure-vms-automation.md) 또는 [Azure CLI](/cli/azure/)를 사용하여 Azure Backup을 만들고 복원할 수 있습니다.

### <a name="steps-to-enable-a-backup"></a>백업을 사용하도록 설정하는 단계

[Azure Portal](https://portal.azure.com/)을 사용하여 VM의 백업을 사용하도록 설정하는 데 다음 단계를 사용합니다. 정확한 시나리오에 따라 약간 변형될 수 있습니다. 자세한 내용은 [Azure Backup](../backup/backup-azure-vms-introduction.md) 설명서를 참조하세요. 또한 Azure Backup은 [Managed Disks가 있는 VM도 지원합니다](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  VM에 대한 복구 서비스 자격 증명 모음을 만듭니다.

    a. [Azure Portal](https://portal.azure.com/)에서 **모든 리소스**를 찾아보고 **Recovery Services 자격 증명 모음**을 찾습니다.

    b. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 클릭하고, 단계에 따라 VM과 동일한 지역에 새 자격 증명 모음을 만듭니다. 예를 들어 VM이 미국 서부 지역에 있으면 미국 서부를 선택하여 자격 증명 모음을 찾습니다.

1.  새로 만든 자격 증명 모음에 대한 스토리지 복제를 확인합니다. **Recovery Services 자격 증명 모음** 아래에서 자격 증명 모음에 액세스하고 **속성** > **백업 구성** > **업데이트**로 이동합니다. **지역 중복 스토리지** 옵션을 기본적으로 선택하도록 합니다. 이 옵션을 사용하면 자격 증명 모음이 보조 데이터 센터에 자동으로 복제됩니다. 예를 들어 미국 서부의 자격 증명 모음이 미국 동부에 자동으로 복제됩니다.

1.  백업 정책을 구성하고 동일한 UI에서 VM을 선택합니다.

1.  Backup 에이전트가 VM에 설치되어 있는지 확인합니다. Azure 갤러리 이미지를 사용하여 VM을 만든 경우 백업 에이전트가 이미 설치되어 있습니다. 그렇지 않은 경우(즉, 사용자 지정 이미지를 사용하는 경우) [가상 머신에 VM 에이전트 설치](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent)에 대한 지침을 사용합니다.

1.  이전 단계가 완료된 후에 백업 정책에서 지정한 대로 백업이 정기적으로 실행됩니다. 필요한 경우 Azure Portal의 자격 증명 모음 대시보드에서 첫 번째 백업을 수동으로 트리거할 수 있습니다.

스크립트를 사용하여 Azure Backup을 자동화하려면 [VM 백업용 PowerShell cmdlet](../backup/backup-azure-vms-automation.md)을 참조하세요.

### <a name="steps-for-recovery"></a>복구 단계

VM을 복구하거나 다시 작성해야 하는 경우 자격 증명 모음의 백업 복구 지점 중 하나에서 해당 VM을 복원할 수 있습니다. 복구하기 위한 몇 가지 다른 옵션은 다음과 같습니다.

-   백업된 VM의 특정 시점 표현으로 새 VM을 만들 수 있습니다.

-   디스크를 복원한 다음 VM에 대한 템플릿을 사용하여 복원된 VM을 사용자 지정하고 다시 작성할 수 있습니다.

자세한 내용은 [Azure Portal을 사용하여 가상 머신 복원](../backup/backup-azure-arm-restore-vms.md)에 대한 지침을 참조하세요. 또한 이 문서는 기본 데이터 센터에서 재해가 발생한 경우 지역 중복 백업 자격 증명 모음을 사용하여 쌍으로 연결된 데이터 센터에 백업된 VM을 복원하는 특정 단계에 대해 설명하고 있습니다. 이 경우 Azure Backup은 보조 지역의 컴퓨팅 서비스를 사용하여 복원된 가상 머신을 만듭니다.

PowerShell을 사용하여 [복원된 디스크에서 새 VM을 만들](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 수도 있습니다.

## <a name="alternative-solution-consistent-snapshots"></a>대체 솔루션: 일관성 있는 스냅샷

Azure Backup을 사용할 수 없는 경우 스냅샷을 사용하여 사용자 고유의 백업 메커니즘을 구현할 수 있습니다. VM에서 사용하는 모든 디스크에 대해 일관성 있는 스냅샷을 만든 다음, 다른 지역에 해당 스냅샷을 복제하는 것은 복잡합니다. 이러한 이유로 Azure는 사용자 지정 솔루션을 빌드하는 것보다 Backup 서비스를 사용하는 것이 더 나은 선택이라고 인식합니다.

디스크에 읽기 액세스 지역 중복 스토리지/지역 중복 스토리지를 사용하는 경우 스냅샷은 자동으로 보조 데이터 센터에 복제됩니다. 디스크에 로컬 중복 스토리지를 사용하는 경우 데이터를 직접 복제해야 합니다. 자세한 내용은 [증분 스냅샷을 사용하여 Azure 관리되지 않는 VM 디스크 백업](windows/incremental-snapshots.md)을 참조하세요.

스냅샷은 특정 시점의 개체 표현입니다. 스냅샷은 저장된 데이터의 증분 크기에 대한 대금 청구를 발생시킵니다. 자세한 내용은 [Blob 스냅샷 만들기](../storage/blobs/snapshots-overview.md)를 참조하세요.

### <a name="create-snapshots-while-the-vm-is-running"></a>VM이 실행되는 동안 스냅샷 만들기

스냅샷은 언제든지 가져올 수 있지만, VM이 실행 중이면 데이터가 디스크로 여전히 스트리밍되고 진행 중에 있던 일부 작업이 스냅샷에 포함될 수 있습니다. 또한 관련된 디스크가 여러 개 있는 경우 서로 다른 디스크의 스냅샷이 서로 다른 시간에 발생했을 수 있습니다. 이러한 시나리오에서는 스냅샷이 불균형 상태가 될 수 있습니다. 특히 백업 중에 변경되면 파일이 손상될 수 있는 스트라이프 볼륨의 경우 이러한 불균형 상태가 문제가 됩니다.

이러한 상황을 방지하려면 백업 프로세스에서 다음 단계를 구현해야 합니다.

1.  모든 디스크를 고정합니다.

1.  보류 중인 모든 쓰기를 플러시합니다.

1.  모든 디스크에 대해 [Blob 스냅샷을 만듭니다](../storage/blobs/snapshots-manage-dotnet.md).

SQL Server와 같은 일부 Windows 애플리케이션에서는 볼륨 섀도 서비스를 통해 조정된 백업 메커니즘을 제공하여 애플리케이션 일치 백업을 만듭니다. Linux에서 디스크를 조정하기 위해 *fsfreeze*와 같은 도구를 사용할 수 있습니다. 이 도구는 애플리케이션에 일관된 스냅샷이 아닌 파일에 일관된 백업을 제공합니다. 이 프로세스는 복잡하므로 [Azure Backup](../backup/backup-azure-vms-introduction.md) 또는 이 절차를 이미 구현한 타사 백업 솔루션을 사용하는 것이 좋습니다.

이전 프로세스를 수행하면 VM의 특정 시점 보기를 나타내는 모든 VM 디스크에 대해 조정된 스냅샷 모음이 만들어집니다. 이 시점은 VM에 대한 백업 복원 지점입니다. 예약된 간격으로 프로세스를 반복하여 정기적으로 백업을 만들 수 있습니다. DR에 대한 스냅샷을 다른 지역으로 복사하는 단계는 [다른 지역에 백업 복사](#copy-the-snapshots-to-another-region)를 참조하세요.

### <a name="create-snapshots-while-the-vm-is-offline"></a>VM이 오프라인 상태인 동안 스냅샷 만들기

일관성 있는 백업을 만드는 또 다른 옵션은 VM을 종료하고 각 디스크의 Blob 스냅샷을 만드는 것입니다. Blob 스냅샷을 만드는 작업은 실행 중인 VM의 스냅샷을 조정하는 것보다 쉽지만 몇 분 정도의 가동 중지 시간이 필요합니다.

1. VM을 종료합니다.

1. 몇 초 안에 각 가상 하드 드라이브 Blob의 스냅샷을 만듭니다.

    스냅샷을 만들려면 [PowerShell](https://docs.microsoft.com/powershell/module/az.storage), [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/) 또는 [.NET용 스토리지 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/hh488361.aspx)와 같은 Azure Storage 클라이언트 라이브러리 중 하나를 사용할 수 있습니다.

1. 가동 중지 시간이 종료된 VM을 시작합니다. 일반적으로 전체 프로세스는 몇 분 안에 완료됩니다.

이 프로세스에서는 모든 디스크에 대해 일관성 있는 스냅샷 모음을 만들어 VM에 대한 백업 복원 지점을 제공합니다.

### <a name="copy-the-snapshots-to-another-region"></a>스냅샷을 다른 지역에 복사

스냅샷을 만드는 데 DR만으로 충분하지 않을 수도 있습니다. 또한 스냅샷 백업을 다른 지역에 복제해야 합니다.

디스크에 지역 중복 스토리지 또는 읽기 액세스 지역 중복 스토리지를 사용하는 경우 스냅샷은 자동으로 보조 지역에 복제됩니다. 복제하기 전에 몇 분 정도가 지연될 수 있습니다. 스냅샷이 복제를 완료하기 전에 기본 데이터 센터가 중단되면 보조 데이터 센터에서 스냅샷에 액세스할 수 없습니다. 이 경우는 거의 발생하지 않습니다.

> [!NOTE]
> 지역 중복 스토리지 또는 읽기 액세스 지역 중복 스토리지 계정에 디스크가 있는 경우에만 VM 재해로부터 VM이 보호되지 않습니다. 또한 조정된 스냅샷을 만들거나 Azure Backup을 사용해야 합니다. 이는 VM을 일관성 있는 상태로 복구하는 데 필요합니다.

로컬 중복 스토리지를 사용하는 경우 스냅샷을 만든 직후 다른 스토리지 계정에 해당 스냅샷을 복사해야 합니다. 복사 대상은 다른 지역에 있는 로컬 중복 스토리지 계정일 수 있으므로 복사본이 원격 지역에 있게 됩니다. 동일한 지역의 읽기 액세스 지역 중복 스토리지 계정에 스냅샷을 복사할 수도 있습니다. 이 경우에 스냅샷은 원격 보조 지역에 지연 복제됩니다. 복사 및 복제가 완료된 후에 기본 사이트의 재해로부터 백업이 보호됩니다.

DR에 대한 증분 스냅샷을 효율적으로 복사하려면 [증분 스냅샷을 사용하여 관리되지 않는 VM 디스크 백업](windows/incremental-snapshots.md)에 있는 지침을 검토하세요.

![증분 스냅샷을 사용하여 Azure 관리되지 않는 VM 디스크 백업][2]

### <a name="recovery-from-snapshots"></a>스냅샷에서 복구

스냅샷을 검색하려면 복사하여 새 Blob을 만듭니다. 기본 계정에서 스냅샷을 복사하는 경우 스냅샷의 기본 Blob에 스냅샷을 복사할 수 있습니다. 이 프로세스는 디스크를 스냅샷으로 되돌립니다. 이 프로세스는 스냅샷 승격이라고 합니다. 읽기 액세스 지역 중복 스토리지 계정의 경우 보조 계정에서 스냅샷 백업을 복사하면 기본 계정에 복사해야 합니다. [PowerShell](https://docs.microsoft.com/powershell/module/az.storage) 또는 AzCopy 유틸리티를 사용하여 스냅샷을 복사할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)을 참조하세요.

여러 디스크가 있는 VM의 경우 조정된 동일한 복원 지점의 일부인 모든 스냅샷을 복사해야 합니다. 쓰기 가능한 VHD Blob에 스냅샷을 복사한 후에 VM에 대한 템플릿을 사용하여 VM을 다시 만드는 데 Blob을 사용할 수 있습니다.

## <a name="other-options"></a>기타 옵션

### <a name="sql-server"></a>SQL Server

VM에서 실행되는 SQL Server에는 SQL Server 데이터베이스를 Azure Blob Storage 또는 파일 공유에 백업하는 자체의 기본 제공 기능이 있습니다. 스토리지 계정이 지역 중복 스토리지 또는 읽기 액세스 지역 중복 스토리지인 경우 재해 발생 시 스토리지 계정의 보조 데이터 센터에 있는 백업에 액세스할 수 있으며 앞에서 설명한 것과 동일한 제한 사항이 적용됩니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server의 백업 및 복원](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md)을 참조하세요. 백업 및 복원 외에도 [SQL Server Always On 가용성 그룹](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md)은 데이터베이스의 보조 복제본을 유지 관리할 수 있습니다. 이 기능을 통해 재해 복구 시간이 크게 줄어듭니다.

## <a name="other-considerations"></a>기타 고려 사항

이 문서에서는 VM 및 해당 디스크의 스냅샷을 백업하거나 가져와서 재해 복구를 지원하고 해당 백업 또는 스냅샷을 사용하여 데이터를 복구하는 방법에 대해 설명했습니다. Azure Resource Manager 모델을 사용하면 Azure에서 많은 사람들이 템플릿을 사용하여 VM 및 기타 인프라를 만들 수 있습니다. 템플릿을 사용하여 매번 동일한 구성을 갖는 VM을 만들 수 있습니다. VM을 만드는 데 사용자 지정 이미지를 사용하는 경우 이미지를 저장할 읽기 액세스 지역 중복 스토리지 계정을 사용하여 해당 이미지를 보호해야 합니다.

따라서 백업 프로세스는 다음 두 항목의 조합이 될 수 있습니다.

- 데이터(디스크)를 백업합니다.
- 구성(템플릿 및 사용자 지정 이미지) 백업

선택한 백업 옵션에 따라 데이터와 구성의 백업을 처리해야 할 수도 있고, 백업 서비스에서 모든 백업을 처리할 수도 있습니다.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>부록: 데이터 중복성의 영향 이해

Azure에 있는 스토리지 계정의 경우 재해 복구와 관련하여 세 가지 유형의 데이터 중복성, 즉 로컬 중복, 지역 중복 또는 읽기 액세스 지역 중복를 고려해야 합니다. 

로컬 중복 스토리지는 동일한 데이터 센터에서 세 개의 데이터 복사본을 유지합니다. VM이 데이터를 작성할 때 성공이 호출자에게 반환되기 전에 세 복사본 모두가 업데이트되어 동일하다는 것을 알 수 있습니다. 세 복사본이 모두 동시에 영향을 받을 가능성이 별로 없기 때문에 디스크가 로컬 오류로부터 보호됩니다. 로컬 중복 스토리지의 경우 지역 중복이 없으므로 전체 데이터 센터 또는 스토리지 유닛에 영향을 줄 수 있는 치명적인 오류로부터 디스크가 보호될 수 없습니다.

지역 중복 스토리지 및 읽기 액세스 지역 중복 스토리지를 사용하면 사용자가 선택한 기본 지역에 데이터의 세 가지 복사본이 유지됩니다. 추가로 세 개의 데이터 복사본이 Azure에서 설정한 해당 보조 지역에 유지됩니다. 예를 들어 미국 서부에 데이터를 저장하면 미국 동부에 데이터가 복제됩니다. 데이터 보존은 비동기적으로 수행되며, 주 지역 및 보조 사이트에 대한 업데이트 사이에 약간의 지연이 있습니다. 보조 사이트의 디스크 복제본은 디스크마다 일관성이 있지만(지연 포함), 여러 활성 디스크의 복제본은 서로 동기화되지 않을 수 있습니다. 여러 디스크에서 일관성 있는 복제본을 유지하려면 일관성 있는 스냅샷이 필요합니다.

지역 중복 스토리지 및 읽기 액세스 지역 중복 스토리지 간의 주요 차이점은 읽기 액세스 지역 중복 스토리지를 사용하면 언제든지 보조 복사본을 읽을 수 있다는 점입니다. 주 지역의 데이터를 액세스할 수 없게 렌더링하는 문제가 있는 경우 Azure 팀은 액세스를 복원하기 위해 모든 노력을 다할 것입니다. 읽기 액세스 지역 중복 스토리지를 사용하도록 설정하면 기본 데이터 센터가 작동 중단되어 있는 동안에도 보조 데이터 센터의 데이터에 액세스할 수 있습니다. 따라서 기본 데이터 센터에 액세스할 수 없는 동안에 복제본을 읽으려는 경우 읽기 액세스 지역 중복 스토리지를 고려해야 합니다.

심각한 중단으로 판명되면 Azure 팀에서 지리적 장애 조치를 트리거하고 기본 DNS 항목을 보조 스토리지를 가리키도록 변경할 수 있습니다. 이 시점에서 지역 중복 스토리지 또는 읽기 액세스 지역 중복 스토리지를 사용하도록 설정한 경우에는 보조 데이터 센터였던 지역의 데이터에 액세스할 수 있습니다. 즉 스토리지 계정이 지역 중복 스토리지이고 문제가 있을 때 지리적 장애 조치가 있는 경우에만 보조 스토리지에 액세스할 수 있습니다.

자세한 내용은 [Azure Storage 중단이 발생할 경우 수행할 작업](../storage/common/storage-disaster-recovery-guidance.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[증분 스냅숏을 사용 하 여 Azure 관리 되지 않는 가상 머신 디스크 백업](linux/incremental-snapshots.md)을 참조 하세요.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png

