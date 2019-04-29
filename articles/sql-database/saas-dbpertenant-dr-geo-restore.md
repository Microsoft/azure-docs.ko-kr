---
title: 'SaaS 앱: 재해 복구용 Azure SQL Database 지역 중복 백업 | Microsoft Docs'
description: 가동 중단 시 Azure SQL Database 지역 중복 백업을 사용하여 다중 테넌트 SaaS 앱을 복구하는 방법을 알아봅니다.
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
ms.date: 01/14/2019
ms.openlocfilehash: c96f2dc2b44ea2118d9f0dd6c988017efcba5800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557071"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>데이터베이스 백업에서 지역 복원을 사용하여 다중 테넌트 SaaS 애플리케이션 복구

이 자습서에서는 테넌트별 데이터베이스 모델을 사용하여 구현된 다중 테넌트 SaaS 애플리케이션에 대한 전체 재해 복구 시나리오를 살펴봅니다. [지역 복원](sql-database-recovery-using-backups.md)을 사용하여 카탈로그 및 테넌트 데이터베이스를 자동으로 유지 관리되는 지역 중복 백업에서 대체 복구 지역으로 복구합니다. 가동 중단이 해결되면 [지역 복제](sql-database-geo-replication-overview.md)를 사용하여 변경된 데이터베이스를 원래 지역으로 송환합니다.

