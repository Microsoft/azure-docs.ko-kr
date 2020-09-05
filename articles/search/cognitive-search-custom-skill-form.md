---
title: '폼 인식기 사용자 지정 기술 (c #)'
titleSuffix: Azure Cognitive Search
description: 'C # 및 Visual Studio를 사용 하 여 폼 인식기 사용자 지정 기술을 만드는 방법을 알아봅니다.'
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 58f1c2621165a7074c04752832c6560b2fd3e423
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935435"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>예: 양식 인식기 사용자 지정 기술 만들기

이 Azure Cognitive Search 기술 예제에서는 c # 및 Visual Studio를 사용 하 여 폼 인식기 사용자 지정 기술을 만드는 방법을 배웁니다. 양식 인식기는 문서를 분석 하 고 키/값 쌍 및 테이블 데이터를 추출 합니다. [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md)에 폼 인식기를 래핑하여 종단 간 보강 파이프라인의 단계로이 기능을 추가할 수 있습니다. 그러면 파이프라인이 문서를 로드 하 고 다른 변환을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (모든 버전)
- 동일한 형식의 5 개 이상의 양식 이 가이드에서 제공 하는 샘플 데이터를 사용할 수 있습니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>모델 학습

이 스킬을 사용 하기 전에 입력 양식으로 양식 인식기 모델을 학습 해야 합니다. 모델을 학습 하는 방법에 대 한 자세한 내용은 [말아 퀵 스타트](../cognitive-services/form-recognizer/quickstarts/curl-train-extract.md) 를 참조 하세요. 해당 빠른 시작에 제공 된 샘플 양식을 사용 하거나 사용자 고유의 데이터를 사용할 수 있습니다. 모델을 학습 한 후에는 해당 ID 값을 안전한 위치에 복사 합니다.

## <a name="set-up-the-custom-skill"></a>사용자 지정 기술 설정

