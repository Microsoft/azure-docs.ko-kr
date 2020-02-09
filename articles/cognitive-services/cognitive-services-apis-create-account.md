---
title: Azure Portal에서 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 리소스를 만들고 구독 하 여 Azure Cognitive Services를 시작 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482878"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Cognitive Services 리소스 만들기

이 빠른 시작을 사용 하 여 Azure Cognitive Services 사용을 시작 합니다. Azure Portal에서 인지 서비스 리소스를 만든 후에는 응용 프로그램을 인증 하는 데 사용할 수 있는 엔드포인트 및 키를 가져옵니다.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>전제 조건

* 유효한 Azure 구독- [무료로 하나를 만듭니다](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>새 Azure Cognitive Services 리소스 만들기

1. 리소스를 만듭니다.

    #### <a name="multi-service-resourcetabmultiservice"></a>[다중 서비스 리소스](#tab/multiservice)
    
    다중 서비스 리소스는 포털에서 **Cognitive Services** 이름이 지정 됩니다. [Cognitive Services 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    이번에는 다중 서비스 리소스를 사용 하 여 다음 Cognitive Services에 액세스할 수 있습니다.
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision API  | Content Moderator                                    | Face               | LUIS(Language Understanding) | Text Analytics   |
    | Translator Text  | Bing Search v7 <br>(웹, 이미지, 뉴스, 비디오, 시각적 개체) | Bing Custom Search | Bing Entity Search            | Bing Autosuggest |
    | Bing Spell Check |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[단일 서비스 리소스](#tab/singleservice)

    아래 링크를 사용 하 여 사용 가능한 Cognitive Services에 대한 리소스를 만듭니다.

    | 시각                      | Speech                  | 언어                          | 결정             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Computer vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [음성 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [몰입 형 독자](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [변칙 탐지기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [사용자 지정 비전 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [LUIS(Language Understanding)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing Custom Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entity Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [잉크 인식기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [텍스트 분석](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing Spell Check](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Autosuggest](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. **만들기** 페이지에서 다음 정보를 제공합니다.

    #### <a name="multi-service-resourcetabmultiservice"></a>[다중 서비스 리소스](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Services 리소스를 설명하는 이름입니다. 예를 들면 *MyCognitiveServicesResource*입니다. |
    | **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
    | **위치** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
    | **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
    | **리소스 그룹** | Cognitive Services 리소스를 포함 하는 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

    ![리소스 만들기 화면](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    **만들기**를 클릭합니다.

    #### <a name="single-service-resourcetabsingleservice"></a>[단일 서비스 리소스](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Services 리소스를 설명하는 이름입니다. 예를 들면 *TextAnalyticsResource*입니다. |
    | **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
    | **위치** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
    | **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
    | **리소스 그룹** | Cognitive Services 리소스를 포함 하는 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

    ![리소스 만들기 화면](media/cognitive-services-apis-create-account/resource_create_screen.png)

    **만들기**를 클릭합니다.

    ***


## <a name="get-the-keys-for-your-resource"></a>리소스의 키를 가져옵니다.

1. 리소스가 성공적으로 배포 되 면 **다음 단계**아래의 **리소스로 이동** 을 클릭 합니다.

    ![Cognitive Services 검색](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 열리는 빠른 시작 창에서 키 및 엔드포인트에 액세스할 수 있습니다.

    ![키 및 엔드포인트 가져오기](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제 하면 해당 그룹에 포함 된 다른 리소스도 모두 삭제 됩니다.

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 **리소스 그룹**을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스를 포함 하는 리소스 그룹을 찾습니다.
3. 리소스 그룹 목록을 마우스 오른쪽 단추로 클릭 합니다. **리소스 그룹 삭제**를 선택하고 확인합니다.

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Services에 대한 요청 인증](authentication.md)
* [Azure Cognitive Services 이란?](Welcome.md)
* [자연어 지원](language-support.md)
* [Docker 컨테이너 지원](cognitive-services-container-support.md)