![지역 복원 아키텍처](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

지역 복원은 Azure SQL Database에 대한 가장 저렴한 재해 복구 솔루션입니다. 그러나 지역 중복 백업에서의 복원은 최대 1시간의 데이터가 손실될 수 있습니다. 각 데이터베이스의 크기에 따라 시간이 오래 걸릴 수 있습니다. 

> [!NOTE]
> 지역 복원 대신 지역 복제를 사용하여 가능한 가장 낮은 RPO 및 RTO를 통해 애플리케이션을 복구합니다.

이 자습서에서는 복원 및 송환 워크플로를 살펴봅니다. 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> 
> * 데이터베이스 및 탄력적 풀 구성 정보를 테넌트 카탈로그로 동기화합니다.
> * 애플리케이션, 서버 및 풀을 포함하는 복구 지역에 미러 이미지 환경을 설정합니다.   
> * 지역 복원을 사용하여 카탈로그 및 테넌트 데이터베이스를 복구합니다.
> * 가동 중단이 해결되면 지역 복제를 사용하여 테넌트 카탈로그 및 변경된 테넌트 데이터베이스를 송환합니다.
> * 각 데이터베이스가 복원(또는 송환)될 때 카탈로그를 업데이트하여 각 테넌트 데이터베이스의 활성 복사본에 대한 현재 위치를 추적합니다.
> * 대기 시간을 줄이기 위해 애플리케이션 및 테넌트 데이터베이스가 항상 동일한 Azure 지역에 배치되도록 합니다. 
 

이 자습서를 완료하려면 다음과 같은 필수 구성 요소를 완료해야 합니다.
* Wingtip Tickets SaaS 테넌트당 데이터베이스 앱을 배포합니다. 5분 안에 배포를 마치려면 [Wingtip Tickets SaaS 테넌트별 데이터베이스 애플리케이션 배포 및 살펴보기](saas-dbpertenant-get-started-deploy.md)를 참조하세요. 
* Azure PowerShell을 설치합니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>지역 복원 복구 패턴 소개

DR(재해 복구)은 규정 준수 이유 또는 비즈니스 연속성 여부에 관계없이 많은 애플리케이션에서 중요한 고려 사항입니다. 서비스 중단 시간이 길어지면 잘 준비된 DR 계획으로 비즈니스 중단을 최소화할 수 있습니다. 지역 복원에 기반한 DR 계획으로 달성해야 하는 몇 가지 목표는 다음과 같습니다.
 * 선택한 복구 지역에서 필요한 모든 용량을 최대한 빨리 예약하여 테넌트 데이터베이스를 복원하는 데 사용할 수 있도록 합니다.
 * 원래 풀 및 데이터베이스 구성을 반영하는 미러 이미지 복구 환경을 설정합니다. 
 * 원래 지역이 다시 온라인 상태가 되면 복원 프로세스를 도중에 취소할 수 있습니다.
 * 테넌트 프로비전을 빠르게 활성화하여 새 테넌트 온보딩이 최대한 빨리 다시 시작될 수 있도록 합니다
 * 우선 순위에 따라 테넌트를 복원할 수 있도록 최적화합니다.
 * 실제로 어디서든 단계를 가능한 한 빨리 병렬로 수행하여 테넌트가 최대한 빨리 온라인 상태가 되도록 최적화합니다.
 * 실패, 다시 시작 가능 및 멱등원(idempotent)에 대한 복원력이 있어야 합니다.
 * 가동 중단이 해결되면 테넌트에 미치는 영향을 최소화하면서 원래 지역으로 데이터베이스를 송환합니다.  

> [!NOTE]
> 애플리케이션은 해당 애플리케이션이 배포된 지역과 쌍을 이루는 지역에 복구됩니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.   

이 자습서에서는 Azure SQL Database 및 Azure 플랫폼의 기능을 사용하여 다음 과제를 해결합니다.

* [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) - 필요한 모든 용량을 최대한 빨리 예약합니다. Azure Resource Manager 템플릿은 복구 지역에 원래 서버와 탄력적 풀의 미러 이미지를 프로비전하는 데 사용됩니다. 또한 새 테넌트를 프로비전하기 위해 별도의 서버 및 풀이 만들어집니다.
* [EDCL(탄력적 데이터베이스 클라이언트 라이브러리)](sql-database-elastic-database-client-library.md) - 테넌트 데이터베이스 카탈로그를 만들고 유지 관리합니다. 확장된 카탈로그는 정기적으로 새로 고친 풀 및 데이터베이스 구성 정보를 포함합니다.
* EDCL의 [분할된 관리 복구 기능](sql-database-elastic-database-recovery-manager.md) - 복구 및 송환하는 동안 카탈로그에 데이터베이스 위치 항목을 유지 관리합니다.  
* [지역 복원](sql-database-disaster-recovery.md) - 자동으로 유지 관리되는 지역 중복 백업에서 카탈로그 및 테넌트 데이터베이스를 복구합니다. 
* [비동기 복원 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) - 시스템에서 각 풀에 대해 큐에 넣고 풀이 오버로드되지 않도록 일괄적으로 처리하여 테넌트 우선 순위로 보내집니다. 이러한 작업은 필요에 따라 실행 전이나 실행 중에 취소할 수 있습니다.   
* [지역 복제](sql-database-geo-replication-overview.md) - 가동 중단 후 데이터베이스를 원래 지역으로 송환합니다. 지역 복제를 사용하면 데이터 손실이 없고 테넌트에 미치는 영향을 최소화할 수 있습니다.
* [SQL Server DNS 별칭](dns-alias-overview.md) - 카탈로그 동기화 프로세스에서 해당 위치에 관계없이 활성 카탈로그에 연결할 수 있도록 합니다.  

## <a name="get-the-disaster-recovery-scripts"></a>재해 복구 스크립트 가져오기

이 자습서에서 사용되는 DR 스크립트는 [테넌트별 Wingtip Tickets SaaS 데이터베이스 GitHub 리포지토리](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)에서 사용할 수 있습니다. Wingtip Tickets 관리 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인합니다.

> [!IMPORTANT]
> 모든 Wingtip Tickets 관리 스크립트와 마찬가지로, DR 스크립트는 샘플 품질이며 프로덕션 환경에서 사용할 수 없습니다.

## <a name="review-the-healthy-state-of-the-application"></a>애플리케이션의 정상 상태 검토
복구 프로세스를 시작하기 전에 애플리케이션의 정상 상태를 검토합니다.

1. 웹 브라우저에서 Wingtip Tickets 이벤트 허브(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net, replace &lt;user&gt;를 사용자 배포의 사용자 값으로 바꿈)를 엽니다.
    
   페이지 아래쪽으로 스크롤하여 바닥글에서 카탈로그 서버 이름과 위치를 확인합니다. 위치는 앱을 배포한 지역입니다.    

   > [!TIP]
   > 위치를 마우스로 가리키면 디스플레이가 확대됩니다.

   ![원래 지역의 이벤트 허브 정상 상태](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Contoso Concert Hall 테넌트를 선택하고 해당 이벤트 페이지를 엽니다.

   바닥글에서 테넌트의 서버 이름을 확인합니다. 위치는 카탈로그 서버의 위치와 동일합니다.

   ![Contoso Concert Hall 원래 지역](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. [Azure Portal](https://portal.azure.com)에서 앱을 배포한 리소스 그룹을 검토하고 엽니다.

   앱 서비스 구성 요소와 SQL Database 서버가 배포된 리소스와 지역을 확인합니다.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>카탈로그로 테넌트 구성을 동기화합니다.

이 작업에서는 서버, 탄력적 풀 및 데이터베이스의 구성을 테넌트 카탈로그로 동기화하는 프로세스를 시작합니다. 이 정보는 나중에 복구 지역에서 미러 이미지 환경을 구성하는 데 사용됩니다.

> [!IMPORTANT]
> 편의상, 이러한 샘플에서는 동기화 프로세스 및 다른 장기 실행 복구/송환 프로세스가 클라이언트 사용자 로그인에서 실행되는 로컬 PowerShell 작업 또는 세션으로 구현됩니다. 로그인할 때 발급한 인증 토큰은 몇 시간 후에 만료되고 작업이 실패합니다. 프로덕션 시나리오에서 장기 실행 프로세스는 서비스 주체에서 실행되는 어떤 종류의 신뢰할 수 있는 Azure 서비스로 구현되어야 합니다. [Azure PowerShell을 사용하여 인증서로 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)를 참조하세요. 

1. PowerShell ISE에서 ...\Learning Modules\UserConfig.psm1 파일을 엽니다. 10 및 11번 줄의 `<resourcegroup>` 및 `<user>`를 앱을 배포할 때 사용한 값으로 바꿉니다. 파일을 저장합니다.

2. PowerShell ISE에서 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트를 엽니다.

    이 자습서에서는 이 PowerShell 스크립트에서 각 시나리오를 실행하므로 이 파일을 열어 둡니다.

3. 다음과 같이 설정합니다.

    $DemoScenario = 1: 테넌트 서버를 동기화하는 백그라운드 작업을 시작하고 구성 정보를 카탈로그에 풀링합니다.

4. 동기화 스크립트를 실행하려면 F5 키를 누릅니다. 

    이 정보는 나중에 복구를 통해 복구 지역에 서버, 풀 및 데이터베이스의 미러 이미지를 만드는 데 사용됩니다.  

    ![동기화 프로세스](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

PowerShell 창을 백그라운드에서 실행 중인 상태로 두고 이 자습서의 나머지 부분을 계속 진행합니다.

> [!NOTE]
> 동기화 프로세스는 DNS 별칭을 통해 카탈로그에 연결합니다. 별칭은 활성 카탈로그를 가리키도록 복원 및 송환 중에 수정됩니다. 동기화 프로세스는 복구 지역에서 수행된 데이터베이스 또는 풀 구성 변경 내용을 적용하여 카탈로그를 최신 상태로 유지합니다. 이러한 변경 내용은 송환 중에 원래 지역의 해당 리소스에 적용됩니다.

## <a name="geo-restore-recovery-process-overview"></a>지역 복원 복구 프로세스 개요

지역 복원 복구 프로세스는 애플리케이션을 배포하고 데이터베이스를 백업에서 복구 지역으로 복원합니다.

복구 프로세스는 다음을 수행합니다.

1. 원래 지역의 웹앱에 대한 Azure Traffic Manager 엔드포인트를 사용하지 않도록 설정합니다. 엔드포인트를 사용하지 않도록 설정하면 복구 중에 원래 지역이 온라인 상태가 되는 경우 사용자가 잘못된 상태에서 앱에 연결하지 못하게 됩니다.

2. 복구 지역의 복구 카탈로그 서버를 프로비전하고, 카탈로그 데이터베이스를 지리적으로 복원하며, activecatalog 별칭이 복원된 카탈로그 서버를 가리키도록 업데이트합니다. 카탈로그 별칭을 변경하면 카탈로그 동기화 프로세스가 항상 활성 카탈로그로 동기화됩니다.

3. 복구 카탈로그에 있는 기존의 모든 테넌트를 오프라인으로 표시하여 복원되기 전에 테넌트 데이터베이스에 액세스하지 못하도록 방지합니다.

4. 복구 지역에서 앱의 인스턴스를 프로비전하고, 해당 지역에서 복원된 카탈로그를 사용하도록 구성합니다. 대기 시간을 최소화하기 위해 샘플 앱은 항상 동일한 지역의 테넌트 데이터베이스에 연결되도록 설계되었습니다.

5. 새 테넌트가 프로비전될 서버 및 탄력적 풀을 프로비전합니다. 이러한 리소스를 만들면 새 테넌트를 프로비전하더라도 기존 테넌트의 복구를 방해하지 않습니다.

6. 복구 지역에서 새 테넌트 데이터베이스에 대한 서버를 가리키도록 새 테넌트 별칭을 업데이트합니다. 이 별칭을 변경하면 모든 새 테넌트의 데이터베이스가 복구 지역에 프로비전됩니다.
        
7. 테넌트 데이터베이스를 복원하기 위해 복구 지역의 서버 및 탄력적 풀을 프로비전합니다. 이러한 서버와 풀은 원래 지역의 구성에 대한 미러 이미지입니다. 풀이 미리 프로비전되면 모든 데이터베이스를 복원하는 데 필요한 용량을 예약합니다.

    한 지역에서 가동 중단되면 쌍을 이루는 지역에서 사용할 수 있는 리소스에 상당한 압력이 가해질 수 있습니다. DR에 지역 복원을 사용하는 경우 리소스를 빠르게 예약하는 것이 좋습니다. 특정 지역에서 애플리케이션을 복구해야 하는 경우 지역 복제를 사용하는 것이 좋습니다. 

8. 복구 지역에서 웹 응용 프로그램에 대한 Traffic Manager 엔드포인트를 사용하도록 설정합니다. 이 엔드포인트를 사용하면 애플리케이션에서 새 테넌트를 프로비전할 수 있습니다. 이 단계에서 기존 테넌트는 여전히 오프라인 상태입니다.

9. 우선 순위에 따라 데이터베이스를 복원하는 요청의 일괄 처리를 제출합니다. 

    * 일괄 처리는 데이터베이스가 모든 풀에서 병렬로 복원되도록 구성됩니다.  

    * 복원 요청은 비동기적으로 제출되므로 신속하게 제출되고 각 풀의 실행을 위해 큐에 넣어집니다.

    * 복원 요청은 모든 풀에서 병렬로 처리되기 때문에 중요한 테넌트를 여러 풀에 배포하는 것이 좋습니다. 

10. SQL Database 서비스를 모니터링하여 데이터베이스가 복원되는 시기를 결정합니다. 테넌트 데이터베이스가 복원되면 카탈로그에서 온라인으로 표시되고 테넌트 데이터베이스에 대한 rowversion 합계가 기록됩니다. 

    * 테넌트 데이터베이스는 카탈로그에서 온라인으로 표시되는 즉시 애플리케이션에서 액세스할 수 있습니다.

    * 테넌트 데이터베이스에 대한 rowversion 값의 합계는 카탈로그에 저장됩니다. 이 합계는 지문 역할을 하여 송환 프로세스에서 데이터베이스가 복구 지역에서 업데이트되었는지 확인할 수 있습니다.       

## <a name="run-the-recovery-script"></a>복구 스크립트 실행

> [!IMPORTANT]
> 이 자습서에서는 지역 중복 백업에서 데이터베이스를 복원합니다. 이러한 백업은 일반적으로 10분 내에 사용할 수 있지만, 최대 1시간이 걸릴 수도 있습니다. 스크립트는 사용할 수 있을 때까지 일시 중지됩니다.

이제 애플리케이션이 배포되고 복구 스크립트를 실행하는 지역에 가동 중단이 있다고 가정해 보겠습니다.

1. PowerShell ISE의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트에서 다음 값을 설정합니다.

    $DemoScenario = 2: 지역 중복 백업에서 복원하여 앱을 복구 지역으로 복구합니다.

2. 스크립트를 실행하려면 F5 키를 누릅니다.  

    * 스크립트가 새 PowerShell 창에서 열리고, 병렬로 실행되는 일단의 PowerShell 작업이 시작됩니다. 이러한 작업은 서버, 풀 및 데이터베이스를 복구 지역에 복원합니다.

    * 복구 지역은 애플리케이션을 배포한 Azure 지역과 연결된 쌍을 이루는 지역입니다. 자세한 내용은 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요. 

3. PowerShell 창에서 복구 프로세스의 상태를 모니터링합니다.

    ![복구 프로세스](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> 복구 작업 코드를 살펴보려면 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs 폴더에서 PowerShell 스크립트를 검토합니다.

## <a name="review-the-application-state-during-recovery"></a>복구하는 동안 애플리케이션 상태 검토
Traffic Manager에서 애플리케이션 엔드포인트를 사용하지 않도록 설정되면 해당 애플리케이션을 사용할 수 없습니다. 카탈로그가 복원되고 모든 테넌트가 오프라인으로 표시됩니다. 그런 다음, 복구 지역의 애플리케이션 엔드포인트가 활성화되고 애플리케이션이 다시 온라인 상태가 됩니다. 애플리케이션을 사용할 수 있지만 해당 데이터베이스가 복원될 때까지 테넌트는 이벤트 허브에서 오프라인으로 표시됩니다. 애플리케이션은 오프라인 테넌트 데이터베이스를 처리하도록 설계해야 합니다.

* 카탈로그 데이터베이스가 복구되었지만 테넌트가 다시 온라인 상태가 되기 전에 웹 브라우저에서 Wingtip Tickets 이벤트 허브를 새로 고칩니다.

  * 이제 바닥글에서 카탈로그 서버 이름이 -recovery 접미사를 갖고 있고 복구 지역에 있음을 확인할 수 있습니다.

  * 아직 복원되지 않은 테넌트는 오프라인으로 표시되고 선택할 수 없습니다.   
 
    ![복구 프로세스](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * 테넌트가 오프라인 상태인 동안 테넌트의 이벤트 페이지를 직접 열면 해당 페이지에 테넌트 오프라인 알림이 표시됩니다. 예를 들어 Contoso Concert Hall이 오프라인인 경우 http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall을 열어 봅니다.

    ![복구 프로세스](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>복구 지역에 새 테넌트 프로비전
테넌트 데이터베이스가 복원되기 전에도 복구 지역에 새 테넌트를 프로비전할 수 있습니다. 복구 지역에 프로비전된 새 테넌트 데이터베이스는 나중에 복구된 데이터베이스와 함께 송환됩니다.   

1. PowerShell ISE의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트에서 다음 속성을 설정합니다.

    $DemoScenario = 3: 복구 지역에서 새 테넌트를 프로비전합니다.

2. 스크립트를 실행하려면 F5 키를 누릅니다.

3. 프로비전이 완료되면 Hawthorn Hall 이벤트 페이지가 브라우저에서 열립니다. 

    Hawthorn Hall 데이터베이스는 복구 지역에 있습니다.

    ![복구 지역에 프로비전된 Hawthorn Hall](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. 브라우저에서 Wingtip Tickets 이벤트 허브 페이지를 새로 고쳐 Hawthorn Hall이 포함되어 있는지 확인합니다. 

    다른 테넌트가 복원될 때까지 기다리지 않고 Hawthorn Hall을 프로비전한 경우 다른 테넌트는 여전히 오프라인 상태일 수 있습니다.

## <a name="review-the-recovered-state-of-the-application"></a>애플리케이션의 복구 상태 검토

복구 프로세스가 완료되면 애플리케이션과 모든 테넌트가 복구 지역에서 완전하게 작동합니다. 

1. PowerShell 콘솔 창의 디스플레이에 모든 테넌트가 복구되었다고 표시되면 이벤트 허브를 새로 고칩니다. 

    새 테넌트인 Hawthorn Hall을 포함한 모든 테넌트가 온라인으로 표시됩니다.

    ![이벤트 허브에서 복구된 새 테넌트](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Contoso Concert Hall을 클릭하고 이벤트 페이지를 엽니다. 

    바닥글에서 데이터베이스가 복구 지역에 있는 복구 서버에 있는지 확인합니다.

    ![복구 지역의 Contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. [Azure Portal](https://portal.azure.com)에서 리소스 그룹 목록을 엽니다.  

    배포한 리소스 그룹과 복구 리소스 그룹에 -recovery 접미사가 있는지 확인합니다. 복구 리소스 그룹에는 복구 프로세스 중에 만들어진 모든 리소스와 가동 중단 중에 만들어진 새 리소스가 포함되어 있습니다. 

4. 복구 리소스 그룹을 열고 다음 항목을 확인합니다.

   * -recovery 접미사가 있는 카탈로그 및 tenants1 서버의 복구 버전입니다. 이러한 서버의 복원된 카탈로그 및 테넌트 데이터베이스에는 모두 원래 지역에서 사용된 이름이 있습니다.

   * tenants2-dpt-&lt;user&gt;-recovery SQL 서버. 이 서버는 가동 중단 시 새 테넌트를 프로비전하는 데 사용됩니다.

   * 이벤트 앱의 복구 인스턴스인 events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;user&gt;라는 이름의 App Service.

     ![복구 지역의 Contoso 리소스](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. tenants2-dpt-&lt;user&gt;-recovery SQL 서버를 엽니다. hawthornhall 데이터베이스와 Pool1 탄력적 풀이 포함되어 있음을 확인합니다. hawthornhall 데이터베이스는 Pool1 탄력적 풀의 탄력적 데이터베이스로 구성됩니다.

## <a name="change-the-tenant-data"></a>테넌트 데이터 변경 
이 작업에서는 복원된 테넌트 데이터베이스 중 하나를 업데이트합니다. 송환 프로세스는 원래 지역으로 변경된 복원된 데이터베이스를 복사합니다. 

1. 브라우저에서 Contoso Concert Hall에 대한 이벤트 목록을 찾고, 이벤트를 스크롤하여 마지막 이벤트인 Seriously Strauss를 적어둡니다.

2. PowerShell ISE의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트에서 다음 값을 설정합니다.

    $DemoScenario = 4: 복구 지역의 테넌트에서 이벤트를 삭제합니다.

3. 스크립트를 실행하려면 F5 키를 누릅니다.

4. Contoso Concert Hall 이벤트 페이지(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall)를 새로 고치고, Seriously Strauss 이벤트가 누락되어 있는지 확인합니다.

자습서의 이 시점에서는 이제 복구 지역에서 실행되는 애플리케이션을 복구했습니다. 복구 지역에 새 테넌트를 프로비전하고 복원된 테넌트 중 하나의 데이터를 수정했습니다.  

> [!NOTE]
> 샘플의 다른 자습서는 복구 상태의 앱에서 실행되도록 설계되지 않았습니다. 다른 자습서를 탐색하려면 먼저 애플리케이션을 송환해야 합니다.

## <a name="repatriation-process-overview"></a>송환 프로세스 개요

가동 중단이 해결되면 송환 프로세스에서 애플리케이션과 해당 데이터베이스를 원래 지역으로 되돌립니다.

![지역 복원 송환](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

프로세스는 다음과 같습니다.

1. 진행 중인 복원 작업을 모두 중지하고, 미해결 또는 진행 중인 데이터베이스 복원 요청을 모두 취소합니다.

2. 가동 중단 이후 변경되지 않은 원래 지역의 테넌트 데이터베이스에서 다시 활성화합니다. 이러한 데이터베이스에는 아직 복구되지 않은 데이터베이스 및 복구되었으나 나중에 변경되지 않은 데이터베이스가 포함됩니다. 다시 활성화된 데이터베이스는 테넌트에서 마지막으로 액세스한 데이터베이스와 정확히 동일합니다.

3. 원래 지역의 새 테넌트 서버와 탄력적 풀의 미러 이미지를 프로비전합니다. 이 작업이 완료되면 새 테넌트 별칭이 이 서버를 가리키도록 업데이트됩니다. 별칭을 업데이트하면 새 테넌트 온보딩이 복구 지역 대신 원래 지역에서 발생합니다.

3. 지역 복제를 사용하여 카탈로그를 복구 지역에서 원래 지역으로 이동합니다.

4. 원래 지역의 풀 구성을 업데이트하여 가동 중단 시 복구 지역에서 변경한 내용과 일치시킵니다.

5. 가동 중단 중에 만들어진 새 데이터베이스를 호스팅하는 데 필요한 서버와 풀을 만듭니다.

6. 지역 복제를 사용하여 복원 후에 업데이트된 복원된 테넌트 데이터베이스와 가동 중단 중에 프로비전된 모든 새 테넌트 데이터베이스를 송환합니다. 

7. 복원 프로세스 중에 복구 지역에서 만든 리소스를 정리합니다.

송환되어야 하는 테넌트 데이터베이스의 수를 제한하려면 1-3단계를 신속하게 수행합니다.  

4단계는 가동 중단 중에 복구 지역의 카탈로그가 수정된 경우에만 수행됩니다. 새 테넌트가 만들어지거나 복구 지역에서 데이터베이스 또는 풀 구성이 변경되면 카탈로그가 업데이트됩니다.

7단계는 테넌트에 대한 중단을 최소화하고 데이터 손실이 없어야 합니다. 이 목표를 달성하기 위해 프로세스에서 지역 복제를 사용합니다.

각 데이터베이스가 지리적으로 복제되기 전에 원래 지역의 해당 데이터베이스가 삭제됩니다. 그런 다음, 복구 지역의 데이터베이스가 지리적으로 복제되어 원래 지역에 보조 복제본을 만듭니다. 복제가 완료되면 카탈로그에서 테넌트가 오프라인으로 표시되어 복구 지역의 데이터베이스에 대한 연결을 끊습니다. 그런 다음, 데이터베이스가 장애 조치되므로 보류 중인 트랜잭션이 보조 데이터베이스에서 처리되므로 데이터가 손실되지 않습니다. 

장애 조치 시 데이터베이스 역할이 취소됩니다. 원래 지역의 보조 데이터베이스는 주 읽기-쓰기 데이터베이스가 되고, 복구 지역의 데이터베이스는 읽기 전용 보조 데이터베이스가 됩니다. 카탈로그의 테넌트 항목이 원래 지역의 데이터베이스를 참조하도록 업데이트되고, 테넌트가 온라인으로 표시됩니다. 이 시점에서 데이터베이스의 송환이 완료되었습니다. 

연결이 끊어지면 자동으로 다시 연결되도록 애플리케이션을 다시 시도 논리로 작성해야 합니다. 카탈로그를 사용하여 연결을 broker에 다시 연결하면 원래 지역의 송환된 데이터베이스에 연결됩니다. 짧은 연결 해제가 자주 발견되지 않지만, 업무 시간 외에서 데이터베이스를 송환하도록 선택할 수 있습니다.

데이터베이스가 송환되면 복구 지역의 보조 데이터베이스를 삭제할 수 있습니다. 그러면 원래 지역의 데이터베이스에서 DR을 보호하기 위해 지역 복원을 다시 사용합니다.

8단계에서는 복구 서버 및 풀을 포함하여 복구 지역의 리소스가 삭제됩니다.

## <a name="run-the-repatriation-script"></a>송환 스크립트 실행
이제 가동 중단이 해결되었다고 가정하고 송환 스크립트를 실행하겠습니다.

자습서를 수행한 경우 스크립트는 변경되지 않았으므로 Fabrikam Jazz Club 및 Dogwood Dojo를 원래 지역에서 즉시 다시 활성화합니다. 그런 다음, 새 테넌트인 Hawthorn Hall을 송환하고, Contoso Concert Hall이 수정되었기 때문에 이 Contoso Concert Hall도 송환합니다. 또한 이 스크립트는 Hawthorn Hall이 프로비전될 때 업데이트된 카탈로그도 송환합니다.
  
1. PowerShell ISE의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트에서 카탈로그 동기화 프로세스가 해당 PowerShell 인스턴스에서 계속 실행 중인지 확인합니다. 필요한 경우 다음을 설정하여 다시 시작합니다.

    $DemoScenario = 1: 카탈로그로의 테넌트 서버, 풀 및 데이터베이스 구성 정보 동기화를 시작합니다.

    스크립트를 실행하려면 F5 키를 누릅니다.

2.  그런 다음, 송환 프로세스를 시작하려면 다음을 설정합니다.

    $DemoScenario = 5: 앱을 원래 지역으로 되돌립니다.

    새 PowerShell 창에서 복구 스크립트를 실행하려면 F5 키를 누릅니다. 송환에는 몇 분 정도 걸리며, PowerShell 창에서 모니터링할 수 있습니다.

3. 스크립트가 실행되는 동안 이벤트 허브 페이지(http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net)를 새로 고칩니다.

    모든 테넌트가 온라인 상태이며 이 프로세스를 통해 액세스할 수 있습니다.

4. Fabrikam Jazz Club을 선택하여 엽니다. 이 테넌트를 수정하지 않은 경우 바닥글에서 서버가 이미 원래 서버로 되돌아갔는지 확인합니다.

5. Contoso Concert Hall 이벤트 페이지를 열거나 새로 고칩니다. 바닥글에서 처음에 데이터베이스가 여전히 -recovery 서버에 있음을 확인합니다. 

6. 송환 프로세스가 완료되면 Contoso Concert Hall 이벤트 페이지를 새로 고치고, 이제 데이터베이스가 원래 지역에 있는지 확인합니다.

7. 이벤트 허브를 다시 새로 고치고 Hawthorn Hall을 엽니다. 데이터베이스가 원래 지역에 있는지 확인합니다. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>송환 후 복구 지역 리소스 정리
송환이 완료되면 복구 지역의 리소스를 안전하게 삭제할 수 있습니다. 

> [!IMPORTANT]
> 이러한 리소스를 즉시 삭제하여 해당 리소스에 대한 모든 청구를 중지합니다.

복원 프로세스는 복구 리소스 그룹에 모든 복구 리소스를 만듭니다. 정리 프로세스는 이 리소스 그룹을 삭제하고, 카탈로그에서 리소스에 대한 모든 참조를 제거합니다. 

1. PowerShell ISE의 ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 스크립트에서 다음을 설정합니다.
    
    $DemoScenario = 6: 복구 지역에서 사용되지 않는 리소스를 삭제합니다.

2. 스크립트를 실행하려면 F5 키를 누릅니다.

스크립트가 정리되면 애플리케이션이 시작된 위치로 돌아갑니다. 이 시점에서 스크립트를 다시 실행하거나 다른 자습서를 사용해 볼 수 있습니다.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>앱 및 데이터베이스가 공동 배치되도록 애플리케이션 디자인 
애플리케이션은 항상 테넌트의 데이터베이스와 동일한 지역에 있는 인스턴스에서 연결되도록 설계되었습니다. 이 디자인은 애플리케이션과 데이터베이스 간의 대기 시간을 줄여줍니다. 이 최적화에서는 앱-데이터베이스 상호 작용이 사용자-앱 상호 작용보다 더 많이 수행된다고 가정합니다.  

테넌트 데이터베이스는 송환 중에 일정 기간 동안 복구 지역과 원래 지역에 걸쳐 분산되어 있을 수 있습니다. 각 데이터베이스에 대해 응용 프로그램은 테넌트 서버 이름에서 DNS 조회를 수행하여 데이터베이스가 있는 지역을 찾습니다. SQL Database에서 서버 이름은 별칭입니다. 별칭이 지정된 서버 이름에는 지역 이름이 포함되어 있습니다. 애플리케이션이 데이터베이스와 동일한 지역에 있지 않으면, 데이터베이스 서버와 동일한 지역에 있는 인스턴스로 리디렉션됩니다. 데이터베이스와 동일한 지역에 있는 인스턴스로 리디렉션하면 앱과 데이터베이스 간의 대기 시간이 최소화됩니다.  

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> 
> * 테넌트 카탈로그를 사용하여 정기적으로 새로 고친 구성 정보를 보관하면 다른 지역에 미러 이미지 복구 환경을 만들 수 있습니다.
> * 지역 복원을 사용하여 Azure SQL 데이터베이스를 복구 지역으로 복구합니다.
> * 복원된 테넌트 데이터베이스 위치를 반영하도록 테넌트 카탈로그를 업데이트합니다. 
> * DNS 별칭을 사용하여 애플리케이션을 재구성하지 않고 테넌트 카탈로그 전체에 연결할 수 있습니다.
> * 가동 중단이 해결되면 지역 복제를 사용하여 복구된 데이터베이스를 원래 지역으로 송환합니다.

지역 복제를 사용하여 대규모 다중 테넌트 애플리케이션을 복구하는 데 필요한 시간을 대폭 줄이는 방법을 알아보려면 [데이터베이스 지역 복제를 사용하여 다중 테넌트 SaaS 애플리케이션에 대한 재해 복구](saas-dbpertenant-dr-geo-replication.md)를 시도합니다.

## <a name="additional-resources"></a>추가 리소스

[Wingtip SaaS 애플리케이션을 빌드하는 또 다른 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
