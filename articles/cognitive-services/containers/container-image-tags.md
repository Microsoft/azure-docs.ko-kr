---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 Cognitive Service 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 05/13/2021
ms.author: aahi
ms.openlocfilehash: 64beb8763c07178335d6fb3ac3356e730b88d9c7
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374958"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services 컨테이너 이미지 태그 및 릴리스 정보

Azure Cognitive Services는 많은 컨테이너 이미지를 제공합니다. 컨테이너 레지스트리 및 해당 리포지토리는 컨테이너 이미지마다 다릅니다. 각 컨테이너 이미지 이름은 여러 태그를 제공합니다. 컨테이너 이미지 태그는 컨테이너 이미지의 버전을 관리하는 메커니즘입니다. 이 문서는 모든 Cognitive Services 컨테이너 이미지와 사용 가능한 태그를 나열하기 위한 포괄적인 참조로 사용하기 위한 것입니다.

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)을 사용할 때 컨테이너 레지스트리, 리포지토리, 컨테이너 이미지 이름 및 해당 태그는 **대소문자를 구분** 하기 때문에 주의해야 합니다.

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector][ad-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/decision` 리포지토리 내에 있으며 이름은 `anomaly-detector`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Read OCR(광학 인식)

[Computer Vision][cv-containers] Read OCR 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services` 리포지토리 내에 있으며 이름은 `read`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/vision/read`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

`3.2`에 대한 릴리스 정보:

* 이제 Read OCR 컨테이너가 일반 공급됩니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `3.2`                     |       |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)


`3.2-preview.2`에 대한 릴리스 정보:

* Distroless 릴리스
* JSON 응답의 텍스트 줄 순서 중에서 선택하는 ReadingOrder 매개 변수
* 향상된 로깅
* CJK 모델에 대한 핫픽스
* 
`v2.0.013250001-amd64-preview`에 대한 릴리스 정보:

* 컨테이너의 메모리 사용량을 더욱 줄입니다.
* 다중 Pod 설정에는 외부 캐시가 필요합니다. 예를 들어 캐싱을 위해 Redis를 설정합니다.
* Redis 캐시가 설정되고 `ResultExpirationPeriod`이 0으로 설정된 경우 결과가 누락되는 문제를 해결했습니다.
* 요청 본문 크기 제한(26MB)을 제거합니다. 이제 컨테이너는 26MB 이상의 파일을 수용할 수 있습니다.
* 콘솔 로깅에 타임스탬프 및 빌드 버전을 추가합니다.

`1.1.013050001-amd64-preview`에 대한 릴리스 정보

* 시스템이 인식 결과를 정리해야 하는 시기를 지정하기 위해 `ReadEngineConfig:ResultExpirationPeriod` 컨테이너 초기화 구성이 추가되었습니다. 
    * 설정은 시간이며 기본값은 48시간입니다.
    * 이 설정은 특히 컨테이너 메모리 내 스토리지가 사용되는 경우 결과 저장을 위한 메모리 사용량을 줄일 수 있습니다.
    * 예제 1. ReadEngineConfig:ResultExpirationPeriod = 1, 시스템은 프로세스 1시간 후 인식 결과를 지웁니다.
    * 이 구성이 0으로 설정되면 시스템은 결과를 검색한 후 인식 결과를 지웁니다.

* 잘못된 이미지 형식이 시스템에 전달될 때 발생하는 500 내부 서버 오류가 수정되었습니다. 이제 400 오류가 반환됩니다.

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `3.2.2.014850001-49e0eac6-amd64-preview` |       |
| `2.0.013250001-amd64-preview` |       |
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

[Form Recognizer][fr-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/custom-form` 리포지토리 내에 있으며 이름은 `labeltool`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>언어 이해(LUIS)

[LUIS][lu-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/language` 리포지토리 내에 있으며 이름은 `luis`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/language/luis`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>사용자 지정 음성 텍스트 변환

[사용자 지정 음성 텍스트 변환][sp-cstt] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `custom-speech-to-text`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text`입니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list)의 전체 목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

`2.12.0-amd64`에 대한 릴리스 정보:

정기 월간 릴리스

구문 목록 기능으로 인해 이 컨테이너 이미지의 크기가 증가했습니다.

| 이미지 태그                    | 메모 | 다이제스트                                                                   |
|-------------------------------|:------|:-------------------------------------------------------------------------|
| `latest`                      |       | `sha256:22284b8e38f83a735fc59f466fb5ba138d5c56b9e8f446dc05cbb5ac63a9a31f`|
| `2.12.0-amd64`                |       | `sha256:22284b8e38f83a735fc59f466fb5ba138d5c56b9e8f446dc05cbb5ac63a9a31f`|


# <a name="previous-version"></a>[이전 버전](#tab/previous)

`2.11.0-amd64`에 대한 릴리스 정보:

**수정 프로그램**
* 사용자의 입력은 대/소문자를 구분합니다.

`2.10.0-amd64`에 대한 릴리스 정보:

정기 월간 릴리스

`2.9.0-amd64`에 대한 릴리스 정보:

**기능**
* ID로 사용자 지정 모델을 가져올 때 발생하는 문제에 대한 자세한 오류 세부 정보입니다.
* 기본적으로 대화 결과에서 지원될 것으로 추정됩니다.

`2.7.0-amd64`에 대한 릴리스 정보:

**기능**
* 문장 부호는 기본적으로 사용하도록 설정되어 있습니다.

포함된 구문 목록으로 인해 이 컨테이너 이미지의 크기가 증가했습니다.

`2.6.0-amd64`에 대한 릴리스 정보:

**기능**
* Phraselist v2 지원 
* 문구 목록은 다음 로캘에서 지원됩니다.
    * en-au
    * en-ca
    * en-gb
    * en-in
    * ko-kr
    * zh-cn
* 사용자 지정 기본 모델 다운로드를 지원합니다. 
    * `docker run` 명령과 함께 `BaseModelLocale=<locale>` 사용
* .NET 3.1로 완전히 마이그레이션

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1인 문제를 해결했습니다.
* TextAnalytics 3.0 API로 마이그레이션됨

포함된 구문 목록으로 인해 이 컨테이너 이미지의 크기가 증가했습니다.

`2.5.0-amd64`에 대한 릴리스 정보:

**기능**
* 사용자 지정 모델에서 사용자 지정 발음 지원
* Azure 및 Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행 문제 해결

| 이미지 태그                    | 메모               |
|-------------------------------|:--------------------|
| `2.11.0-amd64`                |                     |
| `2.10.0-amd64`                |                     |
| `2.9.0-amd64`                 |                     |
| `2.7.0-amd64`                 |                     |
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `custom-text-to-speech`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech`입니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list)의 전체 목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

`1.14.0-amd64`에 대한 릴리스 정보:

정기 월간 릴리스

| 이미지 태그                    | 메모 | 다이제스트                                                                    |
|-------------------------------|:------|:--------------------------------------------------------------------------|
| `latest`                      |       | `sha256:1c5c56b76de5dd5ae56ad32aa094c5335b82bd4b78ad805767bf3cef68da674b` |
| `1.14.0-amd64`                |       | `sha256:1c5c56b76de5dd5ae56ad32aa094c5335b82bd4b78ad805767bf3cef68da674b` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

`1.13.0-amd64`에 대한 릴리스 정보:

**수정 프로그램**
* 사용자의 입력은 대/소문자를 구분합니다.

`1.12.0-amd64`에 대한 릴리스 정보:

정기 월간 릴리스

`1.11.0-amd64`에 대한 릴리스 정보:

**기능**
* ID로 사용자 지정 모델을 가져올 때 발생하는 문제에 대한 자세한 오류 세부 정보입니다.

`1.9.0-amd64`에 대한 릴리스 정보:

정기 월간 릴리스

`1.8.0-amd64`에 대한 릴리스 정보:

**기능**
* .NET 3.1로 완전히 마이그레이션

`1.7.0-amd64`에 대한 릴리스 정보:

**기능**
* .NET 3.1로 부분적으로 마이그레이션

| 이미지 태그                    | 메모               |
|-------------------------------|:--------------------|
| `1.13.0-amd64`                |                     |
| `1.12.0-amd64`                |                     |
| `1.11.0-amd64`                |                     |
| `1.9.0-amd64`                 |                     |
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   첫 번째 GA 버전    |

---

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트 변환][sp-stt] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `speech-to-text`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text`입니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list)의 전체 목록을 찾을 수 있습니다.

음성 텍스트 변환 v2.5.0부터 이미지는 *미국 정부 버지니아* 지역에서 지원됩니다. 이 지역을 사용할 때는 *미국 정부 버지니아* 청구 엔드포인트 및 API 키를 사용하세요.

# <a name="latest-version"></a>[최신 버전](#tab/current)

`2.12.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

구문 목록 기능으로 인해 이 컨테이너 이미지의 크기가 증가했습니다. 

| 이미지 태그                    | 메모                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | `en-US` 로캘이 있는 컨테이너 이미지입니다.                                                             |
| `2.12.0-amd64-<locale>`       | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.12.0-amd64-en-us`.|

이 컨테이너에는 다음과 같은 로캘을 사용할 수 있습니다.

| v2.12.0에 대한 로캘          | 메모                                    | 다이제스트                                                                    |
|-----------------------------|:-----------------------------------------|:--------------------------------------------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:064aac5ddd8344081961be631d8f21787ca031bfb7b80e539393464d7bb45778` |
| `ar-bh`                     | `ar-BH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e85734b5563b909851f91ee0e6736c1dee90289f97018d594a026a1f29e0ed3e` |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f4a1047fe225a16d515ab0818393114073979c9eff862ca01c1a8fec6cc5db06` |
| `ar-iq`                     | `ar-IQ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:422f18155a43817448b9db0dfdf87972ede8ffdd75c154251e00df20812da233` |
| `ar-jo`                     | `ar-JO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6521a7ad179aed7c8305968613d8688afbc20224e9e12e88a90352a36d59f860` |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:064aac5ddd8344081961be631d8f21787ca031bfb7b80e539393464d7bb45778` |
| `ar-lb`                     | `ar-LB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5aba99800db4709451f9d3cfdbfae38f4f9d33eb1ad582f3734db29adb1a9312` |
| `ar-om`                     | `ar-OM` 로캘이 있는 컨테이너 이미지입니다. | `sha256:96493991234739d6af47b27e00da14b3b380936422d6b91c3c89fcd56252b54d` |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:064aac5ddd8344081961be631d8f21787ca031bfb7b80e539393464d7bb45778` |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:064aac5ddd8344081961be631d8f21787ca031bfb7b80e539393464d7bb45778` |
| `ar-sy`                     | `ar-SY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5ca200a4f178c222833c425635e5e0bc6c1afe1fd8ed36d79c5057eddb2a896b` |
| `bg-bg`                     | `bg-BG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:20349f5cfdf5b494b7b99fd21919fb1293506af45cfa4deeebe86c685fe148fd` |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2c8157e92e43ef2da62cf0cbdc03789d9212af50e631dcb2f05b15da166ca98c` |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b7d7165585c42b71a10a59f56d697c976507d43dd7194497511e9454e30fab5e` |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7dce8b535f20be8c950cf03ffa36d2a0596d82cb894d336933ff8e9fdff9a971` |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a129a9b671f79055d14a94150d47ed466c18c5d0a6309df7a2d3a805683a9cc3` |
| `el-gr`                     | `el-GR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:84d0fa507571051d6879e06882135ab3382b35ea6a28e175e665400047910086` |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c7901ee73bf87040f2b2f8d811007b6282070a72793214e7473b934b460004c9` |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a26b8e87a5d9b0a6ef954770ce1ae6fc0d6bbbf97875274a5610be38e161f80b` |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6ad9c80ba0e1f48ecce5112c685b576a4214671a39fd12749eb26c62c49f8e67` |
| `en-hk`                     | `en-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:82f8abb2396aede6ab16e67f1c705ef58bd71b269e832ce0c43f1769aad74469` |
| `en-ie`                     | `en-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1daad42be533a5d284e4e7bf75e87b1a09943052e2bbe58e2f085b952c069882` |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a29eb8e6c72962096d8d281e6f329daacbd92200a2f9a970e522234a5b08ba54` |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9b1273d7610f585cbb1475d83837b671c43c05625b0c73828176dc93fd9982c1` |
| `en-ph`                     | `en-PH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:aacf6d9111493a6fee960b3fe5f87955a1eef04e56e2908e91a7b2ade5a17638` |
| `en-sg`                     | `en-SG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d47f1fc3235734a4642875442ebef3d73e139d5699c42bee2c252c9534c0cd72` |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3ad65ef9e0a0e496eb80eab187aa2d770c725d84d45689ad570511f5bbac0f2a` |
| `en-za`                     | `en-ZA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8caba9bea8d794a77b6d674bad1e478d1cc925089bb4cc64f80f7b2a15f8a035` |
| `es-ar`                     | `es-AR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:145b10dddf8600b0ab8c6edc4e262ee0843b3c794ca6fc4fe943d5096597c506` |
| `es-bo`                     | `es-BO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:77475a986862bacbe78f86558771ae04e5a2bd4f2d49cab30151e5a8ba6965fb` |
| `es-cl`                     | `es-CL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:72ecd5fbed1138a433a8d4a4021209b3f481cba2c86e5c9b97bf25595cb52bc7` |
| `es-co`                     | `es-CO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:26d20e889edf08bf54ba2e7434793da41f8830c7a2e238f4e21f398f5a4ba054` |
| `es-cr`                     | `es-CR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:37ec3204a2527659c9bbd9c016eb06ce676f9703a579b4e428b49b3f34917157` |
| `es-cu`                     | `es-CU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6880436e0d674942dcc38cba4a5fa63e05718959a5f51dcb7e529ecb25b66602` |
| `es-do`                     | `es-DO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3adab6788008209afd34ccfee95a71cbc85f48ab976ae596dad51c5f8f954936` |
| `es-ec`                     | `es-EC` 로캘이 있는 컨테이너 이미지입니다. | `sha256:50b89dbee77960eb9020bd38194c893cc54ab94edb4f93a0d4c8c9998ad731e2` |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a2d3f2e3d1c205f87b3ebf0c10d09f74529b658c01362b20889a2a0988c19936` |
| `es-gt`                     | `es-GT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3c4d7495781eab151843a945a81086163b5db35338017eb232c99893bc14450a` |
| `es-hn`                     | `es-HN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3289ebe6a3447cec9e66eaf508f2d10fcebba324164a96d85ed02f9bff5585b4` |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5c8ac33806a34238e291b5ec55862429415a60883aaaea84a8bf9e3e67ff7963` |
| `es-ni`                     | `es-NI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5e13ec7acf7160889035041d3237a1b2814749428516a2b3a1843f8d511eb30c` |
| `es-pa`                     | `es-PA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9c34f23de138039354dc1e7d654bfef6d1290846b6da0136519cd1c77d8671c2` |
| `es-pe`                     | `es-PE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d726750e523222f12908e7836ef9aa289873eb91026ed4ae08f138f2af279d88` |
| `es-pr`                     | `es-PR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:04b5ecd38ec30d21a55320dc0193c436484a5203a4b2dcf4ac3b75a2ff266eaf` |
| `es-py`                     | `es-PY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:23468dfddb63b20ba394e07841176c4d26d31626ee4e7d453a6ec9a2652f439e` |
| `es-sv`                     | `es-SV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5a21ffe2d29f1e767634bb28035c44ef8284dd7320fdf22adf638603d6500b6d` |
| `es-us`                     | `es-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0c0904e6794e7d84e259923ca8ebe57cfaef7fb195f29e01f322701bb226afaf` |
| `es-uy`                     | `es-UY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b08612341054b28621d0b462f340aeff03c7064328bbbc3f2dea9c07276bcb10` |
| `es-ve`                     | `es-VE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:96984115e3380c44929bf8cce4b295f9c05d0bf92c1b1d31a87e1a7aaf7eee38` |
| `et-ee`                     | `et-EE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:36f02ef75ce7f09b4ffbf06e5c61a59e9066a503017a7f845f0636bc139d2ac9` |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:05c16cd1d888c707e985d59f187621491c77a4cd5997ac71c49beaa6c86f39f8` |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2e181fc9260f4c85b5dee67379b22bc8031a24d902a810367f4c13cb5282c9f9` |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2a580f39f788e4459e0a3c544bca6724bc3f7b6e9aa64e9ddaf53675d211de7c` |
| `ga-ie`                     | `ga-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:70e820e105ce6897c22260af841ec4049afa83bae5ef1299cbf7b5f7bb820c3c` |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c7c2cfd2090c0658eba6e6da6bf1b4b2649873893c6efa5d0985b7ba6923353a` |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4cace269d6116cc2ca726239150651d2602ae33d95f91b5f76d75c8e9dcacce3` |
| `hr-hr`                     | `hr-HR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:aaf72098d1fa79438f79472470ea3399c29c32b948091a4689e13361c560a913` |
| `hu-hu`                     | `hu-HU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:122f2123913e5869801458cd1c89605c2caba15e02b8c66b61b48d47969c86bc` |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:847c1b0b521c2382b1ca1fd2c50b12ba92e1336f8fb99627ef8e015b32865bac` |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1b8c5e699197b7327dc027e2358ff04c97dbd3570d1523c8d64f3db18599c6ba` |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:38b722e2e4f6479f560af3da727dd29010e8d0d05d3f368cf2f55eb939b3155c` |
| `lt-lt`                     | `lt-LT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5064a002072625b6277b479e3681ee305f567bffd16437ad631b60ba5646d494` |
| `lv-lv`                     | `lv-LV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:feed8608f89a233d01f1611144aa4619651ae34e7667938aebf91488f9f7c95d` |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:51ccd73028e6fe0ea87e3fb16a1380079cc35e89817897ce05b4c3609d92cd1b` |
| `mt-mt`                     | `mt-MT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ce8a8c0edf2a69f6f3cc1e97d55ca9780ddc693c23e4fddda07e15bdf5ae0325` |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:48020e478248404af4353e6a1bcfd587362d347f82a26235b6074f24fa7bda94` |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d6e95e09b39acff519e5a9cbacc4428b63099f09233e3ec4da4df0b2542691e0` |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:37f2514d5a6e92edc5b4e617a29f35081b19b007a5608b8ebfeb8cd73f396c46` |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:99a610bf2011fba1a6019122bbe1e12a4f62fa83134d27bfbb8b29ebcafdf548` |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:14dff7b99958456911addc6cdbb2d949815d2f7f46379603cfbdb7b8a0fbb91b` |
| `ro-ro`                     | `ro-RO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:47bc1aa59c06d86dd23c1fc23522860ec36e598f8438e8212a1a1d2617918415` |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4e414bcfef5014e280eb59a0f5b71c921fe3c555be73d94e4023682844178b52` |
| `sk-sk`                     | `sk-SK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:65dcb88c8cbaa9fdf2a0d7daf33e479db924448334f7cd0f17c542f9e8462313` |
| `sl-si`                     | `sl-SI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8066547ed0a2571ba2d26b34b72691ad9c5cb66cf0fbe4464af971199abd9bf7` |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a9bb856066653abe1458ac7dae12c968a40834fb75d8047ad74d9c15f7df62ed` |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:60339700f76a17d77753659c2895f9fe5c40574641d5530cb482ed4575c59ff1` |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8d05d03fb0fe03eb9ff499d81c1bbf8932848de6d723b66b32eb6ed970225ce6` |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5d02087aa366829162098d21efb084724d3a5b8d72a8815c45de5e2017f9368c` |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0d7198cde7eba3500caa153e869d14ce0ad07568416dfd69108dc5793106e3ec` |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:27ad0c04c41eadb445bab067cb14f84ede08a09ac9ba44bca163f2af89b8a5c2` |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7b816c7e753684e5f886de43ca7630c0b52f47e704e2c7e50581ff2bca138703` |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fb3d06558c1a479377324ed689f13519f5df4a6283b2933401aec3e2a2b0b25a` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

`2.11.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

