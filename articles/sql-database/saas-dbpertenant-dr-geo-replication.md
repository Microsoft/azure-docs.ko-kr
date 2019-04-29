---
title: Azure SQL Database 지역에서 복제를 사용하여 SaaS 앱 재해 복구 | Microsoft Docs
description: 가동 중단 시 Azure SQL Database 지역에서 복제를 사용하여 다중 테넌트 SaaS 앱을 복구하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b6f0d25f621768f79e8262f38617152e91692a23
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129859"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>데이터베이스 지역에서 복제를 사용하여 다중 테넌트 SaaS 애플리케이션 재해 복구

이 자습서에서는 테넌트별 데이터베이스 모델을 사용하여 구현된 다중 테넌트 SaaS 애플리케이션에 대한 전체 재해 복구 시나리오를 살펴봅니다. 작동 중단으로부터 앱을 보호하려면 [_지역에서 복제_](sql-database-geo-replication-overview.md)를 사용하여 대체 복구 지역에서 카탈로그 및 테넌트 데이터베이스에 대한 복제본을 만듭니다. 작동 중단이 발생할 경우 이러한 복제본으로 신속하게 장애 조치(Failover)하여 정상적인 비즈니스 작업을 재개합니다. 장애 조치(Failover) 시 원래 지역의 데이터베이스는 복구 지역에 있는 데이터베이스의 보조 복제본이 됩니다. 이러한 복제본은 다시 온라인 상태가 되면 복구 지역에 있는 데이터베이스의 상태를 자동으로 따라잡습니다. 작동 중단이 해결되면 원래 프로덕션 지역의 데이터베이스로 장애 복구(Failback)합니다.

이 자습서에서는 장애 조치(Failover) 및 장애 복구(Failback) 워크플로에 대해 알아봅니다. 이 문서에서 배울 내용은 다음과 같습니다.
> [!div class="checklist"]
> 
> * 데이터베이스 및 탄력적 풀 구성 정보를 테넌트 카탈로그 동기화
> * 애플리케이션, 서버 및 풀로 구성된 대체 지역에서 복구 환경 설정
> * _지역에서 복제_를 사용하여 카탈로그 및 테넌트 데이터베이스를 복구 지역으로 복제
> * 애플리케이션 및 카탈로그와 테넌트 데이터베이스를 복구 지역으로 장애 조치(Failover) 
> * 나중에 애플리케이션, 카탈로그 및 테넌트 데이터베이스를 원래 지역으로 다시 장애 복구(Failback)
> * 각 테넌트 데이터베이스가 장애 조치(Failover)될 때 카탈로그를 업데이트하여 각 테넌트 데이터베이스의 기본 위치 추적
> * 대기 시간을 줄이기 위해 애플리케이션 및 기본 테넌트 데이터베이스가 항상 동일한 Azure 지역에 배치되는지 확인  
 

이 자습서를 시작하기 전에 먼저 다음 필수 조건이 완료되었는지 확인합니다.
* 테넌트 앱별 Wingtip Tickets SaaS 데이터베이스가 배포되어 있습니다. 5분 안에 배포를 마치려면 [테넌트 애플리케이션별로 Wingtip Tickets SaaS 데이터베이스 배포 및 살펴보기](saas-dbpertenant-get-started-deploy.md)를 참조하세요.  
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>지역에서 복제 복구 패턴 소개

