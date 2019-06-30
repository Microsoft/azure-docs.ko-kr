---
title: 사용자 지정 음성-음성 서비스 시작
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성을 평가 하 여 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성-텍스트 정확도 향상 시킬 수 있는 온라인 도구 집합이 있습니다. 시작하려면 테스트 오디오 파일 몇 개만 있으면 됩니다. 사용자 지정 음성-텍스트 변환 환경을 만들기 시작하려면 아래 링크를 따르세요.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511090"
---
# <a name="what-is-custom-speech"></a>사용자 지정 음성이란?

[사용자 지정 음성](https://aka.ms/custom-speech)은 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트 변환 정확도를 향상시키고 평가할 수 있는 온라인 도구 집합입니다. 시작하려면 테스트 오디오 파일 몇 개만 있으면 됩니다. 사용자 지정 음성-텍스트 변환 환경을 만들기 시작하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-speech"></a>사용자 지정 음성의 기능

사용자 지정 음성으로 작업을 수행하려면, Azure 계정 및 Speech Services 구독이 필요합니다. 계정을 마련한 후 데이터 준비, 모델 학습 및 테스트, 인식 품질 검사, 정확도 평가와 궁극적으로 사용자 지정 음성-텍스트 변환 모델을 배포할 수 있습니다.

이 다이어그램에는 사용자 지정 음성 포털을 구성하는 부분이 강조 표시됩니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용합니다.

![사용자 지정 음성 포털을 구성 하는 다양 한 구성 요소를 강조 표시 합니다.](./media/custom-speech/custom-speech-overview.png)

1. [구독하고 프로젝트 만들기](#set-up-your-azure-account) - Azure 계정을 만들고 음성 서비스를 구독합니다. 이 통합된 구독을 통해 음성-텍스트 변환, 텍스트-음성 변환, 음성 번역 및 사용자 지정 음성 포털 액세스가 가능합니다. 그런 다음 음성 서비스 구독을 사용하여 첫 번째 사용자 지정 음성 프로젝트를 생성합니다.

2. [테스트 데이터 업로드](how-to-custom-speech-test-data.md) - 사용자의 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트를 평가하기 위해 테스트 데이터(오디어 파일)을 업로드합니다.

3. [인식 품질 검사](how-to-custom-speech-inspect-data.md) - 사용자 지정 음성 포털을 사용하여 업로드된 오디오를 재생하고 테스트 데이터의 음성 인식 품질 검사를 합니다. 정량 측정값에 대해서는 [데이터 검사](how-to-custom-speech-inspect-data.md)를 참조합니다.

4. [정확도 평가](how-to-custom-speech-evaluate-data.md) - 음성-텍스트 모델의 정확도를 평가합니다. 사용자 지정 음성 포털에서 추가 학습이 필요한지 확인에 사용할 수 있는 *Word Error Rate*를 제공하게 됩니다. 정확도에 만족하는 경우 음성 서비스 API를 바로 사용할 수 있습니다. 5%-20%의 상대적인 평균으로 정확도를 개선하려는 경우, 사람 레이블이 지정된 기록 및 관련된 텍스트와 같은 추가 학습 데이터를 업로드하려면 포털에서 **교육** 탭을 사용합니다.

5. [모델을 학습](how-to-custom-speech-train-model.md) -작성 된 대 본 (10-1,000 시간)를 제공 하 여 음성-텍스트 모델의 정확도 향상 하 고 텍스트와 관련 된 (< 200MB) 오디오와 함께 데이터를 테스트 합니다. 이 데이터 음성-텍스트 모델을 학습 하는 데 도움이 됩니다. 학습 한 후 다시, 테스트 및 결과 만족 하는 경우에 모델을 배포할 수 있습니다.

6. [모델 배포](how-to-custom-speech-deploy-model.md) - 음성-텍스트 모델에 대한 사용자 지정 끝점을 만들고 응용 프로그램, 도구 또는 제품에서 사용합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 음성 포털을 사용 하 여 사용자 지정 모델을 만들기 전에 음성 서비스 구독이 필요 합니다. 표준 음성 서비스 구독을 만들려면 이러한 지침을 따릅니다. [음성 구독 만들기](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account)합니다.

> [!NOTE]
> 표준 (S0) 구독을 만드는 해야 합니다 (F0) 무료 평가판 구독은 지원 되지 않습니다.

Azure 계정 및 음성 서비스 구독을 만든 후에는 사용자 지정 음성 포털에 로그인하여 구독을 연결해야 합니다.

1. Azure portal에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털](https://aka.ms/custom-speech)에 로그인합니다.
3. 음성 프로젝트를 만들고 작업해야 하는 구독을 선택합니다.
4. 구독을 수정하려는 경우는 위쪽 탐색에서 **코그** 아이콘을 사용합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점같은 콘텐츠는 사용자 지정 음성 포털에서 **프로젝트**로 구성됩니다. 각 프로젝트는 도메인 및 국가/언어에 따라 다릅니다. 예를 들어 미국에서 영어를 사용하는 콜 센터에 대한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 선택 합니다 **음성-에-텍스트/사용자 지정 음성**, 클릭 **새 프로젝트**합니다. 프로젝트를 만드는 마법사에서 제공한 지침을 따릅니다. 프로젝트를 만든 후 4 개 탭이 표시 됩니다. **데이터**, **테스트**합니다 **학습**, 및 **배포**합니다. 에 나와 있는 링크를 사용 하 여 [다음 단계](#next-steps) 각 탭을 사용 하는 방법입니다.

## <a name="next-steps"></a>다음 단계

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
