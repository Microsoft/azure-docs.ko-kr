---
title: '빠른 시작: Python SDK를 사용하여 Bing Custom Search 엔드포인트 호출'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python SDK를 통해 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: d67075fad719b1780682c705f0e17f15c5801559
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136159"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>빠른 시작: Python SDK를 사용하여 Bing Custom Search 엔드포인트 호출 

이 빠른 시작을 사용하여 Python SDK로 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다. Bing Custom Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있지만 Bing Custom Search SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)에서 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Bing Custom Search 인스턴스 [빠른 시작: 처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)에서 자세한 내용을 참조하세요.
- Python [2.x 또는 3.x](https://www.python.org/) 
- [Python용 Bing Custom Search SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Python SDK 설치

다음 명령을 사용하여 Bing Custom Search SDK를 설치합니다.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>새 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 새 Python 파일을 만들고, 다음 가져오기를 추가합니다.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>검색 클라이언트 만들기 및 요청 보내기

1. 구독 키에 대한 변수를 만듭니다.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-custom-endpoint'
    ```

2. 구독 키와 `CognitiveServicesCredentials` 개체를 사용하여 `CustomSearchClient` 인스턴스를 만듭니다. 

    ```python
    client = CustomSearchClient(endpoint, CognitiveServicesCredentials(subscription_key))
    ```

3. `client.custom_instance.search()`를 사용하여 검색 요청을 보냅니다. `query` 매개 변수에 검색 용어를 추가하고 `custom_config`를 검색 인스턴스를 사용하기 위한 사용자 지정 구성 ID로 설정합니다. **프로덕션** 탭을 클릭하여 [Bing Custom Search 포털](https://www.customsearch.ai/)에서 ID를 가져올 수 있습니다.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>검색 결과 보기

웹 페이지 검색 결과가 발견되면 첫 번째 결과를 가져온 후 해당 이름, URL 및 찾은 전체 웹 페이지를 인쇄합니다.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](./tutorials/custom-search-web-page.md)
