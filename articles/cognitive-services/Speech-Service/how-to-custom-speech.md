---
title: Custom Speech-Speech service 시작
titleSuffix: Azure Cognitive Services
description: Custom Speech는 응용 프로그램, 도구 및 제품에 대 한 음성-텍스트 정확도를 평가 하 고 개선할 수 있는 온라인 도구 집합입니다. 시작하려면 테스트 오디오 파일 몇 개만 있으면 됩니다. 사용자 지정 음성-텍스트 변환 환경을 만들기 시작하려면 아래 링크를 따르세요.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c8c849cb83ecb1db5e972c660d94c795092c458e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806014"
---
# <a name="what-is-custom-speech"></a>사용자 지정 음성이란?

[사용자 지정 음성](https://aka.ms/customspeech)은 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트 변환 정확도를 향상시키고 평가할 수 있는 온라인 도구 집합입니다. 시작하려면 테스트 오디오 파일 몇 개만 있으면 됩니다. 사용자 지정 음성-텍스트 변환 환경을 만들기 시작하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-speech"></a>사용자 지정 음성의 기능

Custom Speech를 사용 하 여 모든 작업을 수행 하려면 Azure 계정 및 음성 서비스 구독이 필요 합니다. 계정을 마련한 후 데이터 준비, 모델 학습 및 테스트, 인식 품질 검사, 정확도 평가와 궁극적으로 사용자 지정 음성-텍스트 변환 모델을 배포할 수 있습니다.

이 다이어그램은 [Custom Speech 포털](https://aka.ms/customspeech)을 구성 하는 부분을 강조 표시 합니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용합니다.

![Custom Speech 포털을 구성 하는 다양 한 구성 요소를 강조 표시 합니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스를 구독 합니다. 이 통합 구독은 음성 텍스트, 텍스트 음성 변환, 음성 번역 및 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 대 한 액세스를 제공 합니다. 그런 다음 음성 서비스 구독을 사용 하 여 첫 번째 Custom Speech 프로젝트를 만듭니다.

2. [테스트 데이터 업로드](how-to-custom-speech-test-data.md) - 사용자의 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트를 평가하기 위해 테스트 데이터(오디어 파일)을 업로드합니다.

3. [인식 품질 검사](how-to-custom-speech-inspect-data.md) - [Custom Speech 포털](https://speech.microsoft.com/customspeech) 을 사용 하 여 업로드 된 오디오를 재생 하 고 테스트 데이터의 음성 인식 품질을 검사 합니다. 정량 측정값에 대해서는 [데이터 검사](how-to-custom-speech-inspect-data.md)를 참조합니다.

4. [정확도 평가](how-to-custom-speech-evaluate-data.md) - 음성-텍스트 모델의 정확도를 평가합니다. [Custom Speech 포털](https://speech.microsoft.com/customspeech) 은 추가 교육이 필요한 지 여부를 결정 하는 데 사용할 수 있는 *단어 오류 요금*을 제공 합니다. 정확도에 만족 하는 경우 음성 서비스 Api를 직접 사용할 수 있습니다. 5%-20%의 상대적인 평균으로 정확도를 개선하려는 경우, 사람 레이블이 지정된 기록 및 관련된 텍스트와 같은 추가 학습 데이터를 업로드하려면 포털에서 **교육** 탭을 사용합니다.

5. [모델 학습](how-to-custom-speech-train-model.md) - 작성된 대본(10-1,000 시간)과 관련된 텍스트(< 200MB)를 오디오 테스트 데이터와 함께 제공하여 음성-텍스트 모델의 정확도를 향상시킵니다. 이 데이터는 음성-텍스트 모델을 학습 시키는 데 도움이 됩니다. 학습 후 다시 테스트하고 결과에 만족하는 경우, 모델을 배포할 수 있습니다.

6. [모델 배포](how-to-custom-speech-deploy-model.md) - 음성-텍스트 모델에 대한 사용자 지정 엔드포인트을 만들고 응용 프로그램, 도구 또는 제품에서 사용합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[Custom Speech 포털](https://speech.microsoft.com/customspeech) 을 사용 하 여 사용자 지정 모델을 만들려면 먼저 음성 서비스 구독이 필요 합니다. 표준 음성 서비스 구독을 만들려면 다음 지침을 따르세요. [음성 구독을 만듭니다](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> 표준(S0) 구독을 만들어야 합니다. 무료 평가판(F0) 구독은 지원되지 않습니다.

Azure 계정과 음성 서비스 구독을 만든 후에는 [Custom Speech 포털](https://speech.microsoft.com/customspeech) 에 로그인 하 여 구독을 연결 해야 합니다.

1. Azure Portal에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털](https://aka.ms/custom-speech)에 로그인합니다.
3. 작업 해야 하는 구독을 선택 하 고 음성 프로젝트를 만들어야 합니다.
4. 구독을 수정하려는 경우는 위쪽 탐색에서 **코그** 아이콘을 사용합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 엔드포인트과 같은 콘텐츠는 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에서 **프로젝트로** 구성 됩니다. 각 프로젝트는 도메인 및 국가/언어에만 적용 됩니다. 예를 들어 미국에서 영어를 사용하는 콜 센터에 대한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **음성-텍스트/사용자 지정 음성** 선택한 다음, **새 프로젝트**를 클릭합니다. 프로젝트를 만들려면 마법사에서 제공하는 지침을 따릅니다. 프로젝트를 만든 후에는 **데이터**, **테스트**, **학습**및 **배포**의 네 가지 탭이 표시 됩니다. 각 탭을 사용하는 방법을 배우려면 [다음 단계](#next-steps)에서 제공되는 링크를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
