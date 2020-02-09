---
title: 네임 스페이스 Azure Service Bus-standard에서 premium으로 마이그레이션
description: 기존 Azure Service Bus 표준 네임 스페이스를 premium으로 마이그레이션할 수 있는 가이드
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 610c3aa486b48b2d29df48d98e93b37cfec4854c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790370"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>기존 Azure Service Bus 표준 네임 스페이스를 프리미엄 계층으로 마이그레이션
이전에는 표준 계층 에서만 네임 스페이스를 제공 Azure Service Bus 합니다. 네임 스페이스는 낮은 처리량 및 개발자 환경에 최적화 된 다중 테넌트의 기능입니다. 프리미엄 계층은 예측 가능한 대기 시간에 대 한 네임 스페이스 당 전용 리소스를 제공 하 고 고정 된 가격으로 처리량을 증가 시킵니다. 프리미엄 계층은 높은 처리량 및 추가 엔터프라이즈 기능이 필요한 프로덕션 환경에 최적화 되어 있습니다.

이 문서에서는 기존 표준 계층 네임 스페이스를 프리미엄 계층으로 마이그레이션하는 방법을 설명 합니다.  

>[!WARNING]
> 마이그레이션은 프리미엄 계층으로 업그레이드 될 Service Bus 표준 네임 스페이스를 위한 것입니다. 마이그레이션 도구는 다운 그레이드를 지원 하지 않습니다.

참고 사항: 
- 이 마이그레이션은 기존 발신자 및 수신자 응용 프로그램에서 **코드 또는 구성을 변경할 필요가**없음을 의미 합니다. 기존 연결 문자열이 자동으로 새 프리미엄 네임 스페이스를 가리킵니다.
- 마이그레이션을 성공적으로 수행 하려면 **premium** 네임 스페이스에 **엔터티가** 없어야 합니다. 
- 표준 네임 스페이스의 모든 **엔터티** 는 마이그레이션 프로세스 중에 프리미엄 네임 스페이스로 **복사** 됩니다. 
- 마이그레이션은 프리미엄 계층에서 **메시징 단위당 1000 엔터티** 를 지원 합니다. 필요한 메시징 단위 수를 식별 하려면 현재 표준 네임 스페이스에 있는 엔터티 수로 시작 합니다. 
- **기본 계층** 에서 **프리미어 계층**으로 직접 마이그레이션할 수는 없지만 다음 단계에서 basic에서 standard로 먼저 마이그레이션하고 표준에서 프리미엄으로 마이그레이션하여 간접적으로 수행할 수 있습니다.

## <a name="migration-steps"></a>마이그레이션 단계
일부 조건은 마이그레이션 프로세스와 연결 됩니다. 오류 발생 가능성을 줄이기 위해 다음 단계를 숙지 합니다. 이러한 단계는 마이그레이션 프로세스를 개략적으로 설명 하 고 다음 섹션에서 단계별 세부 정보를 나열 합니다.

1. 새 프리미엄 네임 스페이스를 만듭니다.
1. 표준 및 프리미엄 네임 스페이스를 서로 쌍으로 연결 합니다.
1. 표준에서 프리미엄 네임 스페이스로 엔터티를 동기화 (복사) 합니다.
1. 마이그레이션을 커밋합니다.
1. 네임 스페이스의 마이그레이션 후 이름을 사용 하 여 표준 네임 스페이스의 엔터티를 드레이닝 합니다.
1. 표준 네임 스페이스를 삭제 합니다.

>[!IMPORTANT]
> 마이그레이션이 커밋된 후 이전 표준 네임 스페이스에 액세스 하 여 큐와 구독을 드레이닝 합니다. 메시지가 배출 된 후에는 받는 사람 응용 프로그램에서 처리 하기 위해 새 프리미엄 네임 스페이스로 전송 될 수 있습니다. 큐 및 구독이 방전 된 후에는 이전 표준 네임 스페이스를 삭제 하는 것이 좋습니다.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용 하 여 마이그레이션

Azure CLI 또는 PowerShell 도구를 사용 하 여 Service Bus 표준 네임 스페이스를 premium으로 마이그레이션하려면 다음 단계를 수행 합니다.

1. 새 Service Bus 프리미엄 네임 스페이스를 만듭니다. [Azure Resource Manager 템플릿을](service-bus-resource-manager-namespace.md) 참조 하거나 [Azure Portal를 사용할](service-bus-create-namespace-portal.md)수 있습니다. **Servicebussku** 매개 변수에 대해 **프리미엄** 을 선택 해야 합니다.

