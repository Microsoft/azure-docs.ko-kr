---
title: 처리량 단위 자동 확장 - Azure Event Hubs | Microsoft Docs
description: 처리량 단위를 자동으로 확장하려면 네임스페이스에서 자동 확장을 사용합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d705993c7cd3816e89da21625dc5b003435b9128
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822718"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs 처리량 단위 자동 확장
Azure Event Hubs는 확장성이 뛰어난 데이터 스트리밍 플랫폼입니다. 따라서 서비스를 사용하기 시작한 후에 Event Hubs 사용량이 증가합니다. 그러려면 Event Hubs의 크기를 조정하고 더 빠른 전송 속도를 처리할 수 있도록 미리 지정된 [처리량 단위](event-hubs-features.md#throughput-units)를 늘려야 합니다. Event Hubs의 **자동 확장** 기능은 필요한 사용량에 맞게 처리량 단위 수를 증가하여 자동으로 확장합니다. 처리량 단위를 늘리면 다음과 같은 상황에서 제한 시나리오를 예방할 수 있습니다.

* 데이터 수신 속도가 설정된 처리량 단위를 초과하는 경우.
* 데이터 송신 요청 속도가 설정된 처리량 단위를 초과하는 경우.

ServerBusy 오류로 인한 요청 실패 없이 부하가 최소 임계값을 초과하면 Event Hubs 서비스는 처리량을 높입니다.

## <a name="how-auto-inflate-works"></a>자동 확장 작동 방식

Event Hubs 트래픽은 [처리량 단위](event-hubs-features.md#throughput-units)로 제어됩니다. 단일 처리량 단위는 수신의 경우 1MB/초, 송신의 경우 그 두 배입니다. Standard Event Hubs는 처리량 단위를 1-20으로 구성할 수 있습니다. 자동 확장 기능을 사용하면 선택한 필요한 최소 처리량 단위의 작은 규모부터 시작할 수 있습니다. 그런 다음 트래픽 증가에 따라 필요한 처리량 단위의 최대 한도까지 자동으로 조정합니다. 자동 확장의 이점은 다음과 같습니다.

- 작은 규모부터 시작하여 사용량 증가에 따라 확장하는 효율적인 크기 조정 메커니즘입니다.
- 제한 문제 없이 지정된 상한까지 자동으로 크기 조정합니다.
- 크기 조정의 정도나 시점을 제어하므로 크기 조정을 더 잘 제어할 수 있습니다.

## <a name="enable-auto-inflate-on-a-namespace"></a>네임스페이스에서 자동 확장 사용

다음 방법 중 하나를 사용하여 Event Hubs 네임스페이스에서 자동 확장을 사용하거나 사용하지 않도록 설정할 수 있습니다.

- [Azure Portal](https://portal.azure.com)
- [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>포털을 통해 자동 확장 사용


#### <a name="enable-at-the-time-of-creation"></a>생성 시 사용 
**Event Hubs 네임스페이스를 만들 때** 자동 확장 기능을 사용하도록 설정할 수 있습니다.
 
![이벤트 허브 생성 시 자동 확장 사용](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

이 옵션을 사용하도록 설정하면 작은 처리량 단위부터 시작하여 필요한 사용량 증가에 따라 확장할 수 있습니다. 확장 상한은 가격 책정에 즉시 영향을 미치지 않으며 가격 책정은 시간당 사용된 처리량 단위 수에 따라 다릅니다.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>기존 이벤트 허브에 대해 자동 확장 사용
또한 자동 확장 기능을 사용하도록 설정하고 다음 지침에 따라 해당 설정을 수정할 수 있습니다. 
 
1. **Event Hubs 네임스페이스** 페이지의 **처리량 단위 자동 확장**에서 **사용 안 함**을 선택합니다.  

    ![Event Hubs 네임스페이스 페이지에서 처리량 단위 선택](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. **크기 조정 설정** 페이지에서 **사용** 확인란을 선택합니다(자동 크기 조정 기능을 사용하도록 설정하지 않은 경우).

    ![사용 선택](./media/event-hubs-auto-inflate/scale-settings.png)
3. **최대** 처리량 단위 수를 입력하거나 스크롤 막대를 사용하여 값을 설정합니다. 
4. (선택 사항) 이 페이지 맨 위에서 **최소** 처리량 단위 수를 업데이트합니다. 


> [!NOTE]
> 처리량 단위를 늘리는 자동 팽창 구성을 사용하면 Event Hubs 서비스는 처리량이 증가한 이유 및 시기에 대한 정보를 제공하는 진단 로그를 내보냅니다. 이벤트 허브에 진단 로깅을 사용하려면 Azure Portal에서 이벤트 허브 페이지 왼쪽 메뉴에서 **진단 설정**을 선택합니다. 자세한 내용은 [Azure 이벤트 허브에 대한 진단 로그 설정](event-hubs-diagnostic-logs.md)을 참조하세요. 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 자동 확장 사용

Azure Resource Manager 템플릿을 배포하는 동안 자동 확장을 사용하도록 설정할 수 있습니다. 예를 들어 `isAutoInflateEnabled` 속성을 **true**로 설정하고 `maximumThroughputUnits`를 10으로 설정합니다. 예를 들면 다음과 같습니다.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

전체 템플릿은 GitHub에서 [Event Hubs 네임스페이스 만들기 및 확장 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) 템플릿을 참조하세요.


## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)

