---
title: Text to Speech에 대한 FAQ(질문과 대답)
titleSuffix: Azure Cognitive Services
description: Text to Speech 서비스에 대해 자주 묻는 질문에 대 한 답변을 가져옵니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: f06fda777cbebd8034ac4fd9254a4bd172d011a4
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661506"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Text to Speech에 대한 FAQ(질문과 대답)

이 FAQ에서 질문에 대한 답변을 찾을 수 없는 경우 [다른 지원 옵션](support.md)을 확인하세요.

## <a name="general"></a>일반

**Q: 표준 음성 모델과 사용자 지정 음성 모델 간의 차이점은 무엇인가요?**

**A**: 표준 음성 모델(_음성 글꼴_이라고도 함)은 Microsoft 소유 데이터를 사용하여 학습되었으며 이미 클라우드에 배포되어 있습니다. 사용자 지정 음성 모델을 사용하여 평균 모델을 조정하고, 화자의 음성 스타일의 음색과 표현을 전송하거나 사용자가 준비한 학습 데이터를 기반으로 하여 완전히 새로운 모델을 학습할 수 있습니다. 오늘날, 점점 더 많은 고객이 자신들의 봇에 독특한 유명 상표의 음성을 원합니다. 사용자 지정 음성 빌드 플랫폼은 이러한 옵션에 적합합니다.

**Q: 표준 모델을 사용하려면 어디서 시작해야 하나요?**

**A**: HTTP 요청을 통해 45개 이상의 언어로 된 80개 이상의 표준 음성 모델을 사용할 수 있습니다. 먼저 [구독 키](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)를 가져옵니다. 미리 배포된 음성 모델을 REST를 통해 호출하려면 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)를 참조하세요.

**Q: 사용자 지정 음성 모델을 사용하려는 경우 API가 표준 음성에 사용되는 것과 동일한가요?**

**A**: 사용자 지정 음성 모델이 만들어지고 배포되면 모델에 대한 고유한 엔드포인트가 제공됩니다. 앱에서 음성을 사용하여 발화하도록 하려면 HTTP 요청에 이 엔드포인트를 지정해야 합니다. Text to Speech 서비스용 REST API에서 사용할 수 있는 것과 동일한 기능을 사용자 지정 엔드포인트에서 사용할 수 있습니다. [사용자 지정 엔드포인트를 만들고 사용하는](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint) 방법을 알아보세요.

**Q: 사용자 지정 음성 모델을 만들기 위한 학습 데이터를 직접 준비해야 하나요?**

**A**: 예, 사용자 지정 음성 모델에 대한 학습 데이터를 직접 준비해야 합니다.

사용자 지정된 음성 모델을 만들려면 음성 데이터 컬렉션이 필요합니다. 이 컬렉션은 음성 녹음의 오디오 파일 집합과 각 오디오 파일을 전사한 대한 텍스트 파일로 구성됩니다. 디지털 음성의 결과는 학습 데이터의 품질에 따라 크게 달라집니다. 좋은 텍스트 음성 변환 음성을 생성하려면 고품질의 고정식 마이크를 사용하여 조용한 공간에서 녹음해야 합니다. 일관된 음량, 말하기 속도, 말하기 높낮이, 그리고 일관된 음성 표현 습관조차도 훌륭한 디지털 음성을 작성하는 데 필수적입니다. 음성은 녹음실에서 녹음하는 것이 가장 좋습니다.

현재 온라인 녹음은 지원하지 않으며, 녹음실에 대한 권장 사항도 제공하지 않습니다. 형식 요구 사항은 [녹음 및 전사를 준비하는 방법](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice)을 참조하세요.

**Q: 사용자 지정 음성 학습에 사용할 음성 데이터를 녹음할 때 사용해야 하는 대본은 어떻게 되나요?**

**A**: 음성 녹음용 대본에는 제한이 없습니다. 직접 작성한 스크립트를 사용하여 음성을 녹음할 수 있습니다. 음성 데이터에 충분한 녹음 범위가 적용되고 있는지만 확인하면 됩니다. 사용자 지정 음성을 학습하는 경우 50개의 서로 다른 문장(약 3~5분의 음성)인 작은 양의 음성 데이터로 시작할 수 있습니다. 더 많은 데이터를 제공할수록 음성이 좀 더 자연스러워집니다. 2,000개가 넘는 문장(약 3~4시간의 음성)을 녹음하면 전체 음성 글꼴을 학습할 수 있습니다. 고품질의 전체 음성을 얻으려면 6,000개가 넘는 문장(약 8~10시간의 음성)을 녹음하도록 준비해야 합니다.

녹음 스크립트를 준비하는 데 도움이 되는 추가 서비스가 제공됩니다. 질문이 있으면 [Custom Voice 고객 지원 센터](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)에 문의하세요.

**Q: 기본값 또는 포털에서 제공되는 값보다 높은 동시성이 필요하면 어떻게 해야 하나요?**

**A**: 모델은 20개씩 증분하는 동시 요청으로 강화할 수 있습니다. 더 큰 확장에 대해 문의하려면 [사용자 지정 음성 고객 지원팀](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)에 문의하세요.

**Q: 내 모델을 다운로드하여 로컬로 실행할 수 있나요?**

**A**: 모델을 다운로드하여 로컬로 실행할 수 없습니다.

**Q: 내 요청이 제한되나요?**

**A**: [Speech Services 할당량 및 제한을](speech-services-quotas-and-limits.md)참조 하세요.

## <a name="next-steps"></a>다음 단계

- [문제 해결](troubleshooting.md)
- [릴리스 정보](releasenotes.md)
