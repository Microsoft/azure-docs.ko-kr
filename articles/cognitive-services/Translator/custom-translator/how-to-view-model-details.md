---
title: 모델 세부 정보 보기 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 프로젝트의 모델 탭에는 모델 이름, 모델 상태, BLEU 점수, 학습, 튜닝, 문장 수 테스트와 같은 각 모델의 세부 정보가 표시됩니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 52e305f9d50ab0f383810778be681ac41be97c72
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509797"
---
# <a name="view-model-details"></a>모델 세부 정보 보기

프로젝트의 모델 탭에 해당 프로젝트의 모든 모델이 표시됩니다. 해당 프로젝트에 대해 학습된 모든 모델이 이 탭에는 나열됩니다.

프로젝트의 각 모델에 대해 이러한 세부 정보가 표시 됩니다.

1. 모델 이름: 지정된 모델의 모델 이름을 표시합니다.

2. 상태: 지정된 모델의 상태를 표시합니다. 새로운 학습은 수락될 때까지 Submitted 상태입니다. 상태는 서비스가 문서의 콘텐츠를 평가하는 동안 데이터 처리 중으로 변경됩니다. 문서 평가가 완료되면 상태는 Running으로 변경되며, 자동으로 작성되는 튜닝 및 테스트 세트를 비롯한 학습 과정에 포함된 문장 수를 볼 수 있습니다. 다음은 모델의 상태를 설명하는 모델 상태의 목록입니다.

    - Submitted: 해당 모델에 대한 문서를 처리 중인 백 엔드를 지정합니다.

    - TrainingQueued: 해당 모델에 대한 MT 시스템의 큐에 대기 중인 학습을 지정합니다.

    - Running: 해당 모델에 대한 MT 시스템에서 실행 중인 학습을 지정합니다.

    - Succeeded: MT 시스템에서 성공한 학습 및 사용 가능한 모델을 지정합니다. 이 상태에서는 해당 모델에 대해 BLEU 점수가 표시됩니다.

    - Deployed: 배포에 대해 MT 시스템에 제출된 성공적으로 학습된 모델을 지정합니다.

    - Undeploying: 배포를 취소 중인 배포된 모델을 지정합니다.

    - Undeployed: 성공적으로 완료된 모델의 배포 취소 프로세스를 지정합니다.

    - Training Failed: 실패한 학습을 지정합니다. 학습 오류가 발생한 경우 학습 작업을 다시 시도합니다. 오류가 지속되면 알려주세요. 실패한 모델을 삭제하지 마십시오.

    - DataProcessingFailed: 모델에 속한 하나 이상의 문서에 대해 실패한 데이터 처리를 지정합니다.

    - DeploymentFailed: 실패한 모델 배포를 지정합니다.

    - MigratedDraft: 허브에서 Custom Translator로 마이그레이션한 후 초안 상태의 모델을 지정합니다.

3. BLEU 점수: 번역 시스템의 품질을 나타내는 모델의 BLEU(Bilingual Evaluation Understudy) 점수를 표시합니다. 이 점수는 이 학습의 결과로 번역 시스템이 수행한 번역이 테스트 데이터 세트의 참조 문장과 얼마나 밀접하게 일치하는지 보여줍니다. BLEU 점수는 학습이 성공적으로 완료된 경우 나타납니다. 학습이 완료되지 않았거나 실패인 경우 BLEU 점수를 볼 수 없습니다.

4. 학습 문장의 개수: 학습 집합으로 사용 되는 총 문장의 수를 표시 합니다.

5. 튜닝 문장 수: 튜닝 세트로 사용된 총 문장 수를 표시합니다.

6.  테스트 문장 수: 테스팅 세트로 사용된 총 문장 수를 표시합니다.

7.  Mono 문장 수: Mono 세트로 사용된 총 문장 수를 표시합니다.

8.  배포 작업 단추: 성공적으로 학습된 모델에서 배포되지 않은 경우 “배포” 단추가 표시됩니다. 모델이 배포되면 “배포 취소” 단추가 표시됩니다.

9. 삭제: 모델을 삭제하려는 경우 이 단추를 사용할 수 있습니다. 모델을 삭제해도 해당 모델을 만드는 데 사용된 문서는 삭제되지 않습니다.

    ![모델 세부 정보 보기](media/how-to/how-to-view-model-details.png)

>[!Note]
>동일한 시스템에 대한 연속 학습을 비교하려면 반드시 튜닝 세트 및 테스트 세트 상수를 유지해야 합니다.

## <a name="view-model-training-details"></a>모델 학습 세부 정보 보기

학습이 완료되면 세부 정보 페이지에서 학습에 대한 세부 정보를 검토할 수 있습니다. 프로젝트를 선택하고, 모델 탭을 찾아 선택한 후 모델을 선택합니다.

모델 페이지에 학습 세부 정보 및 테스트라는 두 개의 탭이 있습니다.

1.  **학습 세부 정보:** 이 탭에는 학습에 사용되는 문서 목록이 표시됩니다.

    -  문서 이름: 이 필드에는 문서의 이름이 표시됩니다.

    -  문서 유형: 이 필드에는 이 문서가 병렬/Mono인지 여부가 표시됩니다.

    -  원본 언어의 문장 수: 이 필드에는 원본 언어에 포함된 문장의 수가 표시됩니다.

    -  대상 언어의 문장 수: 이 필드에는 대상 언어에 포함된 문장의 수가 표시됩니다.

    -  정렬된 문장: 이 필드에는 정렬 프로세스 동안 Custom Translator에서 정렬한 문장의 수가 표시됩니다.

    -  사용된 문장: 이 필드에는 이 학습에서 Custom Translator에서 사용한 문장의 수가 표시됩니다.

    ![모델 학습 세부 정보](media/how-to/how-to-model-training-details.png)

2.  **테스트:** 이 탭에는 성공적인 학습용 테스트 세부 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [테스트 결과](how-to-view-system-test-results.md)를 검토하고 학습 결과를 분석합니다.
