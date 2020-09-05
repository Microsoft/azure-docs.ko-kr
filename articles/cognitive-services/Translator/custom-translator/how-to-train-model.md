---
title: 모델 훈련 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 모델 훈련은 번역 모델을 빌드할 때 중요한 단계입니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 824516a327d45feb5b6a084a113633bd3a486abe
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508372"
---
# <a name="train-a-model"></a>모델 학습

모델 학습은 변환 모델을 빌드하기 위한 가장 중요 한 첫 번째 단계입니다. 그렇지 않으면 모델을 빌드할 수 없습니다. 훈련은 사용자가 훈련에 대해 선택한 문서를 바탕으로 이루어집니다. "학습" 문서 형식의 문서를 선택 하는 경우 1만 병렬 문장의 최소 요구 사항에 유의 해야 합니다. 문서를 선택 하면 안내 하는 총 학습 문장 수가 표시 됩니다. 사전 문서 종류의 문서만 모델을 학습 하는 경우에는이 요구 사항이 적용 되지 않습니다.

모델을 훈련하려면 다음을 수행합니다.

1. 모델을 빌드하려는 프로젝트를 선택합니다.

2. 프로젝트의 [데이터] 탭에 프로젝트 언어 쌍에 대한 관련 문서가 모두 표시됩니다. 모델 훈련에 사용할 문서를 수동으로 선택합니다. 이 화면에서 훈련, 튜닝, 테스트 문서를 선택할 수 있습니다. 또는 훈련 세트만 선택하고 튜닝 세트와 테스트 세트는 Custom Translator가 생성하도록 할 수도 있습니다.

    - 문서 이름: 문서의 이름입니다.

    - 페어링: 병렬 문서인지 단일어 문서인지를 나타냅니다. 현재 단일어 문서에는 훈련이 지원되지 않습니다.

    - 문서 유형: 훈련, 튜닝, 테스트 또는 사전일 수 있습니다.

    - 언어 쌍: 프로젝트의 원본 언어와 대상 언어를 보여 줍니다.

    - 원본 문장: 원본 파일에서 추출 된 문장의 수를 표시 합니다.

    - 대상 문장: 대상 파일에서 추출 된 문장의 수를 표시 합니다.

    ![모델 학습](media/how-to/how-to-train-model.png)

3. "모델 만들기" 단추를 클릭 합니다.

4. 대화 상자에서 모델의 이름을 지정 합니다. "모델 만들기" 단추를 클릭 하면 기본적으로 "즉시 학습"을 선택 하 여 학습 파이프라인을 시작할 수 있습니다. "초안으로 저장"을 선택 하 여 모델 메타 데이터를 만들고 모델을 초안 상태로 전환할 수 있지만 모델 학습은 시작 되지 않습니다. 나중에 학습 하려면 초안 상태에서 모델을 수동으로 선택 해야 합니다.

5. "모델 만들기" 단추를 클릭 합니다.

    ![모델 훈련 대화 상자](media/how-to/how-to-train-model-2.png)

6. 사용자 지정 변환기가 학습을 제출 하 고 모델 탭에 학습 상태를 표시 합니다.

    ![모델 훈련 페이지](media/how-to/how-to-train-model-3.png)

>[!Note]
>사용자 지정 변환기는 언제 든 지 작업 영역 내에서 10 개의 동시 학습 지원 합니다.

## <a name="modify-a-model-name"></a>모델 이름 수정

모델 정보 페이지에서 모델 이름을 수정할 수 있습니다.

1. 프로젝트 페이지에서 모델이 존재 하는 프로젝트 이름을 클릭 합니다.
2. 모델 탭을 클릭 합니다.
3. 모델 이름을 클릭 하 여 모델 세부 정보를 확인 합니다.
4. 연필 아이콘을 클릭합니다.

    ![모델 편집](media/how-to/how-to-edit-model.png)

5. 대화 상자에서 모델 이름을 변경 하 고 모델에 의미 있는 이름을 지정 합니다.

    ![추가 편집 대화 상자](media/how-to/how-to-edit-model-dialog.png)

6. 저장을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [모델 세부 정보를 보는 방법](how-to-view-model-details.md)을 알아보세요.
