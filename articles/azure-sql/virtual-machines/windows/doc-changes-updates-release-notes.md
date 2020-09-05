---
title: Azure 가상 머신의 SQL Server 설명서 변경 내용| Microsoft Docs
description: Azure Virtual Machines에서 SQL Server의 다양 한 릴리스에 대 한 새로운 기능 및 향상 된 기능에 대해 알아봅니다.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.openlocfilehash: 5f0ba27a0964f08b32e1af19d6f578b3f41ee300
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006417"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 가상 머신의 SQL Server 설명서 변경 내용
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure에서는 SQL Server 이미지가 기본 제공된 VM(가상 머신)을 배포할 수 있습니다. 이 문서에서는 최신 릴리스의 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 새로운 기능 및 향상된 기능과 관련된 설명서 변경 내용을 요약합니다. 

## <a name="august-2020"></a>2020년 8월

| 변경 | 세부 정보 |
| --- | --- |
| **포털에서 ag 구성** | 이제 [Azure Portal를 통해 가용성 그룹을 구성할](availability-group-azure-portal-configure.md)수 있습니다. 이 기능은 현재 미리 보기로 제공 되며 원하는 지역을 사용할 수 없는 경우 잠시 후에 다시 확인 하세요. | 


## <a name="july-2020"></a>2020년 7월


| 변경 | 세부 정보 |
| --- | --- |
| **로그를 울트라 디스크로 마이그레이션** | 고성능 및 짧은 대기 시간을 활용 하기 위해 [로그 파일을 ultra disk로 마이그레이션하](storage-migrate-to-ultradisk.md) 는 방법에 대해 알아봅니다. | 
| **PowerShell을 사용 하 여 AG 만들기** | 이제 [PowerShell](availability-group-az-commandline-configure.md) 및 Azure CLI를 사용 하 여 가용성 그룹 만들기를 간소화할 수 있습니다. | 


## <a name="june-2020"></a>2020년 6월

| 변경 | 세부 정보 |
| --- | --- |
| **DNN (분산 네트워크 이름)** | Windows Server 2016 +의 SQL Server 2019은 이제 Azure Load Balancer를 사용 하는 대신 [분산 네트워크 이름을](hadr-distributed-network-name-dnn-configure.md) 사용 하 여 fci (장애 조치 (failover) 클러스터 인스턴스)에 대 한 트래픽 라우팅 지원을 미리 볼 수 있습니다. 이 지원은 Azure에서 고가용성 (HA) 솔루션에 대 한 연결을 간소화 하 고 간소화 합니다. | 
| **Azure 공유 디스크를 사용 하는 FCI** | 이제 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md)를 사용 하 여 [fci (장애 조치 (failover) 클러스터 인스턴스)](failover-cluster-instance-overview.md) 를 배포할 수 있습니다. |
| **FCI 문서 재구성** | [Azure vm에서 SQL Server를 사용 하는 장애 조치 (failover) 클러스터 인스턴스에](failover-cluster-instance-overview.md) 대 한 설명서는 명확 하 게 다시 작성 되 고 재구성 되었습니다. [클러스터 구성 모범 사례](hadr-cluster-best-practices.md), [SQL Server fci를 위해 가상 머신을](failover-cluster-instance-prepare-vm.md)준비 하는 방법 및 [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md)를 구성 하는 방법 등 일부 구성 콘텐츠를 분리 했습니다. | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>2020년 5월 

| 변경 | 세부 정보 |
| --- | --- |
| **Azure SQL 제품군** | Azure Virtual Machines SQL Server은 이제 [AZURE SQL 제품군](../../azure-sql-iaas-vs-paas-what-is-overview.md)의 일부입니다. [새로운 모습](../index.yml)을 확인해 보세요! 제품에서 변경 된 내용이 없지만 설명서는 Azure SQL 제품을 보다 쉽게 결정 하는 것을 목표로 합니다. | 


## <a name="january-2020"></a>2020년 1월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure Government 지원** | 이제 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 클라우드에서 호스트 되는 가상 컴퓨터에 대 한 SQL VM 리소스 공급자를 사용 하 여 SQL Server 가상 컴퓨터를 등록할 수 있습니다. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|변경 | 세부 정보 |
 --- | --- |