**수정 프로그램**
* 사용자의 입력은 대/소문자를 구분합니다.

`2.10.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

`2.9.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* ID로 사용자 지정 모델을 가져올 때 발생하는 문제에 대한 자세한 오류 세부 정보입니다.
* 기본적으로 대화 결과에서 지원될 것으로 추정됩니다.

`2.7.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* 다음 새 로캘에 대한 지원:
    * ar-bh, ar-iq, ar-jo, ar-lb, ar-om, ar-sy
    * bg-bg
    * el-gr
    * en-hk, en-ie, en-ph, en-sg, en-za
    * es-ar, es-bo, es-cl, es-co, es-cr, es-cu, es-do, es-ec, es-gt, es-pa, es-pe, es-pr, es-py, es-sv, es-us, es-uy, es-ve
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * mt-mt
    * ro-ro
    * sk-sk
    * sl-sl
* 문장 부호는 기본적으로 사용됩니다.

포함된 구문 목록으로 인해 이 컨테이너 이미지의 크기가 증가했습니다. 

`2.6.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드 및 .NET 3.1로 완전히 마이그레이션
* Phraselist v2 지원
* 문구 목록은 다음 로캘에서 지원됩니다.
    * en-au
    * en-ca
    * en-gb
    * en-in
    * ko-kr
    * zh-cn
* 새 로캘 `cs-CZ`에 대한 지원 
    * 대문자 표시와 문장 부호는 현재 지원되지 않습니다.

**수정 프로그램**
* Diarization 모드에서 신뢰도 점수가 항상 1인 문제 해결
* 마이그레이션은 TextAnalytics 3.0 API를 사용

포함된 구문 목록으로 인해 이 컨테이너 이미지의 크기가 증가했습니다. 

`2.5.0-amd64-<locale>`에 대한 릴리스 정보:

**기능**
* Azure 미국 정부 클라우드 지원

**수정 프로그램**
* Diarization 모드에서 루트가 아닌 사용자로 실행하는 문제 해결

| 이미지 태그                  | 메모                                    |
|-----------------------------|:-----------------------------------------|
| `2.11.0-amd64-<locale>`     | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.11.0-amd64-en-us`.|
| `2.10.0-amd64-<locale>`     | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.10.0-amd64-en-us`.|
| `2.9.0-amd64-<locale>`      | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.9.0-amd64-en-us`. |
| `2.7.0-amd64-<locale>`      | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.7.0-amd64-en-us`. |
| `2.6.0-amd64-<locale>`      | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `2.5.0-amd64-en-us`. |


이 컨테이너에는 다음과 같은 로캘을 사용할 수 있습니다.

| v2.11.0에 대한 로캘          | 메모                                    | 다이제스트                                                                    |
|-----------------------------|:-----------------------------------------|:--------------------------------------------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:32c26ed8370d1f30098811fda382e68aceccabc671570365f15ead37c3d84304` |
| `ar-bh`                     | `ar-BH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a6af48cdaf9f7562bfaced449016106dbde5c678fdd4c69985d166959a38b146` |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:43cec166dcde9dc7cd535228440d11d396518fcfb14d9fa617e6e26f5156dc84` |
| `ar-iq`                     | `ar-IQ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b55095b27e8eef60dfe9657735a425b9ca1fe3c29ce4ff1f3d67bf7b2ac77bb1` |
| `ar-jo`                     | `ar-JO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7cc4ad997a76844414a982982251653525f27dc396db44f23b7f012d20f53677` |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:32c26ed8370d1f30098811fda382e68aceccabc671570365f15ead37c3d84304` |
| `ar-lb`                     | `ar-LB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5d3b402f41f616ee792a5e7e3f41b4ec5638dc8ad60a3c133ec588e07b09d581` |
| `ar-om`                     | `ar-OM` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c4f88fdaec73ebe241d6c94695b20eb2c792a9fd77dbb51f24fc7807dfd0dc61` |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:32c26ed8370d1f30098811fda382e68aceccabc671570365f15ead37c3d84304` |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:32c26ed8370d1f30098811fda382e68aceccabc671570365f15ead37c3d84304` |
| `ar-sy`                     | `ar-SY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a42b6f63a16313f280088bd47978e177bc2f1bf2d392a070cf5c6a06d9f7a62c` |
| `bg-bg`                     | `bg-BG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:21425557e62d71326e9eb614c535878f981a914bf66d9dd883221656ca891858` |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:682e8a8ad5f2582f25a18b0518f9fba9b3849b72eb5dab5454586724272c52de` |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1d0661ae5920f82e607c72ae7d6eee917c190d80c3d13403d770947c67a4294e` |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8d5257d6c326e4d96ba395faa0c717f48c4d437866f8dc1e1252c5e983b3008f` |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:086a4e33f746868fc1865322f1d7dfb5c1c3af64bdbd369804155f18710ad96e` |
| `el-gr`                     | `el-GR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0e2c7d5337f953d45fc7594317e6eab5eecec44a1c15fba51a128fc510519c3f` |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dcfe3fc95b895d0205a7b72368595e98dfdcb4b6398522e7daa2fbbe2b087ef6` |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f04cedb6b50560f0584cb3634cbfee5e9c147d60fc044cbd0df10fc28f04ed98` |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9692c45c6b5b8716f99852a2ddf4b7fd1e2c00ea29f9a20da68e899cf3064fa1` |
| `en-hk`                     | `en-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:97106aa991b4ef5b0f1859ae7a7df3c6e22dd009123281a7458d336a78ebd854` |
| `en-ie`                     | `en-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:da2bc14cd86f200a439b3ce708c6643d507d482daabae87c351bee4c10efa60b` |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f8fc43e5d20afe8108b6f35c3e09d403557f150413672d45322421be1fddff20` |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:abb8ca669c806a71af88d3643694252e1833ca99aacbd739a3962ec00c3cdb61` |
| `en-ph`                     | `en-PH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:13bc7717dd73f4323956a3f7441b24dd2f86c13d41adc709e3f6f26266cacd91` |
| `en-sg`                     | `en-SG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b7f44d7cf4bbe4d89729207a38e91726c321ea03a66c5e5624b27ae9913fdafa` |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d81ee15821646607aec9fa46223c9197f74675a89070912ca892ad5adfcab6f9` |
| `en-za`                     | `en-ZA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2e2f9102c9f6fba0736fb01d745d35b677bf92750eed5cad245ee089998f66f2` |
| `es-ar`                     | `es-AR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dd962ec3f32b8fdeb15f7ab18ea9d19e7c93baf4c801fac59d44f5cf845e9935` |
| `es-bo`                     | `es-BO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f89c0e513f43800e1d19177384b815c1a04f5b07ccba8fd9c80aa5ebf5c71648` |
| `es-cl`                     | `es-CL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3ebc64dceb1b7fbef716de3736a020b23e8fb4e9aceb183524863681e0b278fe` |
| `es-co`                     | `es-CO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ba05465c312acf6b9a1a1866c81c795027470e8bda8389dd0fcb641c9f1af592` |
| `es-cr`                     | `es-CR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:51d49d90f600ae971019974a6a38c71b3bf01a84301ee6e8604c3f424bc6773f` |
| `es-cu`                     | `es-CU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a19f0ab805d0268c06a0e83aad2dcab458638e8c2f7869f5b2315695ae2ea4d8` |
| `es-do`                     | `es-DO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a9539f091ec3feef34511ce9d337436151980eda69c7f8c8f2493e8d1be81e66` |
| `es-ec`                     | `es-EC` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a0f5c19a683b92566747db79e30ac7ad09cde07bcb15451166b5257d036a86bc` |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2aa5e82c726a8771c706a2de38bed09ca9c8298bb166c49fa227b8966011efa4` |
| `es-gt`                     | `es-GT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:60361c1a305d0fef3deb0e4886c4044aebcf41878a748bc0615b94fcf9489cf9` |
| `es-hn`                     | `es-HN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d628b894966988880bb11f1ec1380702077bd45c2a83b912ae3e7451d8fd90cb` |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2bd901c320237e041ecca1ea34c359cf847cf8dacecfcb0e1ed8fd1794463fe5` |
| `es-ni`                     | `es-NI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:099d21e5e5816d5d7e0965cda5878bfe78f5447e4994957dcc45ae40223b14b1` |
| `es-pa`                     | `es-PA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:af6c258b7e984ee17d32b9dfc49969cfc1d7ee33aa2485017fab191d8d574e92` |
| `es-pe`                     | `es-PE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7d0e03c7f44f61b4632b730c2cf8e3d7c584a869bb5d53b9e5021549d1d500a8` |
| `es-pr`                     | `es-PR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ad580c1ac73d919434387869803d9fabec24e19afd6b4cc5aa7e809fb93dc908` |
| `es-py`                     | `es-PY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2e85df2af0003c0a41752c6e989ed8b724a22958e7ed3cbf67e54ca621bb5975` |
| `es-sv`                     | `es-SV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bae49ae543878096c1dd0c77a8f83a30ba1416605efa58dad59ca3577f7006ea` |
| `es-us`                     | `es-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fd9deebe4e5a4466af439a8e40a1a39261a7b0228a4ed979b8086e1c65c60e26` |
| `es-uy`                     | `es-UY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0e69fc4689dafad97e00bed7c4eb7ca44b94e3a3d9357d6d36bed8135963e9e4` |
| `es-ve`                     | `es-VE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:37ebac38fac4306668858140736d83e008ae0756f8e1fe5ed6386780bc9796ba` |
| `et-ee`                     | `et-EE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:223d494cf64cdceaabe6e9bae82d378d7ea53eb8c01d58bdbd2e1ed360aaa34b` |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:378e5735198e38d6bed8c87a59ed69f8c3bd57ac8a462332d74dd8495cb07ed2` |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d92f672c2a61a67db43d9884bc2692c304b3c2c5446bed2d315892876270366b` |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:11dc172c7ae91b6cba7fb4ab1a61e48b27b193bf434a68827eb197c0ba05d6fb` |
| `ga-ie`                     | `ga-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3057eaaf8e0403690c0223c0db3a392b05f2ec45e53511327b8447912e32b8b4` |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:37062edf6805dce30309e4615c2947dded730b5b5be7e3bcd85bb93e38b08f31` |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9f1bf1901a6b0e2caf4c9ff30e0b6bb3f1f4f814ad86fc62a471d4fe1fe4c101` |
| `hr-hr`                     | `hr-HR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:095b40ad1afeebd932c299410a4732fd64da2251230aa044ca2c43b4d0bb6791` |
| `hu-hu`                     | `hu-HU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:60e9257735cee7dc6cde1b5725588b1c1ea84f852220f1f4f3e873177a24fc5c` |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:71c5e3a9196155678a6ad9cd62b812386579521ac410b40e3526dee153d749e1` |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fce7d215575d2a94cdb4818bb1525f6448f5f881fc3e7f04274c64978bd6aaa7` |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d71d8e1e3692bb0781e98b984dea79950a8009a6fa03e729325c338ca5c09a98` |
| `lt-lt`                     | `lt-LT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dc2e35e158c09fd793b180050a0100df4a3716da4d0a7a528dc3ea65b6ecf21b` |
| `lv-lv`                     | `lv-LV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e6ab373eb9477d90d44175fffb646298d403405633e0a61ccf20f9e7381243b8` |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0ce15c2d14bba49639adea30c91df1ac47e7b2a7796be551276bad8ec8312ed4` |
| `mt-mt`                     | `mt-MT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bbe958ff9c7c51efc6521866173b26ac2cfe682d114ce3ed6b1f6b8e9b3a7327` |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4e4d890605e09717ef88982f586611c605342465a8ef81f2280f665ad1378522` |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:60bd2d1f817019e6626876b15f5697be07c3b2b368e4cc7e3c3871c3e9181052` |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8520e7155ef176fb9fea48c541acae995a6a80ba6913ac4289786ee55062ce6` |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8440308a5cb77791f33ae458c49abc084a1be8c418df9feeda9a4aa917a59bc` |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a66739b36a410c181ccd2205c59fee2726b3905d1c5ba4531909be96cf85a55c` |
| `ro-ro`                     | `ro-RO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c4ba7ff5c11d4243a3e128aca1f8110e62df82d956706c97c237016a94cb485f` |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c3fc4117598c0dcea0fd5e6f19adf7763e42732e32e3ac93ff74795fdc167e67` |
| `sk-sk`                     | `sk-SK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:78bcfa610f645c113134cc24c8af8dd3c630065c1b009fb5e36dfab4999c16fb` |
| `sl-si`                     | `sl-SI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:134eb68c900787bae3a98a2bdf192f2a5460fb96b92590d65765d982245a7ccf` |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d194aaefe82a5f91df9e01beec271ad9565c4d36cb0539421e947b5c8e67228d` |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:cf272b112b10587c034f00f7df2bfcdefbf542859fa089c15581040db99ed383` |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7364a1068f9940e9bb6ea5476b0a007a37d42b899dc4ba56be833e4d2b8d359d` |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:21ce33714fa37bfede60560a7a24c17c88566c767b76c58c877a48c51811c9ac` |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b97035a4f0334f890ff3630a2de249b72a879de3c7d4fcc849c3d76aa97f4d2e` |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ae4a89a26768c978d91ed797e9ecb8035fdb61f12c1b1124c86939c79ddcb38e` |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:41bc980abe79cd69034a8ade2be203478b531a00f5e74b1f7b8f9c5267700261` |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:51a50a7fcd5a9db6422235a2df0e8fba360efcd3cefee9abe44ab2cdce62088f` |