![복구 아키텍처](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
DR(재해 복구)은 규정 준수 이유 또는 비즈니스 연속성 여부에 관계없이 많은 애플리케이션에서 중요한 고려 사항입니다. 서비스 중단 시간이 길어지면 잘 준비된 DR 계획으로 비즈니스 중단을 최소화할 수 있습니다. 지역에서 복제를 사용하면 장애 조치(Failover)될 수 있는 복구 지역에 데이터베이스 복제본이 바로 유지되므로 가장 낮은 RPO 및 RTO가 제공됩니다.

지역에서 복제를 기준으로 하는 DR 계획은 다음 세 가지 개별 부분으로 구성됩니다.
* 설정 - 복구 환경 생성 및 유지 관리
* 복구 - 작동 중단 발생 시 앱 및 데이터베이스를 복구 환경으로 장애 조치(Failover)
* 송환 - 애플리케이션이 확인되면 앱 및 데이터베이스를 다시 원래 지역으로 장애 조치(Failover) 

모든 부분을 신중히 고려해야 하며 대규모로 운영하는 경우에는 특히 신중해야 합니다. 전반적으로 이 계획은 다음과 같은 몇 가지 목표를 달성해야 합니다.

* 설정
    * 복구 지역에서 미러 이미지 환경을 설정하고 유지 관리합니다. 이 복구 환경에서 탄력적 풀을 만들고 데이터베이스를 복제하면 복구 지역의 용량이 절약됩니다. 이러한 환경의 유지 관리에는 새 테넌트 데이터베이스가 프로비전될 때 복제하는 작업도 포함됩니다.  
* 복구
    * 일일 비용을 최소화하기 위해 규모가 축소된 복구 환경을 사용하는 경우 풀 및 데이터베이스를 강화하여 복구 지역의 전체 운영 용량을 확보해야 합니다.
    * 가능한 한 빠르게 복구 지역에서 새 테넌트 프로비전을 사용하도록 설정합니다.  
    * 우선 순위에 따라 테넌트를 복원할 수 있도록 최적화합니다.
    * 실제로 어디서든 단계를 가능한 한 빨리 병렬로 수행하여 테넌트가 최대한 빨리 온라인 상태가 되도록 최적화합니다.
    * 실패, 다시 시작 가능 및 멱등원(idempotent)에 대한 복원력이 있어야 합니다.
    * 원래 지역이 다시 온라인 상태가 되면 프로세스를 도중에 취소할 수 있어야 합니다.
* 송환 
    * 테넌트에 최소한의 영향만 미치고, 데이터 손실을 발생하지 않고, 테넌트당 오프라인 기간을 최소화하면서 복구 지역의 데이터베이스를 원래 지역의 복제본으로 장애 조치(Failover)합니다.   

이 자습서에서는 Azure SQL Database 및 Azure 플랫폼의 기능을 사용하여 다음 과제를 해결합니다.

* [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) - 필요한 모든 용량을 최대한 빨리 예약합니다. Azure Resource Manager 템플릿은 복구 지역에 프로덕션 서버와 탄력적 풀의 미러 이미지를 프로비전하는 데 사용됩니다.
* [지역에서 복제](sql-database-geo-replication-overview.md) - 모든 데이터베이스에 대해 비동기적으로 복제된 읽기 전용 보조 데이터베이스를 만듭니다. 작동 중단이 발생하는 동안 복구 지역의 복제본으로 장애 조치(Failover)합니다.  작동 중단이 해결되면 데이터 손실 없이 원래 지역의 데이터베이스로 장애 복구(Failback)합니다.
* [비동기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) - 많은 수의 데이터베이스에 대한 장애 조치(Failover) 시간을 최소화하기 위해 테넌트 우선 순위에 따라 장애 조치(Failover) 작업이 전송됩니다.
* [분할된 관리 복구 기능](sql-database-elastic-database-recovery-manager.md) - 복구 및 송환하는 동안 카탈로그의 데이터베이스 항목을 변경합니다. 이러한 기능을 사용하면 앱을 다시 구성하지 않고도 위치에 관계없이 테넌트 데이터베이스에 연결할 수 있습니다.
* [SQL Server DNS 별칭](dns-alias-overview.md) - 앱이 작동되는 지역에 관계없이 새 테넌트를 원활하게 프로비전할 수 있도록 합니다. 카탈로그 동기화 프로세스에서 해당 위치에 관계없이 활성 카탈로그에 연결할 수 있도록 하기 위해 DNS 별칭도 사용됩니다.

## <a name="get-the-disaster-recovery-scripts"></a>재해 복구 스크립트 가져오기 

> [!IMPORTANT]
> 모든 Wingtip Tickets 관리 스크립트와 마찬가지로, DR 스크립트는 샘플 품질이며 프로덕션 환경에서 사용할 수 없습니다. 

이 자습서에서 사용되는 복구 스크립트와 Wingtip 애플리케이션 소스 코드는 [테넌트별 Wingtip Tickets SaaS 데이터베이스 GitHub 리포지토리](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)에서 사용할 수 있습니다. Wingtip Tickets 관리 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인합니다.

## <a name="tutorial-overview"></a>자습서 개요
이 자습서에서는 먼저 지역에서 복제를 사용하여 다른 지역에 Wingtip Tickets 애플리케이션과 해당 데이터베이스의 복제본을 만듭니다. 그런 다음, 이 지역으로 장애 조치(Failover)하여 작동 중단에서 복구하는 과정을 시뮬레이트합니다. 완료되면 애플리케이션은 복구 지역에서 완벽하게 작동합니다.

나중에, 별도의 송환 단계에서 복구 지역의 카탈로그 및 테넌트 데이터베이스를 원래 지역으로 장애 조치(Failover)합니다. 애플리케이션 및 데이터베이스는 송환이 진행되는 동안 계속 사용할 수 있습니다. 완료되면 애플리케이션은 원래 지역에서 완벽하게 작동합니다.

> [!Note]
> 애플리케이션은 해당 애플리케이션이 배포된 지역과 _쌍을 이루는 지역_에 복구됩니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

## <a name="review-the-healthy-state-of-the-application"></a>애플리케이션의 정상 상태 검토

복구 프로세스를 시작하기 전에 애플리케이션의 정상 상태를 검토합니다.
1. 웹 브라우저에서 Wingtip Tickets 이벤트 허브(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net - &lt;user&gt;를 사용자 배포의 사용자 값으로 바꿈)를 엽니다.
    * 페이지 아래쪽으로 스크롤하여 바닥글에서 카탈로그 서버 이름과 위치를 확인합니다. 위치는 앱을 배포한 지역입니다.
    *팁: 위치 위를 마우스로 가리키면 해당 위치가 크게 표시됩니다.*
    ![원래 지역의 이벤트 허브 정상 상태](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Contoso Concert Hall 테넌트를 클릭하고 해당 이벤트 페이지를 엽니다.
    * 바닥글에서 테넌트 서버 이름을 확인합니다. 위치는 카탈로그 서버의 위치와 동일합니다.

3. [Azure Portal](https://portal.azure.com)에서 앱이 배포된 리소스 그룹을 엽니다.
    * 서버가 배포된 지역을 확인합니다. 

## <a name="sync-tenant-configuration-into-catalog"></a>카탈로그로 테넌트 구성 동기화

이 작업에서는 서버, 탄력적 풀 및 데이터베이스의 구성을 테넌트 카탈로그로 동기화하는 프로세스를 시작합니다. 이 프로세스에서는 이 정보가 카탈로그에 최신 상태로 유지됩니다.  또한 원래 지역에 있든, 복구 지역에 있든 관계없이 활성 카탈로그가 프로세스에서 사용됩니다. 구성 정보는 복구 환경이 원래 환경과 일치하고, 나중에 송환 동안에는 원래 지역이 복구 환경에서 수행된 변경 내용과 일치하도록 복구 프로세스의 일부로 사용됩니다. 테넌트 리소스의 복구 상태를 추적하는 데 카탈로그도 사용됩니다.

> [!IMPORTANT]
> 간단히 하기 위해 동기화 프로세스 및 다른 장기 실행 복구 / 송환 프로세스가 클라이언트 사용자 로그인에서 실행 되는 세션 또는 로컬 PowerShell 작업으로이 자습서에서 구현 됩니다. 로그인할 때 발급한 인증 토큰은 몇 시간 후에 만료되고 작업이 실패합니다. 프로덕션 시나리오에서 장기 실행 프로세스는 서비스 주체에서 실행되는 어떤 종류의 신뢰할 수 있는 Azure 서비스로 구현되어야 합니다. [Azure PowerShell을 사용하여 인증서로 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)를 참조하세요.

1. _PowerShell ISE_에서 ...\Learning Modules\UserConfig.psm1 파일을 엽니다. 10 및 11번 줄의 `<resourcegroup>` 및 `<user>`를 앱을 배포할 때 사용한 값으로 바꿉니다.  파일을 저장합니다.

2. *PowerShell ISE*에서 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트를 열고 다음을 설정합니다.
    * **$DemoScenario = 1** - 테넌트 서버를 동기화하는 백그라운드 작업을 시작하고 구성 정보를 카탈로그에 풀링합니다.

3. **F5** 키를 눌러 동기화 스크립트를 실행합니다. 테넌트 리소스의 구성을 동기화하기 위해 새 PowerShell 세션이 열립니다.
![동기화 프로세스](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

PowerShell 창을 백그라운드에서 실행 중인 상태로 두고 이 자습서의 나머지 부분을 계속 진행합니다. 

> [!Note]
> 동기화 프로세스는 DNS 별칭을 통해 카탈로그에 연결합니다. 이 별칭은 활성 카탈로그를 가리키도록 복원 및 송환 중에 수정됩니다. 동기화 프로세스는 복구 지역에서 수행된 데이터베이스 또는 풀 구성 변경 내용을 적용하여 카탈로그를 최신 상태로 유지합니다.  이러한 변경 내용은 송환 중에 원래 지역의 해당 리소스에 적용됩니다.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>복구 지역에 보조 데이터베이스 복제본 만들기

이 작업에서는 중복된 앱 인스턴스를 배포하고, 카탈로그 및 모든 테넌트 데이터베이스를 복구 지역에 복제하는 프로세스를 시작합니다.

> [!Note]
> 이 자습서는 지역에서 복제 보호 기능을 Wingtip Tickets 샘플 애플리케이션에 추가합니다. 지역에서 복제를 사용하는 애플리케이션에 대한 프로덕션 시나리오에서 각 테넌트는 처음부터 지역에서 복제된 데이터베이스로 프로비전됩니다. [Azure SQL Database를 사용하여 항상 사용 가능한 서비스 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)을 참조하세요.

1. *PowerShell ISE*에서 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트를 열고 다음 값을 설정합니다.
    * **$DemoScenario = 2**, 미러 이미지 복구 환경을 만들고 카탈로그 및 테넌트 데이터베이스를 복제합니다.

2. **F5** 키를 눌러 스크립트를 실행합니다. 복제본을 만들기 위해 새 PowerShell 세션이 열립니다.
![동기화 프로세스](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>일반 애플리케이션 상태 검토

현재, 애플리케이션은 원래 지역에서 정상적으로 실행되고 있으며 지역에서 복제 기능으로 보호됩니다.  읽기 전용 보조 복제본이 모든 데이터베이스에 대한 복구 지역에 존재합니다. 

1. Azure Portal에서 리소스 그룹을 보고, 리소스 그룹이 복구 지역에서 -recovery 접미사를 사용하여 만들어졌음을 확인합니다. 

2. 복구 리소스 그룹에서 리소스를 탐색합니다.  

3. _tenants1-dpt-&lt;user&gt;-recovery_ 서버에서 Contoso Concert Hall 데이터베이스를 클릭합니다.  왼쪽의 지역에서 복제를 클릭합니다. 

    ![Contoso Concert 지역에서 복제 링크](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Azure 지역 맵에서 원래 지역의 주 복제본과 복구 지역의 보조 복제본 사이에 있는 지역에서 복제 링크를 확인합니다.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>애플리케이션을 복구 지역으로 장애 조치(Failover)

### <a name="geo-replication-recovery-process-overview"></a>지역에서 복제 복구 프로세스 개요

복구 스크립트는 다음 작업을 수행합니다.

1. 원래 지역의 웹 응용 프로그램에 대한 Traffic Manager 엔드포인트를 사용하지 않도록 설정합니다. 엔드포인트를 사용하지 않도록 설정하면 복구 중에 원래 지역이 온라인 상태가 되는 경우 사용자가 잘못된 상태에서 앱에 연결하지 못하게 됩니다.

1. 복구 지역에서 카탈로그 데이터베이스의 강제 장애 조치(Failover)를 사용하여 주 데이터베이스로 만들고, 복구 카탈로그 서버를 가리키도록 _activecatalog_ 별칭을 업데이트합니다.

1. 복구 지역의 테넌트 서버를 가리키도록 _newtenant_ 별칭을 업데이트합니다. 이 별칭을 변경하면 모든 새 테넌트의 데이터베이스가 복구 지역에 프로비전됩니다. 

1. 복구 카탈로그에 있는 기존의 모든 테넌트를 오프라인으로 표시하여 장애 조치(Failover)되기 전에 테넌트 데이터베이스에 액세스하지 못하도록 방지합니다.

1. 원래 지역의 해당 구성을 미러링하도록 복구 지역에 있는 모든 탄력적 풀 및 복제된 단일 데이터베이스의 구성을 업데이트합니다. (이 작업은 정상 작동 동안 비용 감소를 위해 복구 환경의 풀 또는 복제된 데이터베이스 규모를 축소하는 경우에만 필요합니다.)

1. 복구 지역에서 웹 응용 프로그램에 대한 Traffic Manager 엔드포인트를 사용하도록 설정합니다. 이 엔드포인트를 사용하면 애플리케이션에서 새 테넌트를 프로비전할 수 있습니다. 이 단계에서 기존 테넌트는 여전히 오프라인 상태입니다.

1. 우선 순위에 따라 데이터베이스를 강제로 장애 조치(Failover)하는 요청의 일괄 처리를 제출합니다.
    * 일괄 처리는 데이터베이스가 모든 풀에서 병렬로 장애 조치(Failover)되도록 구성됩니다.
    * 장애 조치(Failover) 요청은 신속하게 전송되고 여러 요청을 동시에 처리할 수 있도록 하기 위해 비동기 작업을 사용하여 제출됩니다.

   > [!Note]
   > 작동 중단 시나리오에서는 원래 지역의 주 데이터베이스가 오프라인 상태입니다.  보조 복제본에 대해 강제 장애 조치(Failover)를 수행하면 큐에 대기 중인 나머지 트랜잭션의 적용이 시도되지 않고, 주 복제본에 대한 연결이 끊어집니다. 이 자습서와 같은 DR 드릴 시나리오에서는 장애 조치(Failover) 시에 업데이트 작업이 있는 경우 일부 데이터가 손실될 수 있습니다. 나중에 송환을 수행하는 동안, 복구 지역의 데이터베이스를 원래 지역으로 다시 장애 조치(Failover)할 경우 데이터 손실을 방지하기 위해 일반 장애 조치(Failover)가 사용됩니다.

1. SQL 데이터베이스 서비스를 모니터링하여 데이터베이스가 장애 조치(Failover)된 시기를 결정합니다. 테넌트 데이터베이스가 장애 조치(Failover)되면 카탈로그를 업데이트하여 테넌트 데이터베이스의 복구 상태를 기록하고 테넌트를 온라인으로 표시합니다.
    * 테넌트 데이터베이스는 카탈로그에서 온라인으로 표시되는 즉시 애플리케이션에서 액세스할 수 있습니다.
    * 테넌트 데이터베이스에 대한 rowversion 값의 합계는 카탈로그에 저장됩니다. 이 값은 지문 역할을 하여 송환 프로세스에서 데이터베이스가 복구 지역에서 업데이트되었는지 확인할 수 있습니다.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>복구 지역으로 장애 조치(Failover)하는 스크립트 실행

이제 애플리케이션이 배포되고 복구 스크립트를 실행 하는 지역에 가동 중단이 있다고 가정해 보겠습니다.

1. *PowerShell ISE*에서 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트를 열고 다음 값을 설정합니다.
    * **$DemoScenario = 3**, 복제본으로 장애 조치(Failover)하여 앱을 복구 지역으로 복구합니다.

2. **F5** 키를 눌러 스크립트를 실행합니다.  
    * 스크립트가 새 PowerShell 창에서 열리고, 병렬로 실행되는 일련의 PowerShell 작업이 시작됩니다. 이러한 작업은 테넌트 데이터베이스를 복구 지역으로 장애 조치(Failover)합니다.
    * 복구 지역은 애플리케이션을 배포한 Azure 지역과 연결된 _쌍을 이루는 지역_입니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요. 

3. PowerShell 창에서 복구 프로세스의 상태를 모니터링합니다.
    ![장애 조치(Failover) 프로세스](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> 복구 작업 코드를 살펴보려면 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs 폴더에서 PowerShell 스크립트를 검토합니다.

### <a name="review-the-application-state-during-recovery"></a>복구하는 동안 애플리케이션 상태 검토

Traffic Manager에서 애플리케이션 엔드포인트를 사용하지 않도록 설정되면 해당 애플리케이션을 사용할 수 없습니다. 카탈로그가 복구 지역으로 장애 조치(Failover)되고 모든 테넌트가 오프라인으로 표시되면 애플리케이션은 다시 온라인 상태가 됩니다. 애플리케이션을 사용할 수 있지만 해당 데이터베이스가 장애 조치(Failover)될 때까지 이벤트 허브의 각 테넌트는 오프라인으로 표시됩니다. 애플리케이션은 오프라인 테넌트 데이터베이스를 처리하도록 설계해야 합니다.

1. 카탈로그 데이터베이스가 복구되는 즉시, 웹 브라우저에서 Wingtip Tickets 이벤트 허브를 새로 고칩니다.
   * 이제 바닥글에서 카탈로그 서버 이름이 _-recovery_ 접미사를 갖고 있고 복구 지역에 있음을 확인할 수 있습니다.
   * 아직 복원되지 않은 테넌트는 오프라인으로 표시되고 선택할 수 없습니다.  

     > [!Note]
     > 복구할 데이터베이스가 많지 않을 경우, 복구가 완료되기 전에 브라우저를 새로 고칠 수 없으므로 오프라인 상태인 동안 테넌트를 보지 못할 수 있습니다. 
 
     ![오프라인 Events Hub](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * 오프라인 테넌트의 이벤트 페이지를 직접 열면 '테넌트 오프라인' 알림이 표시됩니다. 예를 들어 Contoso Concert Hall이 오프라인인 경우 http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall ![Contoso Offline 페이지](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png)를 열어 봅니다. 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>복구 지역에 새 테넌트 프로비전
기존의 모든 테넌트 데이터베이스가 장애 조치(Failover)되기 전에도 복구 지역에 새 테넌트를 프로비전할 수 있습니다.  

1. *PowerShell ISE*에서 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트를 열고 다음 속성을 설정합니다.
    * **$DemoScenario = 3** - 복구 지역에 새 테넌트를 프로비전합니다.

2. **F5** 키를 눌러 스크립트를 실행하고 새 테넌트를 프로비전합니다. 

3. 프로비전이 완료되면 Hawthorn Hall 이벤트 페이지가 브라우저에서 열립니다. 바닥글에서 Hawthorn Hall 데이터베이스가 복구 지역에서 프로비전된다는 것을 알 수 있습니다.
    ![Hawthorn Hall 이벤트 페이지](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. 브라우저에서 Wingtip Tickets 이벤트 허브 페이지를 새로 고쳐 Hawthorn Hall이 포함되어 있는지 확인합니다. 
    * 다른 테넌트가 복원될 때까지 기다리지 않고 Hawthorn Hall을 프로비전한 경우 다른 테넌트는 여전히 오프라인 상태일 수 있습니다.


## <a name="review-the-recovered-state-of-the-application"></a>애플리케이션의 복구 상태 검토

복구 프로세스가 완료되면 애플리케이션과 모든 테넌트가 복구 지역에서 완전하게 작동합니다. 

1. PowerShell 콘솔 창의 디스플레이에 모든 테넌트가 복구 되었다고 표시되면 이벤트 허브를 새로 고칩니다.  새 테넌트인 Hawthorn Hall을 포함하여 모든 테넌트가 온라인으로 표시됩니다.

    ![이벤트 허브에서 복구된 새 테넌트](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. [Azure Portal](https://portal.azure.com)에서 리소스 그룹 목록을 엽니다.  
    * 배포한 리소스 그룹과 복구 리소스 그룹에 _-recovery_ 접미사가 있는지 확인합니다.  복구 리소스 그룹에는 복구 프로세스 중에 만들어진 모든 리소스와 가동 중단 중에 만들어진 새 리소스가 포함되어 있습니다.  

3. 복구 리소스 그룹을 열고 다음 항목을 확인합니다.
   * _-recovery_ 접미사가 있는 카탈로그 및 tenants1 서버의 복구 버전.  이러한 서버의 복원된 카탈로그 및 테넌트 데이터베이스에는 모두 원래 지역에서 사용된 이름이 있습니다.

   * _tenants2-dpt-&lt;user&gt;-recovery_ SQL 서버.  이 서버는 가동 중단 시 새 테넌트를 프로비전하는 데 사용됩니다.
   * 이벤트 앱의 복구 인스턴스인 _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt_라는 이름의 App Service 

     ![Azure 복구 리소스](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. _tenants2-dpt-&lt;user&gt;-recovery_ SQL 서버를 엽니다.  _hawthornhall_ 데이터베이스와 _Pool1_ 탄력적 풀이 포함되어 있음을 확인합니다.  _hawthornhall_ 데이터베이스는 _Pool1_ 탄력적 풀의 탄력적 데이터베이스로 구성됩니다.

5. 리소스 그룹으로 다시 이동한 후 _tenants1-dpt-&lt;user&gt;-recovery_ 서버에서 Contoso Concert Hall 데이터베이스를 클릭합니다. 왼쪽의 지역에서 복제를 클릭합니다.
    
    ![장애 조치(Failover) 후 Contoso 데이터베이스](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>테넌트 데이터 변경 
이 작업에서는 테넌트 데이터베이스 중 하나를 업데이트합니다. 

1. 브라우저에서 Contoso Concert Hall에 대한 이벤트 목록을 찾고, 마지막 이벤트 이름을 적어둡니다.
2. *PowerShell ISE*의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트에서 다음 값을 설정합니다.
    * **$DemoScenario = 4** - 복구 지역의 테넌트에서 이벤트를 삭제합니다.
3. **F5** 키를 눌러 스크립트를 실행합니다.
4. Contoso Concert Hall 이벤트 페이지(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall - substitute &lt;user&gt;를 배포의 사용자 값으로 바꿈)를 새로 고치고 마지막 이벤트가 삭제된 것을 확인합니다.

## <a name="repatriate-the-application-to-its-original-production-region"></a>애플리케이션을 원래 프로덕션 지역으로 송환

이 작업은 애플리케이션을 원래 지역으로 송환합니다. 실제 시나리오에서는 작동 중단 문제가 해결되면 송환을 시작합니다.

### <a name="repatriation-process-overview"></a>송환 프로세스 개요

![송환 아키텍처](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

송환 프로세스:
1. 모든 미해결 또는 진행 중인 데이터베이스 복원 요청을 취소합니다.
2. 원래 지역의 테넌트 서버를 가리키도록 _newtenant_ 별칭을 업데이트합니다. 이 별칭을 변경하면 모든 새 테넌트의 데이터베이스가 원래 지역에 프로비전됩니다.
3. 변경된 모든 테넌트 데이터를 원래 지역으로 시드합니다.
4. 우선 순위에 따라 테넌트 데이터베이스를 장애 조치(Failover)합니다.

장애 조치(Failover)를 수행하면 결과적으로 데이터베이스가 원래 지역으로 이동됩니다. 데이터베이스가 장애 조치(Failover)되면 모든 열린 연결이 끊어지고 몇 초 정도 데이터베이스를 사용할 수 없게 됩니다. 다시 연결하려면 재시도 논리를 사용하여 애플리케이션을 작성해야 합니다.  짧은 연결 해제가 자주 발견되지 않지만, 업무 시간 외에서 데이터베이스를 송환하도록 선택할 수 있습니다. 


### <a name="run-the-repatriation-script"></a>송환 스크립트 실행
이제 가동 중단이 해결되었다고 가정하고 송환 스크립트를 실행하겠습니다.

1. *PowerShell ISE*의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 스크립트에서

2. 카탈로그 동기화 프로세스가 PowerShell 인스턴스에서 계속 실행 중인지 확인합니다.  필요한 경우 다음을 설정하여 다시 시작합니다.
    * **$DemoScenario = 1** - 카탈로그로의 테넌트 서버, 풀 및 데이터베이스 구성 정보 동기화를 시작합니다.
    * **F5** 키를 눌러 스크립트를 실행합니다.

3.  그런 다음, 송환 프로세스를 시작하려면 다음을 설정합니다.
    * **$DemoScenario = 6** - 앱을 원래 지역으로 송환합니다.
    * **F5** 키를 눌러 새 PowerShell 창에서 복구 스크립트를 실행합니다.  송환에는 몇 분 정도 걸리며, PowerShell 창에서 모니터링할 수 있습니다.
    ![송환 프로세스](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. 스크립트가 실행되는 동안 이벤트 허브 페이지(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net)를 새로 고칩니다.
    * 모든 테넌트가 온라인 상태이며 이 프로세스를 통해 액세스할 수 있습니다.

5. 송환이 완료되면 이벤트 허브를 새로 고치고 Hawthorn Hall에 대한 이벤트 페이지를 엽니다. 이 데이터베이스가 원래 지역으로 송환되었음을 확인합니다.
    ![이벤트 허브가 송환됨](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>애플리케이션 및 데이터베이스가 공동으로 배치되도록 애플리케이션 디자인 
애플리케이션은 항상 테넌트 데이터베이스와 동일한 지역에 있는 인스턴스에서 연결되도록 설계되었습니다. 이 디자인은 애플리케이션과 데이터베이스 간의 대기 시간을 줄여줍니다. 이 최적화에서는 앱-데이터베이스 상호 작용이 사용자-앱 상호 작용보다 더 많이 수행된다고 가정합니다.  

테넌트 데이터베이스는 송환 중에 일정 기간 동안 복구 지역과 원래 지역에 걸쳐 분산되어 있을 수 있습니다. 각 데이터베이스에 대해 응용 프로그램은 테넌트 서버 이름에서 DNS 조회를 수행하여 데이터베이스가 있는 지역을 찾습니다. SQL Database에서 서버 이름은 별칭입니다. 별칭이 지정된 서버 이름에는 지역 이름이 포함되어 있습니다. 애플리케이션이 데이터베이스와 동일한 지역에 있지 않으면, 데이터베이스 서버와 동일한 지역에 있는 인스턴스로 리디렉션됩니다.  데이터베이스와 동일한 지역에 있는 인스턴스로 리디렉션하면 응용 프로그램과 데이터베이스 간의 대기 시간이 최소화됩니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> 
> * 데이터베이스 및 탄력적 풀 구성 정보를 테넌트 카탈로그 동기화
> * 애플리케이션, 서버 및 풀로 구성된 대체 지역에서 복구 환경 설정
> * _지역에서 복제_를 사용하여 카탈로그 및 테넌트 데이터베이스를 복구 지역으로 복제
> * 애플리케이션 및 카탈로그와 테넌트 데이터베이스를 복구 지역으로 장애 조치(Failover) 
> * 애플리케이션, 카탈로그 및 테넌트 데이터베이스를 원래 지역으로 다시 장애 복구(Failback)

[비즈니스 연속성 개요](sql-database-business-continuity.md) 설명서에서 Azure SQL 데이터베이스가 비즈니스 연속성을 사용하도록 설정하기 위해 제공하는 기술에 대해 자세히 알아볼 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Wingtip SaaS 애플리케이션을 빌드하는 또 다른 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
