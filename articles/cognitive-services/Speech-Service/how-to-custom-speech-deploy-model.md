---
title: Custom Speech 용 모델 배포-음성 서비스
titleSuffix: Azure Cognitive Services
description: 이 문서를 만들고 사용자 지정 음성 포털을 사용하는 엔드포인트을 배포하는 방법을 배웁니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806116"
---
# <a name="deploy-a-custom-model"></a>사용자 지정 모델 배포

데이터를 업로드하고 검사하고, 정확도를 평가하고, 사용자 지정 모델을 학습시킨 후에 앱, 도구 및 제품에 사용할 사용자 지정 엔드포인트을 배포할 수 있습니다. 이 문서에서는 [Custom Speech 포털](https://speech.microsoft.com/customspeech)을 사용 하 여 엔드포인트을 만들고 배포 하는 방법을 알아봅니다.

## <a name="create-a-custom-endpoint"></a>사용자 지정 엔드포인트 만들기

새 사용자 지정 엔드포인트을 만들려면 [Custom Speech 포털](https://speech.microsoft.com/customspeech) 에 로그인 하 고 페이지 맨 위에 있는 Custom Speech 메뉴에서 **배포** 를 선택 합니다. 첫 번째 실행의 경우 표에 나열된 엔드포인트이 없는 것을 확인할 수 있습니다. 엔드포인트을 만든 후에 배포된 각 엔드포인트을 추적하려면 이 페이지를 사용 합니다.

다음으로, **엔드포인트 추가**를 선택하고 사용자 지정 엔드포인트에 대한 **이름** 및 **설명**을 입력합니다. 그런 다음 이 엔드포인트과 연결하려는 사용자 지정 모델을 선택합니다. 이 페이지에서 로깅도 사용 설정할 수 있습니다. 로깅을 사용하면 엔드포인트의 트래픽을 모니터링할 수 있습니다. 사용하지 않으면 트래픽을 저장할 수 없습니다.

![모델을 배포 하는 방법](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 사용 약관 및 가격 정보에 동의 해야 합니다.

다음 **만들기**를 선택합니다. 이 작업은 **배포** 페이지로 돌아가게 합니다. 이제 테이블에는 사용자 지정 엔드포인트에 해당하는 항목이 포함됩니다. 엔드포인트의 상태는 현재 상태를 표시 합니다. 사용자 지정 모델을 사용하여 새 엔드포인트을 인스턴스화하는 데는 최대 30분이 걸릴 수 있습니다. 배포의 상태가 **완료**로 변경되면, 엔드포인트을 사용할 준비가 된 것입니다.

엔드포인트이 배포된 후, 엔드포인트 이름이 링크로 표시됩니다. 엔드포인트 키, 엔드포인트 URL 및 샘플 코드와 같은 엔드포인트에 관련된 정보를 표시하려면 링크를 클릭합니다.

## <a name="view-logging-data"></a>로깅 데이터 보기

로깅 데이터는 **엔드포인트 > 세부 정보** 아래에서 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK](speech-sdk.md)를 사용하여 사용자 지정 엔드포인트을 사용합니다.

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
