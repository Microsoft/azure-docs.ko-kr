---
title: Translator Speech API에서 Speech service로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Translator Speech API에서 음성 서비스로 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: ba9484bd5b04e5a79da53a0bb78877153be42a43
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805912"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Translator Speech API에서 Speech service로 마이그레이션

이 문서를 사용 하 여 Microsoft Translator Speech API에서 [음성 서비스로](index.md)응용 프로그램을 마이그레이션합니다. 이 가이드에서는 Translator Speech API와 음성 서비스 간의 차이점에 대해 간략하게 설명 하 고 응용 프로그램을 마이그레이션하기 위한 전략을 제안 합니다.

> [!NOTE]
> Translator Speech API 구독 키는 음성 서비스에서 허용 되지 않습니다. 새 음성 서비스 구독을 만들어야 합니다.

## <a name="comparison-of-features"></a>기능 비교

| 기능                                           | Translator Speech API                                  | Speech Service | 세부 정보                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 텍스트로 번역                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 음성으로 번역                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 전역 엔드포인트                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 음성 서비스는 글로벌 엔드포인트을 제공 하지 않습니다. 전역 엔드포인트는 트래픽을 가장 가까운 지역 엔드포인트로 자동으로 전송하여 애플리케이션의 대기 시간을 줄일 수 있습니다.                                                    |
| 지역별 엔드포인트                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 연결 시간 제한                             | 90분                                               | SDK를 사용할 경우 무제한 WebSocket 연결을 사용할 경우 10분                                                                                                                                                                                                                                                                                   |
| 헤더의 인증 키                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 단일 요청을 통해 여러 언어 번역 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK 사용 가능                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 사용 가능한 Sdk는 [음성 서비스 설명서](index.md) 를 참조 하세요.                                                                                                                                                    |
| WebSocket 연결                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Languages API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 음성 서비스는 [TRANSLATOR API 언어 참조](../translator-speech/languages-reference.md) 문서에 설명 된 것과 동일한 언어 범위를 지원 합니다. |
| 욕설 필터 및 표식                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM을 입력으로 사용                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 다른 파일 형식을 입력으로 사용                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 부분 결과                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 타이밍 정보                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 상관관계 ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Custom Speech 모델                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 음성 서비스는 조직의 고유한 어휘에 대 한 음성 인식을 사용자 지정할 수 있는 사용자 지정 음성 모델을 제공 합니다.                                                                                                                                           |
| 사용자 지정 번역 모델                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Microsoft Text Translation API를 구독하면 [Custom Translator](https://www.microsoft.com/translator/business/customization/)를 사용하여 보다 정확한 번역을 위해 자체 데이터를 사용할 수 있습니다.                                                 |

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직에 Translator Speech API를 사용 하는 개발 또는 프로덕션 환경에서 응용 프로그램이 있는 경우 음성 서비스를 사용 하도록 업데이트 해야 합니다. 사용 가능한 Sdk, 코드 샘플 및 자습서는 [음성 서비스](index.md) 설명서를 참조 하세요. 마이그레이션하는 경우 다음을 고려합니다.

* 음성 서비스는 글로벌 엔드포인트을 제공 하지 않습니다. 애플리케이션이 모든 해당 트래픽에 대해 단일 지역별 엔드포인트를 사용할 때 효율적으로 작동하는지를 확인합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다.

* 애플리케이션이 수명이 긴 연결을 사용하고 사용 가능한 SDK를 사용할 수 없는 경우 WebSocket 연결을 사용할 수 있습니다. 적절한 시간에 다시 연결하여 10분 시간 제한을 관리합니다.

* 응용 프로그램에서 Translator Text API 사용 하 Translator Speech API 고 사용자 지정 변환 모델을 사용 하도록 설정 하는 경우 Speech service를 사용 하 여 직접 범주 Id를 추가할 수 있습니다.

* Translator Speech API와 달리 음성 서비스는 단일 요청에서 여러 언어로 번역을 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [음성 서비스를 무료로 사용해 보세요.](get-started.md)
* [빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>참고 항목

* [Speech Service란?](overview.md)
* [Speech service 및 Speech SDK 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