| v2.10.0에 대한 로캘          | 메모                                    | 다이제스트                                                                    |
|-----------------------------|:-----------------------------------------|:--------------------------------------------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f81f6c53e8ca9c3ae10c335ad45054cea571eca2f4ab32e44e13445936ce3f17` |
| `ar-bh`                     | `ar-BH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:da276dc1b481c002a9b3d2944e190af799175b5a2eabafab87153e22529bdab1` |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c2ae166526cb0c5d481b537daa3accd379c4b1bf51fce6d85ac20591e7e0b4c0` |
| `ar-iq`                     | `ar-IQ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7d4a6cb1d9d66f6bd62f90b82000ef811f8a3dd58b03641b6c51ad6f0f4fd4dc` |
| `ar-jo`                     | `ar-JO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7489a0ed06fdf1da1d25e3211f5a66abe420babee148961a2ffe8cdbd82564a7` |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f81f6c53e8ca9c3ae10c335ad45054cea571eca2f4ab32e44e13445936ce3f17` |
| `ar-lb`                     | `ar-LB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:478e4575073660e9153811f58e74815f62395ee2ebd868d448fbc3a5e16442be` |
| `ar-om`                     | `ar-OM` 로캘이 있는 컨테이너 이미지입니다. | `sha256:025dcbd6a7d1912812b2556ffd7a16ad2158be6c3746e2822f2b97f460aa685b` |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f81f6c53e8ca9c3ae10c335ad45054cea571eca2f4ab32e44e13445936ce3f17` |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f81f6c53e8ca9c3ae10c335ad45054cea571eca2f4ab32e44e13445936ce3f17` |
| `ar-sy`                     | `ar-SY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5af93722e70e445b3a4102bf621e6d5bb5854bcc99f60d4590e23fc24e50297e` |
| `bg-bg`                     | `bg-BG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a9402f03b02150288d51e03ec97b8efb98ad6c444df3ab50a3b4ce1129d02d86` |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:122df16df46a84a14b28e4ff406a047947fdc10a65b40482438beee55579f687` |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7b7d7ef798a0210b8c33a3a201ba149e1264cc7ac6ddaf986721d86e91e5e444` |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ba8dd6564939eda7b81b1a4c13ad31672927528dd146698fce10c12d21f647a9` |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d0fa9bc409238ebdab0a15174b3169c99cbad42323087ea589bb7812a0550149` |
| `el-gr`                     | `el-GR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4c4a115ae8daf53e344c1c4f838ebc68c3de2dae4d1f1aceb021425807d96ac0` |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f18c31f2bc9e655b93f71049b40dae2213c7417169f7a4e42f603d5891857b2a` |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:67f02cdb2285c2891aff8ff8d35ee20bad11f2d1cc1d67c461185466edefa5d6` |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ed606155b5f9b6c6dd68c0c1f5e48a0735bc4a5ded872655c0ef7de2bf084312` |
| `en-hk`                     | `en-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2fb6a64aaea5efdb2cac8bda2c7d437638fca93aa24268a45f2a395285e022df` |
| `en-ie`                     | `en-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9ddb64e481cec6449dfc48091092247fa401fcd48ab1d955c5186565f903bd34` |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:060a87ae817a82486966a4f10d1e872d30370ea58e297ca4c2018d0e034bfbe5` |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ece4299bd7f02fe4403b53320cf55bb2e3ab65da3d94bfea09124c14955a3de3` |
| `en-ph`                     | `en-PH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6b47286a882122de8114942d426cbb8b4f1aded318032317b03a6b68237372e0` |
| `en-sg`                     | `en-SG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:41fa2caec6a732736f75b682e0410b89ba5e12307cd6e2652986a2676a5dd560` |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:80ae57602d8e66c6ed0366327a87c0ed5717b44c596b981a2b5be09c7f5a4c8a` |
| `en-za`                     | `en-ZA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:705c125e5105b6eed37d745e2092d55ca8b6ccff22f4eeac9c2df958f36c72e9` |
| `es-ar`                     | `es-AR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:67f794f16fdac457f0e0a84192e588611adb43777635b14706754c19fd90b130` |
| `es-bo`                     | `es-BO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:94f755e70043dbe011424a0f756970f1d01ec51cb95a469531e3a6b0aa84aed1` |
| `es-cl`                     | `es-CL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c42eb56cbb48e0957f73793f83435c705ed0f857579acb020394025abdd760e2` |
| `es-co`                     | `es-CO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7cfacb01fdb80bd1b5e68d16f9e2741237ae4ec1a41a9121aed1be2622fc9f3f` |
| `es-cr`                     | `es-CR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7dbe5becdf4f3264764eb596d61781a2b2ee54bf9552bbb8f4db5e7fcf75d8f8` |
| `es-cu`                     | `es-CU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a1064b4498b7c5972a8a79ea84b78c2e1e7698c039eab49fd08963d11798ac61` |
| `es-do`                     | `es-DO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:03cd0f0bae11df645dff52b15746e31493522db5399a18878df765b6aace0a80` |
| `es-ec`                     | `es-EC` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3dc8d3f0842089edde4703abe8df3a219fb177afd5ac370c5b04c85abae4ca15` |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7b0927c3b60bf38e995c57a27843680d9062d88611c49378dda8f71a4602f7a4` |
| `es-gt`                     | `es-GT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:72e51683124c76255ec9280cd0641d6e44633199bda769ddb31336362f6e641d` |
| `es-hn`                     | `es-HN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a948970cd11e2597ba150291b2dcc72f2d59ad4f693933ef1f72c210f19fb663` |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b773cd7bbb5eba548bc468c2f6d50732e2553c5f8ba4b955404140def4c3f3fd` |
| `es-ni`                     | `es-NI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:db73492bd83597c1fa47e7c4ab5eedbc1afa7662088fb03df2aaa5b737b5f837` |
| `es-pa`                     | `es-PA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d3a86e840438eb2278d0bbfdf1fc98a48fd744fb8c92118f6d3d6298c45a2b96` |
| `es-pe`                     | `es-PE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b60dae65bf1fe20e698ce32811373473d811bc363d4db093b643238f71461d4c` |
| `es-pr`                     | `es-PR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2a81a9d1b32c546ec03caeeaeddb1b26e5e00747c691f5be62f9d23c5ba84377` |
| `es-py`                     | `es-PY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c4b91cd5e017060a82a34f83d3f62a16b856313c02fea048d300abf149aadf67` |
| `es-sv`                     | `es-SV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2a5bddc5355d6eb0b101423c733d6cf067bafd0e152b63bf6c4dcd943ff561f3` |
| `es-us`                     | `es-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f60037ad8dd2b40f588608a5eace8b0b9f3171d05d39a02c2dd1afe98ea7e18d` |
| `es-uy`                     | `es-UY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e302da84ee0264221f0e663470f579348664ddef37050bc0fe57c620264bae06` |
| `es-ve`                     | `es-VE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b6a79de315c73ec3301aa0cfa7ed920abbf8b6f80fd3d42637b785ee97a85584` |
| `et-ee`                     | `et-EE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2de931f1e6f38cdc2f54a08bc1e64a13876326d57784f0ad1c50384381790b05` |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:47c1b3cceb8a6f0b2ea16160ba8c503d39ac77f44c254dc880b5e17d2aba4a4c` |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bf40fbfce8241e14656df47178d7b57f19022cc6b2598de5b337c6710eba99b6` |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:93e0d58ed07d637c3e394ce80ee93524697063cb693da2aed9013660b2543702` |
| `ga-ie`                     | `ga-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1d239549ecf7f6bef5f9d258f5fd34f81fb0e5fff89c66dfec769e912b1cbf7b` |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:596f42a366a61d1cf05dedb81a4f373cfae2dc04e8bec3479bfec121417dd4fb` |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fcdad9382db8fc7ff0a7ad59fa9fd4cd319ca258edff869b66d76031bcfee640` |
| `hr-hr`                     | `hr-HR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:533a6420a4a98d4a2c947d26511e90651fc341c96b90a02615b38ce2a799f058` |
| `hu-hu`                     | `hu-HU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ec6b95c03d9d5030457c4a9e1fd8e07fbae24ec50b0bb3b2a95eadcd81a1d136` |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:67cc80b8159122c530913505fed0f7bc4edfd3d77b25bc34b6c6157d57178728` |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2b1f3b4220f8a7a44c8339e4c6a4b9a55f7583b5540f045997c9cab8364facb2` |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4703fd5e1c5020d5c58b1adde30e5209b1e6f21d0636bac11013dcf8da9340d3` |
| `lt-lt`                     | `lt-LT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:58c2bb9cf2ead05fc77b3962ee7cef0e0eec33e32697757f65ae8925d55f87b8` |
| `lv-lv`                     | `lv-LV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dcdeed91559fb7e1b7d2ea70215ec373a59afa6b67468d13316af109314ca384` |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:06745d241654571428c219c38cd43b56e92b97eeb5aa6656ac726da79460afc1` |
| `mt-mt`                     | `mt-MT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3c85f1057b5942c5d2094055e7b9ecc6ef995905bbdabfad48bfefb805f436cf` |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:313f2fb20b8c2a18bd6ce5e7877899310575d390a2c3c54cd2519d0538393201` |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7c897fdb38661eb60f576c0a1a9d69bab9e44e7a70e8136fa3d12531cde0e4d7` |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9bf17aa5d4c577a440c770b6a63b66037a201cbea0202af4856257fde0548f0e` |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:08f0bb7f1454c5d6c740d218013f47c54bed17701e05c239364f5b2eba07692e` |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0643e1c342cf6d526620a46b3435c130702b9320a6075ede1351810956ed6ae9` |
| `ro-ro`                     | `ro-RO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:139f83900395a0d1af99dc90e661238ca2fa0bc06c74cbac28631ba0399345bf` |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1c38423ccd1b8042d43eabe013f5b6989556610ada803b4367848b58c4832a76` |
| `sk-sk`                     | `sk-SK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2b33e5d5ae0cc46bd9a4ae860fe22f088903d4978b287df4eff6ae63b91566f3` |
| `sl-si`                     | `sl-SI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:40a667412882bfe8073abf376fe94378d7c364e7b22aee410d7b6e99d65e55be` |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d55464b46585fcfd86c420a30d11b10f3b5c9c0d70390b75f40fe9dbbeeefa99` |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:06f3f986ff92f16e963771da485695ec9e1da482b10f35babb2d54e260da23e7` |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0566062d116cb06c3eb365dce6e86d9c46ce37293b11ef71c4e219c3a11ca559` |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0fa6da985d839919fedb503625383dcda04de6bd39558f2f72b64410675b8f85` |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:88418775c8a8df79aa52de03091b938b7a4efc708907556dfbe3e1d686050e81` |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9087a08cc455772515f5775a788cdde35d7f5bbe3aa3ba34ae99573fd87b29a1` |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:372e1c256520e9ee84c4c400eae935c1d6b1d59adb2be4c4dbc56439db069ba0` |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8406a3be34530c7d654d1dfa1c593dd51b8946b480fe80a100e599e86385dc2b` |

| v2.9.0에 대한 로캘           | 메모                                    | 다이제스트                                                                    |
|-----------------------------|:-----------------------------------------|:--------------------------------------------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-bh`                     | `ar-BH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:41e7942e4026beaad93e50f199a6a2d855f77c74e60bc9636bf2bf2c7d3bd482` |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d27f383435770aa01bb4117ba2d50a05ec172a1da35c4920ab43cd0fb74f44c2` |
| `ar-iq`                     | `ar-IQ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ca2734a6bfc562c4c07981358051d281fb5e089815b9eac14c66a0e6f92e9858` |
| `ar-jo`                     | `ar-JO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:57429ee8e95a76ec953f1b1f94b39a20507626cd7fe5431df826912e5b959e41` |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-lb`                     | `ar-LB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4c5fb6fdc08343e8640222583373effae3d03907cf1262a4fad3303df9385797` |
| `ar-om`                     | `ar-OM` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5ffd280908e3ee65fcb7bea0b532844f9d8510044ab4c2c612dc3c235938ad0a` |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:08885bedb2993daf0c918ecdc6ec775f7982ffa5ca561e80ab9b8a103cde8194` |
| `ar-sy`                     | `ar-SY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:00f3d1fd6ccb857ccef8a72322336e7a097d04027411f0dcc5499b44229fb470` |
| `bg-bg`                     | `bg-BG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:aa6ae12f786dcaa028e5867abba198effed875b6bc4cbafd4be37349e95dceef` |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:515a940ccd76ef1926bab3ad259e1cc7ac2bd90bb3860d28f83d0f6324b3f0fe` |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:03f6242d73de64c3eb3347400ea6e7408a8816bd96f3d6368ea2a8193accd457` |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ed6714e804ff2d1bbd41512c78906ad9b8827dfdfed0076a271817e075c2ec40` |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:386f2bb4c4b6ba797919ddcb5bbc9942bf8a03e774f9b01438f9bae0928414ef` |
| `el-gr`                     | `el-GR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:28696d10c78404fec033794e6e6ae0bfd92b0dab5cf7eb1d24cc2cdfbfcb646d` |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dd9ce70f83767a5bdc52fd62b96e09ce6f79ecc1903ed8e116753099b06b03cd` |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:70095cf952565256f3a0927358d0fd802d28fe1c3b89b26ead31ba1127cd0b06` |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:836bc38328636799ec9c8717618d51ab8b50ea2f0dc9663f342c4454938c9b23` |
| `en-hk`                     | `en-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:eda3702d95d4ae3b64ceb93bda42e8522776e141a18b2a3dde3bc3fcf0e9a2b8` |
| `en-ie`                     | `en-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bfc2126fffb947bf10ac379efb70db3d2c7ee2c16dd541a5b86e03e73d7d477c` |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5660d02eabf4e1e9f58e7993ed7e5917b1990b41ed35a484a715d7265400cd0b` |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:891c1805fd8011865de7371ffd4bde85d879341f2100e8053bbbc722d7c792bc` |
| `en-ph`                     | `en-PH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:21d6d46398f940a769241fdfffec5658356e54b4127b44efe5e061724f7a7681` |
| `en-sg`                     | `en-SG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6f473b8ba56bad098c21a0c0496cb312dafcfb83dc1a2e1aff21011f6b39321d` |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:20aa22d24e35f7d92ceac96d2cbab8ce46ee0ed7bb601f18fa867f1bd0bcf5ab` |
| `en-za`                     | `en-ZA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5e5ad2b016a1ceac500813e0a68ff4108ddf5a4ca98cb0aed4930b6d1e8920dd` |
| `es-ar`                     | `es-AR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b372d9e32e7b518bb9949d8db459bd4e300304e53aed1342aba65a054d4a4c25` |
| `es-bo`                     | `es-BO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d3538f3834c554ebebbdfe75e261a06f104dfa27143353601c3a6a3d41025129` |
| `es-cl`                     | `es-CL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0bb100ef5313b182a59c08949e4baf1086bde2c1a6bca3324c4e052f465f7632` |
| `es-co`                     | `es-CO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:cdab27080ef3ded55dcf89cf85bc2ae16de1372f84a42d836ff5f20612b68a61` |
| `es-cr`                     | `es-CR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e4ea51ffa38f347adc7c0642d50237cfa045683f52b5e3e726e4c28688231d35` |
| `es-cu`                     | `es-CU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f81c0b7f774d64e673a1311d00604f5e4837fdba4d8fb4a2ab0c8bb8b7fde87d` |
| `es-do`                     | `es-DO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:78035c54e649e34cd8276a402f9c9845e13bc40503da6c2f631698a16a049c67` |
| `es-ec`                     | `es-EC` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e4e4d9c123e452f8ae89bf6cc1292a406f7b482668e36b48ef2fbb29f14c4360` |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:10a4ddd279633cc8696b00be77f6e9309494a560244a325982522aaa805806e7` |
| `es-gt`                     | `es-GT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a603a8f9c1778808df5d14e3fa1c7e993ef9cca3e0b515a4d4586c2c3a1d14b6` |
| `es-hn`                     | `es-HN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4f539f8019c489623868bf02f3c61ed4b66d3a85e89250a9b484717a91e9489e` |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. | `sha256:20fc3806f08ad4e6fd5fb1f71318f1f5b591e2085ee4cbba2f25ea06135e5f6a` |
| `es-ni`                     | `es-NI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d65520a4f628f6a416171ac58341579fdffba97ddd2941a910bda385d31c735d` |
| `es-pa`                     | `es-PA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d38ea88613f5db6d6d9f879ef92a204c524bb27766848b825d1e6ce2a9b13cf7` |
| `es-pe`                     | `es-PE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:02205d1ecc29feed3ac8442dbdc1855c419749d9dcbd98028a5d1619166f0328` |
| `es-pr`                     | `es-PR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c9c3e1ac800120a14f472c8be62730a489e00f29df29fe770a56429ea1c09ef5` |
| `es-py`                     | `es-PY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:859c24c40e65bc19a866218466eb7678f71205bedfcb6ee3180b6cb721194b9a` |
| `es-sv`                     | `es-SV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:036f13d34005f5d6634387c9d13c3535724795b0d6cad832fc46363609fc2f11` |
| `es-us`                     | `es-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b8eb300d0a11dc397d0bab02e1f6b26de6091595fd052ebb607f196c28d16f1c` |
| `es-uy`                     | `es-UY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0ffba124ecd79777ca08055689a1d853916ccd8c8f2806d0001edf5eb4aa42fa` |
| `es-ve`                     | `es-VE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4d7caf48264eaf18bb2d07b0258d6f64b7c26815fdbdf812718dd8e88f1a6d1e` |
| `et-ee`                     | `et-EE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:310abdc1a8490990a99ce061f04c9d49cafb7a452fbfdc2790de6f60e1505c6c` |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8f209d30b2d148224b296c2d2c204b5970fbe7aaf5eb3289cf8b6644bfd78373` |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:11b718d4b86d606b198e47deaa25f6ce164cfc53267048e3d2dbe1bc8500cc5a` |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7a4264a0e9560e6aa3fdee80c3e3f55a0e26cddce8ebbeb7a9c87693ab451a25` |
| `ga-ie`                     | `ga-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bbc764ac08b2ef10ac58a8f9534d4d375109fdf16ab75c8cdbf2d57aa692d3e2` |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2d0a83b7bcf1cfc50cf013c95442519e5236a146b7968e75e129b3a5c33ad3a1` |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f0ee8f259035ac5dd9ef38807495d0f8d989ddbb8eacf83893f1fea22265e6b4` |
| `hr-hr`                     | `hr-HR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6101ecac9f5f35c1ea1b8cd8e52fdbbc1be2582e4f3e385c16509fd95a002217` |
| `hu-hu`                     | `hu-HU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9e94c4d6fff73058ce4eef609b8404430a429c6961648655c915cb2fac10656f` |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:44986ad44bb53eaf350e0865e62ea5ba7f37d1f5b52e388f61f56fd7afe8ff32` |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6b7aaa828d1b2d2fce1831e540e08ba60307088b90ca32e96fd002a67aff926b` |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1abeda544a7579daac7f8b8f8d34a2cc63b4bd3631e474315d424973ae024ab0` |
| `lt-lt`                     | `lt-LT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:455da50a7db591df7be69d7cd361a77734b9249101d8cf86b807f0350b5167ef` |
| `lv-lv`                     | `lv-LV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:676e17b6223e35d1897b46536e6f523e1d18b78f834b62ec00bb126ad3a2e71a` |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:dbfb97e52dc4b4c71dec1a9e622714f004b1e59d7900260e09a85bf15912fccd` |
| `mt-mt`                     | `mt-MT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:19f7f644ae3a0639fdcc53acc065d0e534b74c07f8c095418d4d4d444c566bf1` |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d3a13ab6fa2eb5d5ca0e3281b1092452650e9ede8749f6edcab990e3bbb8d198` |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7ad5e61f9a72c600bdc79e4c04ac63c239951ac4c0d44e02fe0607a6aff356cc` |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fe6a4812534d704b145b84fd8857fb3d9052f67fcbbd5d490c5902082e295195` |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:adcd34941d4ace7db01bd476d61c9bbafe071419932b4cfae5231cf202af3a14` |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0534a7e4b391f1ee666b248a274879c081496ed4939b0ad33154d8a96fd67f94` |
| `ro-ro`                     | `ro-RO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:091ea4a31652ff9dbc6259636f6c12b0ceb79a269e2cf3cdec677a1914b6a64e` |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5eef3ae8afb445e60bb913edd6eed1415abb0bfbc439978f69f4cba7b61c8e6e` |
| `sk-sk`                     | `sk-SK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:98709e9349d889b57933317005af42770e47ce8178a7d9c737d9fbdd81148478` |
| `sl-si`                     | `sl-SI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3a9139334c4780dc6f6a9b0f15fba5292e16ecf1f5d45fe49a9c8ef3b0e110b3` |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b29b2a65d83c20d65ba4e4fbca66f9fc07e536e161f90448c2bb360eb8de1e55` |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4302e1d979b24a23595ee2b1fd074a57ee36166ce9ac400a3deb397341ae52b2` |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:69be11a63199d9a6f63ac346e689051ba9cd5214894b110da2879aaa0f4a8e88` |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2e4167dacdcb2c9d91930356ebae311b6b33ceb3e85f908422e880edbd42da64` |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d46289ee9ba71c9c1dbbefa5da439e71310af74633c9d6d6d448d2ebee60da02` |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:49eeee500e07ffd3056ba8aab314d6c8458399a8c0d6d44ce1d9aebf50ddca06` |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5a3251ad6df9565d44dd422de4fa0d83a9b50c8a80ec15213403482940d2b2fc` |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:2c45dd90b0c19d7f12b1be44d3e85fe2603cea2389c2877b79d6de351839cf6a` |

| v2.7.0에 대한 로캘           | 메모                                    | 다이제스트                                                                   |
|-----------------------------|:-----------------------------------------|:-------------------------------------------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | `ar-BH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | `ar-IQ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | `ar-JO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | `ar-LB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | `ar-OM` 로캘이 있는 컨테이너 이미지입니다. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | `ar-SY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | `bg-BG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | `el-GR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | `en-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | `en-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | `en-PH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | `en-SG` 로캘이 있는 컨테이너 이미지입니다. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | `en-ZA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | `es-AR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | `es-BO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | `es-CL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | `es-CO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | `es-CR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | `es-CU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | `es-DO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | `es-EC` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | `es-GT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | `es-HN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | `es-NI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | `es-PA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | `es-PE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | `es-PR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | `es-PY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | `es-SV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | `es-US` 로캘이 있는 컨테이너 이미지입니다. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | `es-UY` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | `es-VE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | `et-EE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | `ga-IE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | `hr-HR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | `hu-HU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | `lt-LT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | `lv-LV` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | `mt-MT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | `ro-RO` 로캘이 있는 컨테이너 이미지입니다. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | `sk-SK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | `sl-SI` 로캘이 있는 컨테이너 이미지입니다. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |

| v2.6.0에 대한 로캘           | 메모                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `cs-cz`                     | `cs-CZ` 로캘이 있는 컨테이너 이미지입니다. |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |

| v2.5.0에 대한 로캘           | 메모                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-eg`                     | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-kw`                     | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-qa`                     | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `ar-sa`                     | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `ca-es`                     | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `da-dk`                     | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `de-de`                     | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `en-au`                     | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `en-ca`                     | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `en-gb`                     | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `en-in`                     | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `en-nz`                     | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `en-us`                     | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `es-es`                     | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `es-mx`                     | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `fi-fi`                     | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `fr-ca`                     | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `fr-fr`                     | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `gu-in`                     | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `hi-in`                     | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `it-it`                     | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `ja-jp`                     | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `ko-kr`                     | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `mr-in`                     | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `nb-no`                     | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `nl-nl`                     | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `pl-pl`                     | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `pt-br`                     | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `pt-pt`                     | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `ru-ru`                     | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `sv-se`                     | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `ta-in`                     | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `te-in`                     | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `th-th`                     | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `tr-tr`                     | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-cn`                     | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-hk`                     | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `zh-tw`                     | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |

---

## <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환][sp-tts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `text-to-speech`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list)의 전체 목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

`1.14.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

| 이미지 태그                                  | 메모                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.                                            | 
| `1.14.0-amd64-<locale-and-voice>`           | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.14.0-amd64-en-us-ariarus`. |

| v1.14.0에 대한 로캘                         | 메모                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            | `sha256:51042fcb326406ae8fc436ad1b33727767c6240f441bd9e0d92b3fe8cb9a0f71` |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           | `sha256:568eabb3cb2de77a44945837d04c065d8b6401e703ce9280d956dcced9df6651` |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            | `sha256:4c093284e6df02d7738e5c009f36ab13655c680bff4c00b3a4a2bd99bfa39a8b` |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:168f1431f162465bc4ece78bbe7f3a3687d9dba7f008a809cc3b5823c8d002c8` |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           | `sha256:ee8d7392a81a0ba98537816ba5ceee0c6db2017ea49b33d7eace62a92967a6e8` |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:36e90323b657d8e525d3b9c047522dc9837d582adf09b4761de9f191680f9a96` |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         | `sha256:0b5a63f842ddfe0f67c9abc22caa749c6710b978d854bec605e1c0591160645b` |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         | `sha256:9a95dfc74a5f5f05ba12cb148ad31b7c02ea858faeb4a776fd65b5cc51643a9f` |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:6302ea75b945fe1772b096dbd2b29d3ec1cd98b13365031f2064b7b6ab43fe6e` |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:6302ea75b945fe1772b096dbd2b29d3ec1cd98b13365031f2064b7b6ab43fe6e` |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:dea531d183eb4e6b6fdf7c09afe1b83f6af39f612f543975358b273b9a725297` |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        | `sha256:fe132ffc0c3688c9602d5d75038ce31a87fd3f98efe18dc05f55f16cbbaebc4a` |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       | `sha256:47149b26fbdb4dabd6e605302ddbcb6682576d25e4f0d6cd35d55f264c22d8e9` |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:0b817be772baba1cedcfa77a75d38c5eff6c846868f85eb0fe7504e4bd6cde92` |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:d59bb06fed865ca7850c419612a4485379a59a1f8631284047654b763e1678b7` |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:bf2e142ab852622562e77135a5ef3b52ed0b5a23a62f8104d6f299a4f741b3f7` |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:1e66a01f064f860879296173e69f9390c9d4e3a85ee303990df4b6fd9d3874ba` |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:19e76f8b8b85262ad4dc5892a03cb973f48ed9914324e68985b66201726bb400` |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:4c4e2e5faf74cc0f55b7081f68c24d88206eb989e0ca4c416d0d9c6b24bbd1bc` |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            | `sha256:d771436bd543bd5500a94205cd047e084cb43081829fcc22ee67c285b0ac06f0` |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:bdbb8fedfb129cb5822f1442f9b6c3ccc1054660d33aba29caf464a2b8174423` |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:b16a0f8e7af04af2f6c849cbcd8f7de75b105fce9d68c98e3038c9d93ab07767` |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:72a6cd4dee54518a1fdb02998861ca5b6c3cda74ceffce0929d911de98fa054b` |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:21be092ed49a687b86dec15349201b9cfab323a68879cfee4a70d063368c06d9` |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:21be092ed49a687b86dec15349201b9cfab323a68879cfee4a70d063368c06d9` |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:64aa702fdb963721dfbef0b425d400f2e10159b8e261048f7a81c962ed80acdc` |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:fefd0b03bc10493e99c2bd4a65b50aed0b585714fa3169dfe6ecfd63e2f41605` |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:65d8cc26d1b514a04adaf17d603edfa81fead9b456a6f5bef3cec80f09bef2f4` |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:5e894da2609c367d97782c2e3cf0248dc83758bcebf2114106ba2465638e1beb` |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:6224461121515e5fa5668a93601b790a784c9ea049feae56c6ce4241a6ba9f5e` |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:1a15fac1dfcbca48381882e9b77c0728d080f48ca05eb441c92efb22f1b539e0` |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:857d8d3c3f2117f7088b232413eecd188feb6d61cabfc068573c884de0c36cc4` |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:4aabd28e70e24fe40c3957b62b8f303f29d9e314c2dab8e622b7488ff74965ee` |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:191f7e12c71b6080d9e8bbd556cda66d2c41ac1f6cd795607e04d4f562978345` |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        | `sha256:76203efca3906dc2a1e11382446b4f043ff522c43914fc45b67bb47266d924a0` |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:6755c93fd4a71417884561089e8e3d1a0ea5162ff2b07cbe66c588040e6dd5bd` |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       | `sha256:8fb69cd140aa39a93d0fd80dc714f00864b04be3179d5b48012bec5484465f11` |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:37117f9c814bb4f1cb4e112bed9fbf9f1146e94043e51576fa0b05aa15601573` |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:75978e6959445e02127505ec7972cc54aa504234326888a1bbff48d12e7672cb` |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:e003ac4732053ee049c1429b149591499dcd4716d3aad23e2732355beb5a3058` |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            | `sha256:8eb007717e34e47f812569893333d5a3e97a7fad53498432d4979a0e3824c1c3` |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          | `sha256:533b3d9a9f15419d906e78b92c9e6e2ec442c12e8e3d012e0801422d04b63709` |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  | `sha256:2287a4963f0db435e7f4a0f85bc68ac17d8923bb6acd568153bb4e3181aa155a` |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         | `sha256:2287a4963f0db435e7f4a0f85bc68ac17d8923bb6acd568153bb4e3181aa155a` |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           | `sha256:6ad69e9f8de0d76b7c899ed0524129aaa66c6227a8b4eb3fe1d2a42ce4e10515` |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        | `sha256:a60f64d59b54fc1070db76ceaf583fa914cbc5a2806ba98f6d4fb2055c5a11b4` |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          | `sha256:4ceb3fdb5ae32845b5d8d512b5042c54e83c00ab3eec0a605668c0dc03cadcd5` |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:7498b4a7b5eb2cd1a626e4adece8469b8eefc6b5f1efc43e7cd4f207938fca1f` |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:8b4143c9d639aab14e000271cba0d1be87a0fb7f19b4c6cdcb37104d329f59c6` |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:aa3d365e1e5f4cdb52002589599d3e88b3298db8652b6cd9e63c7095838460ae` |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:8c5f06028f97934a2022af3b30a8691038d3ad9173a5cb2298bb461b46c8a58a` |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:a1d84e27829a5e9f22c244ec181f6eda39db1ffeec0d78e5bcb128a012c0ab54` |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:0ab1b70a226517a2d5547912a9d34865914b1271e9c7489e32aec5cf3d46489d` |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          | `sha256:95cea6795eb02919f742f09f591e5c670852ea4175e108f3c6c3b33cb2742694` |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:a179563ef1d0c562323e22cd9bdfcb932728f418beecc05207b0df2b8e95bad6` |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:d8932c474fd8b78a0c87a9827ae50eff2329c155e2dd33f52dfba582292ba1d2` |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:5035ac81ee98cf5df695dbde623d5d7957d64cfaba9d0f3acd77204ef4b441ab` |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:762463e1d6e51624558ca663702a92126f6d7faf2091b4af12ad0d3bbf703044` |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:c34ef2b1d489d9c695073884bfb59ae1e7c54944bfc1fb9b3a05476b9ca9f60a` |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:88fb5d5c2f90bb07fe49dfa7779f4b559e5a738fe9819728ed4b01e03f029bd1` |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          | `sha256:d24def1774d8cd5f2faaa223ce35e005c5ba664636b2cc7699e8ad490b3316b7` |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    | `sha256:d177af9d804bc956d2b7a9de707330cecb7895f5a32ade4d201e48477a479936` |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:12fe246c090bfc789b4622613a5835edc9e1c06b48c248252b0da4dbf996e864` |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:35418d54084bbd25101e1a7f0a6f4280b6d4aa2e8c1ba562b9a3c0a6acfa0410` |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           | `sha256:256a25e505bda3c2f392471b3996a5f2d3cf2eb9d14421322c56223095bffd91` |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            | `sha256:8085a021a30be2e30d51a76b18e0f31be083deb0fb19883f7668ce56a5bcddb1` |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:1db0e26b958662ca2db3cc8129130d55f1ad289ab9a10d41ffac3ababc3bd7b4` |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        | `sha256:d1ed46e3db9888aad0a3399404ecba46738c4583d76b3522406e4f36d9065615` |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          | `sha256:d927aed4c64e093f913471352df0d036c30eb7f72f743df75d7293e9eba88bef` |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         | `sha256:2d830fe71c4f00642e62245b84849bda947d6702ddbab45e69c8f94c985e0c92` |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:6aa7fd59cff4b43ddaa8437d319f3e56ba8a8c24f9363c783aa6a7454d382a68` |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              | `sha256:612963bbe3fb81907c6eac34ead489430a07d5c6389f30be5fc5c59143e228dd` |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:54a32e8ef08d57f841be6ee4c700a448bf80033e9931a6c7560e4f3fdb0ab6d0` |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. | `sha256:e9d8f52eae02bedbd0cbfdf2da02cf8dc63d4cf04bfea7d76c15fffe40d120a7` |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:2c4a86a91913edd06eaf01d4b1145eeb429616ccb7496b0b4197d5a8f0b3793a` |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:140d2cc1a4ffe7b1911254c39870727916c1544776bb7c406a7b6d8d9fed0ad6` |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:98025d5a58b1b7c367d7be4c285b4b602eb2819a203b090f48a6869aa7367ce4` |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:98025d5a58b1b7c367d7be4c285b4b602eb2819a203b090f48a6869aa7367ce4` |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:fc3ff023277fdc04cc879ba66e3daf9fdc4affcfdb79503597893e8227105c1a` |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:365693135e6fcc62f68d7bfc92e1dca1662dce31901e5833f7062f517b8bd2b9` |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:1a01b3470bd1298a6c323a121f4776de07e3747e6fe8e42af4be4845ab0a9b10` |


# <a name="previous-version"></a>[이전 버전](#tab/previous)

`1.13.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