이 자습서에서는 [Azure Search 파워 기술](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 리포지토리의 [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) 프로젝트를 사용 합니다. 이 리포지토리를 로컬 컴퓨터에 복제 하 고 **비전/AnalyzeForm/** 로 이동 하 여 프로젝트에 액세스 합니다. 그런 다음 Visual Studio에서 _AnalyzeForm_ 을 엽니다. 이 프로젝트는 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md) 를 충족 하 고 azure Cognitive Search 보강에 사용할 수 있는 azure 함수 리소스를 만듭니다. 양식 문서를 입력으로 사용 하 고 지정 된 키/값 쌍을 텍스트로 출력 합니다.

먼저 프로젝트 수준 환경 변수를 추가 합니다. 왼쪽 창에서 **AnalyzeForm** 프로젝트를 찾은 다음 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다. **속성** 창에서 **디버그** 탭을 클릭 한 다음 **환경 변수** 필드를 찾습니다. **추가** 를 클릭 하 여 다음 변수를 추가 합니다.
* `FORMS_RECOGNIZER_ENDPOINT_URL` 값이 끝점 URL로 설정 된입니다.
* `FORMS_RECOGNIZER_API_KEY` 값이 구독 키로 설정 된입니다.
* `FORMS_RECOGNIZER_MODEL_ID` 값을 학습 한 모델의 ID로 설정 합니다.
* `FORMS_RECOGNIZER_RETRY_DELAY` 값이 1000로 설정 된입니다. 이 값은 프로그램에서 쿼리를 다시 시도 하기 전까지 대기 하는 시간 (밀리초)입니다.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` 값이 100로 설정 된입니다. 이 값은 성공적인 응답을 가져오는 동안 프로그램에서 서비스를 쿼리 하는 횟수입니다.

그런 다음 _AnalyzeForm.cs_ 를 열고 `fieldMappings` 파일 * 의field-mappings.js* 를 참조 하는 변수를 찾습니다. 이 파일 및이 파일을 참조 하는 변수는 양식에서 추출할 키 목록과 각 키에 대 한 사용자 지정 레이블을 정의 합니다. 예를 들어 값이 이면 `{ "Address:", "address" }, { "Invoice For:", "recipient" }` 스크립트는 검색 된 필드와 필드에 대 한 값만 저장 하 `Address:` `Invoice For:` 고 각각 및로 해당 값에 레이블을 만듭니다 `"address"` `"recipient"` .

마지막으로 변수를 확인 `contentType` 합니다. 이 스크립트는 URL에서 참조 하는 원격 문서에서 지정 된 폼 인식기 모델을 실행 하므로 콘텐츠 형식은 `application/json` 입니다. HTTP 요청에 바이트 스트림을 포함 하 여 로컬 파일을 분석 하려는 경우를 `contentType` 파일의 적절 한 [MIME 형식](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) 으로 변경 해야 합니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

프로젝트를 편집한 후에는 저장 하 고 **AnalyzeForm** 프로젝트를 Visual Studio에서 시작 프로젝트로 설정 합니다 (아직 설정 하지 않은 경우). 그런 다음 **f5** 키를 눌러 로컬 환경에서 함수를 실행 합니다. [Postman](https://www.postman.com/) 과 같은 REST 서비스를 사용 하 여 함수를 호출 합니다.

### <a name="http-request"></a>HTTP 요청

다음 요청을 수행 하 여 함수를 호출 합니다.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>요청 본문

아래의 요청 본문 템플릿으로 시작 합니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

여기서는 학습 한 폼과 동일한 형식의 URL을 제공 해야 합니다. 테스트 목적으로 학습 양식 중 하나를 사용할 수 있습니다. 말아 퀵 스타트를 수행 하면 양식이 Azure blob storage 계정에 배치 됩니다. Azure Storage 탐색기 열고 양식 파일을 찾아 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다. 다음 대화 상자 창에 URL 및 SAS 토큰이 제공 됩니다. `"formUrl"` `"formSasToken"` 요청 본문의 및 필드에 이러한 문자열을 각각 입력 합니다.

> [!div class="mx-imgBorder"]
> ![Azure storage 탐색기 pdf 문서를 선택 합니다.](media/cognitive-search-skill-form/form-sas.png)

Azure blob storage에 없는 원격 문서를 분석 하려면 필드에 해당 URL을 붙여넣고 `"formUrl"` `"formSasToken"` 필드를 비워 둡니다.

> [!NOTE]
> 기술이 기술에 통합 된 경우 URL 및 토큰은 Cognitive Search에서 제공 됩니다.

### <a name="response"></a>응답

다음 예제와 유사한 응답이 표시됩니다.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure에 함수 게시

함수 동작에 만족 하는 경우에는 게시할 수 있습니다.

1. Visual Studio의 **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시**를 선택 합니다. **새로 만들기**  >  **게시**를 선택 합니다.
1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.
1. 화면에 표시되는 메시지를 따릅니다. 앱 서비스, Azure 구독, 리소스 그룹, 호스팅 계획 및 사용 하려는 저장소 계정에 대 한 고유한 이름을 지정 합니다. 새 리소스 그룹, 새 호스팅 계획 및 새 저장소 계정 (아직 없는 경우)을 만들 수 있습니다. 작업이 완료되면 **만들기**를 선택합니다.
1. 배포가 완료 되 면 사이트 URL을 확인 합니다. 이 URL은 Azure에서 함수 앱의 주소입니다. 임시 위치에 저장 합니다.
1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 하 고 게시 한 함수를 찾습니다 `AnalyzeForm` . **관리** 섹션 아래에 호스트 키가 표시됩니다. *기본* 호스트 키를 복사 하 여 임시 위치에 저장 합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결

Cognitive Search 파이프라인에서이 기술을 사용 하려면 기술 정의를 기술에 추가 해야 합니다. 다음 JSON 블록은 샘플 기술 정의입니다. 특정 시나리오와 기술 환경을 반영 하기 위해 입력 및 출력을 업데이트 해야 합니다. 를 `AzureFunctionEndpointUrl` 함수 URL로 바꾸고을 `AzureFunctionDefaultHostKey` 호스트 키로 바꿉니다.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Azure 양식 인식기 서비스에서 사용자 지정 기술을 만들었습니다. 사용자 지정 기술에 대해 자세히 알아보려면 다음 리소스를 참조 하세요. 

* [Azure Search 전원 기술: 사용자 지정 기술의 리포지토리입니다.](https://github.com/Azure-Samples/azure-search-power-skills)
* [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
* [기술 집합 정의](cognitive-search-defining-skillset.md)
* [기술 만들기 (REST)](/rest/api/searchservice/create-skillset)
* [지도 보강 필드](cognitive-search-output-field-mapping.md)