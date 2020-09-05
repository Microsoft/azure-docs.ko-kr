---
title: Azure의 SQL Server에 대한 성능 지침 | Microsoft Docs
description: Microsoft Azure Virtual Machines에서 SQL Server 성능을 최적화 하는 방법에 대 한 지침을 제공 합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a2ba89a9adec5443ed8ae2a10e0230874b571f46
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690241"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server에 대 한 성능 지침
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Microsoft Azure Virtual Machines의 SQL Server 성능 최적화에 대 한 지침을 제공 합니다.

## <a name="overview"></a>개요

 Azure Virtual Machines에서 SQL Server를 실행 하는 동안 온-프레미스 서버 환경에서 SQL Server에 적용 되는 것과 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용 하는 것이 좋습니다. 그러나 퍼블릭 클라우드의 관계형 데이터베이스 성능은 가상 머신의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라집니다.

[Azure Portal에서 프로 비전 된 SQL Server 이미지](sql-vm-create-portal-quickstart.md) 는 일반적인 저장소 구성 모범 사례를 따릅니다. 저장소 구성 방법에 대 한 자세한 내용은 [SQL Server vm (가상 컴퓨터)에 대 한 저장소 구성](storage-configuration.md)을 참조 하세요. 프로비전 후에는 이 문서에서 설명하는 다른 최적화를 적용해 보는 방안을 고려해 봅니다. 워크로드에 따라 선택하고 테스트를 통해 확인합니다.

> [!TIP]
> 일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 문서는 Azure Virtual Machines에서 SQL Server에 대 한 *최상의* 성능을 얻는 데 중점을 두었습니다. 작업이 적은 경우 아래 나열된 모든 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="quick-checklist"></a>빠른 검사 목록

다음은 Azure Virtual Machines에서 SQL Server의 최적의 성능을 위한 빠른 검사 목록입니다.

