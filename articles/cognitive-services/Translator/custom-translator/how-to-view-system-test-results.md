---
title: 시스템 테스트 결과와 배포 보기 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 훈련을 성공적으로 마쳤으면 시스템 테스트를 검토하여 훈련 결과를 분석합니다. 훈련 결과가 만족스러우면 훈련된 모델에 대한 배포 요청을 제출합니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 04e21e4bcd56506ba27730ebc1f9b0db8dc39615
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509688"
---
# <a name="view-system-test-results"></a>시스템 테스트 결과 보기

훈련을 성공적으로 마쳤으면 시스템 테스트를 검토하여 훈련 결과를 분석합니다. 훈련 결과가 만족스러우면 훈련된 모델에 대한 배포 요청을 제출합니다.

## <a name="system-test-results-page"></a>시스템 테스트 결과 페이지

프로젝트를 선택하고 해당 프로젝트의 [모델] 탭을 선택한 다음 사용하려는 모델을 찾고 [테스트] 탭을 선택합니다.

[테스트] 탭에는 다음과 같은 항목이 표시됩니다.

1.  **시스템 테스트 결과:** 학습에 있는 테스트 프로세스의 결과입니다. 테스트 프로세스는 BLEU 점수를 생성합니다.

    **문장 개수:** 테스트 세트에 사용된 병렬 문장의 개수입니다.

     **BLEU 점수:** 훈련 완료 후에 모델에 대해 생성된 BLEU 점수입니다.

    **상태:** 테스트 프로세스가 완료되었는지 아니면 진행 중인지 나타냅니다.

    ![시스템 테스트 결과](media/how-to/how-to-system-test-results.png)

2.  시스템 테스트 결과를 클릭하면 테스트 결과 세부 사항 페이지가 나타납니다. 이 페이지는 테스트 데이터 세트에 포함된 문장의 기계 번역을 보여 줍니다.

3.  테스트 결과 세부 사항 페이지의 표에는 언어 쌍의 각 언어에 대한 열 2개가 있습니다. 원본 언어 열에는 번역할 문장이 표시됩니다. 대상 언어 열에는 행 하나에 문장 2개가 포함되어 있습니다.

    **참조:** 이 문장은 테스트 데이터 세트에 포함된 원본 문장에 대한 참조 번역입니다.

    **MT:** 이 문장은 훈련이 실시된 후에 모델에 의해 수행된 원본 문장에 대한 자동 번역입니다.

    ![시스템 테스트 결과 비교](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>테스트 다운로드

번역 다운로드 링크를 클릭하여 zip 파일을 다운로드합니다. zip 파일에는 테스트 데이터 세트에 포함된 원본 문장의 기계 번역이 들어 있습니다.

![테스트 다운로드](media/how-to/how-to-system-test-download.png)

다운로드한 zip 아카이브에는 파일 3개가 들어 있습니다.

1.  **custom.mt.txt:** 이 파일에는 사용자 데이터를 사용 하 여 학습 한 모델에서 수행 하는 대상 언어로 원본 언어 문장의 기계 번역이 포함 됩니다.

2.  **ref.txt:** 이 파일에는 대상 언어로 된 소스 언어 문장의 번역이 사용자에 게 포함 되어 있습니다.

3.  **source.txt:** 이 파일은 소스 언어의 문장을 포함 합니다.

    ![다운로드한 시스템 테스트 결과](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>모델 배포

배포를 요청하려면 다음을 수행합니다.

1.  프로젝트를 선택하고 [모델] 탭으로 이동합니다.

2. 성공적으로 훈련된 모델에는 (아직 배포되지 않은 경우) “배포” 단추가 표시됩니다.

    ![모델 배포](media/how-to/how-to-deploy-model.png)

3.  [배포]를 클릭합니다.
4.  모델을 배포할 지역에 대해 **배포됨**을 선택하고 [저장]을 클릭합니다. 여러 개의 지역에 대해 **배포됨**을 선택할 수 있습니다.

    ![모델 배포](media/how-to/how-to-deploy-model-regions.png)

5.  “상태” 열에서 모델의 상태를 확인할 수 있습니다.

>[!Note]
>사용자 지정 변환기는 언제 든 지 작업 영역 내에서 10 개의 배포 된 모델을 지원 합니다.

## <a name="update-deployment-settings"></a>배포 설정 업데이트

배포 설정을 업데이트하려면

1.  프로젝트를 선택하고 **모델** 탭으로 이동합니다.

2. 성공적으로 배포된 모델에는 **업데이트** 단추가 표시됩니다.

    ![모델 배포](media/how-to/how-to-update-undeploy-model.png)

3.  **업데이트**를 선택합니다.
4.  모델을 배포 또는 배포 취소할 지역에 대해 **배포됨** 또는 **배포 취소됨**을 선택하고 **저장**을 클릭합니다.

    ![모델 배포](media/how-to/how-to-undeploy-model.png)

>[!Note]
>모든 지역에 대해 **배포 취소됨**을 선택하는 경우 모든 지역에서 모델이 배포 취소되고 배포 취소됨 상태로 전환됩니다. 이제 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 배포된 사용자 지정 번역 모델을 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)를 통해 사용합니다.
- [설정을 관리](how-to-manage-settings.md)하여 작업 영역을 공유하고 구독 키를 관리하는 방법을 알아봅니다.
- [Microsoft Translator Hub](https://hub.microsofttranslator.com)에서 [작업 영역과 프로젝트를 마이그레이션하는 방법](how-to-migrate.md)을 알아봅니다.
