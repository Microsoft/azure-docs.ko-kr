---
title: SQL Server 및 Azure Site Recovery를 사용하여 SQL Server에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 SQL Server 및 Azure Site Recovery를 사용하여 SQL Server에 대한 재해 복구를 설정하는 방법을 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 67526eddd19c5869aa54432f963d9b80396f878d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471726"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server에 대한 재해 복구 설정

이 문서에서는 SQL Server BCDR(비즈니스 연속성 및 재해 복구) 기술 및 [Azure Site Recovery](site-recovery-overview.md)를 조합하여 애플리케이션의 SQL Server 백 엔드를 보호하는 방법을 설명합니다.

시작하기 전에 장애 조치(failover) 클러스터링, Always On 가용성 그룹, 데이터베이스 미러링 및 로그 전달을 비롯한 SQL Server 재해 복구 기능을 알고 있어야 합니다.


## <a name="sql-server-deployments"></a>SQL Server 배포

다양한 작업은 SQL Server를 기반으로 사용하고 데이터 서비스를 구현하도록 SharePoint, Dynamics 및 SAP와 같은 앱으로 통합될 수 있습니다.  SQL Server는 다양한 방법으로 배포될 수 있습니다.

* **독립 실행형 SQL Server**: SQL Server 및 모든 데이터베이스는 단일 머신(실제 또는 가상)에서 호스트됩니다. 가상화된 경우 호스트 클러스터링은 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 구현되지 않습니다.
* **SQL Server 장애 조치(failover) 클러스터링 인스턴스(Always On FCI)**: 공유 디스크로 인스턴스화된 SQL Server를 실행하는 둘 이상의 노드가 Windows 장애 조치(failover) 클러스터에 구성됩니다. 노드가 다운되는 경우 클러스터는 다른 인스턴스로 SQL Server를 장애 조치할 수 있습니다. 이 설치 프로그램은 기본 사이트에 고가용성을 구현하는 데 일반적으로 사용됩니다. 이 배포는 공유 저장소 계층에서 정전이나 오류에 대해서는 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 공유 vhdx를 사용하여 구현할 수 있습니다.
* **SQL Always On 가용성 그룹**: 동기 복제 및 자동 장애 조치(failover)를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스가 있는 공유되지 않은 클러스터에서 둘 이상의 노드가 설정됩니다.

  이 문서에서는 원격 사이트에 대한 데이터베이스 복구에 다음과 같은 네이티브 SQL 재해 복구 기술을 활용합니다.

* SQL Server 2012 또는 2014 Enterprise 버전용 재해 복구에 대해 제공하는 SQL Always On 가용성 그룹.
* SQL Server Standard 버전(모든 버전) 또는 SQL Server 2008 R2에 대한 높은 보안 모드에서 SQL 데이터베이스 미러링.

## <a name="site-recovery-support"></a>Site Recovery 지원

### <a name="supported-scenarios"></a>지원되는 시나리오
Site Recovery는 표에 요약된 대로 SQL Server를 보호할 수 있습니다.

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예
**물리적 서버** | 예 | 예
**Azure** |해당 없음| 예

### <a name="supported-sql-server-versions"></a>지원되는 SQL Server 버전
지원되는 시나리오에 대해 이러한 SQL Server 버전이 지원됩니다.

* SQL Server 2016 Enterprise 및 Standard
* SQL Server 2014 Enterprise 및 Standard
* SQL Server 2012 Enterprise 및 Standard
* SQL Server 2008 R2 Enterprise 및 Standard

### <a name="supported-sql-server-integration"></a>지원되는 SQL Server 통합

Site Recovery는 재해 복구 솔루션을 제공하기 위해 표에 요약된 네이티브 SQL Server BCDR 기술과 함께 통합할 수 있습니다.

