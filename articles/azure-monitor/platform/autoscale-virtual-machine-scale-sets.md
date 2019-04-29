---
title: Azure Virtual Machines를 사용한 고급 자동 크기 조정
description: 크기 조정 작업에 대한 전자 메일을 전송하고 웹후크 URL을 호출하는 여러 규칙 및 프로필에 Resource Manager 및 VM Scale Sets를 사용합니다.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787804"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Resource Manager 템플릿을 사용하여 VM Scale Sets에 대한 고급 자동 크기 조정 구성
되풀이 일정 또는 특정 날짜에 성능 메트릭 임계값을 기반으로 Virtual Machine Scale Sets의 규모를 확장 및 감축할 수 있습니다. 또한 크기 조정 동작에 대한 전자 메일 및 웹후크 알림을 구성할 수 있습니다. 이 연습에서는 VM 확장 집합에서 Resource Manager 템플릿을 사용하여 이 모든 개체를 구성하는 예를 보여 줍니다.

> [!NOTE]
> 이 연습에서는 VM Scale Sets에 대한 단계를 설명하지만 [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) 및 [API Management 서비스](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)의 자동 크기 조정에도 동일한 정보가 적용됩니다. CPU 등의 단순한 성능 메트릭에 따른 VM 확장 집합의 간단한 규모 감축/확장 설정은 [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) 및 [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) 문서를 참조하세요.
>
>

