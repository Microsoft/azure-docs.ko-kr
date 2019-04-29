---
title: Azure Site Recovery를 사용하여 재해 복구에서 복구 계획 사용 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하여 재해 복구에 대한 복구 계획을 사용하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 520f30b5fabebf299b5407a502b76d7d30850bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947354"
---
# <a name="about-recovery-plans"></a>복구 계획 정보

이 문서는 [Azure Site Recovery](site-recovery-overview.md)에서 복구 계획을 설명합니다.

복구 계획은 복구 그룹으로 컴퓨터를 수집합니다. 순서, 지침 및 수행할 작업을 추가하여 계획을 사용자 지정할 수 있습니다. 계획을 정의한 후에 장애 조치(failover)를 실행할 수 있습니다.



## <a name="why-use-a-recovery-plan"></a>복구 계획을 사용하는 이유는 무엇인가요?

복구 계획은 장애 조치(failover)할 수 있는 독립적인 작은 단위를 만들어서 체계적인 복구 프로세스를 정의하는 데 도움이 됩니다. 단위는 일반적으로 사용자 환경에서 앱을 나타냅니다. 복구 계획은 컴퓨터가 장애 조치(failover)되는 방식 및 장애 조치(failover) 후 시작되는 순서를 정의합니다. 다음에 복구 계획을 사용합니다.

* 해당 종속성 주변의 앱을 모델링합니다.
* RTO를 줄이기 위해 복구 작업을 자동화합니다.
* 앱이 복구 계획의 일부인지 확인하여 마이그레이션 또는 재해 복구를 위해 준비되었는지 확인합니다.
* 복구 계획에서 테스트 장애 조치(failover)를 실행하여 재해 복구 또는 마이그레이션이 예상대로 작동하는지 확인합니다.


## <a name="model-apps"></a>앱 모델링

앱 특정 속성을 캡처하도록 복구 그룹을 계획하고 만들 수 있습니다. 예를 들어 SQL 서버 백 엔드, 미들웨어 및 웹 프런트 엔드의 일반적인 3계층 애플리케이션을 생각해 봅시다. 일반적으로 각 계층의 컴퓨터에서 장애 조치(failover) 후 올바른 순서로 시작하도록 복구 계획을 사용자 지정합니다.

    - SQL 백 엔드를 먼저, 미들웨어를 다음으로, 웹 프런트 엔드를 마지막으로 시작해야 합니다.
    - 이러한 시작 순서를 통해 마지막 컴퓨터가 시작되면 앱이 작동하도록 합니다.
    - 이 순서를 사용하면 미들웨어가 시작되고 SQL Server 계층에 연결하려고 시도할 때, SQL Server 계층이 이미 실행 중입니다. 
    - 이 순서는 또한 모든 구성 요소가 시작되어 실행되고 앱이 요청을 수락할 준비가 되기 전에 최종 사용자가 앱 URL에 연결되지 않도록 프런트 엔드 서버가 마지막에 시작되도록 할 수도 있습니다.

이 순서를 만들려면 복구 그룹에 그룹을 추가하고, 그룹에 컴퓨터를 추가합니다.
- 순서가 지정되면 시퀀싱이 사용됩니다. 작업은 애플리케이션 복구 RTO를 향상시키도록 적절한 곳에서 병렬로 실행됩니다.
- 단일 그룹의 컴퓨터는 병렬로 장애 조치(failover)됩니다.
- 서로 다른 그룹의 컴퓨터는 그룹 1의 모든 컴퓨터가 장애 조치(failover)되고 시작된 후에만 그룹 2 컴퓨터가 해당 장애 조치(failover)를 시작하도록 그룹 순서로 장애 조치(failover)됩니다.

    ![예제 복구 계획](./media/recovery-plan-overview/rp.png)

이 사용자 지정을 준비하여 복구 계획에서 장애 조치(failover)를 실행할 때 다음이 발생합니다. 

