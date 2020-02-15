---
title: Azure Virtual Machines에서 SQL Server에 대 한 설명서 변경 내용 Microsoft Docs
description: Azure VM에서 SQL Server에 대 한 새로운 기능 및 향상 된 기능에 대해 알아봅니다.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201648"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server에 대 한 설명서 변경

Azure를 사용 하면 기본 제공 SQL Server 이미지를 사용 하 여 VM (가상 머신)을 배포할 수 있습니다. 이 문서에서는 [Azure Virtual Machines에서 최신 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)릴리스의 새로운 기능 및 향상 된 기능에 관련 된 설명서 변경 내용을 요약 합니다. 


## <a name="january-2020"></a>2020년 1월

| 변경 | 세부 정보 |
| --- | --- |
| **지원 Azure Government** | 이제 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 클라우드에서 호스트 되는 가상 컴퓨터에 대 한 SQL VM 리소스 공급자를 사용 하 여 SQL Server 가상 컴퓨터를 등록할 수 있습니다. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|변경 | 세부 정보 |
 --- | --- |
| **Azure에서 무료 DR 복제본** | [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)있는 경우 온-프레미스 SQL Server 인스턴스에 대해 Azure에서 재해 복구를 위한 [무료 수동 인스턴스](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) 를 호스트할 수 있습니다. | 
| **대량 리소스 공급자 등록** | 이제 리소스 공급자를 사용 하 여 SQL 가상 머신을 [대량 등록할](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) 수 있습니다. | 
|**성능 최적화 저장소 구성** | 이제 새 SQL Server VM를 만들 때 [저장소 구성을 완전히 사용자 지정할](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) 수 있습니다. |
|**FCI 용 프리미엄 파일 공유** | 이제 [스토리지 공간 다이렉트](virtual-machines-windows-portal-sql-create-failover-cluster.md)의 원래 방법 대신 [프리미엄 파일 공유](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) 를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스를 만들 수 있습니다. 
| **Azure 전용 호스트** | [Azure 전용 호스트](virtual-machines-windows-sql-dedicated-host.md)에서 SQL Server VM를 실행할 수 있습니다. | 
| **다른 지역으로 SQL VM 이동** | Azure Site Recovery를 사용 하 여 [SQL Server VM 한 지역에서 다른 지역으로 마이그레이션할](virtual-machines-windows-sql-move-different-region.md)수 있습니다. |
|  **새 SQL IaaS 설치 모드** | 이제 SQL Server IaaS 확장을 [경량 모드로](virtual-machines-windows-sql-server-agent-extension.md) 설치 하 여 SQL Server 서비스를 다시 시작 하지 않도록 할 수 있습니다.  |
| **SQL Server 버전 수정** | 이제 SQL Server VM의 [버전 속성](virtual-machines-windows-sql-change-edition.md) 을 변경할 수 있습니다. |
| **SQL VM 리소스 공급자에 대 한 변경 내용** | 새 SQL IaaS 모드를 사용 하 여 [SQL VM 리소스 공급자에 SQL Server VM를 등록할](virtual-machines-windows-sql-register-with-resource-provider.md) 수 있습니다. 이 기능에는 [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) 이미지가 포함 됩니다.|
| **Azure 하이브리드 혜택를 사용 하 여 사용자 라이선스 이미지 가져오기** | Azure Marketplace에서 배포 된 사용자 고유의 라이선스 이미지는 이제 [라이선스 유형을 종 량](virtual-machines-windows-sql-ahb.md#remarks)제로 전환할 수 있습니다.| 
| **Azure Portal의 새 SQL Server VM 관리** | 이제 Azure Portal에서 SQL Server VM를 관리 하는 방법이 있습니다. 자세한 내용은 [Azure Portal에서 SQL Server Vm 관리](virtual-machines-windows-sql-manage-portal.md)를 참조 하세요.  | 
| **SQL Server 2008/2008 R2에 대 한 확장 된 지원** | *를* Azure VM으로 마이그레이션하여 SQL Server 2008 및 SQL Server 2008 r 2에 대 한 [지원을 확장](virtual-machines-windows-sql-server-2008-eos-extend-support.md) 합니다. | 
| **사용자 지정 이미지 지원 가능성** | 이제 [유연한 라이선스](virtual-machines-windows-sql-ahb.md)의 제한 된 기능을 제공 하는 사용자 지정 OS 및 SQL 이미지에 [SQL Server IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md#installation) 을 설치할 수 있습니다. SQL 리소스 공급자를 사용 하 여 사용자 지정 이미지를 등록 하는 경우 라이선스 유형을 "AHUB"로 지정 합니다. 그렇지 않으면 등록이 실패 합니다. | 
| **명명 된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거 된 경우 명명 된 인스턴스를 사용 하 여 [SQL Server IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md#installation) 을 사용할 수 있습니다. | 
| **포털 기능 향상** | SQL Server VM 배포에 대 한 Azure Portal 환경은 유용성을 개선 하기 위해 개선 된 되었습니다. 자세한 내용은 간략 한 [빠른](quickstart-sql-vm-create-portal.md) 시작 및 더 철저 한 [방법 가이드](virtual-machines-windows-portal-sql-server-provision.md) 를 참조 하 여 SQL Server VM 배포를 참조 하세요.|
| **포털 개선** | 이제 [Azure Portal](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)를 사용 하 여 사용자 라이선스를 사용 하 여 SQL Server VM에 대 한 라이선스 모델을 종 량 제로 변경할 수 있습니다.|
| **Azure SQL Server VM CLI를 사용 하 여 가용성 그룹 배포 간소화** | 이제 가용성 그룹을 Azure의 SQL Server VM에 배포 하는 것이 더 쉬워졌습니다. [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 를 사용 하 여 명령줄에서 Windows 장애 조치 (failover) 클러스터, 내부 부하 분산 장치 및 가용성 그룹 수신기를 만들 수 있습니다. 자세한 내용은 azure [SQL SERVER VM CLI를 사용 하 여 AZURE VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성을](virtual-machines-windows-sql-availability-group-cli.md)참조 하세요. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 변경 | 세부 정보 |
| --- | --- |
|  **SQL Server 클러스터에 대 한 새 리소스 공급자** | 새 리소스 공급자 (SqlVirtualMachine/SqlVirtualMachineGroups)는 Windows 장애 조치 (failover) 클러스터의 메타 데이터를 정의 합니다. WSFC (Windows Server 장애 조치 (Failover) 클러스터) 서비스를 부트스트랩 *Sqlvirtualmachinegroups* 에 SQL Server VM를 조인 하 고 VM을 클러스터에 조인 합니다.  |
| **Azure 빠른 시작 템플릿을 사용 하 여 가용성 그룹 배포의 자동 설정** |이제 Windows 장애 조치 (failover) 클러스터를 만들고, SQL Server Vm에 연결 하 고, 수신기를 만들고, 두 개의 Azure 빠른 시작 템플릿을 사용 하 여 내부 부하 분산 장치를 구성할 수 있습니다. 자세한 내용은 azure 빠른 시작 [템플릿을 사용 하 여 AZURE VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성을](virtual-machines-windows-sql-availability-group-quickstart-template.md)참조 하세요. | 
| **SQL VM 리소스 공급자에 대 한 자동 등록** | 이번 달 이후에 배포되는 SQL Server VM은 새로운 SQL Server 리소스 공급자에 자동으로 등록됩니다. 이 달 이전에 배포 된 SQL Server Vm은 수동으로 등록 해야 합니다. 자세한 내용은 [SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server virtual Machine 등록](virtual-machines-windows-sql-register-with-resource-provider.md)을 참조 하세요.|
|**새 SQL VM 리소스 공급자**: |  새 리소스 공급자 (SqlVirtualMachine)를 통해 SQL Server Vm을 보다 효율적으로 관리할 수가 있습니다. Vm을 등록 하는 방법에 대 한 자세한 내용은 [SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server virtual Machine 등록](virtual-machines-windows-sql-register-with-resource-provider.md)을 참조 하세요. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용 하 여 SQL Server VM에 대 한 사용량 단위 및 사용자 고유의 라이선스 모델을 전환할 수 있습니다. 자세한 내용은 [Azure에서 SQL Server 가상 컴퓨터에 대 한 라이선스 모델을 변경 하는 방법](virtual-machines-windows-sql-ahb.md)을 참조 하세요. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines에서 SQL Server에 대 한 응용 프로그램 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux 가상 머신 프로 비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
