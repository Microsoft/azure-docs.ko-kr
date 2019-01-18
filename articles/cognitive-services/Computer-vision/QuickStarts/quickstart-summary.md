---
title: Computer Vision 빠른 시작 요약
titleSuffix: Azure Cognitive Services
description: 이러한 빠른 시작에서는 Computer Vision API를 사용하여 이미지를 분석하고, 썸네일을 만들고, 인쇄되고 필기된 텍스트를 추출합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 645c57c9bfeaefcfd2fc50b2dd431713fffaa42b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578787"
---
# <a name="quickstart-summary"></a>빠른 시작: 요약

이러한 빠른 시작에서는 Computer Vision 서비스를 사용하여 빠르게 시작하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다.

이 샘플은 Computer Vision API에 대한 직접 HTTP 호출을 생성합니다. HTTP 호출을 래핑하는 편의 메서드를 제공하는 Computer Vision 클라이언트 라이브러리를 사용하는 샘플은 *SDK 빠른 시작* 섹션을 참조하세요.

Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

Computer Vision 서비스를 사용하여 다음 작업을 수행할 수 있습니다.

* 원격 이미지 분석
* 로컬 이미지 분석
* 유명인 및 랜드마크 검색(도메인 모델)
* 지능적으로 썸네일 생성
* 이미지에서 인쇄된 텍스트(OCR) 검색 및 추출
* 이미지에서 필기된 텍스트 검색 및 추출

각 샘플의 코드는 유사합니다. 그러나 다음과 같은 서비스를 사용하여 데이터를 교환하는 다양한 기술과 함께 다양한 Computer Vision 기능을 강조 표시합니다.

* _썸네일 만들기_ 는 응답 본문에서 이미지를 바이트 배열로 반환합니다.
* _로컬 이미지 분석_ 은 요청에서 이미지를 바이트 배열로 포함해야 합니다.
* _필기된 텍스트 추출_ 은 텍스트를 검색하기 위해 두 번 호출해야 합니다.

## <a name="summary"></a>요약

| 빠른 시작               | 요청 매개 변수                          | response          |
| ------------------------ | ------------------------------------------- | ----------------  |
| 원격 이미지 분석   | visualFeatures=Categories,Description,Color | JSON 문자열       |
| 로컬 이미지 분석    | data=image_data(byte array)                | JSON 문자열       |
| 유명인 검색       | model=celebrities                           | JSON 문자열       |
| 썸네일 생성     | width=200&height=150&smartCropping=true     | 바이트 배열        |
| 인쇄된 텍스트 추출     | language=unk&detectOrientation=true         | JSON 문자열       |
| 필기 텍스트 추출 | handwriting=true                            | URL, JSON 문자열  |

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 탐색합니다.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
