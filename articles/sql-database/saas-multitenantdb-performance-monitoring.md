---
title: 다중 테넌트 SaaS 앱에서 분할된 다중 테넌트 Azure SQL 데이터베이스의 성능 모니터링 | Microsoft Docs
description: 다중 테넌트 SaaS 앱에서 분할된 다중 테넌트 Azure SQL 데이터베이스의 성능 모니터링 및 관리
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: be7dbe35800bbe911bc56d1883462534a16499a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485595"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>다중 테넌트 SaaS 앱에서 분할된 다중 테넌트 Azure SQL 데이터베이스의 성능 모니터링 및 관리

이 자습서에서는 SaaS 애플리케이션에서 사용되는 몇 가지 주요 성능 관리 시나리오를 살펴봅니다. 부하 생성기를 사용하여 분할된 다중 테넌트 데이터베이스에서의 활동을 시뮬레이션하여 SQL Database의 기본 제공 모니터링 및 경고 기능을 보여줍니다.

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱은 테넌트 ID에 의해 여러 데이터베이스에 걸쳐 행사장(테넌트) 데이터가 분산된 분할된 다중 테넌트 데이터 모델을 사용합니다. 많은 SaaS 애플리케이션과 마찬가지로 예상 테넌트 워크로드 패턴은 예측하기 어렵고 간헐적입니다. 예를 들어 티켓 판매는 아무 때나 발생할 수 있습니다. 이러한 일반적인 데이터베이스 사용 패턴을 이용하려면 솔루션의 비용을 최적화하기 위해 데이터베이스를 확장 또는 축소할 수 있어야 합니다. 이 유형의 패턴에서는 여러 데이터베이스에 걸쳐 부하가 합리적으로 균형을 유지하도록 데이터베이스 리소스 사용을 모니터링해야 합니다. 또한 개별 데이터베이스에 적합한 리소스가 있고 [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) 한도에 도달하지 않도록 해야 합니다. 이 자습서에서는 데이터베이스를 모니터링하고 관리하는 방법과 워크로드 변화에 따라 정정 작업을 실행하는 방법을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 제공된 부하 생성기를 사용하여 분할된 다중 테넌트 데이터베이스에서 사용량 시뮬레이션하기
> * 부하 증가에 대응하여 변화하는 데이터베이스 모니터링하기
> * 데이터베이스 부하 증가에 대응하여 데이터베이스 확장하기
> * 단일 테넌트 데이터베이스에 테넌트 프로비전

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱이 배포되어 있어야 합니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS 성능 관리 패턴 소개

데이터베이스 성능 관리는 성능 데이터 컴파일 및 분석한 다음, 애플리케이션에 대해 허용되는 응답 시간을 유지하도록 매개 변수를 조정하여 이 데이터에 반응하는 과정으로 구성됩니다. 

### <a name="performance-management-strategies"></a>성능 관리 전략

* 수동으로 성능을 모니터링할 필요가 없도록 하려면 **데이터베이스가 정상 범위를 벗어난 경우 트리거되는 경고를 설정**하는 것이 가장 효과적입니다.
* 데이터베이스 컴퓨팅 크기의 단기적 변동에 대응하기 위해 **DTU 수준을 확장하거나 축소할 수 있습니다**. 이러한 변동이 정기적으로 또는 예측 가능한 단위로 발생하는 경우 **데이터베이스 크기 조정을 자동으로 일어나도록 예약**할 수 있습니다. 예를 들어 워크로드가 가볍다고 알고 있는 경우 야간 또는 주말 중으로 규모를 축소할 수 있습니다.
* 더 장기적인 변동 또는 테넌트 변화에 대응하기 위해 **개별 테넌트를 다른 데이터베이스로 이동**할 수 있습니다.
* *개별* 테넌트 부하의 단기적 증가에 대응하기 위해 **개별 테넌트를 데이터베이스에서 제외하고 개별 컴퓨팅 크기를 할당**할 수 있습니다. 부하가 감소하면 테넌트를 다중 테넌트 데이터베이스로 반환할 수 있습니다. 이러한 상황을 미리 안다면 데이터베이스에 언제나 필요한 리소스가 있도록 하고 다중 테넌트 데이터베이스에 있는 다른 테넌트에 영향을 미치지 않도록 테넌트를 선제적으로 이동할 수 있습니다. 이 요구 사항이 예측 가능하다면 인기 있는 이벤트에 대한 티켓 판매의 급격한 증가를 경험한 영역을 애플리케이션에 통합할 수 있습니다.

