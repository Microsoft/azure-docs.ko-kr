---
title: Face 서비스란?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 데 사용되는 알고리즘을 제공합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/01/2020
ms.author: pafarley
ms.openlocfilehash: e1b5ca8cfb9a6bc0fec7d85f609c411f38d1bbbc
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280073"
---
# <a name="what-is-the-azure-face-service"></a>Azure Face 서비스란?
<!--
> [!IMPORTANT]
> On June 11, 2020, Microsoft announced that it will not sell facial recognition technology to police departments in the United States until strong regulation, grounded in human rights, has been enacted. As such, customers may not use facial recognition features or features included in Azure Services, such as Face or Video Indexer, if a customer is, or is allowing use of such services by or for, a police department in the United States.
-->
![Legal ](./media/overview/banner.png)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Cognitive Services Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 알고리즘을 제공합니다. 사람의 얼굴 정보를 처리하는 기능은 서로 다른 다양한 소프트웨어 시나리오에서 중요합니다. 시나리오의 예에는 보안, 자연스러운 사용자 인터페이스, 이미지 콘텐츠 분석 및 관리, 모바일 앱과 로봇 공학이 포함됩니다.

Face 서비스는 다음 섹션에 각각 설명된 여러 가지 기능을 제공합니다.

## <a name="face-detection"></a>얼굴 감지

Face 서비스는 이미지에서 사람의 얼굴을 감지하고 해당 위치의 사각형 좌표를 반환합니다. 얼굴 감지는 필요에 따라 일련의 얼굴 관련 특성을 추출할 수 있습니다. 머리 자세, 성별, 연령, 감정, 수염, 안경 등이 그 예입니다.

> [!NOTE]
> 얼굴 감지 기능은 [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)를 통해 사용할 수도 있습니다. 얼굴 데이터를 사용하여 추가 작업을 수행하려면 이 문서에서 설명하는 서비스인 Face 서비스를 사용합니다.

![얼굴 주위에 사각형이 그려지고 연령과 성별이 표시된 여자와 남자의 이미지](./Images/Face.detection.jpg)

얼굴 감지에 대한 자세한 내용은 [얼굴 감지](concepts/face-detection.md) 개념 문서를 참조하세요. [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조 설명서도 참조하세요.

## <a name="face-verification"></a>얼굴 확인

Verify API는 감지된 두 얼굴을 비교하여 인증하거나 하나의 감지된 얼굴과 하나의 개인 개체를 비교하여 인증합니다. 실질적으로 두 얼굴이 같은 사람인지 여부를 평가합니다. 이 기능은 보안 시나리오에서 유용할 수 있습니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 참조 설명서를 참조하세요.

## <a name="find-similar-faces"></a>유사 얼굴 찾기

Find Similar API는 대상 얼굴을 여러 후보 얼굴과 비교하여 대상 얼굴과 비슷한 얼굴을 찾습니다. matchPerson 및 matchFace의 두 가지 작업 모드가 지원됩니다. matchPerson 모드는 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)를 사용하여 동일한 사람을 필터링한 후에 유사한 얼굴을 표시합니다. matchFace 모드는 동일한 사람 필터를 무시합니다. 이 모드는 동일한 사람에게 속하거나 속하지 않을 수 있는 유사한 후보 얼굴의 목록을 표시합니다.

다음 예제에서는 대상 얼굴을 보여 줍니다.

![웃는 여자](./Images/FaceFindSimilar.QueryFace.jpg)

그리고 다음 이미지는 후보 얼굴입니다.

![5개의 웃는 사람들의 이미지 a와 b 이미지는 동일한 사람을 보여 줍니다.](./Images/FaceFindSimilar.Candidates.jpg)

4개의 유사한 얼굴을 찾기 위해 matchPerson 모드는 a와 b를 표시하며 동일한 사람을 대상 얼굴로 나타냅니다. matchFace 모드는 일부가 대상과 동일한 사람이 아니거나 유사성이 낮은 경우에도 4개의 후보인 a, b, c, d&mdash;를 정확히 표시합니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 참조 설명서를 참조하세요.

## <a name="face-grouping"></a>얼굴 그룹화

Group API는 알 수 없는 얼굴을 유사성에 따라 여러 그룹으로 나눕니다. 각 그룹은 서로 공통점이 없는 여러 개의 고유한 원래 얼굴에 속합니다. 그룹의 모든 얼굴은 동일한 사람에게 속할 가능성이 큽니다. 한 사람에게 여러 다른 그룹이 있을 수 있습니다. 그룹은 식과 같은 다른 요소로 구분됩니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 참조 설명서를 참조하세요.

## <a name="person-identification"></a>사람 식별

Identify API는 사람 데이터베이스에서 감지된 얼굴을 식별하는 데 사용됩니다. 이 기능은 사진 관리 소프트웨어에서 이미지 태그를 자동으로 지정하는 데 유용할 수 있습니다. 데이터베이스는 미리 만든 후에 시간이 지남에 따라 편집할 수 있습니다.

다음 이미지는 `"myfriends"`라는 데이터베이스의 예를 보여 줍니다. 각 그룹은 최대 1백만 개의 서로 다른 사람 개체를 포함할 수 있습니다. 각 사람 개체에 대해 최대 248개의 얼굴을 등록할 수 있습니다.

![서로 다른 사람에 대한 3개의 열 및 각 열에 3개의 얼굴 이미지 행이 있는 그리드](./Images/person.group.clare.jpg)

데이터베이스를 만들고 학습시킨 후에 새로 감지된 얼굴과 그룹을 비교하여 식별할 수 있습니다. 얼굴이 그룹에 속한 사람의 것으로 식별되면 해당 사람 개체가 표시됩니다.

사람 식별에 대한 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 참조 설명서를 참조하세요.

## <a name="containers"></a>컨테이너

데이터에 더 유사한 표준화된 Docker 컨테이너를 설치하여 [얼굴 컨테이너를 통해](face-how-to-install-containers.md) 얼굴을 감지, 인식, 식별합니다.

## <a name="sample-apps"></a>샘플 앱

다음 샘플 애플리케이션에서는 Face 서비스를 사용하는 몇 가지 방법을 보여 줍니다.

- [Face API: Windows 클라이언트 라이브러리 및 샘플](https://github.com/Microsoft/Cognitive-Face-Windows)은 얼굴 감지, 분석 및 식별에 대한 몇 가지 시나리오를 보여 주는 WPF 앱입니다.
- [FamilyNotes UWP 앱](https://github.com/Microsoft/Windows-appsample-familynotes)은 가족 메모 공유 시나리오에서 음성, Cortana, 잉크 및 카메라와 함께 얼굴 식별 기능을 사용하는 UWP(유니버설 Windows 플랫폼) 앱입니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services 리소스와 마찬가지로 Face 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. 자세한 내용은 Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작에 따라 코드에서 얼굴 감지 시나리오를 구현합니다.

- [빠른 시작: .NET SDK 및 C#을 사용하여 이미지에서 얼굴 감지](quickstarts/csharp.md). 다른 언어도 사용할 수 있습니다.