1. 마이그레이션 명령을 간소화 하기 위해 다음 환경 변수를 설정 합니다.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 마이그레이션 후 별칭/이름 (post_migration_dns_name)은 이전 표준 네임 스페이스 마이그레이션 후에 액세스 하는 데 사용 됩니다. 이를 사용 하 여 큐와 구독을 드레이닝 하 고 네임 스페이스를 삭제 합니다.

1. 표준 및 프리미엄 네임 스페이스를 페어링 하 고 다음 명령을 사용 하 여 동기화를 시작 합니다.

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. 다음 명령을 사용 하 여 마이그레이션 상태를 확인 합니다.
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    마이그레이션은 다음과 같은 값을 볼 때 완료 된 것으로 간주 됩니다.
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    이 명령은 마이그레이션 구성도 표시 합니다. 값이 올바르게 설정 되었는지 확인 합니다. 또한 포털에서 프리미엄 네임 스페이스를 확인 하 여 모든 큐 및 항목이 만들어지고 표준 네임 스페이스에 있던 항목과 일치 하는지 확인 합니다.

1. 다음의 전체 명령을 실행 하 여 마이그레이션을 커밋합니다.
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 마이그레이션

Azure Portal를 사용 하 여 마이그레이션하는 경우에는 명령을 사용 하 여 마이그레이션하는 것과 동일한 논리 흐름이 사용 됩니다. Azure Portal를 사용 하 여 마이그레이션하려면 다음 단계를 수행 합니다.

1. 왼쪽 창의 **탐색** 메뉴에서 **premium으로 마이그레이션**을 선택 합니다. **시작** 단추를 클릭 하 여 다음 페이지로 이동 합니다.
    ![마이그레이션 방문 페이지][]

1. **설치**를 완료 합니다.
   설정 네임 스페이스를 ![][]
   1. 프리미엄 네임 스페이스를 만들고 할당 하 여 기존 표준 네임 스페이스를로 마이그레이션합니다.
        ![설정 네임 스페이스-premium 네임 스페이스 만들기][]
   1. **마이그레이션 후 이름을**선택 합니다. 마이그레이션이 완료 된 후이 이름을 사용 하 여 표준 네임 스페이스에 액세스 합니다.
        ![설치 네임 스페이스-마이그레이션 후 이름 선택][]
   1. 계속 하려면 **' 다음 '** 을 선택 합니다.
1. 표준 네임 스페이스와 프리미엄 네임 스페이스 간에 엔터티를 동기화 합니다.
    ![설정 네임 스페이스-동기화 엔터티-시작][]

   1. **동기화 시작** 을 선택 하 여 엔터티 동기화를 시작 합니다.
   1. 대화 상자에서 **예** 를 선택 하 여 동기화를 확인 하 고 시작 합니다.
   1. 동기화가 완료 될 때까지 기다립니다. 상태 표시줄에 상태를 사용할 수 있습니다.
        ![네임 스페이스 동기화 엔터티-진행률][]
        >[!IMPORTANT]
        > 어떤 이유로 든 마이그레이션을 중단 해야 하는 경우이 문서의 FAQ 섹션에서 중단 흐름을 검토 하세요.
   1. 동기화가 완료 되 면 페이지 아래쪽에서 **다음** 을 선택 합니다.

1. 요약 페이지에서 변경 내용을 검토 합니다. **마이그레이션 완료** 를 선택 하 여 네임 스페이스를 전환 하 고 마이그레이션을 완료 합니다.
    ![스위치 네임 스페이스-전환 메뉴][]  
    마이그레이션이 완료 되 면 확인 페이지가 표시 됩니다.
    ![스위치 네임 스페이스-성공][]

## <a name="caveats"></a>주의 사항

Azure Service Bus 표준 계층에서 제공 하는 일부 기능은 Azure Service Bus 프리미엄 계층에서 지원 되지 않습니다. 프리미엄 계층은 예측 가능한 처리량 및 대기 시간에 대 한 전용 리소스를 제공 하기 때문에 이러한 방식으로 설계 되었습니다.

다음은 프리미엄 및 해당 완화에서 지원 되지 않는 기능 목록입니다. 

