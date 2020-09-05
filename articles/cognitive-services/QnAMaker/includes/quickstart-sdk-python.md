---
title: '빠른 시작: Python용 QnA Maker 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: d99ed657098a8046a64c10ae48433aa87c2f06f6
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246344"
---
python용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 기술 자료 만들기
* 기술자료 업데이트
* 기술 자료 게시
* 예측 런타임 엔드포인트 키 가져오기
* 장기 실행 작업 대기
* 기술 자료 다운로드
* 답변 구하기
* 기술 자료 삭제

[참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [패키지(PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)를 만들어 제작 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 선택합니다.
    * 애플리케이션을 QnA Maker API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

`quickstart-file.py`라는 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

> [!IMPORTANT]
> Azure Portal로 이동하여 필수 구성 요소에서 만든 QnA Maker 리소스의 키와 엔드포인트를 찾습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다.
> 기술 자료를 만들려면 전체 키가 필요합니다. 엔드포인트의 리소스 이름만 필요합니다. 형식은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`입니다.
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)는 보안 키 스토리지를 제공합니다.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>개체 모델

[QnA Maker](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) Maker는 다음과 같은 두 가지 개체 모델을 사용합니다.
* **[QnAMakerClient](#qnamakerclient-object-model)** 는 기술 자료를 만들고, 관리하고, 게시하고, 다운로드하는 개체입니다.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 은 GenerateAnswer API를 사용하여 기술 자료를 쿼리하고 Train API([활성 학습](../concepts/active-learning-suggestions.md)의 일부로)를 사용하여 새로운 제안된 질문을 보내는 개체입니다.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 개체 모델

QnA Maker 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) 개체입니다.

클라이언트를 만든 후 [기술 자료](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python) 속성을 사용하여 기술 자료를 생성, 관리 및 게시합니다.

JSON 개체를 전송하여 기술 자료를 관리합니다. 즉각적인 작업의 경우 메서드는 일반적으로 상태를 나타내는 JSON 개체를 반환합니다. 장기 실행 작업의 경우 응답은 작업 ID입니다. 작업 ID로 [operations.get_details](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.operations(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) 메서드를 호출하여 [요청의 상태](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation(class)?view=azure-python)를 확인합니다.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 개체 모델

예측 QnA Maker 클라이언트는 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python) 개체입니다. 이 개체는 기술 자료가 게시된 후에 제작 클라이언트 호출([client.EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-))에서 반환된 예측 런타임 키를 포함합니다.

[generate_answer](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.operations.runtimeoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 쿼리 런타임에서 답변을 가져옵니다.


## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>기술 자료 제작을 위한 클라이언트 인증

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 CognitiveServicesCredentials 개체를 만들고, 엔드포인트에서 이를 사용하여 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qnamakerclient?view=azure-python) 개체를 만듭니다.

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

 클라이언트 개체를 사용하여 [기술 자료 작업](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python) 개체를 가져옵니다.

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠**의 경우 [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python) 개체를 사용합니다.
    * 메타데이터 및 후속 프롬프트를 사용하려면 이 데이터가 개별 QnA 쌍 수준에 추가되기 때문에 편집 컨텍스트를 사용합니다.
* **파일**의 경우 [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python) 개체를 사용합니다. FileDTO에는 파일에 연결하기 위한 파일 이름과 공용 URL이 포함됩니다.
* **URL**의 경우 문자열 목록을 사용하여 공개적으로 사용 가능한 URL을 나타냅니다.

[create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) 메서드를 호출한 다음, 반환된 작업 ID를 [Operations.getDetails](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

다음 코드의 마지막 줄은 MonitorOperation의 응답에서 기술 자료 ID를 반환합니다.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`_monitor_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python), [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python) 및 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) DTO 개체를 포함하는 [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python)를 [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. 성공적으로 업데이트되었는지 확인하려면 [Operation.getDetail](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`_monitor_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="download-a-knowledge-base"></a>기술 자료 다운로드

[download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qnadocumentsdto?view=azure-python) 목록으로 데이터베이스를 다운로드합니다. 이 메서드의 결과가 TSV 파일이 아니므로 QnA Maker 포털에 있는 **설정** 페이지의 내보내기와 동일하지 않습니다.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 기술 자료를 게시합니다. 기술 자료 ID에서 참조하는 현재 저장 및 학습된 모델을 사용하며, 이를 엔드포인트에 게시합니다.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>쿼리 런타임 키 가져오기

기술 자료가 게시된 후에는 런타임 쿼리를 위한 쿼리 런타임 키가 필요합니다. 이는 원래 클라이언트 개체를 만드는 데 사용되는 키와 동일하지 않습니다.

[EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 [EndpointKeysDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.endpointkeysdto?view=azure-python) 클래스를 가져옵니다.

개체에서 반환된 키 속성 중 하나를 사용하여 기술 자료를 쿼리합니다.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>응답을 생성하기 위한 런타임 인증

기술 자료를 쿼리하는 [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python)를 만들어 답변을 생성하거나 활성 학습으로부터 학습합니다.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

QnAMakerRuntimeClient를 사용하여 기술 자료에서 답변을 얻거나 새로 제안된 질문을 [활성 학습](../concepts/active-learning-suggestions.md)에 대한 기술 자료에 보냅니다.

## <a name="generate-an-answer-from-the-knowledge-base"></a>기술 자료에서 답변 생성

RuntimeClient.runtime.generateAnswer 메서드를 사용하여 게시된 기술 자료에서 답변을 생성합니다. 이 메서드는 기술 자료 ID 및 QueryDTO를 허용합니다. 채팅 봇에서 사용할 Top 및 Context와 같은 QueryDTO의 추가 속성에 액세스합니다.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

다음은 기술 자료를 쿼리하는 간단한 예제입니다. 고급 쿼리 시나리오를 이해하려면 [기타 쿼리 예제](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)를 검토하세요.

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 기술 자료를 삭제합니다.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 작업 ID를 사용하여 폴링합니다(다시 시도 논리 사용).

다음 코드 블록의 _setTimeout_ 호출은 비동기 코드를 시뮬레이션하는 데 사용됩니다. 재시도 논리로 바꿉니다.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>애플리케이션 실행

빠른 시작 파일에서 python 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)에서 확인할 수 있습니다.