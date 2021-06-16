---
title: Azure Face 서비스란?
titleSuffix: Azure Cognitive Services
description: Azure Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 데 사용하는 AI 알고리즘을 제공합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 얼굴 인식, 얼굴 인식 소프트웨어, 얼굴 분석, 얼굴 일치, 얼굴 인식 앱, 이미지별 얼굴 검색, 얼굴 인식 검색
ms.openlocfilehash: d135e97b4792d5c4b71d3758800223d238a6990c
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111810183"
---
# <a name="what-is-the-azure-face-service"></a>Azure Face 서비스란?

> [!WARNING]
> 2020년 6월 11일, Microsoft는 인권에 기반한 강력한 규정이 적용될 때까지 미국 경찰 당국에 얼굴 인식 기술을 판매하지 않겠다고 발표했습니다. 따라서 미국 경찰 당국에 의해 또는 미국 경찰 당국을 위해 이 서비스를 사용하거나 허용하는 경우 고객은 얼굴 인식 기능 또는 Face나 Video Indexer와 같은 Azure 서비스에 포함된 기능을 사용하지 않을 수도 있습니다.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 AI 알고리즘을 제공합니다. 얼굴 인식 소프트웨어는 보안, 자연스러운 사용자 인터페이스, 이미지 콘텐츠 분석 및 관리, 모바일 앱, 로봇과 같은 다양한 시나리오에서 중요합니다.