[Azure Portal](https://portal.azure.com)은 대부분의 리소스에 대한 기본 제공 모니터링 및 경고를 제공합니다. SQL Database의 경우 데이터베이스에 대한 모니터링 및 경고가 가능합니다. 이 기본 제공 모니터링 및 경고는 리소스별로 다르므로 소수의 리소스에 사용하면 편리하지만 많은 리소스에 작업하는 경우 편리하지 않을 수 있습니다.

대규모 시나리오의 경우 많은 리소스를 사용 하 여 작업 하는 위치를 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 사용할 수 있습니다. 내보낸된 진단 및 Log Analytics 작업 영역에서 수집 된 원격 분석을 통해 분석을 제공 하는 별도 Azure 서비스입니다. Azure Monitor 로그 많은 서비스에서 원격 분석을 수집 및 쿼리 및 경고를 설정 하는 데 사용 됩니다.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 소스 코드 및 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 리포지토리에서 확인할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.

## <a name="provision-additional-tenants"></a>추가 테넌트 프로비전

성능 모니터링과 관리가 규모에 따라 작동하는 방법을 잘 이해하기 위해 이 자습서에서는 분할된 다중 테넌트 데이터베이스에 있는 다중 테넌트가 구성되어 있어야 합니다.

이미 이전 자습서에서 테넌트의 배치를 프로비전한 경우 [모든 테넌트 데이터베이스에 대한 사용량 시뮬레이션](#simulate-usage-on-all-tenant-databases) 섹션을 건너뛸 수 있습니다.

1. **PowerShell ISE**에서 ...\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*을 엽니다. 이 자습서를 실행하는 동안 여러 시나리오를 실행할 때 이 스크립트를 열어 두세요.
1. **$DemoScenario** = **1**, _Provision a batch of tenants_ 설정
1. **F5** 키를 눌러 스크립트를 실행합니다.

스크립트는 몇 분 안에 다중 테넌트 데이터베이스에 17개의 테넌트를 배포합니다. 

*New-TenantBatch* 스크립트는 분할된 다중 테넌트 데이터베이스에 고유 테넌트 키를 갖는 새 테넌트를 생성하고 생성된 테넌트에 테넌트 이름과 행사장 유형을 부여하여 초기화합니다. 이 방법은 앱이 새 테넌트를 프로비전하는 방법과 일치합니다. 

## <a name="simulate-usage-on-all-tenant-databases"></a>모든 테넌트 데이터베이스에 대한 사용 시뮬레이션

다중 테넌트 데이터베이스에 대해 실행되는 워크로드를 시뮬레이션하는 *Demo-PerformanceMonitoringAndManagement.ps1* 스크립트가 제공됩니다. 사용 가능한 부하 시나리오 중 하나를 사용하여 부하가 생성됩니다.

| 데모 | 시나리오 |
|:--|:--|
| 2 | 표준 강도 부하 (약 30 DTU) 생성 |
| 3 | 테넌트당 버스트가 더 높은 부하 생성|
| 4 | DTU 버스트가 더 높은 (약 70 DTU) 테 넌 트 당 부하 생성|
| 5 | 단일 테 넌 트를 비롯 한 다른 모든 테 넌 트에 표준 강도 부하를에 높은 강도 (약 90 DTU) 생성 |

부하 생성기는 *가상* CPU만의 부하를 모든 테넌트 데이터베이스에 적용합니다. 이 생성기는 각 테넌트 데이터베이스에 대해 작업을 시작하여 부하를 생성하는 저장 프로세서를 주기적으로 호출합니다. 부하 수준(DTU 단위), 기간 및 간격은 모든 데이터베이스에 걸쳐 변화하여 예측 불가능한 테넌트 작업을 시뮬레이션합니다.

1. **PowerShell ISE**에서 ...\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*을 엽니다. 이 자습서를 실행하는 동안 여러 시나리오를 실행할 때 이 스크립트를 열어 두세요.
1. **$DemoScenario** = **2**, _일반 강도 부하 생성_을 설정합니다.
1. **F5** 키를 눌러 모든 테넌트에 부하를 적용합니다.

Wingtip Tickets SaaS 다중 테넌트 데이터베이스는 SaaS 앱이며 SaaS 앱상의 실제 부하는 일반적으로 간헐적이고 예측할 수 없습니다. 이를 시뮬레이션하기 위해 부하 생성기에서 모든 테넌트에 대해 분산된 임의의 부하를 생성합니다. 부하 패턴이 나타나기 위해서는 몇 분 정도 걸리므로, 다음 섹션의 부하 모니터링을 수행하기 전에 약 3~5분 동안 부하 생성기를 실행합니다.

> [!IMPORTANT]
> 부하 생성기가 새 PowerShell 창에서 일련의 작업으로 실행 중입니다. 세션을 닫으면 부하 생성시가 중단됩니다. 부하 생성기는 *job-invoking* 상태로 유지되어 부하 생성기가 시작된 후에 프로비전되는 모든 새 테넌트에서 부하를 생성합니다. *Ctrl-C*를 사용하여 새 작업 호출을 중지하고 스크립트를 종료합니다. 부하 생성기는 계속 실행되지만 기존 테넌트에만 실행됩니다.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Azure Portal을 사용하여 리소스 사용 모니터링

적용 중인 부하 때문에 발생하는 리소스 사용을 모니터링하려면 테넌트가 포함된 다중 테넌트 데이터베이스 **tenants1**의 포털을 엽니다.

1. [Azure Portal](https://portal.azure.com)을 열고 *tenants1-mt-&lt;USER&gt;* 서버로 이동합니다.
1. 아래로 스크롤하여 데이터베이스를 찾은 다음 **tenants1**을 클릭합니다. 이 분할된 다중 테넌트 데이터베이스에는 지금까지 만든 모든 테넌트가 포함되어 있습니다.

![데이터베이스 차트](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

**DTU** 차트를 관찰합니다.

## <a name="set-performance-alerts-on-the-database"></a>데이터베이스에서 성능 경고 설정하기

데이터베이스에 대해 다음과 같이 사용률 \>75%일 때 트리거되는 경고를 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 *tenants1-mt-&lt;USER&gt;* 서버의 *tenants1* 데이터베이스를 엽니다.
1. **경고 규칙**, **+ 경고 추가**를 차례로 클릭합니다.

   ![경고 추가](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. **높은 DTU** 등과 같은 이름을 제공
1. 다음 값을 설정합니다.
   * **메트릭 = DTU 백분율**
   * **조건 = 보다 큼**
   * **임계값 = 75**.
   * **기간 = 지난 30분 동안**
1. *추가 관리자 전자 메일* 상자에 메일 주소를 추가하고 **확인**을 클릭합니다.

   ![경고 설정](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>사용 중인 데이터베이스 확장하기

데이터베이스에 대한 부하 수준이 데이터베이스에서 최대로 출력되는 지점까지 증가하고 DTU 사용률이 100%에 도달한 경우 데이터베이스 성능에 영향을 주며 쿼리 응답 시간이 느려질 가능성이 있습니다.

**단기적으로** 추가 리소스를 제공하도록 데이터베이스를 확장하거나 다중 테넌트 데이터베이스에서 테넌트를 제거할 것을 생각할 수 있습니다(다중 테넌트 데이터베이스에서 독립형 데이터베이스로 이동).

**장기적으로** 데이터베이스 성능을 개선하기 위해 쿼리 또는 인덱스의 최적화를 생각할 수 있습니다. 성능 문제에 대한 애플리케이션의 민감도에 따라 데이터베이스가 사용률 100% DTU에 도달하기 전에 데이터베이스를 확장하는 것이 최선일 수 있습니다. 경고를 사용하여 미리 경고합니다.

생성기에 의해 생성된 부하를 증가시켜 사용 중인 데이터베이스를 시뮬레이션할 수 있습니다. 테넌트가 더 자주 그리고 더 오래 버스트하도록 하면 개별 테넌트의 요구 사항을 변경하지 않고 다중 테넌트 데이터베이스에 대한 부하가 증가합니다. 데이터베이스 확장은 포털 내에서 또는 PowerShell에서 쉽게 수행됩니다. 이 연습에서는 포털을 사용합니다.

1. *$DemoScenario* = **3**를 설정하고, _데이터베이스별로 더 오래 더 잦은 버스트를 사용하여 부하를 생성하여_ 각 테넌트 요구하는 최고 부하를 변경하지 않고 데이터베이스에 대한 집계 부하의 강도를 증가시킵니다.
1. **F5**를 눌러 모든 테넌트 데이터베이스에 부하를 적용합니다.
1. Azure Portal에서 **tenants1** 데이터베이스로 이동합니다.

상위 차트에서 증가한 데이터베이스 DTU 사용량을 모니터링합니다. 더 높은 새 부하를 시작하려면 몇 분 걸리지만 데이터베이스가 최대 사용률에 도달하기 시작하는 것을 보고 바로 알 수 있으며 부하가 새로운 패턴으로 안정됨에 따라 데이터베이스에 빠르게 과부하가 걸립니다.

1. 데이터베이스를 확장하려면 설정 블레이드에서 **가격 책정 계층(배율 DTU)** 을 클릭합니다.
1. **DTU** 설정을 **100**으로 조정합니다. 
1. **적용**을 클릭하여 데이터베이스 크기 조정 요청을 제출합니다.

**tenants1** > **개요**로 돌아가 모니터링 차트를 볼 수 있습니다. 데이터베이스에 더 많은 리소스를 제공한 효과를 모니터링합니다. (테넌트 수가 적고 부하가 임의의 값을 갖기 때문에 일정 시간 동안 실행할 때까지 확실한 결과를 보기가 어려울 수 있습니다.) 차트를 볼 때 하위 차트 100%는 여전히 50 DTU이지만 상위 차트의 100%가 이제 100 DTU를 나타낸다는 사실을 염두에 두세요.

데이터베이스는 프로세스 전체에 걸쳐 온라인이고 완전하게 사용할 수 있도록 남아 있습니다. 애플리케이션 코드는 언제나 끊어진 연결을 재시도하도록 작성해야 합니다. 따라서 데이터베이스 연결이 다시 수행됩니다.

## <a name="provision-a-new-tenant-in-its-own-database"></a>새로운 테넌트를 자체 데이터베이스에서 프로비전 

분할된 다중 테넌트 모델을 사용하면 다중 테넌트 데이터베이스에서 다른 테넌트와 함께 새 테넌트를 프로비전할지 아니면 자체 데이터베이스에서 테넌트를 프로비전할지 선택할 수 있습니다. 자체 데이터베이스에서 테넌트를 프로비전하면 별도의 데이터베이스의 격리된 특성 덕분에 다른 테넌트와 독립적으로 테넌트의 성능을 관리하고 테넌트를 개별적으로 복원할 수 있습니다. 예를 들어 다중 테넌트 데이터베이스에 평가판 고객 또는 정규 고객을 배치하고 개별 데이터베이스에 프리미엄 고객을 배치할 수 있습니다.  격리된 단일 테넌트 데이터베이스를 만든 경우에도 탄력적 풀에서 함께 관리하여 리소스 비용을 최적화할 수 있습니다.

자체 데이터베이스에서 새 테넌트를 프로비전했다면 아래의 단계를 건너뜁니다.

1. **PowerShell ISE**에서 …\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*을 엽니다. 
1. **$TenantName = “Salix Sals”** 및 **$VenueType  = “dance”** 로 수정합니다.
1. **$Scenario** = **2**, _새 단일 테넌트 데이터베이스에서 테넌트 프로비전_을 설정합니다.
1. **F5** 키를 눌러 스크립트를 실행합니다.

이 스크립트는 테넌트를 별도의 데이터베이스에서 프로비전하고, 데이터베이스와 테넌트를 카탈로그와 함께 등록하고, 브라우저에서 테넌트의 이벤트 페이지를 엽니다. 이벤트 허브 페이지를 새로 고치면 “Salix Salsa” 행사장이 추가된 것을 볼 수 있습니다.

## <a name="manage-performance-of-an-individual-database"></a>개별 데이터베이스의 성능 관리

다중 테넌트 데이터베이스의 단일 테넌트에 지속적으로 높은 부하가 발생하면 데이터베이스 리소스를 잠식하여 동일한 데이터베이스에 속한 다른 테넌트에 영향을 줄 수 있습니다. 이 활동이 일정 기간 동안 지속될 경우 테넌트를 데이터베이스에서 제거하여 자체 단일 테넌트 데이터베이스로 이동할 수 있습니다. 이렇게 하면 해당 테넌트에 필요한 추가적인 리소스를 제공할 수 있고, 동시에 다른 테넌트로부터 완전히 격리할 수 있습니다.

이 연습에서는 인기 있는 이벤트의 티켓을 판매할 때 높은 부하를 경험하는 Salix Salsa의 영향을 시뮬레이션합니다.

1. ...\\*Demo-PerformanceMonitoringAndManagement.ps1* 스크립트를 엽니다.
1. 설정할 **$DemoScenario = 5**, _표준 부하와 단일 테 넌 트 (약 90 DTU)에 대 한 높은 부하를 생성 합니다._
1. **$SingleTenantName = Salix Salsa**로 설정합니다.
1. **F5**를 사용하여 스크립트를 실행합니다.

포털에서 **salixsalsa** > **개요**로 이동하여 모니터링 차트를 확인합니다. 

## <a name="other-performance-management-patterns"></a>기타 성능 관리 패턴

**테넌트 셀프 서비스 크기 조정**

크기 조정은 관리 API가 통해 쉽게 호출되는 작업이므로 테넌트 데이터베이스를 테넌트가 마주치는 애플리케이션으로 크기 조정하는 기능을 쉽게 만들어 SaaS 서비스의 기능으로 제공할 수 있습니다. 예를 들어 테넌트가 확장과 축소를 직접 관리하게 할 수 있으며, 이는 아마도 테넌트의 대금 청구에 직접 연결될 것입니다!

**데이터베이스를 사용량 패턴에 맞게 일정에 따라 확장 및 축소**

집계 테넌트 사용량이 예측 가능한 사용량 패턴을 따르는 경우 Azure Automation을 사용하여 데이터베이스를 일정에 따라 확장 및 축소할 수 있습니다. 예를 들어 리소스 요구 사항이 떨어지는 것으로 알고 있는 평일 오후 6시 후에 데이터베이스를 축소하고 오전 6시 전에 다시 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 제공된 부하 생성기를 사용하여 분할된 다중 테넌트 데이터베이스에서 사용량 시뮬레이션하기
> * 부하 증가에 대응하여 변화하는 데이터베이스 모니터링하기
> * 데이터베이스 부하 증가에 대응하여 데이터베이스 확장하기
> * 단일 테넌트 데이터베이스에 테넌트 프로비전

## <a name="additional-resources"></a>추가 리소스

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)