| 영역 | 최적화 |
| --- | --- |
| [VM 크기](#vm-size-guidance) | - [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) 이상 또는 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) 이상과 같은 vCPU가 4개 이상 있는 VM 크기를 사용합니다.<br/><br/> - [Es, Eas, Ds 및 Das 시리즈](../../../virtual-machines/sizes-general.md)는 OLTP 워크로드 성능에 필요한 최적의 메모리 대 vCPU 비율을 제공합니다. <br/><br/> - [M 시리즈](../../../virtual-machines/m-series.md)는 중요 업무용 성능에 필요한 최고 메모리 대 vCPU 비율을 제공하며 데이터 웨어 하우스 워크로드에 이상적입니다. <br/><br/> - [애플리케이션 성능 요구 사항 점검 목록](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)에 따라 사용량이 가장 많은 시간의 대상 워크로드 [IOPS](../../../virtual-machines/windows/premium-storage-performance.md#iops), [처리량](../../../virtual-machines/windows/premium-storage-performance.md#throughput) 및 [대기 시간](../../../virtual-machines/windows/premium-storage-performance.md#latency) 요구 사항을 수집한 후 워크로드의 성능 요구 사항에 맞게 확장할 수 있는 [VM 크기](../../../virtual-machines/sizes-general.md)를 선택합니다.|
| [스토리지](#storage-guidance) | -Azure Virtual Machines에서 SQL Server 성능에 대 한 자세한 테스트 및 TPC_C 벤치 마크에 대 한 자세한 내용은 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)블로그를 참조 하세요. <br/><br/> - 최고의 가격/성능 이점을 위해 [프리미엄 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)를 사용합니다. 데이터 파일에 대한 [ReadOnly 캐시](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)를 구성하고 로그 파일에 대한 캐시는 구성하지 않습니다. <br/><br/> - 워크로드에 1ms 미만의 스토리지 대기 시간이 필요한 경우 [Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk)를 사용합니다. 자세히 알아보려면 [ultra 디스크로 마이그레이션](storage-migrate-to-ultradisk.md) 을 참조 하세요. <br/><br/> - 디스크 유형을 선택하기 전에 [애플리케이션을 모니터링](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)하여 SQL Server 데이터, 로그 및 임시 DB 파일의 스토리지 대기 시간 요구 사항을 수집합니다. 1ms 미만의 스토리지 대기 시간이 필요하면 Ultra Disks를 사용하고, 그렇지 않으면 프리미엄 SSD를 사용합니다. 짧은 대기 시간이 로그 파일에만 필요하고 데이터 파일에는 필요하지 않은 경우 로그 파일에 대해서만 필요한 IOPS 및 처리량 수준에서 [Ultra Disk를 프로비저닝](../../../virtual-machines/disks-enable-ultra-ssd.md)합니다. <br/><br/> -  [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 는 SQL Server 장애 조치 (failover) 클러스터 인스턴스에 대 한 공유 저장소로 권장 됩니다. 프리미엄 파일 공유는 캐싱을 지원하지 않으며 프리미엄 SSD 디스크에 비해 제한된 성능을 제공합니다. 독립 실행형 SQL 인스턴스의 프리미엄 파일 공유보다 프리미엄 SSD 관리 디스크를 선택합니다. 그러나 손쉬운 유지 관리와 유연한 확장성을 위해 장애 조치(failover) 클러스터 인스턴스 공유 스토리지에 프리미엄 파일 공유를 활용합니다. <br/><br/> - 표준 스토리지는 개발 및 테스트 용도나 백업 파일에만 권장되며 프로덕션 워크로드에는 사용하면 안 됩니다. <br/><br/> - [스토리지 계정](../../../storage/common/storage-create-storage-account.md)과 SQL Server VM을 동일한 Azure 지역에 유지합니다.<br/><br/> - 스토리지 계정의 Azure [지역 중복 스토리지](../../../storage/common/storage-redundancy.md)(지역에서 복제)를 사용하지 않도록 설정합니다.  |
| [디스크](#disks-guidance) | - 최소 2개의 [프리미엄 SSD 디스크](../../../virtual-machines/disks-types.md#premium-ssd)를 사용합니다(로그 파일용 1개 및 데이터 파일용 1개). <br/><br/> - 1ms 미만의 IO 대기 시간이 필요한 워크로드의 경우 M 시리즈에 쓰기 가속기를 사용하도록 설정하고 Es 및 DS 시리즈에 Ultra SSD 디스크 사용을 고려합니다. <br/><br/> - 데이터 파일을 호스트하는 디스크에서 [읽기 전용 캐싱](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)을 사용하도록 설정합니다.<br/><br/> - [SQL Server 데이터, 로그 및 TempDB 파일에 대 한 저장소를 구성할](storage-configuration.md) 때 워크 로드에 필요한 것 보다 20% 프리미엄 IOPS/처리량 용량을 추가 합니다. <br/><br/> - 운영 체제 또는 임시 디스크를 데이터베이스 저장 또는 로깅에 사용하지 마세요.<br/><br/> - 로그 파일을 호스팅하는 디스크에서 캐싱을 사용하지 마세요.  **중요**: Azure Virtual Machines 디스크에 대 한 캐시 설정을 변경 하는 경우 SQL Server 서비스를 중지 합니다.<br/><br/> - 스토리지 처리량이 증가하도록 여러 Azure 데이터 디스크를 스트라이프합니다.<br/><br/> - 문서화된 할당 크기로 포맷합니다. <br/><br/> - TempDB는 중요 업무를 위한 SQL Server 워크로드용 로컬 SSD `D:\` 드라이브에 넣습니다(올바른 VM 크기를 선택한 후). Azure Portal 또는 Azure 빠른 시작 템플릿에서 VM을 만들고 [임시 DB를 로컬 디스크에 저장](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) 하는 경우 추가 작업이 필요 하지 않습니다. 다른 모든 경우에는를 다시 시작한 후 오류를 방지 하기 위해  [ssd를 사용 하 여 TempDB를 저장](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) 하기 위한 블로그의 단계를 따르세요. 로컬 드라이브의 용량이 임시 DB 크기에 충분하지 않은 경우 [읽기 전용 캐싱](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)을 사용하여 프리미엄 SSD 디스크에 [스트라이프된](../../../virtual-machines/windows/premium-storage-performance.md) 스토리지 풀에 임시 DB를 넣습니다. |
| [I/O](#io-guidance) |- 데이터베이스 페이지 압축을 사용하도록 설정합니다.<br/><br/> - 데이터 파일에 즉시 파일 초기화를 사용하도록 설정합니다.<br/><br/> - 데이터베이스의 자동 확장을 제한합니다.<br/><br/> - 데이터베이스의 자동 축소를 해제합니다.<br/><br/> - 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.<br/><br/> - SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.<br/><br/> - 기본 백업 및 데이터베이스 파일 위치를 구성합니다.<br/><br/> - [메모리에 잠긴 페이지를 사용하도록 설정합니다](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - SQL Server 성능 픽스를 적용합니다. |
| [기능별](#feature-specific-guidance) | -Azure Blob storage에 직접 백업 합니다.<br/><br/>- 12TB보다 큰 데이터베이스에 [파일 스냅샷 백업](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)을 사용합니다. <br/><br/>- 여러 개의 임시 DB 파일, 코어당 1개의 파일, 최대 8개의 파일을 사용합니다.<br/><br/>- 운영 체제에 대해 최대 서버 메모리를 90% 또는 최대 50GB 정도 남게 설정합니다. <br/><br/>- 소프트 NUMA를 사용하도록 설정합니다. |

이러한 최적화를 수행하는 *방법* 및 *이유*에 대한 자세한 내용은 다음 섹션에 나와 있는 세부 정보 및 지침을 참조하세요.

## <a name="vm-size-guidance"></a>VM 크기 지침

먼저 사용량이 가장 많은 시간의 워크로드 cpu, 메모리 및 스토리지 처리량 요구 사항을 수집합니다. \LogicalDisk\Disk Reads/Sec 및 \LogicalDisk\Disk Writes/Sec 성능 카운터를 사용하여 읽기 및 쓰기 IOPS 요구 사항을 수집하고 \LogicalDisk\Disk Bytes/Sec 카운터를 사용하여 데이터, 로그 및 임시 DB 파일에 대한 [스토리지 처리량 요구 사항](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 수집할 수 있습니다. 사용량이 가장 많은 시간의 IOPS 및 처리량 요구 사항이 정의되면 VM 크기 평가에서 해당 용량을 제공합니다. 예를 들어 사용량이 가장 많은 시간에 20K 읽기 IOPS 및 10K 쓰기 IOPS가 워크로드에 필요한 경우 2개의 P30 디스크와 함께 E16s_v3(최대 32K의 캐시된 IOPS 및 25600의 캐시되지 않은 IOPS) 또는 M16_s(최대 20K의 캐시된 IOPS 및 10K의 캐시되지 않은 IOPS)를 선택할 수 있습니다. VM마다 IOPS 및 처리량에 대한 스케일링 제한이 다르므로 워크로드의 처리량 및 IOPS 요구 사항을 모두 이해해야 합니다.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) 및 [Es_v3 시리즈](../../../virtual-machines/ev3-esv3-series.md)는 Intel Haswell 또는 Broadwell 프로세서를 사용하는 범용 하드웨어에서 호스팅됩니다. [M 시리즈](../../../virtual-machines/m-series.md)는 가장 큰 SQL Server 워크로드에 가장 많은 vCPU 수와 메모리를 제공하며 Skylake 프로세서 제품군의 메모리 최적화 하드웨어에서 호스팅됩니다. 이러한 VM 시리즈는 호스트 수준 읽기 캐시로 최고의 성능을 위해 권장되는 Premium Storage를 지원합니다. Es_v3 및 M 시리즈는 모두 [제한 된 코어 크기](../../../virtual-machines/constrained-vcpu.md)에서 사용할 수 있으며,이를 통해 계산 및 고용량 저장소 용량 요구가 낮은 워크 로드에 대 한 비용을 절감할 수 있습니다. 

## <a name="storage-guidance"></a>스토리지 지침

TPC-E 및 TPC_C 벤치 마크를 사용 하 여 Azure Virtual Machines에서 SQL Server 성능에 대 한 자세한 테스트는 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)블로그를 참조 하세요. 

프리미엄 SSD를 사용하는 Azure Blob 캐시는 모든 프로덕션 워크로드에 권장됩니다. 

> [!WARNING]
> 표준 HDD 및 SSD는 대기 시간 및 대역폭이 다양하므로 개발/테스트 워크로드에만 권장됩니다. 프로덕션 워크로드에는 프리미엄 SSD를 사용해야 합니다.

또한 전송 지연을 줄이기 위해 SQL Server 가상 머신과 동일한 데이터 센터에서 Azure Storage 계정을 만드는 것이 좋습니다. 스토리지 계정을 만들 때 여러 디스크 간에 일관된 쓰기 순서가 보장되지 않기 때문에 지역에서 복제를 사용하지 않도록 설정합니다. 대신 두 Azure 데이터 센터 간에 SQL Server 재해 복구 기술을 구성하는 것이 좋습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)를 참조하세요.

## <a name="disks-guidance"></a>디스크 지침

Azure virtual machines에는 세 가지 주요 디스크 유형이 있습니다.

* **OS 디스크**: Azure 가상 머신을 만들 때 플랫폼은 운영 체제 디스크에 대 한 VM에 하나 이상의 디스크 ( **C** 드라이브로 레이블이 지정 됨)를 연결 합니다. 이 디스크는 스토리지에 페이지 Blob으로 저장된 VHD입니다.
* **임시 디스크**: Azure 가상 머신은 임시 디스크(**D**: 드라이브로 레이블이 지정됨)라는 다른 디스크를 포함합니다. 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.
* **데이터 디스크**: 추가 디스크는 가상 머신에 데이터 디스크로 연결될 수도 있으며 스토리지에 페이지 Blob으로 저장됩니다.

다음 섹션에서는 이러한 서로 다른 디스크를 사용하기 위한 권장 사항을 설명합니다.

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 중인 버전으로 부팅 하 고 탑재할 수 있는 VHD 이며 **C** 드라이브로 레이블이 지정 됩니다.

운영 체제 디스크의 기본 캐싱 정책은 **읽기/쓰기**입니다. 성능이 중요한 애플리케이션의 경우 운영 체제 디스크 대신에 데이터 디스크를 사용하는 것이 좋습니다. 아래의 데이터 디스크에 관한 섹션을 참조하세요.

### <a name="temporary-disk"></a>임시 디스크

**D** 드라이브로 레이블이 지정 된 임시 저장소 드라이브는 Azure Blob storage에 유지 되지 않습니다. 사용자 데이터베이스 파일 또는 사용자 트랜잭션 로그 파일은 **D**: 드라이브에 저장하지 않도록 합니다.

TempDB는 중요 업무를 위한 SQL Server 워크로드용 로컬 SSD `D:\` 드라이브에 넣습니다(올바른 VM 크기를 선택한 후). Azure Portal 또는 Azure 빠른 시작 템플릿에서 VM을 만들고 [임시 DB를 로컬 디스크에 저장](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)하는 경우에는 추가 작업이 필요 하지 않습니다. 다른 모든 경우에는를 다시 시작한 후 오류를 방지 하기 위해  [ssd를 사용 하 여 TempDB를 저장](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) 하기 위한 블로그의 단계를 따르세요. 로컬 드라이브의 용량이 임시 DB 크기에 충분하지 않은 경우 [읽기 전용 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 사용하여 프리미엄 SSD 디스크에 [스트라이프된](../../../virtual-machines/premium-storage-performance.md) 스토리지 풀에 임시 DB를 넣습니다.

프리미엄 SSD를 지원하는 VM의 경우 읽기 캐싱을 사용하도록 설정된 프리미엄 SSD를 지원하는 디스크에도 TempDB를 저장할 수 있습니다.


### <a name="data-disks"></a>데이터 디스크

* **데이터 및 로그 파일에 프리미엄 SSD 디스크 사용**: 디스크 스트라이프를 사용하지 않는 경우 프리미엄 SSD 디스크 2개(로그 파일용 1개 및 데이터용 1개)를 사용합니다. 각 프리미엄 SSD는 [디스크 유형 선택](../../../virtual-machines/disks-types.md) 문서에 설명된 대로 해당 크기에 따라 여러 IOPS 및 대역폭(MB/초)을 제공합니다. 스토리지 공간과 같은 디스크 스트라이프 기술을 사용하는 경우 로그 파일 및 데이터 파일에 대한 다른 두 개의 풀을 소유함으로써 최적의 성능을 얻습니다. 그러나 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)를 사용하려는 경우 하나의 풀을 구성하거나 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md)를 대신 활용해야 합니다.

   > [!TIP]
   > - 다양 한 디스크 및 워크 로드 구성에 대 한 테스트 결과는 [Azure Virtual Machines에서 SQL Server에 대 한 저장소 구성 지침](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)블로그 게시물을 참조 하세요.
   > - ~50,000 IOPS가 필요한 중요 업무용 고성능 SQL Server의 경우 -P30 디스크 10개를 울트라 SSD로 바꾸는 방안을 고려해 보세요. 자세한 내용은 블로그 게시물 [울트라 SSD를 사용하는 중요 업무용 고성능](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)을 참조하세요.

   > [!NOTE]
   > 포털에서 SQL Server VM을 프로비전하는 경우 스토리지 구성을 편집하는 옵션이 있습니다. 구성에 따라 Azure에서는 하나 이상의 디스크를 구성합니다. 여러 디스크를 제거하여 단일 스토리지 풀로 결합합니다. 이 구성에서는 데이터 및 로그 파일이 함께 배치됩니다. 자세한 내용은 [SQL Server VM에 대한 스토리지 구성](storage-configuration.md)을 참조하세요.

* **디스크 스트라이프**: 더 많은 처리량이 필요한 경우 추가 데이터 디스크를 추가 하 고 디스크 스트라이프를 사용할 수 있습니다. 데이터 디스크 수를 확인하려면 로그 파일과 데이터 및 TempDB 파일에 필요한 IOPS 및 대역폭 수를 분석해야 합니다. VM 크기에 따라 IOPs 및 대역폭 수에 대한 제한이 다릅니다. [VM 크기](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)별 IOPS에 대한 표를 참조하세요. 다음 지침을 사용하세요.

  * Windows 8/Windows Server 2012 이상인 경우 다음 지침을 통해 [스토리지 공간](https://technet.microsoft.com/library/hh831739.aspx)을 사용합니다.

      1. 파티션 잘못 맞춤으로 인한 영향이 성능에 미치지 않도록 하려면 OLTP 워크로드에는 64KB(65,536바이트), 데이터 웨어하우징 워크로드에는 256KB(262,144바이트)로 인터리빙(스트라이프 크기)을 설정합니다. 이는 PowerShell로 설정되어야 합니다.
      2. 열 수를 실제 디스크 수로 설정합니다. 8개 이상의 디스크(서버 관리자 UI 아님)를 구성하는 경우 PowerShell을 사용합니다. 

    예를 들어 다음 PowerShell은 인터리브 크기가 64 KB이 고 저장소 풀의 실제 디스크 양과 동일한 열 수를 사용 하 여 새 저장소 풀을 만듭니다.

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 또는 이전 버전에서는 동적 디스크(OS 스트라이프 볼륨)를 사용할 수 있으며 스트라이프 크기는 항상 64KB입니다. 이 옵션은 Windows 8/Windows Server 2012부터 사용되지 않습니다. 자세한 내용은 [가상 디스크 서비스가 Windows 스토리지 관리 API로 전환](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)에 있는 지원 설명을 참조하세요.

  * [SQL Server 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-storage-spaces-direct-manually-configure.md)와 함께 [S2D(스토리지 공간 다이렉트)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)를 사용하는 경우 단일 풀을 구성해야 합니다. 해당 단일 풀에서 여러 볼륨을 만들 수 있지만 모두 동일한 캐싱 정책 등의 동일한 특성을 공유합니다.

  * 예상되는 부하에 따라 스토리지 풀에 연결되는 디스크 수를 결정합니다. VM 크기가 다르면 연결된 데이터 디스크 수도 다를 수 있다는 점에 유의하세요. 자세한 내용은 [가상 컴퓨터의 크기](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조 하세요.

  * Premium Ssd (개발/테스트 시나리오)를 사용 하지 않는 경우 [VM 크기](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 에서 지 원하는 최대 데이터 디스크 수를 추가 하 고 디스크 스트라이프를 사용 하는 것이 좋습니다.

* **캐싱 정책**: 스토리지 구성에 따라 캐싱 정책에 대한 다음 권장 사항을 확인합니다.

  * 데이터 및 로그 파일에 별도의 디스크를 사용하는 경우 데이터 파일 및 TempDB 데이터 파일을 호스트하는 데이터 디스크에서 읽기 캐싱을 사용하도록 설정합니다. 이렇게 하면 성능이 대폭 향상될 수 있습니다. 로그 파일을 보관하는 디스크에서 캐시를 사용하도록 설정하면 성능이 약간 저하되므로 설정하지 마세요.

  * 단일 스토리지 풀에서 디스크 스트라이프를 사용하는 경우 대부분의 워크로드에서 읽기 캐싱의 이점이 있습니다. 로그 및 데이터 파일에 대한 별도 스토리지 풀이 있는 경우 데이터 파일에 대한 스토리지 풀에만 읽기 캐싱을 활성화합니다. 쓰기 워크로드가 대량으로 있는 경우 캐싱을 사용하지 않을 때 성능이 향상될 수 있습니다. 이는 테스트를 통해서만 확인할 수 있습니다.

  * 이전 권장 사항은 프리미엄 SSD에 적용됩니다. 프리미엄 SSD를 사용하지 않는 경우 데이터 디스크에서 캐싱을 사용하도록 설정하지 마세요.

  * 디스크 캐싱 구성에 대한 지침은 다음 문서를 참조하세요. 클래식(ASM) 배포 모델의 경우 다음을 참조하세요. [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 및 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Azure Resource Manager 배포 모델의 경우 다음을 참조하세요. [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 및 [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)

     > [!WARNING]
     > 데이터베이스 손상 가능성을 방지 하기 위해 Azure Virtual Machines 디스크의 캐시 설정을 변경 하는 경우 SQL Server 서비스를 중지 합니다.

* **NTFS 할당 단위 크기**: 데이터 디스크를 포맷할 때 TempDB뿐만 아니라 데이터 및 로그 파일에 대해 64KB 할당 단위 크기를 사용하는 것이 좋습니다. TempDB가 임시 디스크(D:\ 드라이브)에 배치되는 경우 이 드라이브를 활용하여 얻은 성능이 64K 할당 단위 크기에 대한 필요성보다 큽니다. 

* **디스크 관리 모범 사례**: 데이터 디스크를 제거하거나 캐시 유형을 변경할 때 변경하는 동안 SQL Server 서비스를 중지합니다. OS 디스크에 대한 캐싱 설정이 변경되면 Azure는 VM을 중지하고, 캐시 유형을 변경하고 VM을 다시 시작합니다. 데이터 디스크의 캐시 설정이 변경될 경우 변경 중에 VM은 중지되지 않지만 데이터 디스크가 VM에서 분리되었다가 다시 연결됩니다.

  > [!WARNING]
  > 이러한 작업 중에 SQL Server 서비스를 중지하지 못하면 데이터베이스가 손상될 수 있습니다.


## <a name="io-guidance"></a>I/O 지침

* 프리미엄 SSD를 사용하여 최상의 결과를 얻으려면 애플리케이션과 요청을 병렬 처리합니다. 프리미엄 SSD는 IO 큐 크기가 1보다 큰 시나리오에 맞게 설계되었기 때문에 스토리지를 많이 사용하더라도 단일 스레드 직렬 요청에 대한 성능 이점이 거의 없거나 전혀 없습니다. 예를 들어 이는 SQLIO와 같은 성능 분석 도구의 단일 스레드 테스트 결과에 영향을 줄 수 있습니다.

* [데이터베이스 페이지 압축](https://msdn.microsoft.com/library/cc280449.aspx)을 사용하면 I/O가 많은 작업의 성능이 향상될 수 있습니다. 그러나 데이터 압축은 데이터베이스 서버의 CPU 사용량을 늘릴 수 있습니다.

* 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 즉시 파일 초기화를 이용하려면 SE_MANAGE_VOLUME_NAME으로 SQL Server(MSSQLSERVER) 서비스 계정을 부여하고 이를 **볼륨 유지 관리 작업 수행** 보안 정책에 추가합니다. Azure용 SQL Server 플랫폼 이미지를 사용하면 기본 서비스 계정(NT Service\MSSQLSERVER)이 **볼륨 유지 관리 작업 수행** 보안 정책에 추가되지 않습니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](https://msdn.microsoft.com/library/ms175935.aspx)를 참조하세요.

* 자동 **증가** 는 예기치 않은 증가에 대 한 단순한 대체 방법으로 간주 됩니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가를 사용하면 Size 스위치를 사용하여 파일을 사전에 증가시킵니다.

* **자동 축소**를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.

* 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다. 자세한 내용은 [시스템 데이터베이스 이동](https://msdn.microsoft.com/library/ms345408.aspx)을 참조하세요.

* SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다. SQL Server 구성 관리자에서 SQL Server 인스턴스를 마우스 오른쪽 단추로 클릭하고 속성을 선택하여 이를 수행할 수 있습니다. 오류 로그 및 추적 파일 설정은 **시작 매개 변수** 탭에서 변경할 수 있습니다. 덤프 디렉터리는 **고급** 탭에서 지정합니다. 다음 스크린샷에서는 오류 로그 시작 매개 변수를 찾을 위치를 보여 줍니다.

    ![SQL 오류 로그 스크린샷](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 기본 백업 및 데이터베이스 파일 위치를 설정 합니다. 이 문서의 권장 사항을 사용하여 서버 속성 창의 설정을 변경합니다. 지침은 [데이터 및 로그 파일의 기본 위치 보기 또는 변경(SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)을 참조하세요. 다음 스크린샷에서는 이러한 변경 작업을 수행하는 위치를 보여 줍니다.

    ![SQL 데이터 로그 및 Backup 파일](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* 잠긴 페이지를 사용하도록 설정하여 IO 및 페이징 작업을 줄입니다. 자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows)](https://msdn.microsoft.com/library/ms190730.aspx)을 참조하세요.

* SQL Server 2012를 실행 중인 경우 서비스 팩 1 누적 업데이트 10을 설치합니다. 이 업데이트에는 SQL Server 2012에서 임시 테이블에 대한 select 문을 실행할 때 I/O 성능 저하에 대한 픽스가 포함되어 있습니다. 자세한 내용은 [기술 자료 문서](https://support.microsoft.com/kb/2958012)를 참조하세요.

* 모든 데이터 파일은 Azure에서 송수신할 때 압축하는 것이 좋습니다.

## <a name="feature-specific-guidance"></a>기능별 지침

더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음 목록에는 성능 개선에 도움이 되는 일부 SQL Server 기능이 나와 있습니다.

### <a name="back-up-to-azure-storage"></a>Azure Storage에 백업
Azure Virtual Machines에서 실행 되는 SQL Server에 대 한 백업을 수행 하는 경우 [URL에 백업 SQL Server](https://msdn.microsoft.com/library/dn435916.aspx)사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다. Azure Storage에서 백업/복원 하는 경우 [URL에 대 한 백업 모범 사례 및 문제 해결 및 Azure Storage에 저장 된 백업에서 복원에서 SQL Server](https://msdn.microsoft.com/library/jj919149.aspx)제공 하는 권장 사항을 따르세요. [Azure Virtual Machines에서 SQL Server에 대 한 자동화 된 백업을](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)사용 하 여 이러한 백업을 자동화할 수도 있습니다.

SQL Server 2012 이전 버전에서는 [Azure에 SQL Server Backup 도구](https://www.microsoft.com/download/details.aspx?id=40740)를 사용할 수 있습니다. 이 도구는 여러 백업 스트라이프 대상을 사용하여 백업 처리량을 늘릴 수 있습니다.

### <a name="sql-server-data-files-in-azure"></a>Azure의 SQL Server 데이터 파일

SQL Server 2014부터 새로운 기능인 [Azure의 SQL Server 데이터 파일](https://msdn.microsoft.com/library/dn385720.aspx)을 사용할 수 있습니다. Azure에서 데이터 파일로 SQL Server를 실행하면 Azure 데이터 디스크를 사용하는 것과 견줄 만한 성능 특성을 보여 줍니다.

### <a name="failover-cluster-instance-and-storage-spaces"></a>장애 조치(Failover) 클러스터 및 스토리지 공간

저장소 공간을 사용 하는 경우 **확인** 페이지에서 클러스터에 노드를 추가 하는 경우 **클러스터에 사용할 수 있는 모든 저장소를 추가**합니다. 확인란의 선택을 취소 합니다. 

![적격 스토리지 선택 취소](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

만약 스토리지 공간을 사용하면서 **클러스터에 사용할 수 있는 모든 스토리지를 추가하세요.** 확인란을 선택 취소하지 않으면 Windows에서 클러스터링 프로세스 도중 가상 디스크를 분리합니다. 그 결과, 스토리지 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다. 스토리지 공간은 여러 디스크를 스토리지 풀로 그룹화합니다. 자세한 내용은 [스토리지 공간](/windows-server/storage/storage-spaces/overview)을 참조하세요.

## <a name="next-steps"></a>다음 단계

저장소 및 성능에 대 한 자세한 내용은 [Azure의 SQL Server에 대 한 저장소 구성 지침 Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) 을 참조 하세요.

보안 모범 사례는 [Azure Virtual Machines에서 SQL Server에 대 한 보안 고려 사항](security-considerations-best-practices.md)을 참조 하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.
