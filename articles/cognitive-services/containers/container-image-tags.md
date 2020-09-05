---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 인식 서비스 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2a57c5244630c2328320d7d99eed98c892d49897
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230758"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services 컨테이너 이미지 태그

Azure Cognitive Services는 많은 컨테이너 이미지를 제공 합니다. 컨테이너 레지스트리 및 해당 리포지토리는 컨테이너 이미지 마다 다릅니다. 각 컨테이너 이미지 이름은 여러 태그를 제공 합니다. 컨테이너 이미지 태그는 컨테이너 이미지의 버전을 관리 하는 메커니즘입니다. 이 문서는 모든 Cognitive Services 컨테이너 이미지와 사용 가능한 태그를 나열 하는 포괄적인 참조로 사용 하기 위한 것입니다.

> [!TIP]
> 를 사용 하는 경우 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) **대/소문자를 구분**하므로 컨테이너 레지스트리, 리포지토리, 컨테이너 이미지 이름 및 해당 태그의 대/소문자 구분에 주의 해야 합니다.

## <a name="anomaly-detector"></a>Anomaly Detector

[변칙 탐지기][ad-containers] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `anomaly-detector` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision][cv-containers] 읽기 OCR 컨테이너 이미지는 컨테이너 레지스트리에서 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-read` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-read` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • 컨테이너의 메모리 사용량을 더 줄입니다. |
|                                          | • 다중 pod 설치에는 외부 캐시가 필요 합니다. 예를 들어 캐싱에 Redis을 설정 합니다. |
|                                          | • Redis cache가 설정 되 고 ResultExpirationPeriod = 0 인 경우 결과 누락 문제가 해결 됩니다.  |
|                                          | • 요청 본문 크기 제한 제거 26MB 이제 컨테이너는 26MB 파일 >수용할 수 있습니다.  |
|                                          | • 타임 스탬프 및 빌드 버전을 콘솔 로깅에 추가 합니다.  |
| `1.1.013050001-amd64-preview`            | * ReadEngineConfig: ResultExpirationPeriod container 초기화 구성을 추가 하 여 시스템이 인식 결과를 정리 해야 하는 시기를 지정 합니다. |
|                                          | 설정은 시간 단위 이며 기본값은 48hr입니다.   |
|                                          |   이 설정은 특히 컨테이너 메모리 내 저장소를 사용 하는 경우 결과를 저장 하는 메모리 사용량을 줄일 수 있습니다.  |
|                                          |    * 예 1. ReadEngineConfig: ResultExpirationPeriod = 1 인 경우 시스템은 프로세스 후에 인식 결과 1 시간을 지웁니다.   |
|                                          |    * 예 2. ReadEngineConfig: ResultExpirationPeriod = 0 인 경우 시스템은 결과 검색 후 인식 결과를 지웁니다.  |
|                                          | 잘못 된 이미지 형식이 시스템에 전달 되는 경우 500 내부 서버 오류가 수정 되었습니다. 이제 400 오류가 반환 됩니다.   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

[Face][fa-containers] 컨테이너 이미지는 컨테이너 레지스트리에서 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-face` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-face` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

[양식 인식기][fr-containers] 컨테이너 이미지는 컨테이너 레지스트리에서 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-form-recognizer` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>언어 이해(LUIS)

[LUIS][lu-containers] 컨테이너 이미지는 컨테이너 레지스트리 게시에서 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `luis` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/luis` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Custom Speech 텍스트