| **Azure의 무료 DR 복제본** | [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)가 있는 경우 온-프레미스 SQL Server 인스턴스 재해 복구를 위한 [무료 수동 인스턴스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 Azure에서 호스트할 수 있습니다. | 
| **대량 리소스 공급자 등록** | 이제 리소스 공급자를 사용 하 여 SQL Server 가상 컴퓨터를 [대량 등록할](sql-vm-resource-provider-bulk-register.md) 수 있습니다. | 
|**성능 최적화 저장소 구성** | 이제 새 SQL Server VM을 만들 때 [스토리지 구성를 완전히 사용자 지정](storage-configuration.md#new-vms)할 수 있습니다. |
|**FCI용 프리미엄 파일 공유** | 이제 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md)의 원래 방법 대신 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스를 만들 수 있습니다. 
| **Azure Dedicated Host** | [Azure 전용 호스트](dedicated-host.md)에서 SQL Server VM를 실행할 수 있습니다. | 
| **다른 지역으로 마이그레이션 SQL Server VM** | Azure Site Recovery를 사용하여 [SQL Server VM을 한 지역에서 다른 지역으로 마이그레이션](move-sql-vm-different-region.md)할 수 있습니다. |
|  **새 SQL IaaS 설치 모드** | 이제 SQL Server 서비스를 다시 시작하지 않도록 SQL Server IaaS 확장을 [경량 모드](sql-server-iaas-agent-extension-automate-management.md)에서 설치할 수 있습니다.  |
| **SQL Server 버전 수정** | 이제 SQL Server VM에 대한 [버전 속성](change-sql-server-edition.md)을 변경할 수 있습니다. |
| **SQL VM 리소스 공급자에 대 한 변경 내용** | 새 SQL IaaS 모드를 사용하여 [SQL Server VM을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)할 수 있습니다. 이 기능에는 [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) 이미지가 포함됩니다.|
| **Azure 하이브리드 혜택을 사용하는 BYOL 이미지** | Azure Marketplace에서 배포된 BYOL 이미지는 이제 [라이선스 유형을 종량제](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)로 전환할 수 있습니다.| 
| **Azure Portal의 새 SQL Server VM 관리** | 이제 Azure Portal에서 SQL Server VM를 관리하는 새로운 방법이 있습니다. 자세한 내용은 [Azure Portal에서 SQL Server VM 관리](manage-sql-vm-portal.md)를 참조하세요.  | 
| **SQL Server 2008 및 2008 R2에 대 한 확장 된 지원** | Azure VM으로 있는 그대로 마이그레이션하여 SQL Server 2008 및 SQL Server 2008 R2에 대한 [지원을 연장](sql-server-2008-extend-end-of-support.md)합니다. | 
| **사용자 지정 이미지 지원 가능성** | 이제 사용자 지정 OS 및 SQL Server 이미지에 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation) 을 설치 하 여 [유연한 라이선스](licensing-model-azure-hybrid-benefit-ahb-change.md)의 제한 된 기능을 제공 합니다. SQL VM 리소스 공급자를 사용 하 여 사용자 지정 이미지를 등록 하는 경우 라이선스 유형을 "AHUB"로 지정 합니다. 그렇지 않으면 등록이 실패합니다. | 
| **명명된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거되면 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation)을 명명된 인스턴스와 함께 사용할 수 있습니다. | 
| **포털 기능 향상** | SQL Server VM 배포를 위한 Azure Portal 환경이 사용성을 향상하도록 수정되었습니다. SQL Server VM 배포에 대한 자세한 내용은 간략한 [빠른 시작](sql-vm-create-portal-quickstart.md) 또는 보다 자세한 [방법 가이드](create-sql-vm-portal.md)를 참조하세요.|
| **포털 개선** | 이제 [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)을 사용하여 SQL Server VM의 라이선스 모델을 종량제에서 BYOL로 변경할 수 있습니다.|
| **Azure CLI를 통해 SQL Server VM에 대 한 가용성 그룹 배포 간소화** | 이제 Azure에서 SQL Server VM에 가용성 그룹을 배포하기가 더 쉬워졌습니다. [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 를 사용 하 여 명령줄에서 Windows 장애 조치 (failover) 클러스터, 내부 부하 분산 장치 및 가용성 그룹 수신기를 만들 수 있습니다. 자세한 내용은 [Azure CLI를 사용 하 여 AZURE VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성을](availability-group-az-cli-configure.md)참조 하세요. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 변경 | 세부 정보 |
| --- | --- |
|  **SQL Server 클러스터에 대한 새 리소스 공급자** | 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)는 Windows 장애 조치(failover) 클러스터의 메타데이터를 정의합니다. SQL Server VM을 *SqlVirtualMachineGroups*에 조인하면 WSFC(Windows Server 장애 조치(failover) 클러스터) 서비스가 부트스트랩되고 VM이 클러스터에 연결됩니다.  |
| **Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 배포 설정 자동화** |이제 Windows 장애 조치 (failover) 클러스터를 만들고, SQL Server Vm에 연결 하 고, 수신기를 만들고, 두 개의 Azure 빠른 시작 템플릿을 사용 하 여 내부 부하 분산 장치를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-quickstart-template-configure.md)을 참조하세요. | 
| **SQL VM 리소스 공급자에 대한 등록 자동화** | 이번 달 이후에 배포 된 SQL Server Vm은 새 SQL VM 리소스 공급자에 자동으로 등록 됩니다. 이번 달 이전에 배포된 SQL Server VM은 계속해서 수동으로 등록해야 합니다. 자세한 내용은 [Azure의 SQL Server 가상 머신을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)을 참조하세요.|
|**새 SQL VM 리소스 공급자**: |  새 리소스 공급자(Microsoft.SqlVirtualMachine)를 통해 SQL Server VM을 보다 효율적으로 관리할 수 있습니다. VM을 등록하는 방법에 대한 자세한 내용은 [Azure의 SQL Server 가상 머신을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)을 참조하세요. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용하여 SQL Server VM의 라이선스 모델을 종량제와 BYOL 간에 전환할 수 있습니다. 자세한 내용은 [Azure의 SQL Server 가상 머신에 대한 라이선스 모델을 변경하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM에서 SQL Server 프로 비전](create-sql-vm-portal.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)
* [Azure 가상 머신의 SQL Server에 대한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](performance-guidelines-best-practices.md)
* [Azure 가상 머신의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux 가상 머신에서 SQL Server 프로 비전](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ(Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