### <a name="express-entities"></a>Express 엔터티

   저장소에 메시지 데이터를 커밋하지 않는 Express 엔터티는 Premium에서 지원 되지 않습니다. 전용 리소스는 엔터프라이즈 메시징 시스템에서와 같이 데이터가 유지 되도록 하는 동시에 상당한 처리량 향상을 제공 했습니다.
   
   마이그레이션 중에 표준 네임 스페이스의 모든 express 엔터티는 비 express 엔터티로 프리미엄 네임 스페이스에 만들어집니다.
   
   ARM (Azure Resource Manager) 템플릿을 활용 하는 경우 자동화 된 워크플로가 오류 없이 실행 되도록 배포 구성에서 ' enableExpress ' 플래그를 제거 해야 합니다.

### <a name="partitioned-entities"></a>분할 된 엔터티

   분할 된 엔터티는 다중 테넌트 설정에서 더 나은 가용성을 제공 하기 위해 표준 계층에서 지원 됩니다. 프리미엄 계층에서 네임 스페이스 당 전용 리소스를 프로 비전 할 경우이는 더 이상 필요 하지 않습니다.
   
   마이그레이션하는 동안 표준 네임 스페이스에 있는 모든 분할 된 엔터티는 비 분할 된 엔터티로 프리미엄 네임 스페이스에 만들어집니다.
   
   ARM 템플릿에서 특정 큐 또는 토픽에 대해 ' enablePartitioning '을 ' t r u e '로 설정 하면 broker에서 무시 됩니다.

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>마이그레이션이 커밋되면 어떻게 되나요?

마이그레이션이 커밋된 후 표준 네임 스페이스를 가리키는 연결 문자열은 premium 네임 스페이스를 가리킵니다.

발신자 및 수신자 응용 프로그램은 표준 네임 스페이스와의 연결을 끊고 premium 네임 스페이스에 자동으로 다시 연결 합니다.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>표준에서 프리미엄으로의 마이그레이션이 완료 된 후에는 어떻게 해야 하나요?

표준에서 프리미엄으로 마이그레이션하려면 토픽, 구독 및 필터와 같은 엔터티 메타 데이터가 표준 네임 스페이스에서 premium 네임 스페이스로 복사 됩니다. 표준 네임 스페이스에 커밋된 메시지 데이터는 표준 네임 스페이스에서 premium 네임 스페이스로 복사 되지 않습니다.

마이그레이션이 진행 되는 동안에는 표준 네임 스페이스에 전송 되 고 커밋된 일부 메시지가 있을 수 있습니다. 표준 네임 스페이스에서 이러한 메시지를 수동으로 드레이닝 하 고 premium 네임 스페이스로 수동으로 보냅니다. 메시지를 수동으로 드레이닝 하려면 마이그레이션 명령에 지정한 마이그레이션 후 DNS 이름을 사용 하 여 표준 네임 스페이스 엔터티를 드레이닝 하는 스크립트 또는 콘솔 앱을 사용 합니다. 이러한 메시지를 수신자가 처리할 수 있도록 premium 네임 스페이스로 보냅니다.

메시지가 배출 된 후 표준 네임 스페이스를 삭제 합니다.

>[!IMPORTANT]
> 표준 네임 스페이스의 메시지가 방전 된 후 표준 네임 스페이스를 삭제 합니다. 표준 네임 스페이스를 처음 참조 하는 연결 문자열은 이제 프리미엄 네임 스페이스를 참조 하기 때문에이는 중요 합니다. 표준 네임 스페이스는 더 이상 필요 하지 않습니다. 마이그레이션한 표준 네임 스페이스를 삭제 하면 나중에 혼동을 줄일 수 있습니다.

### <a name="how-much-downtime-do-i-expect"></a>얼마나 많은 가동 중지 시간이 필요 한가요?
마이그레이션 프로세스는 응용 프로그램의 예상 가동 중지 시간을 줄이기 위한 것입니다. 발신자 및 수신자 응용 프로그램이 새 프리미엄 네임 스페이스를 가리키는 데 사용 하는 연결 문자열을 사용 하 여 가동 중지 시간을 줄일 수 있습니다.

응용 프로그램에서 발생 하는 가동 중지 시간은 프리미엄 네임 스페이스를 가리키도록 DNS 항목을 업데이트 하는 데 걸리는 시간으로 제한 됩니다. 가동 중지 시간은 약 5 분입니다.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>마이그레이션을 수행 하는 동안 구성을 변경 해야 하나요?
아니요, 마이그레이션을 수행 하는 데 필요한 코드 또는 구성 변경 내용이 없습니다. 보낸 사람 및 받는 사람 응용 프로그램이 표준 네임 스페이스에 액세스 하는 데 사용 하는 연결 문자열은 프리미엄 네임 스페이스에 대 한 별칭 역할을 하도록 자동으로 매핑됩니다.

