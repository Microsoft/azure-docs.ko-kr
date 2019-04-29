---
title: SaaS 다중 테넌트 Azure에서 프로비전 | Microsoft Docs
description: Azure SQL Database 다중 테넌트 SaaS 앱에서 새 테넌트를 프로비전하고 카탈로그로 만드는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: d29baaad6090cea5eb31f5f50bba444cb3771155
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485981"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>공유 다중 테넌트 Azure SQL 데이터베이스를 사용하여 SaaS 애플리케이션에 새 테넌트를 프로비전하고 카탈로그로 만들기

이 문서에서는 *다중 분할된 데이터베이스* 모델 또는 패턴에서 새 테넌트의 프로비전 및 카탈로그 만들기에 대해 다룹니다.

이 문서에는 두 개의 주요 부분에 있습니다.

- [개념적 토론](#goto_2_conceptual)은 새 테넌트의 프로비전 및 카탈로그를 만듭니다.

- [자습서](#goto_1_tutorial)는 프로비전 및 카탈로그 작업을 수행하는 PowerShell 스크립트 코드를 강조합니다.
  - 자습서는 다중 테넌트 분할 데이터베이스 패턴에 맞게 Wingtip Tickets SaaS 애플리케이션을 사용합니다.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>데이터베이스 패턴

이 섹션 및 이어지는 몇 가지는 다중 테넌트 분할 데이터베이스 패턴의 개념을 설명합니다.

이 다중 테넌트 분할 모델에서 각 데이터베이스 내 테이블 스키마에는 테넌트 데이터를 저장하는 테이블의 기본 키에 테넌트 키를 포함합니다. 테넌트 키를 통해 각 개별 데이터베이스는 0, 1 또는 여러 테넌트를 저장할 수 있습니다. 분할된 데이터베이스를 사용하면 애플리케이션 시스템은 손쉽게 매우 많은 테넌트를 지원할 수 있습니다. 한 테넌트에 대한 모든 데이터는 하나의 데이터베이스에 저장됩니다. 테넌트 수가 많으면 여러 분할된 데이터베이스로 분산됩니다. 카탈로그 데이터베이스는 해당 데이터베이스에 각 테넌트의 매핑을 저장합니다.

#### <a name="isolation-versus-lower-cost"></a>격리 대 저렴한 비용

자체에 데이터베이스가 있는 테넌트는 격리의 이점을 이용할 수 있습니다. 테넌트는 다른 테넌트에 대한 영향으로 인한 제한을 받지 않으면서 이전 날짜로 복원된 데이터베이스를 가질 수 있습니다. 데이터베이스 성능은 다시 다른 테넌트를 손상시키지 않으면서 한 테넌트에 최적화되도록 조정할 수 있습니다. 문제는 격리 비용이 다른 테넌트와 데이터베이스를 공유하는 데 드는 비용에 비해 더 높다는 점입니다.

새로운 테넌트가 프로비전되면 다른 테넌트와 데이터베이스를 공유하거나 새로운 고유 데이터베이스에 배치할 수 있습니다. 나중에 생각이 바뀌면 데이터베이스를 다른 상황으로 이동할 수 있습니다.

다중 테넌트 및 단일 테넌트를 사용하는 데이터베이스는 동일한 SaaS 애플리케이션에서 혼합되어 각 테넌트의 비용 또는 격리 수준을 최적화할 수 있습니다.

   ![테넌트 카탈로그가 있는 공유 다중 테넌트 데이터베이스 앱](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>테넌트 카탈로그 패턴

각기 하나 이상의 테넌트를 포함하는 둘 이상의 데이터베이스가 있는 경우 애플리케이션은 현재 관심 테넌트를 저장하는 데이터베이스를 검색할 수 있어야 합니다. 카탈로그 데이터베이스는 이러한 매핑을 저장합니다.

#### <a name="tenant-key"></a>테넌트 키

각 테넌트의 경우 Wingtip 애플리케이션은 테넌트 키인 고유 키를 파생할 수 있습니다. 앱은 웹 페이지 URL에서 테넌트 이름을 추출합니다. 앱은 키를 가져오기 위해 이름을 해시합니다. 앱은 카탈로그에 액세스하는 키를 사용합니다. 테넌트가 저장되는 데이터베이스에 대한 카탈로그 상호 참조 정보입니다. 앱은 연결을 위해 데이터베이스 정보를 사용합니다. 다른 테넌트 키 스키마를 사용할 수도 있습니다.

카탈로그를 사용하면 프로비전 후 애플리케이션을 방해하지 않고 테넌트 데이터베이스의 이름 또는 위치를 변경할 수 있습니다. 다중 테넌트 데이터베이스 모델에서 카탈로그는 데이터베이스 간에 테넌트를 이동할 수 있습니다.

#### <a name="tenant-metadata-beyond-location"></a>테넌트 메타데이터 위치 초과

카탈로그는 테넌트가 유지 관리 또는 기타 작업에 대해 오프라인 상태인지 여부를 나타낼 수도 있습니다. 또한 카탈로그는 다음 항목과 같이 추가 테넌트나 데이터베이스 메타데이터를 저장하도록 확장할 수 있습니다.
- 데이터베이스의 서비스 계층 또는 버전.
- 데이터베이스 스키마의 버전.
- 테넌트 이름 및 해당 SLA(서비스 수준 약정).
- 애플리케이션 관리, 고객 지원 또는 devops 프로세스를 사용하도록 설정하기 위한 정보.  

카탈로그를 사용하여 테넌트 간 보고, 스키마 관리, 분석용 데이터 추출을 지원할 수도 있습니다. 

### <a name="elastic-database-client-library"></a>Elastic Database 클라이언트 라이브러리 

Wingtip에서 카탈로그는 *tenantcatalog* 데이터베이스에서 구현됩니다. *tenantcatalog*는 [EDCL(Elastic Database 클라이언트 라이브러리)](sql-database-elastic-database-client-library.md)의 분할 관리 기능을 사용하여 생성됩니다. 라이브러리는 애플리케이션에서 데이터베이스에 저장된 기반 *분할 맵*을 생성, 관리 및 사용할 수 있게 해줍니다. 분할 맵은 분할된 데이터베이스인 해당 분할을 통해 테넌트 키를 상호 참조합니다.

테넌트를 프로비전하는 동안 분할 맵에서 항목을 만들도록 애플리케이션 또는 PowerShell 스크립트에서 EDCL 함수를 사용할 수 있습니다. 나중에 올바른 데이터베이스에 연결하도록 EDCL 함수를 사용할 수 있습니다. EDCL은 연결 정보를 캐시하여 카탈로그 데이터베이스에 대한 트래픽을 최소화하고 연결 속도를 높입니다.

> [!IMPORTANT]
> 직접 액세스를 통해 카탈로그 데이터베이스에서 데이터를 편집하지 *마십시오*! 직접 업데이트는 데이터 손상의 위험이 높아 지원되지 않습니다. 대신, EDCL API만 사용하여 매핑 데이터를 편집합니다.

## <a name="tenant-provisioning-pattern"></a>테넌트 프로비전 패턴

#### <a name="checklist"></a>검사 목록

기존 공유 데이터베이스에 새 테넌트를 프로비전하려는 경우 공유 데이터베이스에서 다음 사항을 확인해야 합니다.
- 새 테넌트용으로 남겨 둔 공간이 충분합니까?
- 테이블에 새 테넌트에 대한 필요한 참조 데이터가 있습니까? 또는 데이터를 추가할 수 있습니까?
- 새 테넌트에 대해 기본 스키마의 적절한 변형이 있습니까?
- 새 테넌트와 가까운 적절한 지리적 위치에 있습니까?
- 새 테넌트에 대해 올바른 서비스 계층에 있나요?

새 테넌트를 자체 고유한 데이터베이스에서 격리하려고 하는 경우 테넌트에 대한 사양을 충족시키도록 만들 수 있습니다.

프로비전이 완료된 후 테넌트를 카탈로그에 등록해야 합니다. 마지막으로, 적절한 분할된 데이터베이스를 참조하도록 테넌트 매핑을 추가할 수 있습니다.

#### <a name="template-database"></a>템플릿 데이터베이스

SQL 스크립트를 실행하거나 bacpac을 배포하거나 템플릿 데이터베이스를 복사하여 데이터베이스를 프로비전합니다. Wingtip 앱은 템플릿 데이터베이스를 복사하여 새 테넌트 데이터베이스를 만듭니다.

모든 애플리케이션과 마찬가지로 Wingtip은 점점 발전합니다. 때때로 Wingtip은 데이터베이스를 변경해야 합니다. 변경 내용에는 다음 항목이 포함될 수 있습니다.
- 새롭거나 변경된 스키마.
- 새롭거나 변경된 참조 데이터.
- 최적의 앱 성능을 보장하기 위한 일상적인 데이터베이스 유지 관리 작업.

SaaS 애플리케이션에서 이러한 변경 내용은 테넌트 데이터베이스의 잠재적인 대규모 fleet에 통합된 방식으로 배포되어야 합니다. 이러한 변경 내용을 이후 테넌트 데이터베이스에 포함하려면 프로비전 프로세스에 해당 변경 내용을 통합해야 합니다. 이 문제는 [스키마 관리 자습서](saas-tenancy-schema-management.md)에서 더 자세히 다룹니다.

#### <a name="scripts"></a>스크립트

이 자습서의 테넌트 프로비전 스크립트는 다음과 같은 시나리오를 모두 지원합니다.
- 테넌트를 다른 테넌트와 공유하는 기존 데이터베이스에 프로비전.
- 테넌트를 자체 데이터베이스에 프로비전.

그런 다음 테넌트 데이터가 초기화되고 카탈로그 분할 맵에 등록됩니다. 샘플 앱에서 여러 테넌트를 포함하는 데이터베이스는 *tenants1* 또는 *tenants2*와 같은 제네릭 이름이 지정됩니다. 단일 테넌트를 포함하는 데이터베이스에는 테넌트의 이름이 지정됩니다. 샘플에 사용 되는 특정 명명 규칙은 패턴에서 중요한 부분이 아닙니다. 카탈로그를 사용하면 데이터베이스에 아무 이름이나 할당할 수 있기 때문입니다.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>자습서 시작

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 다중 테넌트 데이터베이스에 테넌트 프로비전
> * 단일 테넌트 데이터베이스에 테넌트 프로비전
> * 다중 테넌트 및 단일 테넌트 데이터베이스에 테넌트 일괄 처리 프로 비전
> * 카탈로그에 데이터베이스 및 테넌트 매핑 등록

#### <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

- Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

- Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱이 배포되어 있어야 합니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)을 참조하세요.

- Wingtip 스크립트 및 소스 코드를 가져옵니다.
    - Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 리포지토리에서 확인할 수 있습니다.
    - Wingtip 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 참조하세요. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>다른 테넌트와 *공유하는* 데이터베이스에 테넌트 프로비전

이 섹션에서는 PowerShell 스크립트에서 수행한 프로비전에 대한 주요 동작의 목록을 표시합니다. 그런 다음 PowerShell ISE 디버거를 사용하여 코드에서 작업을 참조하는 스크립트를 단계별로 실행합니다.

#### <a name="major-actions-of-provisioning"></a>프로비전의 주요 작업

다음은 단계별로 실행하는 프로비전 워크플로의 주요 요소입니다.

- **새 테넌트 키 계산**: 테넌트 이름에서 테넌트 키를 만드는 데에는 해시 함수가 사용됩니다.
- **테넌트 키가 이미 있는지 확인**: 카탈로그를 확인하여 키가 아직 등록되지 않았는지 확인합니다.
- **기본 테넌트 데이터베이스에서 테넌트 초기화**: 새 테넌트 정보를 추가하도록 테넌트 데이터베이스가 업데이트됩니다.  
- **카탈로그에 테넌트 등록**: 새 테넌트 키와 기존 tenants1 데이터베이스 간의 매핑이 카탈로그에 추가됩니다. 
- **카탈로그 확장 테이블에 테넌트 이름 추가**: 장소 이름이 카탈로그의 테넌트 테이블에 추가됩니다.  이 추가는 추가적인 애플리케이션 관련 데이터를 지원하도록 카탈로그 데이터베이스를 확장하는 방법입니다.
- **새 테넌트에 대한 이벤트 페이지 열기**: 브라우저에서 *Bushwillow Blues* 이벤트 페이지가 열립니다.

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>디버거 단계

Wingtip 앱이 공유 데이터베이스에 새 테넌트를 프로비전하는 방법을 이해하려면 중단점을 추가하여 워크플로를 단계별로 실행합니다.

1. *PowerShell ISE*에서 ...\\학습 모듈\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*을 열고 다음 매개 변수를 설정합니다.
   - **$TenantName** = **Bushwillow 블루스**: 새 장소의 이름.
   - **$VenueType** = **블루스**: 미리 정의된 장소 유형 중 하나: 블루스, 클래식 음악, 댄스, 재즈, 유도, 자동차 경주, 다목적, 오페라, 록 음악, 축구(공백 없이 소문자로).
   - **$DemoScenario** = **1**: 다른 테넌트와 공유하는 데이터베이스에 테넌트 프로비전.

2. 커서를 38행(*New-Tenant `*)의 아무 위치에 놓고 중단점을 추가한 다음, **F9** 키를 누릅니다.

   ![중단점](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. **F5** 키를 눌러 스크립트를 실행합니다.

4. 스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 한 단계씩 코드를 실행합니다.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. **디버그** 메뉴 옵션(**F10** 및 **F11** 키)을 사용하여 스크립트 실행을 추적하면서 피호출 함수로 이동합니다.

PowerShell 스크립트를 디버깅하는 방법에 대한 자세한 내용은 [PowerShell 스크립트 사용 및 디버깅 관련 팁](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)을 참조하세요.

## <a name="provision-a-tenant-in-its-own-database"></a>테넌트를 *자체* 데이터베이스에 프로비전

#### <a name="major-actions-of-provisioning"></a>프로비전의 주요 작업

다음은 스크립트를 추적하는 동안 단계별로 실행하는 워크플로의 주요 요소입니다.

- **새 테넌트 키 계산**: 테넌트 이름에서 테넌트 키를 만드는 데에는 해시 함수가 사용됩니다.
- **테넌트 키가 이미 있는지 확인**: 카탈로그를 확인하여 키가 아직 등록되지 않았는지 확인합니다.
- **새 테넌트 데이터베이스 만들기**: Resource Manager 템플릿을 사용해 *basetenantdb*를 복사하여 데이터베이스를 만듭니다.  새 데이터베이스 이름은 테넌트 이름을 기반으로 합니다.
- **카탈로그에 데이터베이스 추가**: 새 테넌트 데이터베이스가 카탈로그에 하나의 분할된 데이터베이스로 등록됩니다.
- **기본 테넌트 데이터베이스에서 테넌트 초기화**: 새 테넌트 정보를 추가하도록 테넌트 데이터베이스가 업데이트됩니다.  
- **카탈로그에 테넌트 등록**: 새 테넌트 키와 *sequoiasoccer* 데이터베이스 간의 매핑이 카탈로그에 추가됩니다.
- **카탈로그에 테넌트 이름 추가**: 장소 이름이 카탈로그의 테넌트 확장 테이블에 추가됩니다.
- **새 테넌트에 대한 이벤트 페이지 열기**: 브라우저에서 *Sequoia Soccer* 이벤트 페이지가 열립니다.

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>디버거 단계

이제 자체 데이터베이스에 테넌트를 만드는 스크립트 프로세스를 단계별로 알아보겠습니다.

1. ...\\학습 모듈\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*을 열고 다음 매개 변수를 설정합니다.
   - **$TenantName** = **Sequoia Soccer**: 새 장소의 이름.
   - **$VenueType** = **축구**: 미리 정의된 장소 유형 중 하나: 블루스, 클래식 음악, 댄스, 재즈, 유도, 자동차 경주, 다목적, 오페라, 록 음악, 축구(공백 없이 소문자로).
   - **$DemoScenario** = **2**: 자체 데이터베이스에 테넌트 프로비전.

2. 커서를 57행(*&&nbsp;$PSScriptRoot\New-TenantAndDatabase `*)의 아무 위치에 두고 새로운 중단점을 추가한 다음 **F9**를 누릅니다.

   ![중단점](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. **F5** 키를 눌러 스크립트를 실행합니다.

4. 스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 한 단계씩 코드를 실행합니다.  **F10** 및 **F11** 키를 사용하여 함수를 건너뛰거나 시작하면서 실행을 추적합니다.

## <a name="provision-a-batch-of-tenants"></a>테넌트의 배치 프로비전

이 연습에서는 17개의 테넌트의 배치를 프로비전합니다. 데이터베이스가 여러 개 있도록 다른 Wingtip Tickets 자습서를 시작하기 전에 이 테넌트의 일괄 처리를 프로비전하는 것이 좋습니다.

1. *PowerShell ISE*에서 ...\\학습 모듈\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*을 열고 *$DemoScenario* 매개 변수를 4로 변경합니다.
   - **$DemoScenario** = **4**: 공유 데이터베이스에 테넌트 일괄 처리 프로비전.

2. **F5**를 누르고 스크립트를 실행합니다.

### <a name="verify-the-deployed-set-of-tenants"></a>배포된 테넌트 집합 확인 

이 단계에서는 공유 데이터베이스에 혼합 테넌트가 배포되었고, 테넌트가 자체 데이터베이스에 배포되었습니다. Azure Portal을 사용하여 생성된 데이터베이스를 검사할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 SQL 서버 목록으로 이동하여 **tenants1-mt-\<USER\>** 서버를 엽니다.  **SQL 데이터베이스** 목록에 공유 데이터베이스 **tenants1** 및 자체 데이터베이스에 있는 테넌트의 데이터베이스가 표시될 것입니다.

   ![데이터베이스 목록](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Azure Portal은 테넌트 데이터베이스를 표시하지만 공유 데이터베이스 *내부*의 테넌트는 볼 수 없습니다. 테넌트 전체 목록은 Wingtip **Events Hub** 페이지에서 카탈로그를 탐색하여 확인할 수 있습니다.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Wingtip 티켓 이벤트 허브 페이지 사용

브라우저에서 이벤트 허브 페이지 열기(http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>카탈로그 데이터베이스 사용

테넌트의 전체 목록 및 각각에 해당하는 데이터베이스는 카탈로그에서 사용할 수 있습니다. 테넌트 이름을 데이터베이스 이름에 조인하는 SQL 보기가 제공됩니다. 이 보기는 카탈로그에 저장된 메타데이터를 확장하면 어떤 가치가 있는지 아주 잘 보여 줍니다.
- SQL 보기는 tenantcatalog 데이터베이스에서 사용할 수 있습니다.
- 테넌트 이름이 테넌트 테이블에 저장됩니다.
- 데이터베이스 이름은 분할 관리 테이블에 저장됩니다.

1. SQL Server 관리 Studio (SSMS)에서 테 넌 트 서버에 연결할 **catalog-mt\<사용자\>. database.windows.net**, 로그인 = **개발자**, 및 암호 = **P\@ssword1**

    ![SSMS 연결 대화 상자](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. SSMS 개체 탐색기에서 *tenantcatalog* 데이터베이스의 보기를 찾습니다.

3. *TenantsExtended* 보기를 마우스 오른쪽 단추로 클릭하고 **상위 1000개 행 선택**을 선택합니다. 테넌트 이름과 다른 테넌트의 데이터베이스 간 매핑을 확인합니다.

    ![SSMS의 ExtendedTenants 보기](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>기타 프로비전 패턴

이 섹션에서는 다른 흥미로운 프로비전 패턴을 설명합니다.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>탄력적 풀에 데이터베이스를 미리 프로비전

미리 프로비전은 탄력적 풀을 사용하는 경우 데이터베이스가 아닌 풀을 대상으로 요금이 청구된다는 것을 활용하는 패턴입니다. 따라서 필요할 때 추가 비용 없이 데이터베이스를 탄력적 풀에 추가할 수 있습니다. 이 미리 프로비전은 데이터베이스에 테넌트를 프로비전하는 데 걸리는 시간을 대폭 단축시킵니다. 버퍼를 예상 프로비전 속도에 적합하게 유지하기 위해 필요한 경우 미리 프로비전한 데이터베이스 수를 조정할 수 있습니다.

#### <a name="auto-provisioning"></a>자동 프로비전

자동 프로비전 패턴에서는 전용 프로비전 서비스를 사용하여 서버, 풀 및 필요한 경우 데이터베이스를 자동으로 프로비전합니다. 이 자동화는 탄력적 풀에 데이터베이스의 미리 프로비전을 포함합니다. 또한 데이터베이스가 디커미션되고 삭제된 경우 원한다면 이로 인해 발생하는 탄력적 풀의 빈 공간을 프로비전 서비스로 채울 수 있습니다.

자동화된 서비스의 이 유형은 간단할 수도 있고 복잡할 수도 있습니다. 예를 들어 자동화는 여러 지리적 영역에 걸쳐 프로비전을 처리할 수도 있고, 재해 복구에 대해 지역에서 복제를 설정할 수도 있습니다. 자동 프로비전 패턴에서 클라이언트 애플리케이션 또는 스크립트는 프로비전 서비스에 의해 처리할 프로비전 요청을 큐에 제출합니다. 그런 다음 스크립트는 폴링하여 완료를 결정할 수 있습니다. 미리 프로비전을 사용하는 경우 요청이 신속하게 처리되고, 다른 서비스가 백그라운드에서 대체 데이터베이스 프로비전을 관리합니다.

## <a name="additional-resources"></a>추가 리소스

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)
- [Windows PowerShell ISE에서 스크립트를 디버그하는 방법](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 새로운 단일 테넌트를 공유 다중 테넌트 데이터베이스 및 자체 데이터베이스에 프로비전
> * 추가 테넌트의 배치 프로비전
> * 테넌트를 프로비전하고 카탈로그에 테넌트를 등록하는 구체적인 방법을 단계별로 알아봅니다.

[성능 모니터링 자습서](saas-multitenantdb-performance-monitoring.md)를 시도해 봅니다.

