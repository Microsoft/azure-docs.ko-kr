---
title: 사용자 지정 기술 예제 (Python)
titleSuffix: Azure Cognitive Search
description: Python 개발자를 위해 Azure Functions 및 Visual Studio를 사용 하 여 사용자 지정 기술을 빌드하기 위한 도구 및 기술에 대해 알아보세요. 사용자 지정 기술에는 Azure Cognitive Search에서 AI-보강 인덱싱 파이프라인에 추가할 수 있는 사용자 정의 모델 또는 논리가 포함 됩니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210468"
---
# <a name="example-create-a-custom-skill-using-python"></a>예: Python을 사용 하 여 사용자 지정 기술 만들기

이 Azure Cognitive Search 기술 예제에서는 Python 및 Visual Studio Code를 사용 하 여 web API 사용자 지정 기술을 만드는 방법을 알아봅니다. 이 예제에서는 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)를 구현 하는 [Azure 함수](https://azure.microsoft.com/services/functions/) 를 사용 합니다.

사용자 지정 기술은 Python의 사용자 지정 기술 개발에 사용 되는 도구와 기술에 집중할 수 있도록 디자인 (두 문자열 연결)에 따라 간단 합니다. 간단한 기술을 성공적으로 완료 한 후에는 더 복잡 한 시나리오로 분기할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 사용자 지정 기술에서 구현 해야 하는 입력/출력 인터페이스에 대 한 소개를 보려면 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md) 를 검토 합니다.

+ 환경을 설정 합니다. Visual Studio Code 및 Python 확장을 사용 하 여 서버 리스 Azure 함수를 설정 하는 [이 자습서를 완료](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) 했습니다. 이 자습서에서는 다음 도구 및 구성 요소를 설치 하는 과정을 안내 합니다. 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Visual Studio Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions 핵심 도구](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Visual Studio Code용 Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 예제에서는 Azure 함수를 사용 하 여 web API를 호스트 하는 개념을 보여 주지만 다른 방법이 가능 합니다. [인식 기술에 대한 인터페이스 요구 사항](cognitive-search-custom-skill-interface.md)을 충족하기만 하면, 사용하는 방식은 중요하지 않습니다. 그러나 Azure Functions를 사용하면 사용자 지정 기술을 쉽게 만들 수 있습니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

Visual Studio Code의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 함수 앱을 통해 함수를 논리적 단위로 그룹화하여 관리, 배포 및 리소스를 공유할 수 있습니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Create new project...`을 검색하여 선택합니다.

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택**을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 따라서 작업 영역의 일부인 프로젝트 폴더를 선택 하지 마십시오.

1. 함수 앱 프로젝트에 사용할 언어를 선택 합니다. 이 자습서에서는 **Python**을 선택 합니다.
1. Python 버전을 선택 합니다 (버전 3.7.5는 Azure Functions에서 지원 됨).
1. 프로젝트의 첫 번째 함수에 사용할 템플릿을 선택 합니다. **Http 트리거** 를 선택 하 여 새 함수 앱에서 http 트리거 함수를 만듭니다.
1. 함수 이름을 제공 합니다. 이 경우 **Concatenator** 을 사용 하겠습니다. 
1. 권한 수준으로 **기능** 을 선택 합니다. 즉, 함수의 HTTP 끝점을 호출 하는 [함수 키](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) 를 제공 합니다. 
1. 프로젝트를 열 방법을 선택 합니다. 이 단계에서는 **작업 영역에 추가** 를 선택 하 여 현재 작업 영역에서 함수 앱을 만듭니다.

Visual Studio Code는 새 작업 영역에서 함수 앱 프로젝트를 만듭니다. 이 프로젝트에는 [host.json](../azure-functions/functions-host-json.md) 및 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 구성 파일과 언어별 프로젝트 파일이 포함됩니다. 

새 HTTP 트리거된 함수는 함수 앱 프로젝트의 **Concatenator** 폴더에도 만들어집니다. 그 안에는 다음과 같은 내용이 포함 된 "\_\_init__. py" 라는 파일이 있습니다.

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

이제 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)를 따르도록 해당 코드를 수정 하겠습니다. 다음 콘텐츠를 사용 하 여 코드를 수정 합니다.

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

**Transform_value** 메서드는 단일 레코드에 대해 작업을 수행 합니다. 특정 요구 사항에 맞게 메서드를 수정할 수 있습니다. 필요한 입력 유효성 검사를 수행 하 고 레코드에 대 한 작업을 완료할 수 없는 경우 생성 되는 오류 및 경고를 반환 해야 합니다.

### <a name="debug-your-code-locally"></a>로컬에서 코드 디버그

Visual Studio Code를 사용 하면 코드를 쉽게 디버그할 수 있습니다. ' F5 ' 키를 누르거나 **디버그** 메뉴로 이동 하 여 **디버깅 시작**을 선택 합니다.

관심 있는 줄에서 ' F9 '를 눌러 코드에서 중단점을 설정할 수 있습니다.

디버깅을 시작 하면 함수는 로컬로 실행 됩니다. Postman 또는 Fiddler와 같은 도구를 사용 하 여 localhost에 요청을 실행할 수 있습니다. 터미널 창에서 로컬 끝점의 위치를 확인 합니다. 

## <a name="publish-your-function"></a>함수 게시

함수 동작에 만족 하는 경우에는 게시할 수 있습니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서를 검색 하 고 **함수 앱에 배포를**선택 합니다. 

1. 응용 프로그램을 배포 하려는 Azure 구독을 선택 합니다.

1. **Azure에서 + 새 함수 앱 만들기를** 선택 합니다.

1. 함수 앱에 대 한 전역적으로 고유한 이름을 입력 합니다.

1. Python 버전을 선택 합니다 (이 함수에 대해 Python 3.7. x가 작동 함).

1. 새 리소스의 위치를 선택 합니다 (예: 미국 서 부 2).

이제 azure 구독에 azure에서 새 Azure 함수를 호스트 하는 데 필요한 리소스가 생성 됩니다. 배포가 완료될 때가지 기다립니다. 출력 창에 배포 프로세스의 상태가 표시 됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 리소스** 로 이동 하 고 이름으로 게시 한 함수를 찾습니다. 이름을 **Concatenator**로 지정한 경우 리소스를 선택 합니다.

1. **</> 함수 URL 가져오기** 단추를 클릭 합니다. 이를 통해 URL을 복사 하 여 함수를 호출할 수 있습니다.

## <a name="test-the-function-in-azure"></a>Azure에서 함수 테스트

이제 기본 호스트 키가 있으므로 다음과 같이 함수를 테스트합니다.

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>요청 본문
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

이 예에서는 로컬 환경에서 함수를 실행할 때 이전에 살펴본 것과 동일한 결과를 생성 해야 합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결

이제 새 사용자 지정 기술이 있으므로 기능에 추가할 수 있습니다. 아래 예제에서는 문서의 제목과 작성자를 merged_title_author 호출 하는 단일 필드에 연결 하는 기술을 호출 하는 방법을 보여 줍니다. `[your-function-url-here]`를 새 Azure 함수의 URL로 바꿉니다.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계
축하합니다! 첫 번째 사용자 지정 기술을 만들었습니다. 이제 동일한 패턴을 따라 고유한 사용자 지정 기능을 추가할 수 있습니다. 자세한 내용을 보려면 다음 링크를 클릭 하십시오.

+ [전원 기술: 사용자 지정 기술의 리포지토리입니다.](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