`1.12.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**
* 최신 모델로 업그레이드합니다.

`1.11.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**
* ID로 사용자 지정 모델을 가져올 때 발생하는 문제에 대한 자세한 오류 세부 정보입니다.

`1.9.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

* 정기 월간 릴리스

`1.8.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**

* .NET 3.1로 완전히 마이그레이션

`1.7.0-amd64-<locale-and-voice>`에 대한 릴리스 정보:

**기능**

* .NET 3.1로 업그레이드된 구성 요소

| 이미지 태그                                  | 메모                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.13.0-amd64-<locale-and-voice>`           | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.13.0-amd64-en-us-ariarus`. |
| `1.12.0-amd64-<locale-and-voice>`           | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.12.0-amd64-en-us-ariarus`. |
| `1.11.0-amd64-<locale-and-voice>`           | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.11.0-amd64-en-us-ariarus`. |
| `1.9.0-amd64-<locale-and-voice>`            | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.9.0-amd64-en-us-ariarus`.  |
| `1.8.0-amd64-<locale-and-voice>`            | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | 첫 번째 GA 버전. `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.7.0-amd64-en-us-ariarus`.  |

| v1.13.0에 대한 로캘                         | 메모                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            | `sha256:8ff6360ba584d81b987582ce1c2cb6bb624cf68e4d71544805b9afc0401542dd` |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           | `sha256:da5037de95c00362cb1871374735778c3eb68640ae4cb6a260659e7e0a67c37e` |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            | `sha256:871140e57c126ac79c92c69572b86587150d1f14447c91152de3d4b10b3ef9f6` |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:7291ca9c579b1967cca941ce11321daa06ed6a9a1f0922d425d39f70a4aa8acd` |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           | `sha256:c8f34c3a7fc5af5141da5439b520614e039d133b6180e8157f12ec7279e9163a` |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:694eb294595700266355f8d57530ec3cccd4e04aa74dd630b96558bf2b481e71` |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         | `sha256:f875435d8fadb56df2123d5aa1ceca34990d00f4c75678eb2526b83058972717` |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         | `sha256:c58359bd6e6676e23dda181a86caee1771366b0329a44fae0f363bbd381058ad` |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:c8e615d40c6e96216b90e329bf7185060de646db1e92fd1fdcd344a52bd86b55` |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:c8e615d40c6e96216b90e329bf7185060de646db1e92fd1fdcd344a52bd86b55` |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:e8e3f04f0ee74d4247ffb7c69e54559f0cc6db66a121406e06ceb9dcdc3c4379` |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        | `sha256:15112a55bc7ccb6c29ee0a1de464fa6352a0e9953399032e5c8a0d29ec064af0` |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       | `sha256:9a77bb5451889f62b8a146bfcc4a412c1cef95fd2102650528ccee84a08b25b8` |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:90ee1094fbb8e739788545b3b9f4fabad5b4dffb5b7087cfd01c3b21ba1b2473` |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:43b7d3c87162129253fd5c150307a5d9dc6ea28b8fa19776b66f4aa7a546f43b` |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:75a4423d5b24136efdc5de28a7a5b50a3a09b65b3824f86dd50a95eefea7ead6` |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:87e926f7db4a27870c735c80ad801bc5480fb2665594727ae760c8c287677088` |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:3fbd6a824831f158762036aa41c0397f7c1148150a4dc045db5f19ba840e74b6` |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:646810c4129f8919ff56d91701b488e229bd12b3dd9c89a1635868f9340e00b8` |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            | `sha256:641abfa96380f142d4b2f9145cd02886d44f01bce68614094b48c1e01b50ed59` |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:c0acfffceae9c1ff5ad305d8b98929d9c65eca25f49ddcb8999d7de6118392d2` |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:fbdc9ef0b4308ffce87d6ff6854814804b3cafacad6c4dc5cdac6a47c6de7975` |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:f31c40c9db2f1e826686649e748d0b2be0c00abcac62c2aae5b8981b0d8c681d` |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:1232b798aae3ce68d1e555a5b35142bde5b4c871488f8c82c3d7c0767925afd8` |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:1232b798aae3ce68d1e555a5b35142bde5b4c871488f8c82c3d7c0767925afd8` |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:5fd7e9fbcc84ab467d04e95b18f5411579ce2d9a153b7f6e396f2412d08898dc` |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:5fbbd16ab58b7f2440778b258bb0cd966286de0dbb3ce7f5e54d0f244f63dd3f` |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:806b92916b2fe1e7855023a009742033a48cb7eddde84ddf7c93be93b9621026` |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:507d9f40dcb846a5d1511a5e9e1cf94b360b1d9922f4b1143c3146d1b3bc69a2` |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:594add691d03d02fa5925f817e6a25c091fac1a924e0ea4b626e0fce858a78cb` |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:09d288b58fea080689471618227d1cb3ccc467f2edc9477eaaffffb09b3d6d8b` |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:7019c80c88444a60bf1016eb66284745dc8184b051685df4a1b3c40d32c8ad7f` |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:eed46588733b884c330fff1ff7f4e3e3fd6416cb340ebd80e44c4b3d1e085e55` |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:00f7a854c4a01bdbef88e0b138c97f732f1c6008a8b2c1722fc8da3a91fa79a4` |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        | `sha256:5f32e838a0925c560d2961a42487b99dd7e79e04661a7711f905d36c55973fd6` |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:6f3d3237c990f8f04d4c8f488746f74fa94edd2c5f1def758af90b2be251900e` |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       | `sha256:282e2e48c1147b74d927e801534be52b1301a081ff881994e85bb9d85b6e85fb` |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:16370c22530c93fc6c5ebeaf10663de7c3d45db58eccc716abd5274b5bee56d3` |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:e6541e82b8555f748f1feb5eef1c0ebf884245c5448f0ced46e6f25dabb925a2` |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:a4cf0bab208a31da3e796bf353969dfd98184b30e0cf713df49cb4fb07ff568b` |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            | `sha256:4417d0a14098b564eb4ba91772eb7ad5976ac52b0b59ae484fc3a88017e0776b` |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          | `sha256:da086a3e2bc3e17f4e44165055fc61679e9356688d3735ee8cfd81e6265b8622` |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  | `sha256:0c9915bf34e3045e39aa245c597aa7223fbf6100d7e20cbcc1bf131f89ee785e` |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         | `sha256:0c9915bf34e3045e39aa245c597aa7223fbf6100d7e20cbcc1bf131f89ee785e` |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           | `sha256:fc08c968efe882ed11ad0ee0755a9d43eff88b96da8ec19e7a5c071810c84d8c` |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        | `sha256:b6ad73f07efd1576e166b4d7e54a4ff419bfedc513a175fbb968389eb289a4ee` |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          | `sha256:3aad5ccf0c155593934c29a3e50502bc80b0370fa29626e67cda141d4bf5ac89` |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:01502f274bad378e6e99bed5f80fdb476880ce04e8775ca56d338de2f2d43e8c` |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:fdc20724194612d99e8339d25c72c7fe937ad741abe46d86def6c62880913c2a` |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:abf0e442ec972e25743a8af55da49a6fd5bf2ffd6ca09619d68e4dc9f9db779a` |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:9eff152cd4bea6f9de3b101c0704f37c8a061e060287e3f9f8fc2eb28d7dcec7` |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:83aa3c569f7598843d4957f075915ac2635d3aaf577ac1158c12a1238dd7e148` |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:ea404c7857f9df0a23cbf3fac12ae00f11c32a6822d91078a321302f09f01082` |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          | `sha256:d4c15f7da8e03650395489b6cb6975d59322b1bbd2c59957617f0c0a297409ee` |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:cb2c0fb57513c66e00bd6b8cbb44882d5bb7d483c19784d2b1e09511d58842bc` |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:7b9a92ab8a9856f422e65b428b845571a059c0923dc1c348134f271ed7a4abe0` |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:cface74973368a78d75a2a079214aa748574c5f037b0c4189888269b6016f230` |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:cc3e74228002b8d4e7dc487ff6f930316ac5d7a93f97937942a23f41b484ba8c` |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:dca613867e2f559d9485f9ba553ecea3de6d4b2779d4eed0ce1e53e7f7939773` |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:791ac2b3100725f909cfeceb17fc0d5fd1022242db45ba455d7ea088d76ac033` |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          | `sha256:3b93df188bcbdf9416d203a7e30ade8908728316666cd3451a5f0320cdf219a9` |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    | `sha256:d2f636e35e67be196a4ad79f168e4df74d2f00d5b5c6123bd61f9aec72bfd1a7` |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:247a4c6025faced1be1738d816c1bb74b23bbc5d49458f9afe95dc32ab3ea71c` |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:355c3a0f64f003d0a041a757b8ddcdea8130b6a56a7c4003a68ba0412400c446` |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           | `sha256:55fff1cde012a7791c756104ba68a360e609a765bd776024a9f5f00199f568e5` |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            | `sha256:7f80965dde85e3a5aae9f69561c296d073289f0b6aa37e95ff0aa5192a5b7f90` |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:1bd43f513a5b2752c44a107e1898459cdda5d7267ec21f379679d411700e5189` |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        | `sha256:8062e2479a6a3dc17b8342c07a94a39dd1e1f788c1def0a1ab55a885b491bbab` |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          | `sha256:6ce345df654bd1db213c16c866b608037dcefb1d056fc14727db3b9e21437762` |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         | `sha256:9b9c8ad7f8621f887f3e9fda26f43995855dba76831fdf2598ef383cf3d20f39` |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:2e45f019df702d8788c1d9c20ff75cfd94aecaaf6facb9f41b642ef1bfe7d318` |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              | `sha256:3b142a414ff9f30ebef144e22bf979589600f226442d2f882384695795739178` |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:23b76501492c9b60e8888eda2f6b0258859f68ed6ff7fb49bacbb18cd5f542ed` |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. | `sha256:e9acc58168f6800d9dd11cbc569c9d279ecf28f3d17c702528d25f67edd447c9` |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:85e7d7ae77d41195de5102b772621ef34564d40fad224a0ed21a8fe8daf98b0f` |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:1fcba05138c0e5bf36447530311800e2d4044824b5d893439a12f3ebc6380135` |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:d02bd8759e085abbc95725aa4f70f124c4505aa0856a17696a1555b2cf64512e` |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:d02bd8759e085abbc95725aa4f70f124c4505aa0856a17696a1555b2cf64512e` |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:a3f68538088b5b07f4dc27239fa3a6308d949c2643638634c74f3ee132bca911` |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:bb0696685f3a90fe6898ff1487cb0c5957e02f3c63cdb7d02394b5c061339bf3` |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:1772b3bc8b166f429356b00d07ca438202c75d578b6d1655351b9c1e06ae1424` |