컨테이너 레지스트리에서 [Custom Speech 텍스트][sp-cstt] 컨테이너 이미지를 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-custom-speech-to-text` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그            | 참고 |
|-----------------------|:------|
| `latest`              |       |
| `2.3.1-amd64-preview` |       | 
| `2.3.0-amd64-preview` |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 컨테이너 레지스트리에서 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-custom-text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그            | 참고 |
|-----------------------|:------|
| `latest`              |       |
| `1.6.0-amd64-preview` |       |
| `1.5.0-amd64-preview` |       |
| `1.4.0-amd64-preview` |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>음성 텍스트 변환

컨테이너 레지스트리에서 [음성-텍스트][sp-stt] 컨테이너 이미지를 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-speech-to-text` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` 입니다.
*미국 정부 버지니아* 버전의 음성 텍스트 이미지는 v 2.4.0에서 지원 되며에서 찾을 수 있습니다 `containerpreview.azurecr.io/microsoft/fairfax/cognitive-services-speech-to-text` .

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                  | 참고                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.4.0-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.4.0-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.4.0-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.4.0-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.4.0-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.4.0-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.4.0-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.4.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.4.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.4.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.4.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.4.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.4.0-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.4.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.4.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.4.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.4.0-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.4.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.4.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.4.0-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.4.0-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.4.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.4.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.4.0-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.4.0-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.4.0-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.4.0-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.4.0-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.4.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.4.0-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.4.0-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.4.0-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.4.0-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.4.0-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.4.0-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.4.0-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.4.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.4.0-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.4.0-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.3.1-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.3.1-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.3.1-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.3.1-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.3.1-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.3.1-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.3.1-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.3.1-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.3.1-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.3.1-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.3.1-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.3.1-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.3.1-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.3.1-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.3.1-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.3.1-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.3.1-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.3.1-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.3.1-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.3.1-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.3.1-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.3.1-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.3.1-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.3.1-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.3.1-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.3.1-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.3.1-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.3.1-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.3.1-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.3.1-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.3.1-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.3.1-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.3.1-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.3.1-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.3.1-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.3.1-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.3.1-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.3.1-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.3.1-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.3.0-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.3.0-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.3.0-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.3.0-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.3.0-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.3.0-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.3.0-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.3.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.3.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.3.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.3.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.3.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.3.0-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.3.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.3.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.3.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.3.0-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.3.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.3.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.3.0-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.3.0-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.3.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.3.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.3.0-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.3.0-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.3.0-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.3.0-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.3.0-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.3.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.3.0-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.3.0-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.3.0-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.3.0-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.3.0-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.3.0-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.3.0-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.3.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.3.0-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.3.0-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.2.0-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.2.0-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.2.0-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.2.0-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.2.0-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.2.0-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.2.0-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.2.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.2.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.2.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.2.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.2.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.2.0-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.2.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.2.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.2.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.2.0-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.2.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.2.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.2.0-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.2.0-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.2.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.2.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.2.0-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.2.0-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.2.0-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.2.0-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.2.0-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.2.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.2.0-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.2.0-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.2.0-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.2.0-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.2.0-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.2.0-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.2.0-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.2.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.2.0-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.2.0-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.1.1-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.1.1-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.1.1-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.1.1-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.1.1-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.1.1-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.1.1-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.1.1-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.1.1-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.1.1-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.1.1-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.1.1-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.1.1-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.1.1-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.1.1-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.1.1-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.1.1-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.1.1-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.1.1-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.1.1-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.1.1-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.1.1-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.1.1-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.1.1-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.1.1-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.1.1-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.1.1-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.1.1-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.1.1-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.1.1-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.1.1-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.1.1-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.1.1-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.1.1-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.1.1-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.1.1-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.1.1-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.1.1-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.1.1-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.1.1-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.1.0-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.1.0-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.1.0-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.1.0-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.1.0-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.1.0-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.1.0-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.1.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.1.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.1.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.1.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.1.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.1.0-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.1.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.1.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.1.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.1.0-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.1.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.1.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.1.0-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.1.0-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.1.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.1.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.1.0-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.1.0-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.1.0-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.1.0-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.1.0-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.1.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.1.0-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.1.0-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.1.0-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.1.0-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.1.0-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.1.0-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.1.0-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.1.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.1.0-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.1.0-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.0.3-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.0.2-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.0.2-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.0.2-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.0.2-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.0.2-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.0.2-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.0.2-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.0.2-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.0.2-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.0.2-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.0.2-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.0.2-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.0.2-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.0.2-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.0.2-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.0.2-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.0.2-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.0.2-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.0.2-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.0.2-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.0.2-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.0.2-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.0.2-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.0.2-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.0.2-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.0.2-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.0.2-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.0.2-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.0.2-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.0.2-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.0.2-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.0.2-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.0.2-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.0.2-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.0.2-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.0.2-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.0.2-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.0.2-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.0.2-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.0.1-amd64-ar-ae-preview` | 로캘이 있는 컨테이너 이미지 `ar-AE` 입니다. |
| `2.0.1-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.0.1-amd64-ar-kw-preview` | 로캘이 있는 컨테이너 이미지 `ar-KW` 입니다. |
| `2.0.1-amd64-ar-qa-preview` | 로캘이 있는 컨테이너 이미지 `ar-QA` 입니다. |
| `2.0.1-amd64-ar-sa-preview` | 로캘이 있는 컨테이너 이미지 `ar-SA` 입니다. |
| `2.0.1-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.0.1-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.0.1-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.0.1-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.0.1-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.0.1-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.0.1-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.0.1-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.0.1-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.0.1-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.0.1-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.0.1-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.0.1-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.0.1-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.0.1-amd64-gu-in-preview` | 로캘이 있는 컨테이너 이미지 `gu-IN` 입니다. |
| `2.0.1-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.0.1-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.0.1-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.0.1-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.0.1-amd64-mr-in-preview` | 로캘이 있는 컨테이너 이미지 `mr-IN` 입니다. |
| `2.0.1-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.0.1-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.0.1-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.0.1-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.0.1-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.0.1-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.0.1-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.0.1-amd64-ta-in-preview` | 로캘이 있는 컨테이너 이미지 `ta-IN` 입니다. |
| `2.0.1-amd64-te-in-preview` | 로캘이 있는 컨테이너 이미지 `te-IN` 입니다. |
| `2.0.1-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.0.1-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.0.1-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.0.1-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.0.1-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `2.0.0-amd64-ar-eg-preview` | 로캘이 있는 컨테이너 이미지 `ar-EG` 입니다. |
| `2.0.0-amd64-ca-es-preview` | 로캘이 있는 컨테이너 이미지 `ca-ES` 입니다. |
| `2.0.0-amd64-da-dk-preview` | 로캘이 있는 컨테이너 이미지 `da-DK` 입니다. |
| `2.0.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `2.0.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `2.0.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `2.0.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `2.0.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `2.0.0-amd64-en-nz-preview` | 로캘이 있는 컨테이너 이미지 `en-NZ` 입니다. |
| `2.0.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `2.0.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `2.0.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `2.0.0-amd64-fi-fi-preview` | 로캘이 있는 컨테이너 이미지 `fi-FI` 입니다. |
| `2.0.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `2.0.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `2.0.0-amd64-hi-in-preview` | 로캘이 있는 컨테이너 이미지 `hi-IN` 입니다. |
| `2.0.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `2.0.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `2.0.0-amd64-ko-kr-preview` | 로캘이 있는 컨테이너 이미지 `ko-KR` 입니다. |
| `2.0.0-amd64-nb-no-preview` | 로캘이 있는 컨테이너 이미지 `nb-NO` 입니다. |
| `2.0.0-amd64-nl-nl-preview` | 로캘이 있는 컨테이너 이미지 `nl-NL` 입니다. |
| `2.0.0-amd64-pl-pl-preview` | 로캘이 있는 컨테이너 이미지 `pl-PL` 입니다. |
| `2.0.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `2.0.0-amd64-pt-pt-preview` | 로캘이 있는 컨테이너 이미지 `pt-PT` 입니다. |
| `2.0.0-amd64-ru-ru-preview` | 로캘이 있는 컨테이너 이미지 `ru-RU` 입니다. |
| `2.0.0-amd64-sv-se-preview` | 로캘이 있는 컨테이너 이미지 `sv-SE` 입니다. |
| `2.0.0-amd64-th-th-preview` | 로캘이 있는 컨테이너 이미지 `th-TH` 입니다. |
| `2.0.0-amd64-tr-tr-preview` | 로캘이 있는 컨테이너 이미지 `tr-TR` 입니다. |
| `2.0.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `2.0.0-amd64-zh-hk-preview` | 로캘이 있는 컨테이너 이미지 `zh-HK` 입니다. |
| `2.0.0-amd64-zh-tw-preview` | 로캘이 있는 컨테이너 이미지 `zh-TW` 입니다. |
| `1.2.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.2.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.2.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.2.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.2.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.2.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.2.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.2.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.2.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.2.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.2.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.2.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.2.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.2.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `1.1.3-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.1.3-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.1.3-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.1.3-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.1.3-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.1.3-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.1.3-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.1.3-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.1.3-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.1.3-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.1.3-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.1.3-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.1.3-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.1.3-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `1.1.2-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.1.2-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.1.2-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.1.2-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.1.2-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.1.2-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.1.2-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.1.2-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.1.2-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.1.2-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.1.2-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.1.2-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.1.2-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.1.2-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `1.1.1-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.1.1-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.1.1-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.1.1-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.1.1-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.1.1-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.1.1-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.1.1-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.1.1-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.1.1-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.1.1-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.1.1-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.1.1-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.1.1-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `1.1.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.1.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.1.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.1.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.1.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.1.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.1.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.1.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.1.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.1.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.1.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.1.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.1.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.1.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |
| `1.0.0-amd64-de-de-preview` | 로캘이 있는 컨테이너 이미지 `de-DE` 입니다. |
| `1.0.0-amd64-en-au-preview` | 로캘이 있는 컨테이너 이미지 `en-AU` 입니다. |
| `1.0.0-amd64-en-ca-preview` | 로캘이 있는 컨테이너 이미지 `en-CA` 입니다. |
| `1.0.0-amd64-en-gb-preview` | 로캘이 있는 컨테이너 이미지 `en-GB` 입니다. |
| `1.0.0-amd64-en-in-preview` | 로캘이 있는 컨테이너 이미지 `en-IN` 입니다. |
| `1.0.0-amd64-en-us-preview` | 로캘이 있는 컨테이너 이미지 `en-US` 입니다. |
| `1.0.0-amd64-es-es-preview` | 로캘이 있는 컨테이너 이미지 `es-ES` 입니다. |
| `1.0.0-amd64-es-mx-preview` | 로캘이 있는 컨테이너 이미지 `es-MX` 입니다. |
| `1.0.0-amd64-fr-ca-preview` | 로캘이 있는 컨테이너 이미지 `fr-CA` 입니다. |
| `1.0.0-amd64-fr-fr-preview` | 로캘이 있는 컨테이너 이미지 `fr-FR` 입니다. |
| `1.0.0-amd64-it-it-preview` | 로캘이 있는 컨테이너 이미지 `it-IT` 입니다. |
| `1.0.0-amd64-ja-jp-preview` | 로캘이 있는 컨테이너 이미지 `ja-JP` 입니다. |
| `1.0.0-amd64-pt-br-preview` | 로캘이 있는 컨테이너 이미지 `pt-BR` 입니다. |
| `1.0.0-amd64-zh-cn-preview` | 로캘이 있는 컨테이너 이미지 `zh-CN` 입니다. |

