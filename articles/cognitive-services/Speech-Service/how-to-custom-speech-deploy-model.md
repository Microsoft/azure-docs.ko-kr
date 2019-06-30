---
title: 사용자 지정 음성-음성 서비스에 대한 모델을 배포합니다.
titlesuffix: Azure Cognitive Services
description: 이 문서를 만들고 사용자 지정 음성 포털을 사용하는 끝점을 배포하는 방법을 배웁니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026721"
---
# <a name="deploy-a-custom-model"></a>사용자 지정 모델 배포

업로드 하 고 데이터를 검사, 정확도 평가 하 고 사용자 지정 모델을 학습, 후에 앱, 도구 및 제품을 사용 하는 사용자 지정 끝점을 배포할 수 있습니다. 이 문서를 만들고 사용자 지정 음성 포털을 사용하는 끝점을 배포하는 방법을 배웁니다.

## <a name="create-a-custom-endpoint"></a>사용자 지정 끝점 만들기

새 사용자 지정 끝점을 만들려면, 페이지의 맨 위에 있는 사용자 지정 음성 메뉴에서 **배포**를 선택합니다. 첫 번째 실행의 경우 표에 나열된 끝점이 없는 것을 확인할 수 있습니다. 끝점을 만든 후에 배포된 각 끝점을 추적하려면 이 페이지를 사용 합니다.

다음으로, **끝점 추가**를 선택하고 사용자 지정 끝점에 대한 **이름** 및 **설명**을 입력합니다. 그런 다음 이 끝점과 연결하려는 사용자 지정 모델을 선택합니다. 이 페이지에서 로깅도 사용 설정할 수 있습니다. 로깅을 사용하면 끝점의 트래픽을 모니터링할 수 있습니다. 사용하지 않으면 트래픽을 저장할 수 없습니다.

![모델을 배포 하는 방법](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 가격 책정 세부 정보 및 사용 약관에 동의 해야 합니다.

다음 **만들기**를 선택합니다. 이 작업은 **배포** 페이지로 돌아가게 합니다. 이제 테이블에는 사용자 지정 끝점에 해당하는 항목이 포함됩니다. 끝점의 상태는 현재 상태를 표시합니다. 사용자 지정 모델을 사용하여 새 끝점을 인스턴스화하는 데는 최대 30분이 걸릴 수 있습니다. 배포의 상태가 **완료**로 변경되면, 끝점을 사용할 준비가 된 것입니다.

끝점이 배포된 후, 끝점 이름이 링크로 표시됩니다. 끝점 키, 끝점 URL 및 샘플 코드와 같은 끝점에 관련된 정보를 표시하려면 링크를 클릭합니다.

## <a name="view-logging-data"></a>로깅 데이터 보기

로깅 데이터는 아래에서 다운로드할 수 있습니다 **끝점 > 세부 정보**합니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK](speech-sdk.md)를 사용하여 사용자 지정 끝점을 사용합니다.

## <a name="additional-resources"></a>추가 리소스

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