| v1.12.0에 대한 로캘                         | 메모                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            | `sha256:987e6b3e9e13570eb29117e87829a4905b35c712a0f36429dd6404793af31627` |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           | `sha256:7d1d3c337b7e3bdc6ae2b3e074828ffc3c64ffc0ab94abcb89896e623148d963` |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            | `sha256:cf01bea4f1f6b7112871da84fd82fb7e6de106c11cc933f21131385173f1da09` |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:d6060a1e16cbe40990677b3c46f14dc619ee6887d39a4af1cac51fba2baca9a9` |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           | `sha256:5033185bd60257033989fc4ff124c69b1dd02d5b99b79ff5c52ae84572095693` |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:ac9655166f8181db2d0e6684cc3a5b6e089da788f17c78067af2cf061c8db660` |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         | `sha256:90d222aa43c3efac04b9bc3e746b9ebea446cc16c3bdbb471b81065edfbc3023` |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         | `sha256:0c08c10f559c97eda9a0a3f8527f8b05810a53e8a3fd2b8e9f2ab35f587d6c46` |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:bf54713a1691f2378cf701a1f68ed0f4d32adeab25b2cbd9493f753d56d13e39` |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:bf54713a1691f2378cf701a1f68ed0f4d32adeab25b2cbd9493f753d56d13e39` |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:b94c79ace4b33bad944f88259da4dab5f52da7e78af85a8b6eee0e99ed05a387` |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        | `sha256:3b331be0a6eb32b12d5c6244691bd51ee1d6b218bd3dc066c0f9cb5b78864e14` |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       | `sha256:1bbbd1214119d2e02539f7bef8eeba48e86f17b968f2532a7d96e96ef40ecbe3` |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:aa0a38fd20cabcf33baa97b3a88f354d01055f57ed9376bf98b7ea0993333ffa` |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:57966c65522862572e07ba474fba7e2c6038091cc1b8a35861645dffc2fc5f5b` |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:57c6ff08057f199a8eb75668f8ddce26b92c87a7e01e9003b74339b98ea438c4` |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:89a8b8b8e900e6dbda665d245fd8a911d6e3286ee16a92e46f1993dc3667b631` |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:18347ce1c4e4e21180f64c27bb4bcbebbf52597e25db7e24dbeb57edcea56109` |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:015905bd42f8fb4ec575d971ff2d710ac5f904da2b84909270d3a7e51f5e3029` |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            | `sha256:4a490dcc6be935178761f14edbdd0c6e4036626046dbfeda002336d871c36fdc` |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:f26fb9b32ca82aa00c40f8824ed5d3d95ba1be5a10343e8649946d9468f9f74f` |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:43f5fffad77d3446bc08922df36e244115ecf6090e7c48c42281c2fa62d23b90` |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:0ca4a07585a61a6e15c7fd951b77bab6b5cf8934ecff65fe4ca6cfe8e47f351b` |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:00857cb570528dee93f7c9c7f96bb2e11763ff6aa9cc7405a05bcbad3d85b08d` |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:00857cb570528dee93f7c9c7f96bb2e11763ff6aa9cc7405a05bcbad3d85b08d` |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:3d7c911788bda58225a7100ba1a9afbb61e0a9f8b7633b383fe6e9faa48471d0` |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:251841a8399bd168644460e3ebf6d92f093dc8ea60f9defdc663a7e1f60515fa` |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:dbc6bb44b283902755907d9cee5694f880c95c6cf939f328059d826fefe53dfa` |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:9f11111e24b554d907d36516d130324d64a477b512cbd7faffa0b7d3895aa538` |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:04add8f669539cb2522237a1b01d263b30ed609332cd2ff6dcf2c88fcd24764a` |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:d375f7eea3592e041943a56ba18bec9ebc4bba1c99dea4d583f2012aee31cff7` |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:437e38d9cb97d2cee27890529eccc1d0b96622749c83844b89c50dc119176b61` |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:b6c0937fddd2e4d39a7cd96628a3d7d6004936f356cb553942e4f7dd48824b52` |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:5a359ab047d811996cccb9f3f95a59a7e023ee5be72ff0f509e7ebfeb0d3a07a` |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        | `sha256:439bab9f2933c73e52e78f1683a027e81a251c32fb8aa49b6cd8e7c9b2451f15` |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:ca798c5d25454b60cafca44f7f7e32896146966a8de94d00cced06235e38bf00` |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       | `sha256:e696a65a7c40209a8dd8d9ff59ca5334811e993f5b454f6d741ce0fc59258e07` |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:ab6e7c023ee6cef95f8dc4eeb3c804ea1b8af937cadb17efcc12e5b18adcfc69` |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:cb8f51f75a0b93baf6efb1624d7d01cd736926769922d61a63773eb3a1097399` |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:482aa2eb44f41294780cf299e6105a1a3105a2d8065233b34ef1837879f95b7f` |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            | `sha256:2f24ef0e620eeec3ea14262302d22cbb539a8afa85d356ffa446ca9cfd723b31` |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          | `sha256:0338f8e24eddb819c45909ec3a92c430b1d5ec1567a71495cc19c9a74382b224` |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  | `sha256:5d7e10ab0fd18d1d163c31341765b6f65bb198048424aa622b854172e845726d` |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         | `sha256:5d7e10ab0fd18d1d163c31341765b6f65bb198048424aa622b854172e845726d` |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           | `sha256:08d606969abd0165a798a8e0061e6439d4a33ad6af71aa58a1228e98018e7da9` |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        | `sha256:9613dbf91878054e2ab79d5d9c8f3686d5fe80b16c40d38db9aec3a2c3816555` |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          | `sha256:037ca355d8dcf9bff5fda9b9a4a9c2a54a03f3a48c378693c11437a36a245836` |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:647b92d1591501ed032d67cf2cfd719e95c24ffb624143d301c2b6dc5eed7397` |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:c35e40ffe1352870b9f177dcf70c1cd9eec9f22f92d35080fb5baa1fa65eac8d` |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:4fa1436d83439cc9672fe82e35f57a366d2c1a6eb1df1f9f9175d3a588b09610` |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:82f13a16e7812857143d311b5443cecfd7c199a88235728f437ba03e7cd92342` |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:565bfa8bab3a11608fd5fecae1a0cd655b4508404c354d5574af0e88ff1aec76` |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:2b9ab2e9d946e152b46a634ae291fedd220c76a7ba133346e80b4b19bcaa1422` |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          | `sha256:3a05e09241b43c149132b42079f486f0a076d493d4e4c7e4a56b8a030c5b55c7` |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:bb018c3c7d65c825c1755c510aca7f73f058ac4dce236dc114131c5699a1cb61` |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:eb2f7dc4db0981717b5fdd16c290ecb8135bd5ae409e0b569e3de34a9fb9f071` |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:098fabd9284caabafd4af526d52d5fa70ccbd0dc0e0c658753d7c644ab3bf813` |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:c7c033ef39c3da6c82ed1870e6796f501654403605268bcc8136cedd37c5ad1f` |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:2da1e4c972b47efd82a28b4a8324637d878b100bc730f90e9c9d16a6ccec75e9` |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:2f0ba437a2f7fbce9923a4da986aec53ec0ad3d52858e6aa12a7464cfa190240` |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          | `sha256:847e60ea915697dd038319a071757e095229ca0001bf05f1d922d4c52ff4b22a` |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    | `sha256:37914d4ed1a12d3999385592d5dc0c0ed11148f71f09e11a1bb4c9394691e3b7` |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:3a800cee6d1520a1c0502d9b682a7e0f98ef01de58bb39ea31573a9711ef1271` |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:70ad01c5cf6da459e0938c1da17348624e38d94b3ce4f22e181b9516262e961c` |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           | `sha256:8920e7acd70d6d550b66eb3c23878d070dc98219bd59fa8fce1abaf622da4c2f` |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            | `sha256:c17313ddab7e7d9c2777d4a19df65b34da4e30e52b4a21f81e5c59bacdfce979` |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:91315b4a62bbf69e117cdb4ef88facb02d3ee3d436a1e313af94ba6cb0b8608d` |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        | `sha256:d04761de48003062617397de4c4c5f448cd9b4bf57262587d245277d4e408431` |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          | `sha256:e41002cf7f56d948d2737adc23c0750b430d553d78abb2ac53c42427de971299` |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         | `sha256:5f556a0c113750d8780c09be8af7db28bc29784056d22389aec61c256ab9cbcb` |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:c893b27edd98c0760b7e510c365018e333aa0976ef742f7714ad59c92950a8e2` |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              | `sha256:bc34adc094183bbbc461e0350d7aa8e5140ece5e89cd9e77c60f2c96276037b2` |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:20b23d368f83d4b2926b6d8529d23c4dd84727bb063593d549fb959ce3ace8d2` |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. | `sha256:cb638e72c8966204ab9142810b94cf4c2da54f3fd5917ae0e12a11d28a4253bb` |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:041a22b054b0acf494ff3085cdb2cd2eb4faeb7b692027f1723d27c341a8ee33` |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:7d9d2766713507b04c0bf3332367e867524ff392b693f4eb8a8c003a4dfc3bac` |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:b6dfbdbc5ef0d91812d96c88393c0ae4835eea42dbba4c3d36ab9c5e806bb772` |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:b6dfbdbc5ef0d91812d96c88393c0ae4835eea42dbba4c3d36ab9c5e806bb772` |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:9802fc4a9656063cb9f215ca757db5289960d323244272ce280db0395ddd46ac` |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:05f50dffbeb17e4215a5a53cc0791d825b63bc1e2b007b00797e5d0e1b1d6d1e` |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:e96f4aecba6e3c0741218f3e1aec35e53147b12543be9fdcd76ff98d4c34cf84` |

| v1.11.0에 대한 로캘                         | 메모                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            | `sha256:7ba558f444ea482eca87b3e850e9b416c71391282b26a590d1ee3d9a81350188` |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           | `sha256:7f0afcc205340dea7ffd959812dcba6a11448f6c5c1ab55c1422a360bd876137` |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            | `sha256:fde80af0e2e8e49b49ddec5f1502a246cf308328738d6f572f0043e625673782` |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:fb2b50b128aa84ad0cd05db2462337d316ff2d2d78f393c5a9dece588a80654e` |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           | `sha256:9dde22e5e2164bee77aaf9fe4e8fc141d9dfbe3c92c4b07da969d34aa14f7fd0` |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:4a756cd10ad21dcc2b1c7006ec961f7e267f6d2204d9ad4efd6d4730d67a4ccc` |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         | `sha256:9d531c162c4279830f99ef0d44a506a023a0137723aab3adff7a663043a1c576` |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         | `sha256:353d07168b4a44fcc12a0239f5bf20e2d29365b9abe26b9b844fb6194e7c9bcc` |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:d76ff817fc154ba0f5ce1abb93c5a0269fe5bf7b4feb3b3fe9fe8ffe6fd4fee4` |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:d76ff817fc154ba0f5ce1abb93c5a0269fe5bf7b4feb3b3fe9fe8ffe6fd4fee4` |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:8e22964dc4b77c05f602f72b0e706a534a89a271c4d17b5117af122c34df9a18` |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        | `sha256:fcd6288d5fd4ddfe3d3e65e860895f6f7a7e81216c7113f71e7b1b01eb501150` |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       | `sha256:e49a5ec17b696a3a73d10383d369a2ff88ccddb812898a2eedefe6e6a009ce5a` |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:b7fb06bd992982c7e2e71da217898da45b742aab08e901bfcef9c43acf546bc0` |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:efd7d85845ca597937b8cbea7724cf31797855e0de5f30d66984ab9bac688152` |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:8211077d55b440dbb26e42db6322b35ef6ec88e8c2ec6647831e0046668ed8a4` |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:f6e924720b71d8f9a1edd4f5f2280e9054263eb79ce5364e03c9b802ad92f2dd` |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:de702f70c53e4c1647e5fdd3432d37dc8972e069fcc103a1fc2b0be70f0d6d71` |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:5077cb575ffeb64e3d70184a68259438821891f6c9865350d2f887ea43ee99c1` |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            | `sha256:c6f734cc12f04697a4d9b2003c46c5a4efd8c68da90838debb5628d9f8e70104` |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:f5a78e857bc1563cbcd74f7b856bc2e4bd981675b397aeccfa134137f1cd3392` |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:667729cafd6bf5afe071a0a2989f836943e3bb6d3d1ebe35b7fab9bb311bfebc` |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:e46533f972235f297dd31fd338638f5117e3f04fa4a434d678d1cecc76db023b` |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:a8f881b60021468dbd96d9733606bd00f7f889ccb523d1773492a8301128e596` |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:a8f881b60021468dbd96d9733606bd00f7f889ccb523d1773492a8301128e596` |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:53ee105977b6440f1a7fe5088255a9c6e437c39b7c66e5cd4aba984a1667b25c` |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:537d2018f414b825aa9995d2e15e0bdb0119e45f2c6fc10d326e3df6f49ef713` |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:05da3347d457ca040cbe9b3e3d586d298a844f906b34ef7b6d768c247274ff1f` |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:481cc43ba896a0d3291903af84120fa618130e2a2c8dce9b0ef23172b66858a8` |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:8cb9d071a1e01dc3e63d5f1b1c040aa6fee94488a5bbd60f2c91704abfd921cc` |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:da293ff5c49435c020044614962382040f41b6339ec83677301921a6dabbafb7` |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:9677d5bbbbe0c73df93948d4ecf3f367830ef9e7cfb3b42557cf94ec514b6c68` |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:a5109a6a659aa321892d4c6844e102ac72990fc2d58f32e45a072b291849fee8` |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:f8f1aa8168660ee1c21dfa4a92530bcba6f1aeb765cee9087a6cc29d7c332a8a` |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        | `sha256:450f0f75f26299a89a80efc3ce93b42d6447a32022aaf4f88edc935e56100191` |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:7b18adf90e6db8f8e2c5955f38aa0adfbdbd10a9a95e2cf13035b9c5416000e8` |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       | `sha256:ec3c238d0bfc3d26f20349ade1c4e19805b796f4bb3d5bf1fe4a9801b1ea1471` |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:7b13613a9c5260e03ed831c79e5538633b4201867068ca0e1624b2c39fa8cf39` |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:162c777447e3077438865332ac34df956be43c0429ce9962bcf5df9b210dbf01` |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:8cdf28dc31d40a69eb6720fd42b8c19792f973c4e58760abbb6573c6129c81c1` |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            | `sha256:3f9ec9201deca21f5e3e561d6dd673ee6fb2a7f13b4cae2985ffb69622994b99` |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          | `sha256:c6de645816587116384ada93c02257f257a13a4b696e1bd8aeecebb9a9668f15` |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  | `sha256:455ab4c9bc7c2457e2e48265065789a54513e07a1dc9e4bc108651f118f1570d` |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         | `sha256:455ab4c9bc7c2457e2e48265065789a54513e07a1dc9e4bc108651f118f1570d` |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           | `sha256:6ac24252194f91cd815736bd8be03fb95e0b965fabed5de4c631e99cd917da97` |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        | `sha256:bf20ea91d922beb682e321a31cabb11ebec474f47edcf4e3787882e2a204b3b5` |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          | `sha256:859bef31e5d882b508154ec00632e5e1e95bc8ea2dde6198f157703d759746c7` |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:b6c81ab4bd0aba217977b0bd83a8a65f7c09b5954cda0870dea15aec0dbbe1ed` |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:e216a1390a0d4d9f111c56c1d655f36614947eea18d6ec91a9f6d050048b1ad4` |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:ba2042523ea1fff9d2c8b805ac36075169c3aecce0c965d09e326c06eab5a36f` |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:fdbc8f59fc1c4b52c11d248ee9a5d7fe4e58343f036e558fbb33282e24d5b71f` |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:08ea0ed61ac152dc5caea2d4cacc81175c272cb4a835eecaa7f8e7c5485740b7` |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:40ff95e5fb92278e369b4f37d7dbb109431ecb115b1b9516aa887e6bb4fd030b` |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          | `sha256:70cfe68a81ee860136cfaed35909f522c28c20ef5514c2d9d96c283892f8b7f5` |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:9941cda0e65884900532e6a0ba68e475f373277105594bf09e67225450192d3c` |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:c71d980dfc70575421d1589c74e8b3e7cc036551412d0ad0f89dbc543252a405` |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:e5fbd98a70eb1dcf80c446b48b8f17e47ac12853bb255f0aed174c78196de257` |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:9f57f9847f2372fa341cf037410ac68ada1c3075ab9b77cffbcf01d199f7c1f5` |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:ef546532c582392e6ed47df55c0fbfa6dca6d3e523547089263b57354a4efb1a` |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:116aefb76ddf39bed379c023c8260d2607314ad1b31ddef83ec2818ad9805a0b` |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          | `sha256:6968fdefdd798adab48faeb40857c8cdca55712dbf4806703e11ccdfab874051` |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    | `sha256:48add20e3c147fb4be26c948841a12736c8b10d053aa7d25984df8e4016e939f` |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:ce5c055aedb3f9323f41a9de8d8f3dd23fb2ad0621d499f914f5cb3856e995f3` |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:badc02f9ccdee13ab7dbd4e178bd5c57d332cc3acd2d4a9a3f889d317e0517be` |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           | `sha256:763d4fe74b6f04a976482880eed76175854f659bb5bfcb315dce8ef69acead2e` |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            | `sha256:73374363f9b69e03b8b9de34b319d7797876a3dae40bdce0830a67cf4bb4d4f2` |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:317d6b5d69f56c9087cd1e8004e60a48841b997937dcdccc97e7c0b2e2ffb631` |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        | `sha256:d1aaad1d5f32a910e245e6c117178c0703d39035e4053fe2dd2bb646fc02f7b8` |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          | `sha256:0224ac3b2de11c4f6ef65ce0bdcd1b9c4112ea472b3bd5626fdff47a5185f54c` |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         | `sha256:16c7384bfe210f30e09eae3542a58ff9bdbfa9253fdf4d380a53b37809f82c7d` |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:5c7786c00a66346438ee4065e3eaa03ef9f8323ba839068344492b8a3b6d997a` |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              | `sha256:6925744597c45eed8761a9597f3525f435dd420b67ff775a73211fdef9cd9cb2` |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:b38a3f465062853b171d2bce6c6d8afa14d223e24bfd5ea0827e34c26a09a2c8` |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. | `sha256:fa9555e2f520340457d5cebe469af40516237fb9398a5f90046565655b2862f8` |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:d7eeca43e45d09a1c22611f865fb1f8b42673688a11a2acffd37a4e08a7fd8c4` |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:ee7257c0179fbe015324b4d29f16fe93964e5f1901906240477fb1d820a500f2` |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:dfa4effbf7d0ec6c9130c142241b3e247e226e13dc218fd44f986ca1c7fff2ed` |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:dfa4effbf7d0ec6c9130c142241b3e247e226e13dc218fd44f986ca1c7fff2ed` |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:263153fd6e05970e04af9a9bd95fb13591f0138ac030a632a6a78d95936afa4b` |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:b8289bb550b9328d83d6a7ec93bdf9524087222f537a55db0b2eb5402c2bf663` |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:af4bc0ef2211f69a92541bb14596341375e1003aef541aefcea7843192046b4c` |