1. 종료 단계는 온-프레미스 컴퓨터를 끄려고 합니다. 예외는 테스트 장애 조치(failover)를 실행하는 경우이며 이 경우 기본 사이트는 계속 실행됩니다. 
2. 종료는 복구 계획의 모든 컴퓨터에 대한 병렬 장애 조치(failover)를 트리거합니다.
3. 장애 조치(failover)는 복제된 데이터를 사용하여 가상 머신 디스크를 준비합니다.
4. 시작 그룹이 순서대로 실행되고 각 그룹에서 컴퓨터가 시작됩니다. 먼저 그룹 1이 실행된 다음, 그룹2, 마지막으로 그룹 3이 실행됩니다. 모든 그룹에 둘 이상의 컴퓨터가 있는 경우 모든 컴퓨터가 병렬로 시작됩니다.


## <a name="automate-tasks"></a>작업 자동화

대규모 애플리케이션 복구는 복잡한 작업일 수 있습니다. 수동 단계는 프로세스를 오류가 발생하기 쉽게 만들고, 장애 조치(failover)를 실행하는 사용자는 모든 앱 복잡성을 인식하지 못할 수 있습니다. 복구 계획을 사용하여 순서를 적용하고 Azure 또는 스크립트로 장애 조치(failover)에 대한 Azure Automation Runbook을 사용하여 각 단계에 필요한 작업을 자동화할 수 있습니다. 자동화할 수 없는 작업의 경우 복구 계획에 수동 작업에 대한 일시 중지를 삽입할 수 있습니다. 두 가지 유형의 작업을 구성할 수 있습니다.

* **장애 조치(failover) 후 Azure VM에서 작업**: Azure로 장애 조치(failover)하는 경우 일반적으로 장애 조치(failover) 후 VM에 연결할 수 있도록 작업을 수행해야 합니다. 예를 들면 다음과 같습니다. 
    * Azure VM에서 공용 IP 주소를 만듭니다.
    * Azure VM의 네트워크 어댑터에 네트워크 보안 그룹을 할당합니다.
    * 가용성 집합에 부하 분산 장치를 추가합니다.
* **장애 조치(failover) 후 VM 내 작업**: 이러한 작업은 일반적으로 새로운 환경에서 앱이 올바르게 작동하도록 머신에서 실행되는 앱을 재구성합니다. 예를 들면 다음과 같습니다.
    * 컴퓨터 내에서 데이터베이스 연결 문자열을 수정합니다.
    * 웹 서버 구성 또는 규칙을 변경합니다.


## <a name="test-failover"></a>테스트 장애 조치

복구 계획을 사용하여 테스트 장애 조치(failover)를 트리거할 수 있습니다. 다음 모범 사례를 사용합니다.

- 항상 전체 장애 조치(failover)를 실행하기 전에 앱에서 테스트 장애 조치(failover)를 완료합니다. 테스트 장애 조치(failover)는 앱이 복구 사이트에서 작동되는지 여부를 확인하는 데 도움이 됩니다.
- 누락된 것을 발견한 경우 정리를 트리거한 다음, 테스트 장애 조치(failover)를 다시 실행합니다. 
- 앱이 원활하게 복구될 때까지 테스트 장애 조치(failover)를 여러 번 실행합니다.
- 각 앱은 고유하기 때문에 각 애플리케이션에 맞게 사용자 지정된 복구 계획을 구축하고 각각에서 테스트 장애 조치(failover)를 실행해야 합니다.
- 앱 및 해당 종속성은 자주 변경됩니다. 복구 계획을 최신 상태로 유지하려면 각 앱에 대해 매 분기별로 테스트 장애 조치(failover)를 실행합니다.

    ![Site Recovery의 테스트 복구 계획 예제 스크린샷](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>비디오 보기

2 계층 WordPress 앱에 대 한 클릭 장애 조치를 보여 주는 빠른 예제 비디오를 시청 하세요.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>다음 단계

- 복구 계획을 [만듭니다](site-recovery-create-recovery-plans.md).
- [장애 조치(failover) 실행](site-recovery-failover.md)에 대해 알아봅니다.  
