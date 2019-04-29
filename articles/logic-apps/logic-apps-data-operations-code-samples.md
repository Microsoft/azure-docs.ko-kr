---
title: 데이터 작업 샘플 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 데이터 작업 작업 정의에 대한 코드 샘플입니다.
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: v-yiso
manager: jeconnoc
ms.topic: reference
origin.date: 07/25/2018
ms.date: 12/10/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8de70e8df50b79f27c19c74307d9321ff8cd1eb5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128552"
---
# <a name="data-operation-code-samples-for-azure-logic-apps"></a>Azure Logic Apps용 데이터 작업 코드 샘플

다음은 [데이터 작업 수행](../logic-apps/logic-apps-perform-data-operations.md) 문서의 데이터 작업 작업 정의에 대한 코드 샘플입니다. 이러한 샘플은 사용자 고유 논리 앱의 기본 워크플로 정의, Azure 구독 및 API 연결을 사용하여 예제를 사용하려는 경우에 사용할 수 있습니다. 이러한 작업 정의를 복사하여 논리 앱의 워크플로 정의에 대한 코드 보기 편집기에 붙여넣은 다음, 특정 워크플로에 대한 정의를 수정하기만 하면 됩니다. 

이러한 작업 정의는 JSON(JavaScript Object Notation) 표준에 따라 사전순으로 표시됩니다. 그러나 Logic Apps 디자이너에서는 각 작업 정의의 `runAfter` 속성이 실행 순서를 지정하므로 워크플로 내의 올바른 순서로 각 정의가 표시됩니다. 

<a name="compose-action-example"></a>

## <a name="compose"></a>Docker Compose

[**작성** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#compose-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
  "Compose": {
    "type": "Compose",
    "inputs": {
      "age": "@variables('ageVar')",
      "fullName": "@{variables('lastNameVar')}, @{variables('firstNameVar')}"
    },
    "runAfter": {
      "Initialize_variable_-_ageVar": [
          "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_ageVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "ageVar",
          "type": "Integer",
          "value": 35
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_lastNameVar": [
        "Succeeded"
      ]
    }
  },
  "Initialize_variable_-_firstNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "firstNameVar",
          "type": "String",
          "value": "Sophie "
        }
      ]
    },
    "runAfter": {}
  },
  "Initialize_variable_-_lastNameVar": {
    "type": "InitializeVariable",
    "inputs": {
      "variables": [
        {
          "name": "lastNameVar",
          "type": "String",
          "value": "Owen"
        }
      ]
    },
    "runAfter": {
      "Initialize_variable_-_firstNameVar": [
        "Succeeded"
      ]
    }
  }
},
```

<a name="create-csv-table-action-example"></a>

## <a name="create-csv-table"></a>CSV 테이블 만들기

[**CSV 만들기 테이블** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#create-csv-table-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Create_CSV_table": {
      "type": "Table",     
      "inputs": {
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="create-html-table-action-example"></a>

## <a name="create-html-table"></a>HTML 테이블 만들기

[**HTML 테이블 만들기** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#create-html-table-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Create_HTML_table": {
      "type": "Table",     
      "inputs": {
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      },
      "runAfter": {
         "Initialize_variable_-_JSON_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_JSON_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myJSONArray",
               "type": "Array",
                  "value": [
                     {
                        "Description": "Apples",
                        "Product_ID": 1
                     },
                     {
                        "Description": "Oranges",
                        "Product_ID": 2
                     }
                  ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="filter-array-action-example"></a>

## <a name="filter-array"></a>배열 필터링

[**배열 필터링** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Filter_array": {
      "type": "Query",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "where": "@greater(item(), 1)"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
            "Succeeded"
         ]
      }
   },
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   }
},
```

<a name="join-action-example"></a>

## <a name="join"></a>Join

[**조인** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#join-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Join": {
      "type": "Join",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "joinWith": ":"
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
             "Succeeded"
         ]
      }
   }
},
```

<a name="parse-json-action-example"></a>

## <a name="parse-json"></a>Parse JSON

[**구문 분석 JSON**  작업 예제](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Initialize_variable_-_JSON_object": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [
            {
               "name": "myJSONObject",
               "type": "Object",
               "value": {
                  "Member": {
                     "Email": "Sophie.Owen@contoso.com",
                     "FirstName": "Sophie",
                     "LastName": "Owen"
                  }
               }
            }
         ]
      },
      "runAfter": {}
   },
   "Parse_JSON": {
      "type": "ParseJson",
      "inputs": {
         "content": "@variables('myJSONObject')",
         "schema": {
            "type": "object",
            "properties": {
               "Member": {
                  "type": "object",
                  "properties": {
                     "Email": {
                        "type": "string"
                     },
                     "FirstName": {
                        "type": "string"
                     },
                     "LastName": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      },
      "runAfter": {
         "Initialize_variable_-_JSON_object": [
            "Succeeded"
         ]
      }
},
```

<a name="select-action-example"></a>

## <a name="select"></a>여기서

[**선택** 작업 예제](../logic-apps/logic-apps-perform-data-operations.md#select-action)를 사용하려면 다음과 같은 작업 정의를 사용할 수 있습니다.

```json
"actions": {
   "Initialize_variable_-_integer_array": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ 
            {
               "name": "myIntegerArray",
               "type": "Array",
               "value": [
                  1,
                  2,
                  3,
                  4
               ]
            }
         ]
      },
      "runAfter": {}
   },
   "Select": {
      "type": "Select",
      "inputs": {
         "from": "@variables('myIntegerArray')",
         "select": {
            "Product_ID": "@item()"
         }
      },
      "runAfter": {
         "Initialize_variable_-_integer_array": [
           "Succeeded"
         ]
      }
   }
},
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [데이터 작업 수행](../logic-apps/logic-apps-perform-data-operations.md)