| v1.9.0에 대한 로캘                          | 메모                                                                      | 다이제스트                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |

| v1.8.0에 대한 로캘                          | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   |

| v1.7.0에 대한 로캘                          | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | `ar-EG` 로캘과 `ar-EG-Hoda` 음성이 있는 컨테이너 이미지입니다.            |
| `ar-sa-naayf`                               | `ar-SA` 로캘과 `ar-SA-Naayf` 음성이 있는 컨테이너 이미지입니다.           |
| `bg-bg-ivan`                                | `bg-BG` 로캘과 `bg-BG-Ivan` 음성이 있는 컨테이너 이미지입니다.            |
| `ca-es-herenarus`                           | `ca-ES` 로캘과 `ca-ES-HerenaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `cs-cz-jakub`                               | `cs-CZ` 로캘과 `cs-CZ-Jakub` 음성이 있는 컨테이너 이미지입니다.           |
| `da-dk-hellerus`                            | `da-DK` 로캘과 `da-DK-HelleRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `de-at-michael`                             | `de-AT` 로캘과 `de-AT-Michael` 음성이 있는 컨테이너 이미지입니다.         |
| `de-ch-karsten`                             | `de-CH` 로캘과 `de-CH-Karsten` 음성이 있는 컨테이너 이미지입니다.         |
| `de-de-hedda`                               | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           |
| `de-de-heddarus`                            | `de-DE` 로캘과 `de-DE-Hedda` 음성이 있는 컨테이너 이미지입니다.           |
| `de-de-stefan-apollo`                       | `de-DE` 로캘과 `de-DE-Stefan-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `el-gr-stefanos`                            | `el-GR` 로캘과 `el-GR-Stefanos` 음성이 있는 컨테이너 이미지입니다.        |
| `en-au-catherine`                           | `en-AU` 로캘과 `en-AU-Catherine` 음성이 있는 컨테이너 이미지입니다.       |
| `en-au-hayleyrus`                           | `en-AU` 로캘과 `en-AU-HayleyRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `en-ca-heatherrus`                          | `en-CA` 로캘과 `en-CA-HeatherRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `en-ca-linda`                               | `en-CA` 로캘과 `en-CA-Linda` 음성이 있는 컨테이너 이미지입니다.           |
| `en-gb-george-apollo`                       | `en-GB` 로캘과 `en-GB-George-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `en-gb-hazelrus`                            | `en-GB` 로캘과 `en-GB-HazelRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `en-gb-susan-apollo`                        | `en-GB` 로캘과 `en-GB-Susan-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `en-ie-sean`                                | `en-IE` 로캘과 `en-IE-Sean` 음성이 있는 컨테이너 이미지입니다.            |
| `en-in-heera-apollo`                        | `en-IN` 로캘과 `en-IN-Heera-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `en-in-priyarus`                            | `en-IN` 로캘과 `en-IN-PriyaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `en-in-ravi-apollo`                         | `en-IN` 로캘과 `en-IN-Ravi-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `en-us-benjaminrus`                         | `en-US` 로캘과 `en-US-BenjaminRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `en-us-guy24krus`                           | `en-US` 로캘과 `en-US-Guy24kRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-aria24krus`                          | `en-US` 로캘과 `en-US-Aria24kRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-ariarus`                             | `en-US` 로캘과 `en-US-AriaRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `en-us-zirarus`                             | `en-US` 로캘과 `en-US-ZiraRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `es-es-helenarus`                           | `es-ES` 로캘과 `es-ES-HelenaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `es-es-laura-apollo`                        | `es-ES` 로캘과 `es-ES-Laura-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `es-es-pablo-apollo`                        | `es-ES` 로캘과 `es-ES-Pablo-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-hildarus`                            | `es-MX` 로캘과 `es-MX-HildaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `es-mx-raul-apollo`                         | `es-MX` 로캘과 `es-MX-Raul-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `fi-fi-heidirus`                            | `fi-FI` 로캘과 `fi-FI-HeidiRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `fr-ca-caroline`                            | `fr-CA` 로캘과 `fr-CA-Caroline` 음성이 있는 컨테이너 이미지입니다.        |
| `fr-ca-harmonierus`                         | `fr-CA` 로캘과 `fr-CA-HarmonieRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ch-guillaume`                           | `fr-CH` 로캘과 `fr-CH-Guillaume` 음성이 있는 컨테이너 이미지입니다.       |
| `fr-fr-hortenserus`                         | `fr-FR` 로캘과 `fr-FR-HortenseRUS` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-fr-julie-apollo`                        | `fr-FR` 로캘과 `fr-FR-Julie-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-paul-apollo`                         | `fr-FR` 로캘과 `fr-FR-Paul-Apollo` 음성이 있는 컨테이너 이미지입니다.     |
| `he-il-asaf`                                | `he-IL` 로캘과 `he-IL-Asaf` 음성이 있는 컨테이너 이미지입니다.            |
| `hi-in-hemant`                              | `hi-IN` 로캘과 `hi-IN-Hemant` 음성이 있는 컨테이너 이미지입니다.          |
| `hi-in-kalpana-apollo`                      | `hi-IN` 로캘과 `hi-IN-Kalpana-Apollo` 음성이 있는 컨테이너 이미지입니다.  |
| `hi-in-kalpana`                             | `hi-IN` 로캘과 `hi-IN-Kalpana` 음성이 있는 컨테이너 이미지입니다.         |
| `hr-hr-matej`                               | `hr-HR` 로캘과 `hr-HR-Matej` 음성이 있는 컨테이너 이미지입니다.           |
| `hu-hu-szabolcs`                            | `hu-HU` 로캘과 `hu-HU-Szabolcs` 음성이 있는 컨테이너 이미지입니다.        |
| `id-id-andika`                              | `id-ID` 로캘과 `id-ID-Andika` 음성이 있는 컨테이너 이미지입니다.          |
| `it-it-cosimo-apollo`                       | `it-IT` 로캘과 `it-IT-Cosimo-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `it-it-luciarus`                            | `it-IT` 로캘과 `it-IT-LuciaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ja-jp-ayumi-apollo`                        | `ja-JP` 로캘과 `ja-JP-Ayumi-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `ja-jp-harukarus`                           | `ja-JP` 로캘과 `ja-JP-HarukaRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `ja-jp-ichiro-apollo`                       | `ja-JP` 로캘과 `ja-JP-Ichiro-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `ko-kr-heamirus`                            | `ko-KR` 로캘과 `ko-KR-HeamiRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ms-my-rizwan`                              | `ms-MY` 로캘과 `ms-MY-Rizwan` 음성이 있는 컨테이너 이미지입니다.          |
| `nb-no-huldarus`                            | `nb-NO` 로캘과 `nb-NO-HuldaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `nl-nl-hannarus`                            | `nl-NL` 로캘과 `nl-NL-HannaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `pl-pl-paulinarus`                          | `pl-PL` 로캘과 `pl-PL-PaulinaRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `pt-br-daniel-apollo`                       | `pt-BR` 로캘과 `pt-BR-Daniel-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `pt-br-heloisarus`                          | `pt-BR` 로캘과 `pt-BR-HeloisaRUS` 음성이 있는 컨테이너 이미지입니다.      |
| `pt-pt-heliarus`                            | `pt-PT` 로캘과 `pt-PT-HeliaRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `ro-ro-andrei`                              | `ro-RO` 로캘과 `ro-RO-Andrei` 음성이 있는 컨테이너 이미지입니다.          |
| `ru-ru-ekaterinarus`                        | `ru-RU` 로캘과 `ru-RU-EkaterinaRUS` 음성이 있는 컨테이너 이미지입니다.    |
| `ru-ru-irina-apollo`                        | `ru-RU` 로캘과 `ru-RU-Irina-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `ru-ru-pavel-apollo`                        | `ru-RU` 로캘과 `ru-RU-Pavel-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `sk-sk-filip`                               | `sk-SK` 로캘과 `sk-SK-Filip` 음성이 있는 컨테이너 이미지입니다.           |
| `sl-si-lado`                                | `sl-SI` 로캘과 `sl-SI-Lado` 음성이 있는 컨테이너 이미지입니다.            |
| `sv-se-hedvigrus`                           | `sv-SE` 로캘과 `sv-SE-HedvigRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `ta-in-valluvar`                            | `ta-IN` 로캘과 `ta-IN-Valluvar` 음성이 있는 컨테이너 이미지입니다.        |
| `te-in-chitra`                              | `te-IN` 로캘과 `te-IN-Chitra` 음성이 있는 컨테이너 이미지입니다.          |
| `th-th-pattara`                             | `th-TH` 로캘과 `th-TH-Pattara` 음성이 있는 컨테이너 이미지입니다.         |
| `tr-tr-sedarus`                             | `tr-TR` 로캘과 `tr-TR-SedaRUS` 음성이 있는 컨테이너 이미지입니다.         |
| `vi-vn-an`                                  | `vi-VN` 로캘과 `vi-VN-An` 음성이 있는 컨테이너 이미지입니다.              |
| `zh-cn-huihuirus`                           | `zh-CN` 로캘과 `zh-CN-HuihuiRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `zh-cn-kangkang-apollo`                     | `zh-CN` 로캘과 `zh-CN-Kangkang-Apollo` 음성이 있는 컨테이너 이미지입니다. |
| `zh-cn-yaoyao-apollo`                       | `zh-CN` 로캘과 `zh-CN-Yaoyao-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-hk-danny-apollo`                        | `zh-HK` 로캘과 `zh-HK-Danny-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `zh-hk-tracy-apollo`                        | `zh-HK` 로캘과 `zh-HK-Tracy-Apollo` 음성이 있는 컨테이너 이미지입니다.    |
| `zh-hk-tracyrus`                            | `zh-HK` 로캘과 `zh-HK-TracyRUS` 음성이 있는 컨테이너 이미지입니다.        |
| `zh-tw-hanhanrus`                           | `zh-TW` 로캘과 `zh-TW-HanHanRUS` 음성이 있는 컨테이너 이미지입니다.       |
| `zh-tw-yating-apollo`                       | `zh-TW` 로캘과 `zh-TW-Yating-Apollo` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-tw-zhiwei-apollo`                       | `zh-TW` 로캘과 `zh-TW-Zhiwei-Apollo` 음성이 있는 컨테이너 이미지입니다.   |

---

## <a name="neural-text-to-speech"></a>인공신경망 텍스트 음성 변환

[인공신경망 텍스트 음성 변환][sp-ntts] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `neural-text-to-speech`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list)의 전체 목록을 찾을 수도 있습니다.


# <a name="latest-version"></a>[최신 버전](#tab/current)

`v1.6.0`에 대한 릴리스 정보:
* 품질 향상 및 버그 수정을 통해 최신 모델로 업그레이드

| 이미지 태그                                  | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `1.6.0-amd64-<locale-and-voice>`            | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.6.0-amd64-en-us-arianeural`. |