### <a name="what-happens-when-i-abort-the-migration"></a>마이그레이션을 중단 하면 어떻게 되나요?
@No__t_0 명령을 사용 하거나 Azure Portal를 사용 하 여 마이그레이션을 중단할 수 있습니다. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![중단 흐름-중단 동기화][]
![중단 흐름-중단 완료][]

마이그레이션 프로세스가 중단 되 면 표준에서 프리미엄 네임 스페이스로 엔터티 (토픽, 구독 및 필터)를 복사 하는 프로세스를 중단 하 고 페어링을 중단 합니다.

프리미엄 네임 스페이스를 가리키도록 연결 문자열이 업데이트 되지 않았습니다. 기존 응용 프로그램은 마이그레이션을 시작 하기 전과 마찬가지로 계속 작동 합니다.

그러나 premium 네임 스페이스의 엔터티를 삭제 하거나 premium 네임 스페이스를 삭제 하지 않습니다. 마이그레이션을 사용 하 여 앞으로 이동 하지 않기로 결정 한 경우 수동으로 엔터티를 삭제 합니다.

>[!IMPORTANT]
> 마이그레이션을 중단 하기로 결정 한 경우 리소스에 대 한 요금이 청구 되지 않도록 마이그레이션하기 위해 프로 비전 한 premium 네임 스페이스를 삭제 합니다.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>메시지를 드레이닝 하지 않으려고 합니다. 어떻게 하나요?

마이그레이션이 진행 되는 동안 및 마이그레이션이 커밋될 때까지 발신자 응용 프로그램에서 전송 되 고 표준 네임 스페이스의 저장소에 커밋된 메시지가 있을 수 있습니다.

마이그레이션하는 동안 실제 메시지 데이터/페이로드가 표준에서 프리미엄 네임 스페이스로 복사 되지 않습니다. 메시지는 수동으로 배출 된 후 premium 네임 스페이스로 전송 되어야 합니다.

그러나 계획 된 유지 관리/정리 기간 동안 마이그레이션할 수 있고 메시지를 수동으로 드레이닝 및 보내지 않으려는 경우 다음 단계를 수행 합니다.

1. 발신자 응용 프로그램을 중지 합니다. 받는 사람 응용 프로그램은 현재 표준 네임 스페이스에 있는 메시지를 처리 하 고 큐를 드레이닝 합니다.
1. 표준 네임 스페이스의 큐와 구독이 비어 있는 후 앞에서 설명한 절차에 따라 표준에서 프리미엄 네임 스페이스로 마이그레이션을 실행 합니다.
1. 마이그레이션이 완료 된 후 발신자 응용 프로그램을 다시 시작할 수 있습니다.
1. 이제 발신자와 수신자가 premium 네임 스페이스와 자동으로 연결 됩니다.

    >[!NOTE]
    > 마이그레이션하기 위해 수신자 응용 프로그램을 중지할 필요는 없습니다.
    >
    > 마이그레이션이 완료 되 면 수신자 응용 프로그램은 표준 네임 스페이스와의 연결을 끊고 premium 네임 스페이스에 자동으로 연결 됩니다.

## <a name="next-steps"></a>다음 단계

* [표준 및 프리미엄 메시징의 차이점](./service-bus-premium-messaging.md)에 대해 자세히 알아보세요.
* [Service Bus 프리미엄에 대 한 고가용성 및 지리적 재해 복구 측면](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)에 대해 알아봅니다.

[마이그레이션 방문 페이지]: ./media/service-bus-standard-premium-migration/1.png
[설치 네임 스페이스]: ./media/service-bus-standard-premium-migration/2.png
[설치 네임 스페이스-premium 네임 스페이스 만들기]: ./media/service-bus-standard-premium-migration/3.png
[네임 스페이스 설정-마이그레이션 후 이름 선택]: ./media/service-bus-standard-premium-migration/4.png
[네임 스페이스-동기화 엔터티 설정-시작]: ./media/service-bus-standard-premium-migration/5.png
[네임 스페이스-동기화 엔터티 설정-진행률]: ./media/service-bus-standard-premium-migration/8.png
[네임 스페이스 전환-전환 메뉴]: ./media/service-bus-standard-premium-migration/9.png
[네임 스페이스 전환-성공]: ./media/service-bus-standard-premium-migration/12.png

[흐름 중단-동기화 중단]: ./media/service-bus-standard-premium-migration/abort1.png
[흐름 중단-중단 완료]: ./media/service-bus-standard-premium-migration/abort3.png
