---
title: 기존 Azure Service Bus 표준 네임 스페이스 프리미엄 계층으로 마이그레이션 | Microsoft Docs
description: Premium 기존 Azure Service Bus 표준 네임 스페이스의 마이그레이션을 허용 하도록 가이드
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: d2aa018566695b5b94cd8a7e64931a8b776b151d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766303"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>프리미엄 계층으로 기존 Azure Service Bus 표준 네임 스페이스 마이그레이션
이전에 Azure Service Bus 네임 스페이스의 표준 계층 에서만 제공 됩니다. 네임 스페이스는 개발자 환경과 낮은 처리량에 최적화 된 다중 테 넌 트 설정 합니다. 프리미엄 계층은 예측 가능한 대기 시간 및 고정된 가격으로 향상 된 처리량에 대 한 네임 스페이스 당 전용된 리소스를 제공합니다. 프리미엄 계층은 높은 처리량과 추가 엔터프라이즈 기능을 필요로 하는 프로덕션 환경에 대 한 최적화 됩니다.

이 문서에서는 프리미엄 계층으로 기존 표준 계층 네임 스페이스를 마이그레이션하는 방법을 설명 합니다.

>[!WARNING]
> 마이그레이션은 Service Bus 표준 네임 스페이스에서 프리미엄 계층으로 업그레이드를 위한 것입니다. 마이그레이션 도구를 다운 그레이드 하는 것을 지원 하지 않습니다.

일부의 사항을 유의 해야 합니다. 
- 이 마이그레이션은 기존 즉 현재 위치에서 발생할 것 발신자와 수신자 응용 프로그램 **코드나 구성에 대 한 변경이 필요 하지**합니다. 기존 연결 문자열을 자동으로 새 프리미엄 네임 스페이스를 가리킵니다.
- **premium** 네임 스페이스 있어야 **엔터티가 없습니다.** 에 성공 하려면 마이그레이션에 대 한 합니다. 
- 모든 **엔터티** 표준 네임 스페이스에는 **복사** 마이그레이션 프로세스 중 premium 네임 스페이스에 있습니다. 
- 마이그레이션 지원 **메시징 단위 당 1,000 개의 엔터티** 프리미엄 계층. 필요한 메시징 단위 수를 식별 하려면 현재 표준 네임 스페이스에 있는 엔터티의 수를 사용 하 여 시작 합니다. 

## <a name="migration-steps"></a>마이그레이션 단계
일부 조건 마이그레이션 프로세스에 연결 됩니다. 오류를 방지 하기 위해 다음 단계를 숙지 합니다. 다음이 단계에는 마이그레이션 프로세스를 간략하게 설명 하 고 단계별 세부 정보 섹션에 나열 됩니다.

1. 새 프리미엄 네임 스페이스를 만듭니다.
1. 표준 및 프리미엄 네임 스페이스를 서로 연결 합니다.
1. 표준에서 프리미엄 네임 스페이스에 엔터티를 동기화 (복사-over) 합니다.
1. 마이그레이션을 커밋해야 합니다.
1. 네임 스페이스의 마이그레이션 후 이름을 사용 하 여 표준 네임 스페이스의 엔터티를 드레이닝하십시오.
1. 표준 네임 스페이스를 삭제 합니다.

>[!IMPORTANT]
> 마이그레이션에 커밋된 후 이전 표준 네임 스페이스를 액세스 하 고 드레이닝 큐 및 구독. 메시지가 종료 된 후 이러한 전송할 수 있습니다 새 프리미엄 네임 스페이스 수신자 응용 프로그램에서 처리 되도록 합니다. 큐 및 구독 종료 된 후에 이전 표준 네임 스페이스를 삭제 하는 것이 좋습니다.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용 하 여 마이그레이션

Azure CLI 또는 PowerShell 도구를 사용 하 여 프리미엄 표준 Service Bus 네임 스페이스를 마이그레이션하려면, 다음이 단계를 수행 합니다.