**기능** | **세부 정보** | **SQL Server** |
--- | --- | ---
**Always On 가용성 그룹** | SQL Server의 여러 독립 실행형 인스턴스는 여러 노드가 있는 장애 조치 클러스터에서 실행됩니다.<br/><br/>SQL Server 인스턴스에서 복사(미러링)할 수 있는 장애 조치 그룹으로 데이터베이스를 그룹화하여 공유 저장소가 필요하지 않습니다.<br/><br/>기본 사이트 및 하나 이상의 보조 사이트 간에 재해 복구를 제공합니다. 동기 복제 및 자동 장애 조치를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스로 공유되지 않은 클러스터에서 두 노드를 설정할 수 있습니다. | SQL Server 2016, SQL Server 2014 및 SQL Server 2012 Enterprise Edition
**장애 조치(failover) 클러스터링(Always On FCI)** | SQL Server는 온-프레미스 SQL Server 작업의 고가용성을 위해 Windows 장애 조치(failover) 클러스터링을 활용합니다.<br/><br/>공유 디스크를 사용하는 SQL Server 서버의 인스턴스를 실행하는 노드는 장애 조치 클러스터에서 구성됩니다. 인스턴스가 다운되는 경우 클러스터는 다른 것을 장애 조치합니다.<br/><br/>클러스터는 공유 저장소의 오류 또는 중단을 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 VHDX와 함께 구현할 수 있습니다. | SQL Server Enterprise 버전<br/><br/>SQL Server Standard 버전(노드가 두 개로 제한됨)
**데이터베이스 미러링(높은 보안 모드)** | 하나의 보조 복사본으로 단일 데이터베이스를 보호합니다. 높은 보안(동기) 및 고성능(비동기) 복제 모드에서 모두 사용할 수 있습니다. 장애 조치 클러스터가 필요하지 않습니다. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise 모든 버전
**독립 실행형 SQL Server** | SQL Server 및 데이터베이스는 단일 서버(실제 또는 가상)에서 호스팅됩니다. 호스트 클러스터링은 가상 서버인 경우 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 없습니다. | Enterprise 또는 Standard 에디션

## <a name="deployment-recommendations"></a>배포 권장 사항

이 표에서는 Site Recovery와 SQL Server BCDR 기술을 통합하기 위한 권장 사항을 요약합니다.

| **버전** | **에디션** | **배포** | **온-프레미스에 온-프레미스** | **온-프레미스에서 Azure로** |
| --- | --- | --- | --- | --- |
| SQL Server 2016, 2014 또는 2012 |Enterprise |장애 조치 클러스터 인스턴스 |Always On 가용성 그룹 |Always On 가용성 그룹 |
|| Enterprise |고가용성을 위한 Always On 가용성 그룹 |Always On 가용성 그룹 |Always On 가용성 그룹 |
|| Standard |장애 조치 클러스터 인스턴스(FCI) |로컬 미러를 사용하는 Site Recovery 복제 |로컬 미러를 사용하는 Site Recovery 복제 |
|| Enterprise 또는 Standard |독립 실행형 |Site Recovery 복제 |Site Recovery 복제 |
| SQL Server 2008 R2 또는 2008 |Enterprise 또는 Standard |장애 조치 클러스터 인스턴스(FCI) |로컬 미러를 사용하는 Site Recovery 복제 |로컬 미러를 사용하는 Site Recovery 복제 |
|| Enterprise 또는 Standard |독립 실행형 |Site Recovery 복제 |Site Recovery 복제 |
| SQL Server(모든 버전) |Enterprise 또는 Standard |장애 조치 클러스터 인스턴스 - DTC 애플리케이션 |Site Recovery 복제 |지원되지 않음 |

## <a name="deployment-prerequisites"></a>배포 필수 조건

* 지원되는 SQL Server 버전을 실행하는 온-프레미스 SQL Server 배포입니다. 일반적으로 SQL Server에 대한 Active Directory도 필요합니다.
* 배포하려는 시나리오를 위한 요구 사항입니다. [Azure로 복제](site-recovery-support-matrix-to-azure.md) 및 [온-프레미스](site-recovery-support-matrix.md) 및 [배포 필수 구성 요소](site-recovery-prereq.md)에 대한 지원 요구 사항에 대해 자세히 알아봅니다.

## <a name="set-up-active-directory"></a>Active Directory 설정

