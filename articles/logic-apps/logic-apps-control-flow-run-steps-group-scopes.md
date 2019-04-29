---
title: 그룹 상태에 따라 작업을 실행하는 범위 추가 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 그룹 작업 상태에 따라 워크플로 작업을 실행하는 범위를 만드는 방법입니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685561"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Azure Logic Apps에서 범위가 지정된 그룹 상태에 따라 작업 실행

다른 작업 그룹이 성공하거나 실패한 후에만 작업을 실행하려면 해당 작업을 *범위* 내에 그룹화합니다. 이 구조는 작업을 논리 그룹으로 구성하고, 해당 그룹의 상태를 평가하고, 범위의 상태에 따라 작업을 수행하려는 경우에 유용합니다. 범위 내 모든 작업의 실행이 완료되면 범위에서 자체의 상태도 가져옵니다. 예를 들어 [예외 및 오류 처리](../logic-apps/logic-apps-exception-handling.md#scopes)를 구현하려는 경우 범위를 사용할 수 있습니다. 

범위의 상태를 확인하려면 "성공", "실패", "취소됨" 등과 같이 논리 앱의 실행 상태를 결정하는 데 사용하는 것과 동일한 기준을 사용할 수 있습니다. 기본적으로 범위의 모든 작업이 성공하면 범위 상태가 "성공"으로 표시됩니다. 그러나 범위의 모든 작업이 실패하거나 취소되면 범위의 상태가 "실패"로 표시됩니다. 범위에 대한 제한은 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

예를 들어 특정 작업을 실행하는 범위 및 범위의 상태를 확인하는 조건을 사용하는 상위 수준의 논리 앱이 있습니다. 범위의 작업이 예기치 않게 실패하거나 종료되면, 범위가 각각 "실패" 또는 "중단됨"으로 표시되고, 논리 앱에서 "범위 실패" 메시지를 보냅니다. 범위가 지정된 모든 작업이 성공하면 논리 앱에서 "범위 성공" 메시지를 보냅니다.

!["일정 - 되풀이" 트리거 설정](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>필수 조건

이 문서의 예를 수행하려면 다음 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Logic Apps에서 지원하는 모든 이메일 공급자의 이메일 계정입니다. 이 예에서는 Outlook.com을 사용합니다. 다른 공급자를 사용하는 경우 일반 흐름은 동일하게 유지되지만 UI는 다르게 표시됩니다.

* Bing 지도 키입니다. 이 키를 가져오려면 <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing 지도 키 가져오기</a>를 참조하세요.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

## <a name="create-sample-logic-app"></a>샘플 논리 앱 만들기

나중에 범위를 추가할 수 있도록 먼저 다음과 같은 샘플 논리 앱을 만듭니다.

![샘플 논리 앱 만들기](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* 지정한 간격으로 Bing 지도 서비스를 확인하는 **일정 - 되풀이** 트리거
* 두 위치 사이의 이동 시간을 확인하는 **Bing 지도 - 경로 가져오기** 작업
* 이동 시간이 지정한 이동 시간을 초과하는지 확인하는 조건문
* 현재 이동 시간이 지정한 시간을 초과했다는 이메일을 보내는 작업

논리 앱은 언제든지 저장할 수 있으므로 작업을 자주 저장합니다.

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다(아직 로그인하지 않은 경우). 빈 논리 앱을 만듭니다.

1. 추가 된 **일정-되풀이** 이러한 설정 사용 하 여 트리거: **간격** = "1" 및 **빈도** = "분"

   !["일정 - 되풀이" 트리거 설정](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > 시각적으로 보기를 간소화하고 디자이너에서 각 작업의 세부 정보를 숨기려면 이러한 단계를 진행하면서 각 작업의 셰이프를 축소하세요.

1. **Bing 지도 - 경로 가져오기** 작업을 추가합니다. 

   1. 아직 Bing 지도 연결이 아직 없으면 연결을 만들도록 요청하는 메시지가 표시됩니다.

      | 설정 | 값 | 설명 |
      | ------- | ----- | ----------- |
      | **연결 이름** | BingMapsConnection | 연결 이름을 입력합니다. | 
      | **API 키** | <*your-Bing-Maps-key*> | 이전에 받은 Bing 지도 키를 입력합니다. | 
      ||||  

   1. 다음 이미지 아래의 표와 같은 **경로 가져오기** 작업을 설정합니다.

      !["Bing 지도 - 경로 가져오기" 작업 설정](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      이러한 매개 변수에 대한 자세한 내용은 [경로 계산](https://msdn.microsoft.com/library/ff701717.aspx)을 참조하세요.

      | 설정 | 값 | 설명 |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*start*> | 경로의 출발지를 입력합니다. | 
      | **Waypoint 2** | <*end*> | 경로의 도착지를 입력합니다. | 
      | **Avoid** | 없음 | 고속도로, 톨게이트 등 경로에서 피해야 하는 항목을 입력합니다. 가능한 값은 [경로 계산](https://msdn.microsoft.com/library/ff701717.aspx)을 참조하세요. | 
      | **Optimize** | timeWithTraffic | 거리, 현재 교통 정보와 관련된 시간 등 경로를 최적화하기 위한 매개 변수를 선택합니다. 이 예에서는 "timeWithTraffic" 값을 사용합니다. | 
      | **Distance unit** | <*원하는 단위*> | 경로를 계산하기 위한 거리 단위를 입력합니다. 이 예제에서는이 값을 사용합니다. "마일" | 
      | **Travel mode** | Driving | 경로에 대한 이동 모드를 입력합니다. 이 예에서는 "운전" 값을 사용합니다. | 
      | **Transit Date-Time** | 없음 | 대중교통 모드에만 적용됩니다. | 
      | **Transit Date-Type Type** | 없음 | 대중교통 모드에만 적용됩니다. | 
      ||||  

1. 운행과 관련된 현재 이동 시간이 지정된 시간을 초과하는지 확인하는 [조건을 추가](../logic-apps/logic-apps-control-flow-conditional-statement.md)합니다. 
   이 예제에서는 다음 단계를 따릅니다.

   1. 이 설명이 포함되도록 조건 이름을 바꿉니다. **트래픽 시간 지정 된 시간 보다 큰 경우**

   1. 맨 왼쪽 열에서 **값 선택** 상자 내부를 클릭하면 동적 콘텐츠 목록이 표시됩니다. 이 목록에서 초 단위의 **운행 기간 트래픽** 필드를 선택합니다. 

      ![조건 작성](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 가운데 상자에서 **보다 큼** 연산자를 선택합니다.

   1. 맨 오른쪽 열에는 시간 (초) 및 10 분에 해당 하는이 비교 값을 입력 합니다. **600**

      여기까지 마쳤으면 조건이 다음 예제와 비슷하게 표시됩니다.

      ![완성된 조건](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. **If true**(참인 경우) 분기에서 이메일 공급자에 대한 "이메일 보내기" 작업을 추가합니다. 
   이 이미지 아래의 단계에 따라 이 작업을 설정합니다.

   !["If true" 분기에 "이메일 보내기" 작업 추가](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. **받는 사람** 필드에 테스트 용도의 이메일 주소를 입력합니다.

   1. **제목** 필드에 다음 텍스트를 입력합니다.

      ```Time to leave: Traffic more than 10 minutes```

   1. **본문** 필드에 후행 공백이 있는 다음 텍스트를 입력합니다. 

      ```Travel time:```

      커서가 **본문** 필드에 있는 동안 동적 콘텐츠 목록이 열려 있으므로 이 시점에서 사용할 수 있는 매개 변수를 선택할 수 있습니다.

   1. 동적 콘텐츠 목록에서 **식**을 선택합니다.

   1. **div()** 함수를 찾아서 선택합니다. 
      함수의 괄호 내부에 커서를 놓습니다.

   1. 커서를 함수의 괄호 안에 놓은 상태에서 **동적 콘텐츠**를 선택하여 동적 콘텐츠 목록을 표시합니다. 
   
   1. **경로 가져오기** 섹션에서 **운행 기간 트래픽** 필드를 선택합니다.

      !["운행 기간 트래픽" 선택](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. 필드가 JSON 형식으로 확인되면 **운행 기간 트래픽**의 값을 초 단위에서 분 단위로 변환할 수 있도록 **쉼표**(```,```) 뒤에 숫자(```60```)를 추가합니다. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      이제 식이 다음 예와 같습니다.

      ![식 완성](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. 작업을 완료하면 **확인**을 선택합니다.

   <!-- markdownlint-disable MD038 -->
   1. 식이 확인 되 면 선행 공백 사용 하 여이 텍스트를 추가 합니다. ``` minutes```
  
       이제 **본문** 필드가 다음 예와 같습니다.

       ![완성 된 "본문" 필드](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. 논리 앱을 저장합니다.

다음으로, 특정 작업을 그룹화하고 해당 상태를 평가할 수 있도록 범위를 추가합니다.

## <a name="add-a-scope"></a>범위 추가

1. 논리 앱을 아직 열지 않은 경우 논리 앱 디자이너에서 엽니다. 

1. 원하는 워크플로 위치에 범위를 추가합니다. 예를 들어 논리 앱 워크플로의 기존 단계 사이에 범위를 추가하려면 다음 단계를 수행합니다. 

   1. 범위를 추가하려는 화살표 위로 포인터를 이동합니다. 
   **더하기 기호**(**+**) > **작업 추가**를 선택합니다.

      ![범위 추가](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. 검색 상자에서 필터로 "범위"를 입력합니다. 
   **범위** 작업을 선택합니다.

## <a name="add-steps-to-scope"></a>범위에 단계 추가

1. 이제 단계를 추가하거나 범위 내에서 실행하려는 기존 단계를 끕니다. 이 예에서는 다음 작업을 범위로 끕니다.
      
   * **경로 가져오기**
   * **운행 시간이 지정된 시간을 초과하는 경우**(**true** 및 **false** 분기를 모두 포함)

   이제 논리 앱은 다음 예와 같습니다.

   ![추가된 범위](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. 범위 아래에서 범위의 상태를 확인하는 조건을 추가합니다. 이 설명이 포함되도록 조건 이름을 바꿉니다. **범위가 실패 한 경우**

   ![범위 상태를 확인하는 조건 추가](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. 조건에서, 범위 상태가 "실패함"인지 또는 "중단됨"인지 확인하는 다음 식을 추가합니다. 

   1. 또 다른 행을 추가하려면 **추가**를 선택합니다. 

   1. 각 행에서, 왼쪽 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 
   동적 콘텐츠 목록에서 **식**을 선택합니다. 편집 상자에서 이 식을 입력하고 **확인**을 선택합니다. 
   
      `result('Scope')[0]['status']`

      ![범위 상태를 확인하는 식 추가](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. 두 행 모두에서 **다음과 같음** 연산자를 선택합니다. 
   
   1. 비교 값의 경우 첫 번째 행에 `Failed`를 입력합니다. 
   두 번째 행에 `Aborted`를 입력합니다. 

      여기까지 마쳤으면 조건이 다음 예제와 비슷하게 표시됩니다.

      ![범위 상태를 확인하는 식 추가](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      조건이 범위 상태를 확인하고 이후 단계에서 정의할 매칭 작업을 실행하도록 조건의 `runAfter` 속성을 설정합니다.

   1. **범위가 실패한 경우** 조건에서 **줄임표**(...) 단추를 선택한 다음, **실행 조건 구성**을 선택합니다.

      ![`runAfter` 속성 구성](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. **성공함**, **실패함**, **건너뜀**, **시간이 초과됨** 범위 상태를 모두 선택합니다.

      ![범위 상태 선택](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. 모두 마쳤으면 **완료**를 선택합니다. 
   이제 조건에 "정보" 아이콘이 표시됩니다.

1. **If true**(참인 경우) 및 **If false**(거짓인 경우) 분기에서, 각 범위 상태에 따라 수행하려는 작업(예: 이메일 또는 메시지 보내기)을 추가합니다.

   ![범위 상태에 따라 수행할 작업 추가](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. 논리 앱을 저장합니다.

완성된 논리 앱은 이 예제와 비슷합니다.

![범위를 사용하여 완성된 논리 앱](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>작업 테스트

디자이너 도구 모음에서 **실행**을 선택합니다. 범위가 지정된 모든 작업이 성공하면 "범위 성공" 메시지가 표시됩니다. 범위가 지정된 작업이 성공하지 못하면 "범위 실패" 메시지가 표시됩니다. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON 정의

코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 범위 구조를 대신 정의할 수 있습니다. 예를 들어 이전 논리 앱의 트리거 및 작업에 대한 JSON 정의는 다음과 같습니다.

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