1. 새 Service Bus 프리미엄 네임 스페이스를 만듭니다. 참조할 수 있습니다 합니다 [Azure Resource Manager 템플릿](service-bus-resource-manager-namespace.md) 또는 [Azure portal을 사용 하 여](service-bus-create-namespace-portal.md)입니다. 선택 해야 **premium** 에 대 한 합니다 **serviceBusSku** 매개 변수입니다.

1. 마이그레이션 명령을 단순화 하기 위해 다음 환경 변수를 설정 합니다.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 마이그레이션 후 이름은 (post_migration_dns_name) 이전 표준 네임 스페이스 마이그레이션 후 액세스에 사용 됩니다. 이 사용 하 여 큐 및 구독을 드레이닝 하도록 하 고 네임 스페이스를 삭제 합니다.

1. 표준 및 프리미엄 네임 스페이스 쌍 하 고 다음 명령을 사용 하 여 동기화를 시작 합니다.

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. 다음 명령을 사용 하 여 마이그레이션의 상태를 확인 합니다.
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    다음 값을 표시 하는 경우 마이그레이션은 완료 것으로 간주 됩니다.
    * MigrationState = "활성"
    * pendingReplicationsOperationsCount = 0
    * provisioningState "성공" =

    또한이 명령은 마이그레이션 구성을 표시합니다. 값이 올바르게 설정 되어 있는지 확인 하십시오. 또한 모든 큐와 토픽을 만든 표준 네임 스페이스에 있었으며 무엇과 일치 하는지 확인 하려면 포털에서 프리미엄 네임 스페이스를 확인 합니다.

1. 전체 명령을 실행 하 여 마이그레이션을 커밋하십시오.
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Azure portal을 사용 하 여 마이그레이션

Azure portal을 사용 하 여 마이그레이션 명령을 사용 하 여 마이그레이션할 때와 동일한 논리 흐름을 있습니다. Azure portal을 사용 하 여 마이그레이션하려면 다음이 단계를 수행 합니다.

1. 에 **탐색** 의 왼쪽된 창에서 메뉴 **premium으로 마이그레이션할**합니다. 클릭 합니다 **시작** 단추 다음 페이지로 진행 합니다.
    ![마이그레이션 방문 페이지][]

1. 전체 **설치**합니다.
   ![네임 스페이스를 설정 합니다.][]
   1. 기존 표준 네임 스페이스를 마이그레이션하는 프리미엄 네임 스페이스를 만들고 설정 합니다.
        ![네임 스페이스 설정-프리미엄 네임 스페이스 만들기][]
   1. 선택 된 **마이그레이션 후 이름**합니다. 마이그레이션이 완료 된 후 표준 네임 스페이스에 액세스 하려면이 이름을 사용 합니다.
        ![네임 스페이스 설정-사후 마이그레이션 이름 선택][]
   1. 선택 **'다음'** 를 계속 합니다.
1. 표준 및 프리미엄 네임 스페이스 간 엔터티를 동기화 합니다.
    ![네임 스페이스 동기화 엔터티에-시작 설정][]

   1. 선택 **동기화 시작** 엔터티 동기화를 시작 합니다.
   1. 선택 **예** 확인 하 고 동기화를 시작 하는 대화 상자에서.
   1. 동기화를 마칠 때까지 기다립니다. 상태가 상태 표시줄에서 확인할 수 있습니다.
        ![설치 네임 스페이스 동기화 엔터티에-진행률][]
        >[!IMPORTANT]
        > 마이그레이션을 중단 하 고 어떤 이유로 해야 할 경우이 문서의 FAQ 섹션에서 중단 흐름을 검토 하세요.
   1. 동기화를 완료 한 후 선택 **다음** 페이지의 맨 아래에 있습니다.

1. 요약 페이지에서 변경 내용을 검토 합니다. 선택 **마이그레이션 완료** 네임 스페이스를 전환 하 고 마이그레이션을 완료할 수 있습니다.
    ![네임 스페이스-스위치 메뉴 전환][] 마이그레이션이 완료 되 면 확인 페이지가 나타납니다.
    ![네임 스페이스 스위치-성공][]

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>마이그레이션 커밋될 때 어떻게 되나요?

