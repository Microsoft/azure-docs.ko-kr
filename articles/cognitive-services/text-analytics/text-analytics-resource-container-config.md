---
title: 컨테이너 구성-Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics는 각 컨테이너에 공통된 구성 프레임워크를 제공하므로, 사용자는 컨테이너에 대한 스토리지, 로깅 및 원격 분석은 물론, 보안 설정을 쉽게 구성하고 관리할 수 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795301"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics Docker 컨테이너 구성

Text Analytics는 각 컨테이너에 공통된 구성 프레임워크를 제공하므로, 사용자는 컨테이너에 대한 스토리지, 로깅 및 원격 분석은 물론, 보안 설정을 쉽게 구성하고 관리할 수 있습니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](how-tos/text-analytics-how-to-install-containers.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은[ 구성 설정에 대해 지정된 `Billing`Text Analytics](#billing-configuration-setting) 리소스에 대한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Text Analytics** 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하기 위해 사용하는 Azure 기반 _Text Analytics_ 리소스의 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure 기반 __Text Analytics_ 리소스에 대해 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Text Analytics** 개요, 레이블 `Endpoint`

|필수| Name | 데이터 형식 | 설명 |
|--|------|-----------|-------------|
|예| `Billing` | 문자열 | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대 한 자세한 내용은 [필수 매개 변수 수집](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)을 참조 하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. `--mount`Docker 실행[ 명령의 ](https://docs.docker.com/engine/reference/commandline/run/)옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Text Analytics 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다. 

|옵션| Name | 데이터 형식 | 설명 |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | 문자열 | Text Analytics 컨테이너에는 사용되지 않습니다.|
|옵션| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제 

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션의 docker 명령은 백슬래시 (`\`)를 줄 연속 문자로 사용 합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: docker 컨테이너에 대해 잘 알고 있지 않으면 인수의 순서를 변경 하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| Placeholder | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Text Analytics` 키 페이지에서 사용할 수 있는 `Text Analytics` 리소스의 엔드포인트 키입니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 청구 엔드포인트 값은 Azure `Text Analytics` 개요 페이지에서 사용할 수 있습니다.| 명시적 예제에 대 한 [필수 매개 변수 수집](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) 을 참조 하세요. |

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](how-tos/text-analytics-how-to-install-containers.md#billing)를 참조하세요.
> ApiKey 값은 Azure `Text Analytics` 리소스 키 페이지의 **키** 입니다. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[언어 감지](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](how-tos/text-analytics-how-to-install-containers.md)을 리뷰합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
