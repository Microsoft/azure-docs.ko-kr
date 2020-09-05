---
title: Content Moderator Python 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python용 Azure Cognitive Services Content Moderator 클라이언트 라이브러리를 시작하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 9a8076dcf23c4049ce2728c5099d691f9e865a5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510113"
---
Python용 Content Moderator 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Content Moderator는 자료에서 모욕적이거나, 위험하거나, 원치 않는 텍스트, 이미지 및 비디오 콘텐츠를 확인하는 인지 서비스입니다. 이러한 자료가 발견되면 서비스가 콘텐츠에 적절한 레이블(플래그)을 적용합니다. 그러면 앱이 규정에 부합하고 원하는 사용자 환경을 유지하기 위해 플래그가 지정된 콘텐츠를 처리할 수 있습니다.

Python용 Content Moderator 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [텍스트 조정](#moderate-text)
* [사용자 지정 용어 목록 사용](#use-a-custom-terms-list)
* [이미지 조정](#moderate-images)
* [사용자 지정 이미지 목록 사용](#use-a-custom-image-list)
* [검토 만들기](#create-a-review)

[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>설치

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Content Moderator용 리소스를 만듭니다. 다음도 가능합니다.

* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

리소스에서 키를 가져온 후에는 각각 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 및 `CONTENT_MODERATOR_ENDPOINT`라는 키 및 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).
 
### <a name="create-a-python-script"></a>Python 스크립트 만들기

새 Python 스크립트를 만들어 원하는 편집기 또는 IDE에서 엽니다. 그런 다음, 파일의 위쪽에 다음 `import` 문을 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

다음으로, 리소스의 엔드포인트 위치와 키에 대한 변수를 환경 변수로 만듭니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음 명령을 사용하여 Content Moderator 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>개체 모델

Content Moderator Python 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|이 클래스는 모든 Content Moderator 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|이 클래스는 성인 콘텐츠, 개인 정보 또는 사람 얼굴에 대한 이미지를 분석하는 기능을 제공합니다.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|이 클래스는 언어, 욕설, 오류 및 개인 정보에 대한 텍스트를 분석하는 기능을 제공합니다.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|이 클래스는 작업, 사용자 지정 워크플로 및 사용자 검토를 만드는 메서드를 포함하여 검토 API의 기능을 제공합니다.|

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Content Moderator 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [텍스트 조정](#moderate-text)
* [사용자 지정 용어 목록 사용](#use-a-custom-terms-list)
* [이미지 조정](#moderate-images)
* [사용자 지정 이미지 목록 사용](#use-a-custom-image-list)
* [검토 만들기](#create-a-review)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 Content Moderator 키 및 엔드포인트에 대한 [환경 변수를 만들었다](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)고 가정합니다.

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 개체를 만들고, 엔드포인트에서 이를 사용하여 [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>텍스트 조정

다음 코드에서는 Content Moderator 클라이언트를 사용하여 텍스트 본문을 분석하고 결과를 콘솔에 출력합니다. 먼저 **text_files/** 폴더를 프로젝트의 루트에 만들고, *content_moderator_text_moderation.txt* 파일을 추가합니다. 사용자 고유의 텍스트를 이 파일에 추가하거나 다음 샘플 텍스트를 사용합니다.

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

새 폴더에 대한 참조를 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

그런 다음, 다음 코드를 Python 스크립트에 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>사용자 지정 용어 목록 사용

다음 코드에서는 텍스트를 조정할 사용자 지정 용어의 목록을 관리하는 방법을 보여 줍니다. [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) 클래스를 사용하여 용어 목록을 만들고, 개별 용어를 관리하며, 이와 비교하여 텍스트의 다른 본문을 확인할 수 있습니다.

### <a name="get-sample-text"></a>샘플 텍스트 가져오기

이 샘플을 사용하려면 **text_files/** 폴더를 프로젝트의 루트에 만들고, *content_moderator_term_list.txt* 파일을 추가해야 합니다. 이 파일에는 용어 목록과 비교하여 확인할 유기적 텍스트가 포함되어 있어야 합니다. 다음 샘플 텍스트를 사용할 수 있습니다.

```
This text contains the terms "term1" and "term2".
```

폴더를 아직 정의하지 않은 경우 해당 폴더에 대한 참조를 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>목록 만들기

다음 코드를 Python 스크립트에 추가하여 사용자 지정 용어 목록을 만들고 해당 ID 값을 저장합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>목록 세부 정보 정의

목록 ID를 사용하여 해당 목록의 이름과 설명을 편집할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>목록에 용어 추가

다음 코드는 `"term1"` 및 `"term2"` 용어를 목록에 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>목록의 모든 용어 가져오기

목록 ID를 사용하여 목록의 모든 용어를 반환할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>목록 인덱스 새로 고침

목록에서 용어를 추가하거나 제거할 때마다 인덱스를 새로 고쳐야 업데이트된 목록을 사용할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>목록과 비교하여 텍스트 확인

사용자 지정 용어 목록의 주요 기능은 텍스트 본문을 목록과 비교하고 일치하는 용어가 있는지 확인하는 것입니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>목록에서 용어 제거

다음 코드는 목록에서 `"term1"` 용어를 제거합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>목록에서 모든 용어 제거

다음 코드를 사용하여 모든 용어의 목록을 지웁니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>목록 삭제

다음 코드를 사용하여 사용자 지정 용어 목록을 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>이미지 조정

다음 코드에서는 [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) 개체와 함께 Content Moderator 클라이언트를 사용하여 성인 및 외설 콘텐츠에 대한 이미지를 분석합니다.

### <a name="get-images"></a>이미지 가져오기

분석할 일부 이미지에 대한 참조를 정의합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

그런 다음, 이미지를 반복하는 다음 코드를 추가합니다. 이 섹션의 나머지 코드는 이 루프 내에 들어갑니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>성인/외설 콘텐츠 확인

다음 코드는 지정된 URL의 이미지에서 성인 또는 외설 콘텐츠를 확인하고 결과를 콘솔에 출력합니다. 이러한 용어의 의미에 대한 자세한 내용은 [이미지 조정 개념](../../image-moderation-api.md) 가이드를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>표시되는 텍스트 확인

다음 코드는 이미지에서 표시되는 텍스트 콘텐츠를 확인하고 결과를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>얼굴 확인

다음 코드는 이미지에서 사람의 얼굴을 확인하고 결과를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>사용자 지정 이미지 목록 사용

다음 코드에서는 조정할 이미지의 사용자 지정 목록을 관리하는 방법을 보여 줍니다. 이 기능은 플랫폼에서 차단하려는 동일한 이미지 세트의 인스턴스를 자주 받는 경우에 유용합니다. 이러한 특정 이미지의 목록을 유지 관리하면 성능을 향상시킬 수 있습니다. [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) 클래스를 사용하면 이미지 목록을 만들고, 목록의 개별 이미지를 관리하고, 다른 이미지와 비교할 수 있습니다.

이 시나리오에서 사용할 이미지 URL을 저장하기 위해 다음 텍스트 변수를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> 이는 적절한 목록 자체가 아니라 코드의 `add images` 섹션에 추가될 간단한 이미지 목록입니다.


### <a name="create-an-image-list"></a>이미지 목록 만들기

다음 코드를 추가하여 이미지 목록을 만들고 해당 ID에 대한 참조를 저장합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>목록에 이미지 추가

다음 코드는 모든 이미지를 목록에 추가합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

**add_images** 도우미 함수를 스크립트의 다른 위치에 정의합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>목록에서 이미지 가져오기

다음 코드는 목록에 있는 모든 이미지의 이름을 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>목록 세부 정보 업데이트

목록 ID를 사용하여 해당 목록의 이름과 설명을 업데이트할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>목록 세부 정보 가져오기

다음 코드를 사용하여 목록의 현재 세부 정보를 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>목록 인덱스 새로 고침

이미지가 추가되거나 제거되면 목록 인덱스를 새로 고쳐야 다른 이미지를 확인할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>목록과 비교하여 이미지 일치

이미지 목록의 주요 기능은 새 이미지를 비교하고 일치하는 이미지가 있는지 확인하는 것입니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>목록에서 이미지 제거

다음 코드는 목록에서 항목을 제거합니다. 이 경우 목록 범주와 일치하지 않는 이미지입니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>목록에서 모든 이미지 제거

다음 코드를 사용하여 이미지 목록을 지웁니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>이미지 목록 삭제

다음 코드를 사용하여 지정된 이미지 목록을 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>검토 만들기

Content Moderator Python 클라이언트 라이브러리를 사용하여 사용자 중재자가 검토할 수 있도록 콘텐츠를 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에 제공할 수 있습니다. 검토 도구에 대한 자세한 내용은 [검토 도구 개념 가이드](../../review-tool-user-guide/human-in-the-loop.md)를 참조하세요.

다음 코드에서는 검토 도구의 웹 포털을 통해 사용자 입력을 받은 후에 [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) 클래스를 사용하여 검토를 만들고, 해당 ID를 검색하고, 해당 세부 정보를 확인합니다.

### <a name="get-review-credentials"></a>검토 자격 증명 가져오기

먼저 검토 도구에 로그인하고 팀 이름을 검색합니다. 그런 다음, 이를 코드의 해당 변수에 할당합니다. 필요에 따라 검토 작업에 대한 업데이트를 받도록 콜백 엔드포인트를 설정할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>이미지 검토 만들기

다음 코드를 추가하여 지정된 이미지 URL에 대한 검토를 만들고 게시합니다. 코드는 검토 ID에 대한 참조를 저장합니다. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>검토 세부 정보 가져오기

다음 코드를 사용하여 지정된 검토의 세부 정보를 확인합니다. 검토가 만들어지면 검토 도구로 직접 이동하여 콘텐츠와 상호 작용할 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [리뷰 방법 가이드](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)를 참조하세요. 완료되면 이 코드를 다시 실행할 수 있으며, 검토 프로세스의 결과를 검색합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

이 시나리오에서 콜백 엔드포인트를 사용한 경우 다음과 같은 형식의 이벤트를 받습니다.

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Content Moderator Python 라이브러리를 사용하여 조정 작업을 수행하는 방법을 알아보았습니다. 다음으로, 개념 가이드를 참조하여 이미지 또는 다른 미디어의 조정에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
>[이미지 조정 개념](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)에서 확인할 수 있습니다.