마이그레이션을 커밋한 후 표준 네임 스페이스를 가리키는 연결 문자열 프리미엄 네임 스페이스를 가리킵니다.

발신자와 수신자 응용 프로그램은 표준 Namespace에서 끊고 프리미엄 네임 스페이스를 자동으로 다시 연결 합니다.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>무엇을 표준-프리미엄 마이그레이션 완료 된 후 합니까?

표준-프리미엄 마이그레이션 엔터티 메타 데이터 항목, 구독 및 필터와 같은 프리미엄 네임 스페이스 표준 네임 스페이스에서 복사 되는 것을 확인 합니다. 프리미엄 네임 스페이스 표준 네임 스페이스에서 표준 네임 스페이스에 커밋된 메시지 데이터를 복사 되지 않습니다.

표준 네임 스페이스에는 전송 된 커밋된 마이그레이션이 진행 되는 동안 일부 메시지가 있을 수 있습니다. 수동으로 표준 Namespace에서 이러한 메시지를 소비 하 고 수동으로 프리미엄 Namespace 보냅니다. 메시지를 수동으로 밖에 없었고이에 콘솔 앱 또는 마이그레이션 명령에 지정 된 게시물 마이그레이션 DNS 이름을 사용 하 여 표준 네임 스페이스 엔터티를 드레이닝하는 스크립트를 사용 합니다. 수신자가 처리할 수 있도록 프리미엄 네임 스페이스에 이러한 메시지를 보냅니다.

메시지를이 종료 된 후에 표준 네임 스페이스를 삭제 합니다.

>[!IMPORTANT]
> 표준 네임 스페이스에서 메시지를이 종료 된 후에 표준 네임 스페이스를 삭제 합니다. 이제 표준 네임 스페이스 참조 처음에 연결 문자열을 프리미엄 네임 스페이스를 가리킵니다.이 중요 합니다. 표준 Namespace를 더 이상 필요 하지 않습니다. 마이그레이션한 표준 네임 스페이스를 삭제 하면 나중에 혼동을 줄일 수 있습니다.

### <a name="how-much-downtime-do-i-expect"></a>가동 중지 시간이 얼마나 부담이 있습니까?
응용 프로그램에 대 한 예상된 가동 중지 시간을 줄이려면 마이그레이션 프로세스를 것입니다. 새 프리미엄 네임 스페이스를 가리키도록 발신자와 수신자 응용 프로그램을 사용 하는 연결 문자열을 사용 하 여 가동 중지 시간이 줄어듭니다.

응용 프로그램에서 발생 하는 가동 중지 시간은 프리미엄 네임 스페이스를 가리키도록 DNS 항목을 업데이트 하는 데 걸리는 시간으로 제한 합니다. 가동 중지 시간은 약 5 분입니다.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>마이그레이션을 수행 하는 동안 구성을 변경 해야 합니까?
아니요, 마이그레이션을 수행 하는 데 필요한 코드 또는 구성 변경 없이 있습니다. 연결 문자열 표준 Namespace 액세스를 사용 하 여 송신자와 수신자 응용 프로그램을 자동으로 프리미엄 네임 스페이스에 대 한 별칭으로 역할에 매핑됩니다.

### <a name="what-happens-when-i-abort-the-migration"></a>I 마이그레이션을 중단 하 고 어떻게 되나요?
마이그레이션을 중단할 수를 사용 하 여는 `Abort` 명령 또는 Azure portal을 사용 하 여 합니다. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portal

![흐름을 중단-동기화 중단][]
![흐름 중단-중단 완료][]

마이그레이션 프로세스 중단 될 때 엔터티 (항목, 구독 및 필터) 표준에서 프리미엄 네임 스페이스 복사 프로세스를 중단 하 고 연결이 중단 합니다.