Face 서비스는 다음 섹션에 각각 설명된 여러 가지 얼굴 분석 기능을 제공합니다.

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./Quickstarts/client-libraries.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](./concepts/face-detection.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.
* [자습서](./enrollment-overview.md)는 보다 광범위한 비즈니스 솔루션에서 이 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="face-detection"></a>얼굴 감지

Detect API는 이미지에서 사람의 얼굴을 감지하고 해당 위치의 사각형 좌표를 반환합니다. 필요에 따라 얼굴 감지는 머리 자세, 성별, 연령, 감정, 수염 및 안경과 같은 일련의 얼굴 관련 특성을 추출할 수 있습니다. 이러한 속성은 실제 분류가 아닌 일반적인 예측입니다. 

> [!NOTE]
> 얼굴 감지 기능은 [Computer Vision 서비스](../computer-vision/overview.md)를 통해 사용할 수도 있습니다. 그러나 식별, 확인, 유사 항목 찾기 또는 그룹과 같은 추가 Face 작업을 수행하려면 이 Face 서비스를 대신 사용해야 합니다.

얼굴 감지에 대한 자세한 내용은 [얼굴 감지](concepts/face-detection.md) 개념 문서를 참조하세요. [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조 설명서도 참조하세요.

## <a name="face-verification"></a>얼굴 확인

Verify API는 검색을 기반으로 하며 "이 두 이미지가 동일한 사람인가요?"라는 질문을 해결합니다. 프로브 이미지가 등록된 하나의 템플릿과 비교되기 때문에 확인을 "일대일" 일치라고도 합니다. ID 확인 또는 액세스 제어 시나리오에서 확인을 사용하여 사진이 이전에 캡처한 이미지(예: 정부 발급 ID 카드의 사진)와 일치하는지 확인할 수 있습니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 참조 설명서를 참조하세요.

## <a name="face-identification"></a>얼굴 식별

또한 Identify API는 감지로 시작하여 "데이터베이스에 등록된 얼굴과 이 감지된 얼굴을 일치시킬 수 있나요?"라는 질문에 응답합니다. 얼굴 인식 검색과 비슷하기 때문에 "일대다" 일치라고도 합니다. 감지된 얼굴이 포함된 프로브 템플릿이 등록된 각 템플릿과 얼마나 일치하는지에 따라 후보 일치가 반환됩니다.

다음 이미지는 `"myfriends"`라는 데이터베이스의 예를 보여 줍니다. 각 그룹은 최대 1백만 개의 서로 다른 사람 개체를 포함할 수 있습니다. 각 사람 개체에 대해 최대 248개의 얼굴을 등록할 수 있습니다.

![서로 다른 사람에 대한 3개의 열 및 각 열에 3개의 얼굴 이미지 행이 있는 그리드](./Images/person.group.clare.jpg)

데이터베이스를 만들고 학습시킨 후에 새로 감지된 얼굴과 그룹을 비교하여 식별할 수 있습니다. 얼굴이 그룹에 속한 사람의 것으로 식별되면 해당 사람 개체가 표시됩니다.

사람 식별에 대한 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 참조 설명서를 참조하세요.

## <a name="find-similar-faces"></a>유사 얼굴 찾기

Find Similar API는 대상 얼굴과 일련의 후보 얼굴 간 얼굴 일치를 수행하고 대상 얼굴과 비슷해 보이는 몇 개의 얼굴을 찾습니다. 이 작업은 이미지별 얼굴 검색을 수행하는 데 유용합니다. 

**matchPerson** 및 **matchFace** 의 두 가지 작업 모드가 지원됩니다. **matchPerson** 모드는 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)를 사용하여 동일한 사람에 대해 필터링한 후 유사한 얼굴을 반환합니다. **matchFace** 모드는 동일한 사람 필터를 무시합니다. 이 모드는 동일한 사람에게 속하거나 속하지 않을 수 있는 유사한 후보 얼굴의 목록을 표시합니다.

다음 예제에서는 대상 얼굴을 보여 줍니다.

![웃는 여자](./Images/FaceFindSimilar.QueryFace.jpg)

그리고 다음 이미지는 후보 얼굴입니다.

![5개의 웃는 사람들의 이미지 a와 b 이미지는 동일한 사람을 보여 줍니다.](./Images/FaceFindSimilar.Candidates.jpg)

4개의 유사한 얼굴을 찾기 위해 **matchPerson** 모드는 a와 b를 표시하며 동일한 사람을 대상 얼굴로 나타냅니다. **matchFace** 모드는 일부가 대상과 동일한 사람이 아니거나 유사성이 낮은 경우에도 4개의 후보인 a, b, c, d&mdash;를 정확히 표시합니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 참조 설명서를 참조하세요.

## <a name="face-grouping"></a>얼굴 그룹화

Group API는 알 수 없는 얼굴을 유사성에 따라 여러 그룹으로 나눕니다. 각 그룹은 서로 공통점이 없는 여러 개의 고유한 원래 얼굴에 속합니다. 그룹의 모든 얼굴은 동일한 사람에게 속할 가능성이 큽니다. 한 사람에게 여러 다른 그룹이 있을 수 있습니다. 그룹은 식과 같은 다른 요소로 구분됩니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 참조 설명서를 참조하세요.


## <a name="sample-apps"></a>샘플 앱

다음 샘플 애플리케이션에서는 Face 서비스를 사용하는 몇 가지 방법을 보여 줍니다.

- [Face API: Windows 클라이언트 라이브러리 및 샘플](https://github.com/Microsoft/Cognitive-Face-Windows)은 얼굴 감지, 분석 및 식별에 대한 몇 가지 시나리오를 보여 주는 WPF 앱입니다.
- [FamilyNotes UWP 앱](https://github.com/Microsoft/Windows-appsample-familynotes)은 가족 메모 공유 시나리오에서 음성, Cortana, 잉크 및 카메라와 함께 얼굴 식별 기능을 사용하는 UWP(유니버설 Windows 플랫폼) 앱입니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services 리소스와 마찬가지로 Face 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. 자세한 내용은 Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)를 참조하세요.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 사용자가 선택한 언어로 얼굴 인식 앱의 기본 구성 요소를 코딩합니다.

- [클라이언트 라이브러리 빠른 시작](quickstarts/client-libraries.md)
