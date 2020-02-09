---
title: 긴 오디오 API (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 긴 오디오 API가 긴 형식의 텍스트를 음성으로 통합 하기 위해 디자인 되는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900255"
---
# <a name="long-audio-api-preview"></a>긴 오디오 API (미리 보기)

긴 오디오 API는 긴 형식 텍스트를 음성으로 통합 (예: 오디오 서적) 하도록 설계 되었습니다. 이 API는 합성 오디오를 실시간으로 반환 하지 않습니다. 대신 응답을 폴링하고 서비스에서 제공 되는 출력을 사용 하는 것이 예상 됩니다. 음성 SDK에서 사용 하는 텍스트-음성 API와 달리 긴 오디오 API는 10 분 이상 합성 오디오를 만들 수 있으므로 게시자 및 오디오 콘텐츠 플랫폼에 이상적입니다.

긴 오디오 API의 추가 이점은 다음과 같습니다.

* 서비스에서 반환 되는 합성 음성은 고화질 오디오 출력을 보장 하는 신경망을 사용 합니다.
* 실시간 응답은 지원 되지 않으므로 음성 엔드포인트을 배포할 필요가 없습니다.

> [!NOTE]
> 긴 오디오 API는 이제 [사용자 지정 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)만 지원 합니다.

## <a name="workflow"></a>워크플로

일반적으로 긴 오디오 API를 사용 하는 경우 합성 될 텍스트 파일을 제출 하 고, 상태를 폴링하고, 상태가 성공 인 경우 오디오 출력을 다운로드할 수 있습니다.

이 다이어그램에서는 워크플로의 개략적인 개요를 제공 합니다.

![긴 오디오 API 워크플로 다이어그램](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>합성을 위한 콘텐츠 준비

텍스트 파일을 준비 하는 경우 다음을 확인 합니다.

* 일반 텍스트 (.txt) 또는 SSML 텍스트 (.txt) 중 하나입니다.
* 는 [BOM (바이트 순서 표시)을 사용 하 여 u t f-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) 로 인코딩됩니다.
* 는 zip이 아니라 단일 파일입니다.
* 일반 텍스트의 경우 400 자 보다 크거나, SSML 텍스트에 대 한 400 [청구 가능 문자](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) 를 포함 하며, 1만 단락 보다 작음
  * 일반 텍스트의 경우 각 단락은 **Enter/Return** -View [일반 텍스트 입력 예제](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) 에 따라 구분 됩니다.
  * SSML 텍스트의 경우 각 SSML 조각이 단락으로 간주 됩니다. [Ssml 부분은](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) 다른 단락으로 구분 해야 합니다.
> [!NOTE]
> 중국어 (중국), 중국어 (홍콩), 중국어 (대만), 일본어 및 한국어의 경우 한 단어는 두 문자로 계산 됩니다. 

## <a name="submit-synthesis-requests"></a>합성 요청 제출

입력 콘텐츠를 준비한 후에는 [긴 형식의 오디오 합성 퀵 스타트](https://aka.ms/long-audio-python) 를 따라 요청을 제출 합니다. 둘 이상의 입력 파일이 있는 경우 여러 요청을 제출 해야 합니다. 유의 해야 할 몇 가지 제한 사항이 있습니다. 
* 클라이언트는 각 Azure 구독 계정에 대해 초당 최대 5 개의 요청을 서버에 제출할 수 있습니다. 이 제한이 초과 되 면 클라이언트는 429 오류 코드 (너무 많은 요청)를 가져옵니다. 초당 요청 크기를 줄이십시오.
* 서버를 실행 하 고 각 Azure 구독 계정에 대해 최대 120 요청을 큐에 대기 시킬 수 있습니다. 이 제한이 초과 되 면 서버는 429 오류 코드 (너무 많은 요청)를 반환 합니다. 잠시 후 요청이 완료 될 때까지 새 요청을 제출 하지 마세요.
* 서버는 각 Azure 구독 계정에 대해 최대 2만 개의 요청을 유지 합니다. 제한을 초과 하는 경우 새 요청을 제출 하기 전에 일부 요청을 삭제 하세요.

## <a name="audio-output-formats"></a>오디오 출력 형식

유연한 오디오 출력 형식을 지원 합니다. 단락 당 오디오 출력을 생성 하거나 ' concatenateResult ' 매개 변수를 설정 하 여 오디오 출력을 하나의 출력에 연결할 수 있습니다. 긴 오디오 API에서 지원 되는 오디오 출력 형식은 다음과 같습니다.

> [!NOTE]
> 기본 오디오 형식은 riff-16khz-16 비트입니다.

* riff-8khz-16 비트
* riff-16khz-16 비트
* riff-24khz-16 비트
* riff-48khz-16 비트
* 오디오-16khz-32kbitrate 전송률-mono-mp3
* 오디오-16khz-64kbitrate 전송률-mono-mp3
* 오디오-16khz-128kbitrate 전송률-mono-mp3
* 오디오-24khz-48kbitrate 전송률-mono-mp3
* 오디오-24khz-96kbitrate 전송률-mono-mp3
* 오디오-24khz-160kbitrate 전송률-mono-mp3

## <a name="quickstarts"></a>퀵 스타트

긴 오디오 API를 성공적으로 실행 하는 데 도움이 되는 빠른 시작을 제공 합니다. 이 표에는 언어별 구성 된 긴 오디오 API 퀵 스타트 목록이 포함 되어 있습니다.

* [빠른 시작: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>샘플 코드
긴 오디오 API에 대 한 샘플 코드는 GitHub에서 사용할 수 있습니다.

* [샘플 코드: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [샘플 코드:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [샘플 코드: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