프리미엄 네임 스페이스를 가리키도록 연결 문자열 업데이트 되지 않습니다. 기존 응용 프로그램 계속 마이그레이션을 시작 하기 전에 마찬가지로 작동 합니다.

그러나 프리미엄 네임 스페이스에 엔터티가 삭제 또는 프리미엄 네임 스페이스를 삭제 하지 않습니다. 마이그레이션 진행 하지 않기로 결정 하는 경우에 엔터티를 수동으로 삭제 하십시오.

>[!IMPORTANT]
> 마이그레이션을 중단 하려는 경우 삭제할 프리미엄 Namespace 리소스에 대 한 요금이 부과 되지 않습니다 있도록 마이그레이션에는 프로 비전 했습니다.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>메시지를 비울 필요가 안 함 어떻게 해야 합니까?

메시지 보낸 사람 응용 프로그램에서 전송 되 고 마이그레이션이 진행 되는 동안 및 마이그레이션 커밋 직전 표준 Namespace에 저장소에 커밋에 있을 수 있습니다.

마이그레이션 중에 실제 메시지 데이터 페이로드 프리미엄 네임 스페이스 표준에서 복사 되지 않습니다. 메시지를 수동으로 종료 하 고 프리미엄 네임 스페이스에 전송 해야 합니다.

그러나 계획 된 유지 관리/관리 기간을 마이그레이션할 수 있습니다. 수동으로 비우고 메시지를 보내는 않으려는 다음과이 같이 하십시오.

1. 보낸 사람 응용 프로그램을 중지 합니다. 받는 사람 응용 프로그램은 현재 표준 네임 스페이스, 큐에 방전 됩니다 하는 메시지를 처리 합니다.
1. 큐 및 구독에서 표준 Namespace 빈 되 면 표준에서 프리미엄 네임 스페이스에 마이그레이션을 실행 하려면 앞에서 설명한 절차를 따릅니다.
1. 마이그레이션이 완료 되 면 발신자 응용 프로그램을 다시 시작할 수 있습니다.
1. 발신자와 수신자가 프리미엄 네임 스페이스를 사용 하 여 자동으로 연결 이제 됩니다.

    >[!NOTE]
    > 마이그레이션에 대 한 받는 사람 응용 프로그램을 중지 필요가 없습니다.
    >
    > 마이그레이션이 완료 되 면 수신자 응용 프로그램은 표준 네임 스페이스에서 끊고 프리미엄 네임 스페이스에 자동으로 연결 합니다.

## <a name="next-steps"></a>다음 단계

* 에 대 한 자세한 정보는 [표준 및 프리미엄 메시징 차이점](./service-bus-premium-messaging.md)합니다.
* 에 대 한 자세한 합니다 [Service Bus 프리미엄에 대 한 고가용성 및 지리적 재해 복구 측면](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)합니다.

[마이그레이션 방문 페이지]: ./media/service-bus-standard-premium-migration/1.png
[네임 스페이스를 설정 합니다.]: ./media/service-bus-standard-premium-migration/2.png
[네임 스페이스 설정-프리미엄 네임 스페이스 만들기]: ./media/service-bus-standard-premium-migration/3.png
[네임 스페이스 설정-사후 마이그레이션 이름 선택]: ./media/service-bus-standard-premium-migration/4.png
[네임 스페이스 동기화 엔터티에-시작 설정]: ./media/service-bus-standard-premium-migration/5.png
[설치 네임 스페이스 동기화 엔터티에-진행률]: ./media/service-bus-standard-premium-migration/8.png
[네임 스페이스-스위치 메뉴 전환]: ./media/service-bus-standard-premium-migration/9.png
[네임 스페이스 스위치-성공]: ./media/service-bus-standard-premium-migration/12.png

[흐름을 중단-동기화를 중단 합니다.]: ./media/service-bus-standard-premium-migration/abort1.png
[흐름을 중단-중단 완료]: ./media/service-bus-standard-premium-migration/abort3.png