| v1.5.0 로캘 및 음성           | 메모                                                                      |
|-------------------------------------|:---------------------------------------------------------------------------|
| `de-de-conradneural`                | `de-DE` 로캘과 `de-DE-ConradNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `de-de-katjaneural`                 | `de-DE` 로캘과 `de-DE-KatjaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-au-natashaneural`               | `en-AU` 로캘과 `en-AU-NatashaNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-au-williamneural`               | `en-AU` 로캘과 `en-AU-WilliamNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-ca-claraneural`                 | `en-CA` 로캘과 `en-CA-ClaraNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-ca-liamneural`                  | `en-CA` 로캘과 `en-CA-LiamNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-gb-libbyneural`                 | `en-GB` 로캘과 `en-GB-LibbyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-mianeural`                   | `en-GB` 로캘과 `en-GB-MiaNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-gb-ryanneural`                  | `en-GB` 로캘과 `en-GB-RyanNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-arianeural`                  | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-guyneural`                   | `en-US` 로캘과 `en-US-GuyNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-jennyneural`                 | `en-US` 로캘과 `en-US-JennyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-es-alvaroneural`                | `es-ES` 로캘과 `es-ES-AlvaroNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-es-elviraneural`                | `es-ES` 로캘과 `es-ES-ElviraNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-dalianeural`                 | `es-MX` 로캘과 `es-MX-DaliaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-mx-jorgeneural`                 | `es-MX` 로캘과 `es-MX-JorgeNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ca-antoineneural`               | `fr-CA` 로캘과 `fr-CA-AntoineNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `fr-ca-jeanneural`                  | `fr-CA` 로캘과 `fr-CA-JeanNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `fr-ca-sylvieneural`                | `fr-CA` 로캘과 `fr-CA-SylvieNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-deniseneural`                | `fr-FR` 로캘과 `fr-FR-DeniseNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-henrineural`                 | `fr-FR` 로캘과 `fr-FR-HenriNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `hi-in-madhurneural`                | `hi-IN` 로캘과 `hi-IN-MadhurNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `hi-in-swaraneural`                 | `hi-IN` 로캘과 `hi-IN-Swaraneural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-diegoneural`                 | `it-IT` 로캘과 `it-IT-DiegoNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-elsaneural`                  | `it-IT` 로캘과 `it-IT-ElsaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `it-it-isabellaneural`              | `it-IT` 로캘과 `it-IT-IsabellaNeural` 음성이 있는 컨테이너 이미지입니다.  |
| `ja-jp-keitaneural`                 | `ja-JP` 로캘과 `ja-JP-KeitaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `ja-jp-nanamineural`                | `ja-JP` 로캘과 `ja-JP-NanamiNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-injoonneural`                | `ko-KR` 로캘과 `ko-KR-InJoonNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-sunhineural`                 | `ko-KR` 로캘과 `ko-KR-SunHiNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `pt-br-antonioneural`               | `pt-BR` 로캘과 `pt-BR-AntonioNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `pt-br-franciscaneural`             | `pt-BR` 로캘과 `pt-BR-FranciscaNeural` 음성이 있는 컨테이너 이미지입니다. |
| `tr-tr-ahmetneural`                 | `tr-TR` 로캘과 `tr-TR-AhmetNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `tr-tr-emelneural`                  | `tr-TR` 로캘과 `tr-TR-EmelNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `zh-cn-xiaoxiaoneural`              | `zh-CN` 로캘과 `zh-CN-XiaoxiaoNeural` 음성이 있는 컨테이너 이미지입니다.  |
| `zh-cn-xiaoyouneural`               | `zh-CN` 로캘과 `zh-CN-XiaoYouNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyangneural`               | `zh-CN` 로캘과 `zh-CN-YunYangNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyeneural`                 | `zh-CN` 로캘과 `zh-CN-YunYeNeural` 음성이 있는 컨테이너 이미지입니다.     |

# <a name="previous-version"></a>[이전 버전](#tab/previous)

`v1.5.0`에 대한 릴리스 정보:
* 품질 향상 및 버그 수정을 통해 최신 모델로 업그레이드
* 최대 38개의 인공신경망 음성 지원

`v1.4.0`에 대한 릴리스 정보:
* 최신 모델로 업그레이드합니다. 
* CPU 비용과 대기 시간이 줄었습니다.
* SSML 태그(예: 피치 윤곽선)를 사용하여 운율 튜닝을 보다 효과적으로 지원합니다.

`v1.3.0`에 대한 릴리스 정보:
* 이제 신경망 텍스트 음성 변환 컨테이너가 일반 공급됩니다. 

| 이미지 태그                                  | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `1.5.0-amd64-<locale-and-voice>`            | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.5.0-amd64-en-us-arianeural`. |
| `1.4.0-amd64-<locale-and-voice>`            | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.4.0-amd64-en-us-arianeural`. |
| `1.3.0-amd64-<locale-and-voice>-preview`    | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.3.0-amd64-en-us-arianeural-preview`. |
| `1.2.0-amd64-<locale-and-voice>-preview`    | `<locale>`은 아래에 나열된 사용 가능한 로캘 중 하나로 대체합니다. 예: `1.2.0-amd64-en-us-arianeural-preview`. |

| v1.5.0 로캘 및 음성           | 메모                                                                      |
|-------------------------------------|:---------------------------------------------------------------------------|
| `de-de-conradneural`                | `de-DE` 로캘과 `de-DE-ConradNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `de-de-katjaneural`                 | `de-DE` 로캘과 `de-DE-KatjaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-au-natashaneural`               | `en-AU` 로캘과 `en-AU-NatashaNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-au-williamneural`               | `en-AU` 로캘과 `en-AU-WilliamNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-ca-claraneural`                 | `en-CA` 로캘과 `en-CA-ClaraNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-ca-liamneural`                  | `en-CA` 로캘과 `en-CA-LiamNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-gb-libbyneural`                 | `en-GB` 로캘과 `en-GB-LibbyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-mianeural`                   | `en-GB` 로캘과 `en-GB-MiaNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-gb-ryanneural`                  | `en-GB` 로캘과 `en-GB-RyanNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-arianeural`                  | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-guyneural`                   | `en-US` 로캘과 `en-US-GuyNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-jennyneural`                 | `en-US` 로캘과 `en-US-JennyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-es-alvaroneural`                | `es-ES` 로캘과 `es-ES-AlvaroNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-es-elviraneural`                | `es-ES` 로캘과 `es-ES-ElviraNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-dalianeural`                 | `es-MX` 로캘과 `es-MX-DaliaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-mx-jorgeneural`                 | `es-MX` 로캘과 `es-MX-JorgeNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ca-antoineneural`               | `fr-CA` 로캘과 `fr-CA-AntoineNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `fr-ca-jeanneural`                  | `fr-CA` 로캘과 `fr-CA-JeanNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `fr-ca-sylvieneural`                | `fr-CA` 로캘과 `fr-CA-SylvieNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-deniseneural`                | `fr-FR` 로캘과 `fr-FR-DeniseNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-henrineural`                 | `fr-FR` 로캘과 `fr-FR-HenriNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `hi-in-madhurneural`                | `hi-IN` 로캘과 `hi-IN-MadhurNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `hi-in-swaraneural`                 | `hi-IN` 로캘과 `hi-IN-Swaraneural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-diegoneural`                 | `it-IT` 로캘과 `it-IT-DiegoNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-elsaneural`                  | `it-IT` 로캘과 `it-IT-ElsaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `it-it-isabellaneural`              | `it-IT` 로캘과 `it-IT-IsabellaNeural` 음성이 있는 컨테이너 이미지입니다.  |
| `ja-jp-keitaneural`                 | `ja-JP` 로캘과 `ja-JP-KeitaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `ja-jp-nanamineural`                | `ja-JP` 로캘과 `ja-JP-NanamiNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-injoonneural`                | `ko-KR` 로캘과 `ko-KR-InJoonNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-sunhineural`                 | `ko-KR` 로캘과 `ko-KR-SunHiNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `pt-br-antonioneural`               | `pt-BR` 로캘과 `pt-BR-AntonioNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `pt-br-franciscaneural`             | `pt-BR` 로캘과 `pt-BR-FranciscaNeural` 음성이 있는 컨테이너 이미지입니다. |
| `tr-tr-ahmetneural`                 | `tr-TR` 로캘과 `tr-TR-AhmetNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `tr-tr-emelneural`                  | `tr-TR` 로캘과 `tr-TR-EmelNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `zh-cn-xiaoxiaoneural`              | `zh-CN` 로캘과 `zh-CN-XiaoxiaoNeural` 음성이 있는 컨테이너 이미지입니다.  |
| `zh-cn-xiaoyouneural`               | `zh-CN` 로캘과 `zh-CN-XiaoYouNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyangneural`               | `zh-CN` 로캘과 `zh-CN-YunYangNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyeneural`                 | `zh-CN` 로캘과 `zh-CN-YunYeNeural` 음성이 있는 컨테이너 이미지입니다.     |

| v1.4.0 로캘 및 음성           | 메모                                                                      |
|-------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | `de-DE` 로캘과 `de-DE-KatjaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-au-natashaneural`               | `en-AU` 로캘과 `en-AU-NatashaNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-ca-claraneural`                 | `en-CA` 로캘과 `en-CA-ClaraNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-libbyneural`                 | `en-GB` 로캘과 `en-GB-LibbyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-mianeural`                   | `en-GB` 로캘과 `en-GB-MiaNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-arianeural`                  | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-guyneural`                   | `en-US` 로캘과 `en-US-GuyNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-jennyneural`                 | `en-US` 로캘과 `en-US-JennyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-es-elviraneural`                | `es-ES` 로캘과 `es-ES-ElviraNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-dalianeural`                 | `es-MX` 로캘과 `es-MX-DaliaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ca-sylvieneural`                | `fr-CA` 로캘과 `fr-CA-SylvieNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-deniseneural`                | `fr-FR` 로캘과 `fr-FR-DeniseNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `hi-in-swaraneural`                 | `hi-IN` 로캘과 `hi-IN-Swaraneural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-elsaneural`                  | `it-IT` 로캘과 `it-IT-ElsaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `ja-jp-nanamineural`                | `ja-JP` 로캘과 `ja-JP-NanamiNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-sunhineural`                 | `ko-KR` 로캘과 `ko-KR-SunHiNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `pt-br-franciscaneural`             | `pt-BR` 로캘과 `pt-BR-FranciscaNeural` 음성이 있는 컨테이너 이미지입니다. |
| `zh-cn-xiaoxiaoneural`              | `zh-CN` 로캘과 `zh-CN-XiaoxiaoNeural` 음성이 있는 컨테이너 이미지입니다.  |
| `zh-cn-xiaoyouneural`               | `zh-CN` 로캘과 `zh-CN-XiaoYouNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyangneural`               | `zh-CN` 로캘과 `zh-CN-YunYangNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `zh-cn-yunyeneural`                 | `zh-CN` 로캘과 `zh-CN-YunYeNeural` 음성이 있는 컨테이너 이미지입니다.     |

| v1.3.0 로캘 및 음성           | 메모                                                                      |
|-------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | `de-DE` 로캘과 `de-DE-KatjaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-au-natashaneural`               | `en-AU` 로캘과 `en-AU-NatashaNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-ca-claraneural`                 | `en-CA` 로캘과 `en-CA-ClaraNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-libbyneural`                 | `en-GB` 로캘과 `en-GB-LibbyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-mianeural`                   | `en-GB` 로캘과 `en-GB-MiaNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-arianeural`                  | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-guyneural`                   | `en-US` 로캘과 `en-US-GuyNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-jennyneural`                 | `en-US` 로캘과 `en-US-JennyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `es-es-elviraneural`                | `es-ES` 로캘과 `es-ES-ElviraNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-dalianeural`                 | `es-MX` 로캘과 `es-MX-DaliaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ca-sylvieneural`                | `fr-CA` 로캘과 `fr-CA-SylvieNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-deniseneural`                | `fr-FR` 로캘과 `fr-FR-DeniseNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `hi-in-swaraneural`                 | `hi-IN` 로캘과 `hi-IN-Swaraneural` 음성이 있는 컨테이너 이미지입니다.     |
| `it-it-elsaneural`                  | `it-IT` 로캘과 `it-IT-ElsaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `ja-jp-nanamineural`                | `ja-JP` 로캘과 `ja-JP-NanamiNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-sunhineural`                 | `ko-KR` 로캘과 `ko-KR-SunHiNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `pt-br-franciscaneural`             | `pt-BR` 로캘과 `pt-BR-FranciscaNeural` 음성이 있는 컨테이너 이미지입니다. |
| `zh-cn-xiaoxiaoneural`              | `zh-CN` 로캘과 `zh-CN-XiaoxiaoNeural` 음성이 있는 컨테이너 이미지입니다.  |

| v1.2.0 미리 보기 로캘 및 음성           | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `de-de-katjaneural-preview`                 | `de-DE` 로캘과 `de-DE-KatjaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-au-natashaneural-preview`               | `en-AU` 로캘과 `en-AU-NatashaNeural` 음성이 있는 컨테이너 이미지입니다.   |
| `en-ca-claraneural-preview`                 | `en-CA` 로캘과 `en-CA-ClaraNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-libbyneural-preview`                 | `en-GB` 로캘과 `en-GB-LibbyNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `en-gb-mianeural-preview`                   | `en-GB` 로캘과 `en-GB-MiaNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `en-us-arianeural-preview`                  | `en-US` 로캘과 `en-US-AriaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `en-us-guyneural-preview`                   | `en-US` 로캘과 `en-US-GuyNeural` 음성이 있는 컨테이너 이미지입니다.       |
| `es-es-elviraneural-preview`                | `es-ES` 로캘과 `es-ES-ElviraNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `es-mx-dalianeural-preview`                 | `es-MX` 로캘과 `es-MX-DaliaNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `fr-ca-sylvieneural-preview`                | `fr-CA` 로캘과 `fr-CA-SylvieNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `fr-fr-deniseneural-preview`                | `fr-FR` 로캘과 `fr-FR-DeniseNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `it-it-elsaneural-preview`                  | `it-IT` 로캘과 `it-IT-ElsaNeural` 음성이 있는 컨테이너 이미지입니다.      |
| `ja-jp-nanamineural-preview`                | `ja-JP` 로캘과 `ja-JP-NanamiNeural` 음성이 있는 컨테이너 이미지입니다.    |
| `ko-kr-sunhineural-preview`                 | `ko-KR` 로캘과 `ko-KR-SunHiNeural` 음성이 있는 컨테이너 이미지입니다.     |
| `pt-br-franciscaneural-preview`             | `pt-BR` 로캘과 `pt-BR-FranciscaNeural` 음성이 있는 컨테이너 이미지입니다. |
| `zh-cn-xiaoxiaoneural-preview`              | `zh-CN` 로캘과 `zh-CN-XiaoxiaoNeural` 음성이 있는 컨테이너 이미지입니다.  |

---

## <a name="speech-language-detection"></a>음성 언어 감지

[음성 언어 감지][sp-lid] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/speechservices/` 리포지토리 내에 있으며 이름은 `language-detection`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list)의 전체 목록을 찾을 수도 있습니다.

| 이미지 태그                                  | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>핵심 문구 추출

컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/textanalytics/` 리포지토리 내에 있으며 이름은 `keyphrase`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-version"></a>[최신 버전](#tab/current)


| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[이전 버전](#tab/previous)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>텍스트 언어 감지

[언어 감지][ta-la] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/textanalytics/` 리포지토리 내에 있으며 이름은 `language`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`입니다.


이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)의 전체 목록을 찾을 수도 있습니다.

# <a name="latest-versions"></a>[최신 버전](#tab/current)

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[이전 버전](#tab/previous)


| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>정서 분석

[감정 분석][ta-se] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/textanalytics/` 리포지토리 내에 있으며 이름은 `sentiment`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)의 전체 목록을 찾을 수도 있습니다.

| 이미지 태그 | 메모                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 감정 분석 v3(영어)               |
| `3.0-es`   | 감정 분석 v3(스페인어)               |
| `3.0-fr`   | 감정 분석 v3(프랑스어)                |
| `3.0-it`   | 감정 분석 v3(이탈리아어)               |
| `3.0-de`   | 감정 분석 v3(독일어)                |
| `3.0-zh`   | 감정 분석 v3(중국어 - 간체)  |
| `3.0-zht`  | 감정 분석 v3(중국어 - 번체) |
| `3.0-ja`   | 감정 분석 v3(일본어)              |
| `3.0-pt`   | 감정 분석 v3(포르투갈어)            |
| `3.0-nl`   | 감정 분석 v3(네덜란드어)                 |
| `2.1`    | 감정 분석 v2      |


## <a name="text-analytics-for-health"></a>의료 분야 Text Analytics

[의료용 Text Analytics][ta-he] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/textanalytics/` 리포지토리 내에 있으며 이름은 `healthcare`입니다. 전체 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다. [MCR에서 태그](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/healthcare/tags/list)의 전체 목록을 찾을 수도 있습니다.


`3.0.015490002-onprem-amd64`에 대한 릴리스 정보:

* 새 모델 버전 `2021-03-01`
* 컨테이너가 MCR에 릴리스되었습니다.

| 이미지 태그 | 메모                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0.015490002-onprem-amd64`   |               |


## <a name="translator"></a>Translator

[Translator][tr-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services/translator` 리포지토리 내에 있으며 이름은 `text-translation`입니다. 정규화된 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/translator/text-translation`입니다.

이 컨테이너 이미지에는 다음 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |


[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-he]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare
[tr-containers]: ../translator/containers/translator-how-to-install-container.md