SQL Server를 제대로 실행하려면 보조 복구 사이트에서 Active Directory를 설정합니다.

* **소형 엔터프라이즈**—온-프레미스 사이트에 대한 소량의 애플리케이션 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, Site Recovery 복제를 사용하여 도메인 컨트롤러를 보조 데이터센터 또는 Azure로 복제하는 것이 좋습니다.
* **중형 및 대형 엔터프라이즈**—다량의 애플리케이션 및 Active Directory 포리스트가 있고 애플리케이션 또는 워크로드에 대한 장애 조치를 하려는 경우, 보조 데이터센터 또는 Azure에서 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. Always On 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 복구된 SQL Server 인스턴스에 대해 사용하기 위해 보조 사이트 또는 Azure에서 다른 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다. Site Recovery를 사용한 Active Directory 보호에 대해 [자세히 알아보세요](site-recovery-active-directory.md).


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Azure로 복제하기 위한 SQL Server Always On과 통합

수행해야 할 사항:

1. Azure Automation 계정에 스크립트를 가져옵니다. 여기에는 [Resource Manager 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) 및 [클래식 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)에서 SQL 가용성 그룹의 장애 조치(failover) 스크립트가 포함됩니다.

    [![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. ASR-SQL-FailoverAG를 복구 계획의 첫 번째 그룹에 대한 이전 작업으로 추가합니다.

1. 스크립트에서 사용할 수 있는 지침에 따라 가용성 그룹의 이름을 제공하는 자동화 변수를 만듭니다.

### <a name="steps-to-do-a-test-failover"></a>테스트 장애 조치(failover)를 수행하는 단계

SQL Always On은 테스트 장애 조치(failover)를 고유하게 지원하지 않습니다. 따라서 다음 작업을 수행하는 것이 좋습니다.

1. Azure에서 가용성 그룹 복제본을 호스팅하는 가상 머신에서 [Azure Backup](../backup/backup-azure-arm-vms.md)을 설정합니다.

1. 복구 계획의 테스트 장애 조치를 트리거하기 전에 이전 단계에서 수행된 백업에서 가상 머신을 복구합니다.

    ![Azure Backup에서 복원](./media/site-recovery-sql/restore-from-backup.png)

1. 백업에서 복원된 가상 머신에 [쿼럼을 강제](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)합니다.

1. 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 IP에 수신기의 IP를 업데이트합니다.

    ![수신기 IP 업데이트](./media/site-recovery-sql/update-listener-ip.png)

1. 수신기를 온라인 상태로 만듭니다.

    ![수신기를 온라인 상태로 만들기](./media/site-recovery-sql/bring-listener-online.png)

1. 각 가용성 그룹 수신기에 해당하는 프런트 엔드 IP 풀에서 만든 IP와 백 엔드 풀에 추가된 SQL 가상 머신을 통해 부하 분산 장치를 만듭니다.

     ![부하 분산 장치 만들기 - 프런트 엔드 IP 풀](./media/site-recovery-sql/create-load-balancer1.png)

    ![부하 분산 장치 만들기 - 백 엔드 풀](./media/site-recovery-sql/create-load-balancer2.png)

1. 복구 계획에 대해 테스트 장애 조치(failover)를 수행합니다.

### <a name="steps-to-do-a-failover"></a>장애 조치(failover)를 수행하는 단계

복구 계획에 스크립트를 추가하고 테스트 장애 조치(failover)를 수행하여 복구 계획의 유효성을 검사했으면 복구 계획의 장애 조치(failover)를 수행할 수 있습니다.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>복제를 위해 SQL Server Always On을 보조 온-프레미스 사이트로 통합

SQL Server가 고가용성을 위해 고가용성 그룹(또는 FCI)을 사용하는 경우, 복구 사이트에서도 고가용성 그룹을 사용하는 것이 좋습니다. 이는 분산된 트랜잭션을 사용하지 않는 앱에 적용됩니다.

1. [데이터베이스를 구성](https://msdn.microsoft.com/library/hh213078.aspx) 합니다.
1. 보조 사이트에서 가상 네트워크를 만듭니다.
1. 가상 네트워크 및 기본 사이트 사이에서 사이트 간 VPN 연결을 설정합니다.
1. 복구 사이트에서 가상 컴퓨터를 만들고 SQL Server를 설치합니다.
1. 기존의 Always On 가용성 그룹을 새 SQL Server VM으로 확장합니다. 이 SQL Server 인스턴스를 비동기 복제 복사본으로 구성합니다.
1. 가용성 그룹 수신기를 만들거나 비동기 복제 가상 머신을 포함하도록 기존 수신기를 업데이트합니다.
1. 수신기를 사용하여 애플리케이션 팜이 설정되었는지 확인합니다. 데이터베이스 서버 이름을 사용하여 설정하는 경우, 수신기를 사용하도록 업데이트하여 장애 조치 후에 이를 다시 인식할 필요가 없도록 합니다.

분산된 트랜잭션을 사용하는 애플리케이션의 경우 [VMware/물리적 서버 사이트 간 복제](site-recovery-vmware-to-vmware.md)로 Site Recovery를 배포하는 것이 좋습니다.

### <a name="recovery-plan-considerations"></a>복구 계획 고려 사항
1. 이 샘플 스크립트를 기본 및 보조 사이트의 VMM 라이브러리에 추가합니다.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. 애플리케이션에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 스크립트를 호출하는 Group-1에 대한 사전 작업 스크립트 단계를 추가합니다.

## <a name="protect-a-standalone-sql-server"></a>독립 실행형 SQL Server 보호

이 시나리오에서 SQL Server 컴퓨터를 보호하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다. 정확한 단계는 SQL Server가 VM 또는 물리적 서버인지에 따라, Azure 또는 보조 온-프레미스 사이트로 복제하려는지에 따라 달라집니다. [Site Recovery 시나리오](site-recovery-overview.md)에 대해 자세히 알아봅니다.

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>SQL Server 클러스터 보호(표준 에디션/Windows Server 2008 R2)

SQL Server Standard 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다.

### <a name="on-premises-to-on-premises"></a>온-프레미스 간

* 앱이 분산된 트랜잭션을 사용하는 경우 Hyper-V 환경에서는 [SAN 복제로 Site Recovery](site-recovery-vmm-san.md)를 배포하거나 VMware 환경에서는 [VMware/물리적 서버에서 VMware로](site-recovery-vmware-to-vmware.md) 배포하는 것이 좋습니다.
* DTC가 아닌 애플리케이션인 경우 로컬의 높은 보안 DB 미러를 활용하여 클러스터를 독립 실행형 서버로 복구하려면 위의 접근 방식을 사용합니다.

### <a name="on-premises-to-azure"></a>온-프레미스와 Azure 간

Azure로 복제할 때 Site Recovery는 게스트 클러스터 지원을 제공하지 않습니다. 또한 SQL Server는 Standard 에디션에 대해 저비용 재해 복구 솔루션을 제공하지 않습니다. 이 시나리오에서 독립 실행형 SQL Server에 온-프레미스 SQL Server 클러스터를 보호하고 이를 Azure에서 복구하는 것이 좋습니다.

1. 온-프레미스 사이트에 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.
1. 보호하려는 데이터베이스에 대한 미러로 제공하기 위해 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.
1. [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 또는 [VMware VM/물리적 서버](site-recovery-vmware-to-azure-classic.md)에 대해 온-프레미스 사이트에서 Site Recovery를 구성합니다.
1. Azure에 새 SQL Server 인스턴스를 복제하도록 Site Recovery 복제를 사용합니다. 높은 보안 미러 복사이므로 기본 클러스터와 동기화되지만 Site Recovery 복제를 사용하여 Azure에 복제됩니다.


![표준 클러스터](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server Standard 클러스터의 경우, 계획되지 않은 장애 조치 후의 장애 복구는 SQL Server 백업이 필요하며 미러의 재설정으로 미러 인스턴스에서 원래 클러스터로 복구해야 합니다.

## <a name="next-steps"></a>다음 단계
Site Recovery 아키텍처에 대해 [자세히 알아봅니다](site-recovery-components.md).