## <a name="walkthrough"></a>연습
이 연습에서는 [Azure 리소스 탐색기](https://resources.azure.com/)를 사용하여 확장 집합에 대한 자동 크기 조정 설정을 구성 및 업데이트합니다. Azure 리소스 탐색기는 Resource Manager 템플릿을 통해 Azure 리소스를 관리하는 간편한 방법입니다. Azure 리소스 탐색기 도구를 처음 접하는 경우 [이 소개](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)를 읽어 보세요.

1. 기본 자동 크기 조정 설정으로 새 규모 집합을 배포합니다. 이 문서에서는 기본 자동 크기 조정 템플릿과 함께 Azure 빠른 시작 갤러리에 있는 Windows 규모 집합을 사용합니다. Linux 규모 집합도 동일한 방식으로 작동합니다.
2. 규모 집합을 만든 후 Azure 리소스 탐색기에서 규모 집합 리소스로 이동합니다. Microsoft.Insights 노드 아래에 다음이 표시됩니다.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    템플릿 실행을 통해 **'autoscalewad'** 라는 기본 자동 크기 조정 설정이 생성되었습니다. 오른쪽에서 이 자동 크기 조정 설정의 전체 정의를 볼 수 있습니다. 이 예에서 기본 자동 크기 조정 설정은 CPU% 기반의 규모 확장 및 규모 축소 규칙과 함께 제공됩니다.  

3. 이제 일정 또는 특정 요구 사항에 따라 프로필 및 규칙을 추가할 수 있습니다. 3개의 프로필로 자동 크기 조정 설정을 만듭니다. 자동 크기 조정의 프로필 및 규칙을 이해하려면 [자동 크기 조정 모범 사례](autoscale-best-practices.md)를 검토하세요.  

    | 프로필 및 규칙 | 설명 |
    |--- | --- |
    | **프로필** |**성능/메트릭 기반** |
    | 규칙 |Service Bus 큐 메시지 수 > x |
    | 규칙 |Service Bus 큐 메시지 수 < y |
    | 규칙 |CPU% > n |
    | 규칙 |CPU% < p |
    | **프로필** |**평일 오전 시간(규칙 없음)** |
    | **프로필** |**제품 출시일(규칙 없음)** |

4. 이 연습에 사용할 가상의 크기 조정 시나리오는 다음과 같습니다.

   * **부하 기반** - 확장 세트에서 호스트되는 애플리케이션의 부하에 따라 규모를 확장하거나 감축합니다.*
   * **메시지 큐 크기** - 애플리케이션으로 들어오는 메시지에 Service Bus 큐를 사용합니다. 큐의 메시지 수 및 CPU%를 사용하고, 메시지 수 또는 CPU가 임계값에 도달하면 크기 조정 동작을 트리거하도록 기본 프로필을 구성합니다.\*
   * **요일과 시간** - '평일 오전 시간'이라는 매주 되풀이되는 '시간' 기반 프로필을 사용합니다. 기록 데이터를 통해, 이 시간 동안 특정 개수의 VM 인스턴스에서 애플리케이션 부하를 처리하는 것이 더 효율적임을 알고 있습니다.\*
   * **특정 날짜** - '제품 출시일' 프로필을 추가했습니다. 애플리케이션이 마케팅 알림으로 인한 부하 및 애플리케이션에 새 제품이 추가될 경우의 부하를 처리할 준비가 되도록 특정 날짜에 대해 미리 계획합니다.\*
   * *마지막 두 프로필 내에도 다른 성능 메트릭 기반 규칙이 있을 수 있습니다. 이 경우 성능 메트릭 기반 규칙을 하나만 유지할 필요가 없으며, 기본 성능 메트릭 기반 규칙에 의존할 수 있습니다. 되풀이 및 날짜 기반 프로필의 경우에는 규칙이 선택 사항입니다.*

     프로필 및 규칙에 대한 자동 크기 조정 엔진의 우선 순위 지정은 [자동 크기 조정 모범 사례](autoscale-best-practices.md) 문서에도 설명되어 있습니다.
     자동 크기 조정에 대한 공통 메트릭 목록은 [자동 크기 조정에 대한 공통 메트릭](autoscale-common-metrics.md)을 참조하세요.

5. 리소스 탐색기에서 **읽기/쓰기** 모드에 있는지 확인합니다.

    ![Autoscalewad, 기본 자동 크기 조정 설정](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. 편집을 클릭합니다. 자동 크기 조정 설정의 '프로필' 요소를 다음 구성으로 **대체**합니다.

    ![프로필](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    지원되는 필드와 해당 값은 [자동 크기 조정 REST API 설명서](https://msdn.microsoft.com/library/azure/dn931928.aspx)를 참조하세요. 이제 자동 크기 조정 설정에 이전에 설명한 3개의 프로필이 포함되어 있습니다.

7. 마지막으로 자동 크기 조정 **알림** 섹션을 확인합니다. 규모 확장 또는 축소 동작이 성공적으로 트리거된 경우 자동 크기 조정 알림을 통해 세 가지 작업을 수행할 수 있습니다.
   - 구독의 관리자와 공동 관리자에게 알림
   - 사용자 집합에 전자 메일 보내기
   - 웹후크 호출 트리거. 실행된 경우 이 웹후크는 자동 크기 조정 조건 및 규칙 집합 리소스에 대한 메타데이터를 전송합니다. 자동 크기 조정 webhook의 페이로드에 대한 자세한 내용은 [자동 크기 조정의 Webhook 및 메일 알림 구성](autoscale-webhook-email.md)을 참조하세요.

   해당 값이 null인 **notification** 요소를 대체하여 자동 크기 조정 설정에 다음을 추가합니다.

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   리소스 탐색기에서 **배치** 단추를 눌러 자동 크기 조정 설정을 업데이트합니다.

여러 크기 조정 프로필 및 크기 조정 알림을 포함하도록 VM 확장 집합에 대한 자동 크기 조정 설정을 업데이트했습니다.

## <a name="next-steps"></a>다음 단계
자동 크기 조정에 대해 자세히 알아보려면 다음 링크를 참조하세요.

[Virtual Machine Scale Sets를 사용하여 자동 크기 조정 문제 해결](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[자동 크기 조정에 대한 공통 메트릭](autoscale-common-metrics.md)

[Azure 자동 크기 조정에 대한 모범 사례](autoscale-best-practices.md)

[PowerShell을 사용하여 자동 크기 조정 관리](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[CLI를 사용하여 자동 크기 조정 관리](cli-samples.md#autoscale)

[자동 크기 조정의 Webhook 및 메일 알림 구성](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) 템플릿 참조