## <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환][sp-tts] 컨테이너 이미지는 컨테이너 레지스트리에서 찾을 수 있습니다 `containerpreview.azurecr.io` . 리포지토리 내에 상주 `microsoft` 하 고 이름이로 지정 됩니다 `cognitive-services-text-to-speech` . 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                                  | 참고                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.         |
| `1.6.0-amd64-ar-eg-hoda-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `1.6.0-amd64-ar-sa-naayf-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `1.6.0-amd64-bg-bg-ivan-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `1.6.0-amd64-ca-es-herenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `1.6.0-amd64-cs-cz-jakub-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `1.6.0-amd64-da-dk-hellerus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `1.6.0-amd64-de-at-michael-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `1.6.0-amd64-de-ch-karsten-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `1.6.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.6.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.6.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.6.0-amd64-el-gr-stefanos-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `1.6.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.6.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.6.0-amd64-en-ca-heatherrus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `1.6.0-amd64-en-ca-linda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `1.6.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.6.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.6.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.6.0-amd64-en-ie-sean-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `1.6.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.6.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.6.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.6.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.6.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.6.0-amd64-en-us-aria24krus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.     |
| `1.6.0-amd64-en-us-ariarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.        |
| `1.6.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.6.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.6.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.6.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.6.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.6.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.6.0-amd64-fi-fi-heidirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `1.6.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.6.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.6.0-amd64-fr-ch-guillaume-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `1.6.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.6.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.6.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.6.0-amd64-he-il-asaf-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `1.6.0-amd64-hi-in-hemant-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `1.6.0-amd64-hi-in-kalpana-apollo-preview`  | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  |
| `1.6.0-amd64-hi-in-kalpana-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `1.6.0-amd64-hr-hr-matej-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `1.6.0-amd64-hu-hu-szabolcs-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `1.6.0-amd64-id-id-andika-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `1.6.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.6.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.6.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.6.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.6.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.6.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.6.0-amd64-ms-my-rizwan-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `1.6.0-amd64-nb-no-huldarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `1.6.0-amd64-nl-nl-hannarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `1.6.0-amd64-pl-pl-paulinarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `1.6.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.6.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.6.0-amd64-pt-pt-heliarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `1.6.0-amd64-ro-ro-andrei-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `1.6.0-amd64-ru-ru-ekaterinarus-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `1.6.0-amd64-ru-ru-irina-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `1.6.0-amd64-ru-ru-pavel-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `1.6.0-amd64-sk-sk-filip-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `1.6.0-amd64-sl-si-lado-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `1.6.0-amd64-sv-se-hedvigrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `1.6.0-amd64-ta-in-valluvar-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `1.6.0-amd64-te-in-chitra-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `1.6.0-amd64-th-th-pattara-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `1.6.0-amd64-tr-tr-sedarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `1.6.0-amd64-vi-vn-an-preview`              | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `1.6.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.6.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.6.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.6.0-amd64-zh-hk-danny-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `1.6.0-amd64-zh-hk-tracy-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `1.6.0-amd64-zh-hk-tracyrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `1.6.0-amd64-zh-tw-hanhanrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `1.6.0-amd64-zh-tw-yating-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `1.6.0-amd64-zh-tw-zhiwei-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |
| `1.5.0-amd64-ar-eg-hoda-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `1.5.0-amd64-ar-sa-naayf-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `1.5.0-amd64-bg-bg-ivan-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `1.5.0-amd64-ca-es-herenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `1.5.0-amd64-cs-cz-jakub-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `1.5.0-amd64-da-dk-hellerus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `1.5.0-amd64-de-at-michael-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `1.5.0-amd64-de-ch-karsten-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `1.5.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.5.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.5.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.5.0-amd64-el-gr-stefanos-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `1.5.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.5.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.5.0-amd64-en-ca-heatherrus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `1.5.0-amd64-en-ca-linda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `1.5.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.5.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.5.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.5.0-amd64-en-ie-sean-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `1.5.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.5.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.5.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.5.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.5.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.5.0-amd64-en-us-aria24krus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.     |
| `1.5.0-amd64-en-us-ariarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.        |
| `1.5.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.5.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.5.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.5.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.5.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.5.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.5.0-amd64-fi-fi-heidirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `1.5.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.5.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.5.0-amd64-fr-ch-guillaume-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `1.5.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.5.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.5.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.5.0-amd64-he-il-asaf-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `1.5.0-amd64-hi-in-hemant-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `1.5.0-amd64-hi-in-kalpana-apollo-preview`  | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  |
| `1.5.0-amd64-hi-in-kalpana-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `1.5.0-amd64-hr-hr-matej-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `1.5.0-amd64-hu-hu-szabolcs-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `1.5.0-amd64-id-id-andika-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `1.5.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.5.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.5.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.5.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.5.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.5.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.5.0-amd64-ms-my-rizwan-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `1.5.0-amd64-nb-no-huldarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `1.5.0-amd64-nl-nl-hannarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `1.5.0-amd64-pl-pl-paulinarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `1.5.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.5.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.5.0-amd64-pt-pt-heliarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `1.5.0-amd64-ro-ro-andrei-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `1.5.0-amd64-ru-ru-ekaterinarus-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `1.5.0-amd64-ru-ru-irina-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `1.5.0-amd64-ru-ru-pavel-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `1.5.0-amd64-sk-sk-filip-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `1.5.0-amd64-sl-si-lado-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `1.5.0-amd64-sv-se-hedvigrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `1.5.0-amd64-ta-in-valluvar-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `1.5.0-amd64-te-in-chitra-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `1.5.0-amd64-th-th-pattara-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `1.5.0-amd64-tr-tr-sedarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `1.5.0-amd64-vi-vn-an-preview`              | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `1.5.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.5.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.5.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.5.0-amd64-zh-hk-danny-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `1.5.0-amd64-zh-hk-tracy-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `1.5.0-amd64-zh-hk-tracyrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `1.5.0-amd64-zh-tw-hanhanrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `1.5.0-amd64-zh-tw-yating-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `1.5.0-amd64-zh-tw-zhiwei-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |
| `1.4.0-amd64-ar-eg-hoda-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `1.4.0-amd64-ar-sa-naayf-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `1.4.0-amd64-bg-bg-ivan-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `1.4.0-amd64-ca-es-herenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `1.4.0-amd64-cs-cz-jakub-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `1.4.0-amd64-da-dk-hellerus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `1.4.0-amd64-de-at-michael-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `1.4.0-amd64-de-ch-karsten-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `1.4.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.4.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.4.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.4.0-amd64-el-gr-stefanos-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `1.4.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.4.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.4.0-amd64-en-ca-heatherrus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `1.4.0-amd64-en-ca-linda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `1.4.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.4.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.4.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.4.0-amd64-en-ie-sean-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `1.4.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.4.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.4.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.4.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.4.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.4.0-amd64-en-us-aria24krus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Aria24kRUS` 입니다.     |
| `1.4.0-amd64-en-us-ariarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-AriaRUS` 입니다.        |
| `1.4.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.4.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.4.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.4.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.4.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.4.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.4.0-amd64-fi-fi-heidirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `1.4.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.4.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.4.0-amd64-fr-ch-guillaume-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `1.4.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.4.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.4.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.4.0-amd64-he-il-asaf-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `1.4.0-amd64-hi-in-hemant-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `1.4.0-amd64-hi-in-kalpana-apollo-preview`  | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana-Apollo` 입니다.  |
| `1.4.0-amd64-hi-in-kalpana-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `1.4.0-amd64-hr-hr-matej-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `1.4.0-amd64-hu-hu-szabolcs-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `1.4.0-amd64-id-id-andika-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `1.4.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.4.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.4.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.4.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.4.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.4.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.4.0-amd64-ms-my-rizwan-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `1.4.0-amd64-nb-no-huldarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `1.4.0-amd64-nl-nl-hannarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `1.4.0-amd64-pl-pl-paulinarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `1.4.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.4.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.4.0-amd64-pt-pt-heliarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `1.4.0-amd64-ro-ro-andrei-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `1.4.0-amd64-ru-ru-ekaterinarus-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `1.4.0-amd64-ru-ru-irina-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `1.4.0-amd64-ru-ru-pavel-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `1.4.0-amd64-sk-sk-filip-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `1.4.0-amd64-sl-si-lado-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `1.4.0-amd64-sv-se-hedvigrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `1.4.0-amd64-ta-in-valluvar-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `1.4.0-amd64-te-in-chitra-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `1.4.0-amd64-th-th-pattara-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `1.4.0-amd64-tr-tr-sedarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `1.4.0-amd64-vi-vn-an-preview`              | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `1.4.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.4.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.4.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.4.0-amd64-zh-hk-danny-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `1.4.0-amd64-zh-hk-tracy-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `1.4.0-amd64-zh-hk-tracyrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `1.4.0-amd64-zh-tw-hanhanrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `1.4.0-amd64-zh-tw-yating-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `1.4.0-amd64-zh-tw-zhiwei-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-EG` `ar-EG-Hoda` 입니다.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `ar-SA` `ar-SA-Naayf` 입니다.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `bg-BG` `bg-BG-Ivan` 입니다.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ca-ES` `ca-ES-HerenaRUS` 입니다.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `cs-CZ` `cs-CZ-Jakub` 입니다.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `da-DK` `da-DK-HelleRUS` 입니다.        |
| `1.3.0-amd64-de-at-michael-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-AT` `de-AT-Michael` 입니다.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `de-CH` `de-CH-Karsten` 입니다.         |
| `1.3.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-HeddaRUS` 입니다.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `el-GR` `el-GR-Stefanos` 입니다.        |
| `1.3.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-HeatherRUS` 입니다.      |
| `1.3.0-amd64-en-ca-linda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `en-CA` `en-CA-Linda` 입니다.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.3.0-amd64-en-ie-sean-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IE` `en-IE-Sean` 입니다.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Jessa24kRUS` 입니다.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-JessaRUS` 입니다.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fi-FI` `fi-FI-HeidiRUS` 입니다.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CH` `fr-CH-Guillaume` 입니다.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.3.0-amd64-he-il-asaf-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `he-IL` `he-IL-Asaf` 입니다.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Hemant` 입니다.          |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `hi-IN` `hi-IN-Kalpana` 입니다.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `hr-HR` `hr-HR-Matej` 입니다.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `hu-HU` `hu-HU-Szabolcs` 입니다.        |
| `1.3.0-amd64-id-id-andika-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `id-ID` `id-ID-Andika` 입니다.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ms-MY` `ms-MY-Rizwan` 입니다.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nb-NO` `nb-NO-HuldaRUS` 입니다.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `nl-NL` `nl-NL-HannaRUS` 입니다.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pl-PL` `pl-PL-PaulinaRUS` 입니다.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-PT` `pt-PT-HeliaRUS` 입니다.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `ro-RO` `ro-RO-Andrei` 입니다.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-EkaterinaRUS` 입니다.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Irina-Apollo` 입니다.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ru-RU` `ru-RU-Pavel-Apollo` 입니다.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `sk-SK` `sk-SK-Filip` 입니다.           |
| `1.3.0-amd64-sl-si-lado-preview`            | 로캘과 음성이 포함 된 컨테이너 이미지 `sl-SI` `sl-SI-Lado` 입니다.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `sv-SE` `sv-SE-HedvigRUS` 입니다.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ta-IN` `ta-IN-Valluvar` 입니다.        |
| `1.3.0-amd64-te-in-chitra-preview`          | 로캘과 음성이 포함 된 컨테이너 이미지 `te-IN` `te-IN-Chitra` 입니다.          |
| `1.3.0-amd64-th-th-pattara-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `th-TH` `th-TH-Pattara` 입니다.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `tr-TR` `tr-TR-SedaRUS` 입니다.         |
| `1.3.0-amd64-vi-vn-an-preview`              | 로캘과 음성이 포함 된 컨테이너 이미지 `vi-VN` `vi-VN-An` 입니다.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Danny-Apollo` 입니다.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-Tracy-Apollo` 입니다.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-HK` `zh-HK-TracyRUS` 입니다.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-HanHanRUS` 입니다.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Yating-Apollo` 입니다.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-TW` `zh-TW-Zhiwei-Apollo` 입니다.   |
| `1.2.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-HeddaRUS` 입니다.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.2.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Jessa24kRUS` 입니다.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-JessaRUS` 입니다.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.1.0-amd64-de-de-hedda-preview`           | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Hedda` 입니다.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-HeddaRUS` 입니다.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `de-DE` `de-DE-Stefan-Apollo` 입니다.   |
| `1.1.0-amd64-en-au-catherine-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-Catherine` 입니다.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-AU` `en-AU-HayleyRUS` 입니다.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-George-Apollo` 입니다.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-HazelRUS` 입니다.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-GB` `en-GB-Susan-Apollo` 입니다.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Heera-Apollo` 입니다.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-PriyaRUS` 입니다.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-IN` `en-IN-Ravi-Apollo` 입니다.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Jessa24kRUS` 입니다.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-JessaRUS` 입니다.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-HelenaRUS` 입니다.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Laura-Apollo` 입니다.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `es-ES` `es-ES-Pablo-Apollo` 입니다.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-HildaRUS` 입니다.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `es-MX` `es-MX-Raul-Apollo` 입니다.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-Caroline` 입니다.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-CA` `fr-CA-HarmonieRUS` 입니다.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-HortenseRUS` 입니다.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Julie-Apollo` 입니다.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `fr-FR` `fr-FR-Paul-Apollo` 입니다.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-Cosimo-Apollo` 입니다.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `it-IT` `it-IT-LuciaRUS` 입니다.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ayumi-Apollo` 입니다.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-HarukaRUS` 입니다.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `ja-JP` `ja-JP-Ichiro-Apollo` 입니다.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `ko-KR` `ko-KR-HeamiRUS` 입니다.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-Daniel-Apollo` 입니다.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 로캘과 음성이 포함 된 컨테이너 이미지 `pt-BR` `pt-BR-HeloisaRUS` 입니다.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-BenjaminRUS` 입니다.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Guy24kRUS` 입니다.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-Jessa24kRUS` 입니다.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-JessaRUS` 입니다.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 로캘과 음성이 포함 된 컨테이너 이미지 `en-US` `en-US-ZiraRUS` 입니다.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-HuihuiRUS` 입니다.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Kangkang-Apollo` 입니다. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 음성이 포함 된 컨테이너 이미지 `zh-CN` `zh-CN-Yaoyao-Apollo` 입니다.   |

## <a name="key-phrase-extraction"></a>핵심 문구 추출

컨테이너 레지스트리 게시에서 [핵심 구 추출][ta-kp] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `keyphrase` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/keyphrase` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>언어 검색

컨테이너 레지스트리 게시에서 [언어 감지][ta-la] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `language` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/language` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 참고 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>감정 분석

컨테이너 레지스트리 게시에서 [감정 분석][ta-se] 컨테이너 이미지를 찾을 수 있습니다 `mcr.microsoft.com` . 리포지토리 내에 상주 `azure-cognitive-services` 하 고 이름이로 지정 됩니다 `sentiment` . 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/sentiment` 입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그 | 참고                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 감정 분석 v3 (영어)               |
| `3.0-es`   | 감정 분석 v3 (스페인어)               |
| `3.0-fr`   | 감정 분석 v3 (프랑스어)                |
| `3.0-it`   | 감정 분석 v3 (이탈리아어)               |
| `3.0-de`   | 감정 분석 v3 (독일어)                |
| `3.0-zh`   | 감정 분석 v3 (중국어 간체)  |
| `3.0-zht`  | 감정 분석 v3 (중국어 번체) |
| `3.0-ja`   | 감정 분석 v3 (일본어)              |
| `3.0-pt`   | 감정 분석 v3 (포르투갈어)            |
| `3.0-nl`   | 감정 분석 v3 (네덜란드어)                 |
| `1.1.009301-amd64-preview`    | 감정 분석 v2      |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
