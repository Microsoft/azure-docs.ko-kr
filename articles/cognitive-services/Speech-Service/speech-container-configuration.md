---
title: 음성 컨테이너 구성
titleSuffix: Azure Cognitive Services
description: Speech service는 컨테이너에 대 한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성 하 고 관리할 수 있도록 각 컨테이너에 공통 구성 프레임 워크를 제공 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 34b4664ec13f7ba1871433e37d86170b2207a17a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816564"
---
# <a name="configure-speech-service-containers"></a>음성 서비스 컨테이너 구성

음성 컨테이너를 통해 고객은 강력한 클라우드 기능 및 최첨단 로컬 기능을 모두 활용할 수 있도록 최적화된 단일 음성 응용 프로그램 아키텍처를 구축할 수 있습니다. 이제 지원 되는 네 가지 음성 컨테이너는 **음성 텍스트**, **사용자 지정 음성-텍스트**, **텍스트 음성 변환**및 **사용자 지정 텍스트 음성 변환**입니다.

**음성** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](speech-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 그 값은 [ `Billing` ](#billing-configuration-setting) 구성 설정을 위해 지정된 _음성_ 리소스에 대해 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure Portal: **음성의** 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 리소스 컨테이너에 대한 청구 정보를 계량하기 위해 사용된 Azure의  _음성_ 리소스의 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며 값은 Azure에서 _음성_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure Portal: **음성의** 개요, 레이블이 지정 된 `Endpoint`

| 필수 | name | 데이터 형식 | 설명 |
| -------- | ---- | --------- | ----------- |
| yes | `Billing` | string | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대 한 자세한 내용은 [필수 매개 변수 수집](speech-container-howto.md#gathering-required-parameters)을 참조 하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

표준 음성 컨테이너는 학습 또는 서비스 데이터를 저장 하기 위해 입력 또는 출력 탑재를 사용 하지 않습니다. 그러나 사용자 지정 음성 컨테이너는 볼륨 탑재를 사용 합니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](speech-container-howto.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다.

| 선택 사항 | name | 데이터 형식 | 설명 |
| -------- | ---- | --------- | ----------- |
| 허용되지 않음 | `Input` | string | 표준 음성 컨테이너는이를 사용 하지 않습니다. 사용자 지정 음성 컨테이너는 [볼륨 탑재](#volume-mount-settings)를 사용 합니다.                                                                                    |
| 선택 사항 | `Output` | string | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>볼륨 탑재 설정

사용자 지정 음성 컨테이너는 [볼륨 탑재](https://docs.docker.com/storage/volumes/) 를 사용 하 여 사용자 지정 모델을 유지 합니다. `-v` (또는 `--volume`) 옵션을 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령에 추가 하 여 볼륨 탑재를 지정할 수 있습니다.

사용자 지정 모델은 사용자 지정 음성 컨테이너 docker run 명령의 일부로 새 모델을 처음으로 수집 때 다운로드 됩니다. 사용자 지정 음성 컨테이너에 대해 동일한 `ModelId`의 순차적 실행은 이전에 다운로드 한 모델을 사용 합니다. 볼륨 탑재를 제공 하지 않으면 사용자 지정 모델을 지속할 수 없습니다.

볼륨 탑재 설정은 다음의 세 가지 색 `:` 구분 된 필드로 구성 됩니다.

1. 첫 번째 필드는 호스트 컴퓨터의 볼륨 이름입니다 (예: _C:\input_).
2. 두 번째 필드는 컨테이너의 디렉터리 (예: _/usr/local/models_)입니다.
3. 세 번째 필드 (선택 사항)는 쉼표로 구분 된 옵션 목록입니다. 자세한 내용은 [볼륨 사용](https://docs.docker.com/storage/volumes/)을 참조 하세요.

### <a name="volume-mount-example"></a>볼륨 탑재 예

```bash
-v C:\input:/usr/local/models
```

이 명령은 호스트 컴퓨터 _C:\input_ 디렉터리를 컨테이너 _/usr/local/models_ 디렉터리에 탑재 합니다.

> [!IMPORTANT]
> 볼륨 탑재 설정은 **Custom Speech 텍스트** 및 **사용자 지정 텍스트 음성 변환** 컨테이너에만 적용 됩니다. 표준 **음성 텍스트** 및 **텍스트 음성 변환** 컨테이너는 볼륨 탑재를 사용 하지 않습니다.

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 한번 실행되면 컨테이너는 [중지](speech-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

- **줄 연속 문자**: 다음 섹션의 Docker 명령은 백슬래시 (`\`)를 줄 연속 문자로 사용 합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다.
- **인수 순서**: Docker 컨테이너에 익숙하지 않은 경우 인수의 순서를 변경 하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| Placeholder | Value | 형식 또는 예 |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Azure `Speech` 키 페이지에 있는 `Speech` 리소스의 엔드포인트 키입니다.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | 청구 엔드포인트 값은 Azure의 `Speech` 개요 페이지에서 사용 가능합니다. | 명시적 예제에 대 한 [필수 매개 변수 수집](speech-container-howto.md#gathering-required-parameters) 을 참조 하세요. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.
> ApiKey 값은 Azure 음성 리소스 [키] 페이지의 **키**입니다.

## <a name="speech-container-docker-examples"></a>Speech container Docker 예

다음은 음성 컨테이너에 대한 Docker 예제입니다.

## <a name="speech-to-texttabstt"></a>[Speech-to-text](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>음성 텍스트에 대 한 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>음성 텍스트에 대 한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-texttabcstt"></a>[Custom Speech 텍스트](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Custom Speech 텍스트에 대 한 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Custom Speech 텍스트에 대 한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speechtabtss"></a>[Text-to-speech](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>텍스트 음성 변환에 대 한 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>텍스트 음성 변환에 대 한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speechtabctts"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환에 대 한 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환에 대 한 로깅 예제

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>다음 단계

- [컨테이너 설치 및 실행 방법](speech-container-howto.md)을 리뷰합니다.
