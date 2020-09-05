---
title: 샘플 모델
description: 샘플 모델의 소스를 나열합니다.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507538"
---
# <a name="sample-models"></a>샘플 모델

이 문서에서는 Azure Remote Rendering 서비스를 테스트하는 데 사용할 수 있는 샘플 데이터에 대한 몇 가지 리소스를 나열합니다.

## <a name="built-in-sample-model"></a>기본 제공 샘플 모델

URL **builtin://Engine**을 사용하여 항상 로드할 수 있는 기본 제공 샘플 모델을 제공합니다.

![샘플 모델](./media/sample-model.png "샘플 모델")

모델 통계:

| 속성 | 값 |
|-----------|:-----------|
| [필요한 서버 크기](../how-tos/session-rest-api.md#create-a-session) | 표준 |
| 삼각형 수 | 1870만 |
| 이동 가능한 파트 수 | 2073 |
| 재료 수 | 94 |

## <a name="third-party-data"></a>타사 데이터

Khronos 그룹은 테스트를 위한 glTF 샘플 모델 세트를 유지 관리합니다. ARR은 텍스트( *.gltf*)와 이진( *.glb*) 형식 둘 다에서 glTF 형식을 지원합니다. 최상의 시각적 결과를 위해 PBR 모델을 사용하는 것이 좋습니다.

* [glTF 샘플 모델](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
* [빠른 시작: 렌더링을 위해 모델 변환](../quickstarts/convert-model.md)
