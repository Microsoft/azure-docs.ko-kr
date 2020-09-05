---
title: '자습서: AI Builder - Form Recognizer를 사용하여 양식 처리 앱 만들기'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 AI Builder를 사용하여 양식 처리 애플리케이션을 학습시킵니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 07/01/2020
ms.author: pafarley
ms.openlocfilehash: 981c6f6bb2b0eb597b32ce8e428ef0aa7d19929b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003368"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>자습서: AI Builder를 사용하여 양식 처리 앱 만들기

[AI Builder](https://docs.microsoft.com/ai-builder/overview)는 프로세스를 자동화하고 결과를 예측하여 비즈니스 성과를 높일 수 있는 Power Platform 기능입니다. AI Builder 양식 처리를 사용하여 양식 문서에서 키-값 쌍 및 테이블 데이터를 찾아 추출하는 AI 모델을 만들 수 있습니다.

> [!NOTE]
> 이 프로젝트는 [Microsoft Learn 모듈](https://docs.microsoft.com/learn/modules/get-started-with-form-processing/)로도 제공됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 양식 처리 AI 모델 만들기
> * 모델 학습
> * Azure Power Apps 또는 Power Automate에서 사용할 모델 게시

## <a name="prerequisites"></a>필수 구성 요소

* 학습/테스트 데이터에 사용할 동일한 형식의 양식 세트 5개 이상. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [학습 데이터 세트 빌드](./build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2128080)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* Power Apps 또는 Power Automate 라이선스 - [라이선스 가이드](https://go.microsoft.com/fwlink/?linkid=2085130)를 참조하세요. 라이선스에는 [Common Data Service](https://powerplatform.microsoft.com/en-us/common-data-service/)가 포함되어야 합니다.
* AI Builder [추가 기능 또는 평가판](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)


## <a name="create-a-form-processing-project"></a>양식 처리 프로젝트 만들기

1. [Power Apps](https://make.powerapps.com/) 또는 [Power Automate](https://flow.microsoft.com/signin)로 이동하여 조직 계정으로 로그인합니다.
1. 왼쪽 창에서 **AI Builder** > **빌드**를 선택합니다.
1. **양식 처리** 카드를 선택합니다.
1. 모델 이름을 입력합니다.
1. **만들기**를 선택합니다.

## <a name="upload-and-analyze-documents"></a>문서 업로드 및 분석

**문서 추가** 페이지에서 정보를 추출할 양식 유형에 대한 모델을 학습시키는 데 사용할 샘플 문서를 제공해야 합니다. 문서를 업로드하면 AI Builder는 문서를 분석하여 문서가 모델을 학습시키는 데 충분한지 확인합니다.

> [!NOTE]
> AI Builder는 현재 다음과 같은 형식의 양식 처리 입력 데이터를 지원하지 않습니다.
>
> - 복합 테이블(중첩된 테이블, 병합된 헤더 또는 셀 등)
> - 확인란 또는 라디오 단추
> - 50페이지가 넘는 PDF 문서
> - 채우기 가능 PDF
>
> 입력 문서의 요구 사항에 대한 자세한 내용은 [입력 요구 사항](./overview.md#input-requirements)을 참조하세요.

### <a name="upload-your-documents"></a>문서 업로드

1. **문서 추가**를 선택하고 5개 이상의 문서를 선택한 다음, **업로드**를 선택합니다.
1. 업로드가 완료되면 **닫기**를 선택합니다.
1. 그런 다음, **분석**을 선택합니다.

> [!NOTE] 
> 이러한 문서를 업로드한 후 일부 문서를 제거하거나 추가 문서를 업로드할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![문서 페이지 추가](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>문서 분석

분석 단계에서 AI Builder는 업로드된 문서를 검사하고 필드와 테이블을 감지합니다. 이 작업을 완료하는 데 걸리는 시간은 제공된 문서의 수에 따라 다르지만, 대부분 몇 분 이내에 완료됩니다.

분석이 완료되면 미리 보기를 선택하여 필드 선택 환경을 엽니다.

> [!IMPORTANT]
> 분석이 실패하는 경우 AI Builder가 문서의 정형 텍스트를 감지할 수 없는 것이 원인일 가능성이 높습니다. 업데이트한 문서가 [입력 요구 사항](./overview.md#input-requirements)을 준수하는지 확인합니다.

## <a name="select-your-form-fields"></a>양식 필드 선택

필드 선택 페이지에서 본인에게 중요한 필드를 선택합니다.

1. 필드를 선택하려면 문서에서 감지된 필드를 나타내는 사각형을 클릭하거나, 필드를 클릭하고 끌어서 여러 필드를 선택합니다. 오른쪽 창에서 필드를 선택할 수도 있습니다.
1. 요구 사항에 맞게 이름을 변경하거나 추출된 레이블을 정규화하려면 선택한 필드의 이름을 클릭합니다.

    감지된 필드를 클릭하면 다음 정보가 표시됩니다.

    - **필드 이름**: 감지된 필드의 레이블 이름입니다.
    - **필드 값**: 감지된 필드의 값입니다.

> [!div class="mx-imgBorder"]
> ![문서 페이지 추가](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>감지되지 않은 필드에 레이블 지정

레이블을 지정하려는 필드를 모델이 자동으로 감지하지 못한 경우 해당 값 주위에 사각형을 그리고 나타나는 대화 상자에 레이블 이름을 입력하면 됩니다.

## <a name="train-your-model"></a>모델 학습

1. **다음**을 선택하여 선택한 양식 필드를 확인합니다. 모두 정상이면 **학습**을 선택하여 모델을 학습시킵니다.

    > [!div class="mx-imgBorder"]
    > ![문서 페이지 추가](./media/tutorial-ai-builder/summary-train-page.png)
1. 학습이 완료되면 **학습 완료** 화면에서 **세부 정보 페이지로 이동**을 선택합니다.
## <a name="quick-test-your-model"></a>빠른 모델 테스트

모델을 게시하거나 사용하기 전에 [세부 정보] 페이지에서 모델을 테스트할 수 있습니다.

1. [세부 정보] 페이지에서 **빠른 테스트**를 선택합니다.
2. 문서를 끌어서 놓거나 **내 디바이스에서 업로드**를 선택하여 테스트 파일을 업로드할 수 있습니다. 불과 몇 초면 빠른 테스트 결과가 표시됩니다.
3. **다시 시작**을 선택하여 다른 테스트를 실행하거나, 모두 마쳤으면 **닫기**를 선택합니다.

### <a name="troubleshooting-tips"></a>문제 해결 팁

특정 필드의 결과가 잘못 되었거나 신뢰도 점수가 낮은 경우 다음 팁을 사용해 보세요.

- 각 필드의 값이 다른 양식을 사용하여 다시 학습시킵니다.
- 더 큰 학습 문서 세트를 사용하여 다시 학습시킵니다. 더 많은 문서에 태그를 지정할수록 AI Builder는 필드를 보다 잘 인식하는 방법을 더 많이 배울 수 있습니다.
- 학습에 사용할 특정 페이지만 선택하여 PDF 파일을 최적화할 수 있습니다. **인쇄** > **PDF로 인쇄** 옵션을 사용하여 문서 내의 특정 페이지를 선택할 수 있습니다.

## <a name="publish-your-model"></a>모델 게시

모델에 만족하는 경우 **게시**를 선택하여 모델을 게시합니다. 게시가 완료되면 모델은 **게시됨**으로 승격되어 사용 준비가 완료됩니다.

> [!div class="mx-imgBorder"]
> ![문서 페이지 추가](./media/tutorial-ai-builder/model-page.png)

양식 처리 모델을 게시한 후에는 [Power Apps 캔버스 앱](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps) 또는 [Power Automate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AI Builder 설명서에 따라 양식 처리 모델을 사용합니다.

* [Power Apps에서 양식-프로세서 구성 요소 사용](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)
* [Power Automate에서 양식 처리 모델 사용](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)