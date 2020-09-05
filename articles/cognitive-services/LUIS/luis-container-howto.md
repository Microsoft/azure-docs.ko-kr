---
title: Docker 컨테이너-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 컨테이너는 학습 또는 게시된 앱을 Docker 컨테이너로 로드하고 컨테이너의 API 엔드포인트에서 쿼리 예측에 대한 액세스를 제공합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f5409fea1cdbbc35e9068fae6b3ba7fbc2a95580
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547395"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS docker 컨테이너 설치 및 실행

LUIS (Language Understanding) 컨테이너는 학습 또는 게시 된 Language Understanding 모델을 로드 합니다. [LUIS 앱](https://www.luis.ai)으로 docker 컨테이너는 컨테이너의 API 끝점에서 쿼리 예측에 대 한 액세스를 제공 합니다. 컨테이너에서 쿼리 로그를 수집 하 고 다시 Language Understanding 앱에 업로드 하 여 앱의 예측 정확도를 향상 시킬 수 있습니다.

다음 비디오는 이 컨테이너를 사용하는 방법을 보여줍니다.

[![Cognitive Services에 대 한 컨테이너 데모](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

LUIS 컨테이너를 실행 하려면 다음 필수 구성 요소를 확인 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.|
|Azure `Cognitive Services` 리소스 및 LUIS [패키지 된 앱](luis-how-to-start-new-app.md) 파일 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>* _Cognitive Services_ Azure 리소스 및 연결 된 청구 키 청구 끝점 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하는 데 필요 합니다. <br>* 연결된 앱 ID와 함께 컨테이너에 대한 탑재된 입력으로 패키징된 학습된 또는 게시된 앱 LUIS 포털 또는 제작 Api에서 패키지 파일을 가져올 수 있습니다. [Authoring api](#authoring-apis-for-package-file)에서 LUIS 패키지 된 앱을 가져오는 경우에는 _제작 키_도 필요 합니다.<br><br>이러한 요구 사항은 아래 변수에 대한 명령줄 인수를 전달하는데 사용됩니다.<br><br>**{AUTHORING_KEY}**:이 키는 클라우드의 LUIS 서비스에서 패키지 된 앱을 가져오고 쿼리 로그를 클라우드에 다시 업로드 하는 데 사용 됩니다. 형식은 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`입니다.<br><br>**{APP_ID}**:이 ID는 앱을 선택 하는 데 사용 됩니다. 형식은 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`입니다.<br><br>**{API_KEY}**:이 키는 컨테이너를 시작 하는 데 사용 됩니다. 두 위치에서 엔드포인트 키를 찾을 수 있습니다. 첫 번째는 _Cognitive Services_ 리소스의 키 목록 내의 Azure Portal입니다. 엔드포인트 키는 키와 엔드포인트 설정 페이지에서 LUIS 포털을 사용할 수도 있습니다. 시작 키를 사용하지 마세요.<br><br>**{ENDPOINT_URI}**: 개요 페이지에 제공 된 끝점입니다.<br><br>[작성 키 및 엔드포인트 키](luis-limits.md#key-limits)는 용도가 서로 다릅니다. 서로 교환하여 사용하지 마세요. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>패키지 파일에 대 한 Api 작성

패키지 된 앱에 대 한 Api 작성:

* [게시 된 패키지 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [게시 되지 않음, 학습 전용 패키지 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

아래 표에는 컨테이너 호스트에 대 한 최소 및 권장 값이 나열 되어 있습니다. 요구 사항은 트래픽 볼륨에 따라 달라질 수 있습니다.

|컨테이너| 최소 | 권장 | TPS<br>(최소, 최대)|
|-----------|---------|-------------|--|
|LUIS|1 코어, 2gb 메모리|1 코어, 4gb 메모리|20, 40|

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션 수

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 리포지토리에서 컨테이너 이미지를 다운로드 합니다 `mcr.microsoft.com/azure-cognitive-services/luis` .

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

사용 가능한 태그에 대한 전체 설명은 이전 명령에서 사용되는 `latest`처럼 Docker 허브에서 [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204)를 참조하세요.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

![LUIS(Language Understanding) 컨테이너를 사용하기 위한 프로세스](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. LUIS 포털 또는 LUIS API에서 컨테이너에 대한 [패키지 내보내기](#export-packaged-app-from-luis)
1. [호스트 컴퓨터](#the-host-computer)에서 패키지 파일을 필수 **입력** 디렉터리로 이동합니다. LUIS 패키지 파일의 이름을 바꾸거나 변경 하거나 덮어쓰거나 압축을 해제 하지 마십시오.
1. 필수 _입력 탑재_ 및 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](luis-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 엔드포인트를 쿼리](#query-the-containers-prediction-endpoint)합니다.
1. 컨테이너를 사용하고 나면 LUIS 포털의 출력 탑재에서 [엔드포인트 로그를 가져오고](#import-the-endpoint-logs-for-active-learning) 컨테이너를 [중지](#stop-the-container)합니다.
1. **엔드포인트 발화 검토** 페이지에서 LUIS 포털의 [활성 학습](luis-how-to-review-endpoint-utterances.md)을 사용하여 앱을 개선합니다.

컨테이너에서 실행되는 앱은 변경할 수 없습니다. 컨테이너에서 앱을 변경 하려면 [LUIS](https://www.luis.ai) portal을 사용 하 여 LUIS 서비스에서 앱을 변경 하거나 LUIS [authoring api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 사용 해야 합니다. 그 다음 학습 및/또는 게시한 다음, 새 패키지를 다운로드하고 컨테이너를 다시 실행합니다.

컨테이너 내에서 LUIS 앱은 LUIS 서비스로 다시 내보낼 수 없습니다. 쿼리 로그만 업로드할 수 있습니다.

## <a name="export-packaged-app-from-luis"></a>LUIS에서 패키징된 앱 내보내기

LUIS 컨테이너는 사용자 발화의 예측 쿼리에 응답하기 위해 학습된 또는 게시된 LUIS 앱이 필요합니다. LUIS 앱을 사용하려면 학습된 또는 게시된 패키지 API 중 하나를 사용합니다.

기본 위치는 `docker run` 명령을 실행하는 위치를 기준으로 `input`하위 디렉터리입니다.

패키지 파일을 디렉터리에 배치하여 Docker 컨테이너를 실행하는 경우 입력 탑재로 이 디렉터리를 참조합니다.

### <a name="package-types"></a>패키지 형식

입력 탑재 디렉터리는 앱의 **프로덕션**, **스테이징**및 **버전 관리** 모델을 동시에 포함할 수 있습니다. 모든 패키지는 탑재되어 있습니다.

|패키지 형식|쿼리 엔드포인트 API|쿼리 가용성|패키지 파일 이름 형식|
|--|--|--|--|
|바뀔|GET, POST|컨테이너만|`{APP_ID}_v{APP_VERSION}.gz`|
|준비|GET, POST|Azure 및 컨테이너|`{APP_ID}_STAGING.gz`|
|프로덕션|GET, POST|Azure 및 컨테이너|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> LUIS 패키지 파일의 이름을 바꾸거나 변경 하거나 덮어쓰거나 압축을 해제 하지 마십시오.

### <a name="packaging-prerequisites"></a>패키징 필수 구성 요소

LUIS 애플리케이션을 패키징 전에 다음이 필요 합니다.

|패키징 요구 사항|세부 정보|
|--|--|
|Azure _Cognitive Services_ 리소스 인스턴스|지원되는 지역은 다음을 포함합니다.<br><br>미국 서부(`westus`)<br>서유럽(`westeurope`)<br>오스트레일리아 동부(`australiaeast`)|
|학습된 또는 게시된 LUIS 앱|[지원되지 않는 종속성][unsupported-dependencies]이 없습니다. |
|[호스트 컴퓨터](#the-host-computer)의 파일 시스템에 액세스 |호스트 컴퓨터는 [입력 탑재](luis-container-configuration.md#mount-settings)를 허용해야 합니다.|

### <a name="export-app-package-from-luis-portal"></a>LUIS 포털에서 앱 패키지 내보내기

LUIS [포털](https://www.luis.ai)은 학습된 또는 게시 된 앱의 패키지를 내보내는 기능을 제공합니다.

### <a name="export-published-apps-package-from-luis-portal"></a>LUIS 포털에서 게시된 앱의 패키지 내보내기

게시된 앱의 패키지는 **내 앱** 목록 페이지에서 사용할 수 있습니다.

1. [LUIS](https://www.luis.ai) 포털에 로그인합니다.
1. 목록에서 앱 이름의 왼쪽에 있는 확인란을 선택합니다.
1. 목록 위에 있는 컨텍스트 도구 모음에서 **내보내기** 항목을 선택합니다.
1. **컨테이너에 대한 내보내기(GZIP)** 를 선택합니다.
1. **프로덕션 슬롯** 또는 **스테이징 슬롯**의 환경을 선택합니다.
1. 패키지가 브라우저에서 다운로드됩니다.

![앱 페이지의 내보내기 메뉴에서 컨테이너에 대한 게시된 패키지 내보내기](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>LUIS 포털에서 버전 관리 된 앱의 패키지 내보내기

버전이 지정 된 앱의 패키지는 **버전** 목록 페이지에서 사용할 수 있습니다.

1. [LUIS](https://www.luis.ai) 포털에 로그인합니다.
1. 목록에서 해당 앱을 선택합니다.
1. 앱의 탐색 모음에서 **관리**를 선택합니다.
1. 왼쪽 탐색 모음에서 **버전**을 선택합니다.
1. 목록에서 버전 이름의 왼쪽에 있는 확인란을 선택합니다.
1. 목록 위에 있는 컨텍스트 도구 모음에서 **내보내기** 항목을 선택합니다.
1. **컨테이너에 대한 내보내기(GZIP)** 를 선택합니다.
1. 패키지가 브라우저에서 다운로드됩니다.

![버전 페이지의 내보내기 메뉴에서 컨테이너에 대한 학습된 패키지 내보내기](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>API에서 게시된 앱의 패키지 내보내기

다음 REST API 메서드를 사용하여 이미 [게시된](luis-how-to-publish-app.md) LUIS 앱을 패키징합니다. HTTP 사양 아래의 테이블을 사용하여 API 호출에서 자리 표시자에 대한 사용자 고유 값을 대체합니다.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 자리 표시자 | 값 |
|-------------|-------|
| **{APP_ID}** | 게시된 LUIS 앱의 애플리케이션 ID입니다. |
| **{SLOT_NAME}** | 게시된 LUIS 앱의 환경입니다. 다음 값 중 하나를 사용합니다.<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | 게시된 LUIS 앱에 대한 LUIS 계정의 작성 키입니다.<br/>LUIS 포털의 **사용자 설정** 페이지에서 작성 키를 가져올 수 있습니다. |
| **{AZURE_REGION}** | 해당 Azure 지역:<br/><br/>`westus` - 미국 서부<br/>`westeurope` - 서유럽<br/>`australiaeast` - 오스트레일리아 동부 |

게시 된 패키지를 다운로드 하려면 여기에 있는 [API 설명서][download-published-package]를 참조 하세요. 성공적으로 다운로드 되 면 응답은 LUIS 패키지 파일입니다. 컨테이너의 입력 탑재에 대한 지정된 스토리지 위치에 파일을 저장합니다.

### <a name="export-versioned-apps-package-from-api"></a>API에서 버전 관리 된 앱의 패키지 내보내기

다음 REST API 메서드를 사용하여 이미 [학습된](luis-how-to-train.md) LUIS 애플리케이션을 패키징합니다. HTTP 사양 아래의 테이블을 사용하여 API 호출에서 자리 표시자에 대한 사용자 고유 값을 대체합니다.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| 자리 표시자 | 값 |
|-------------|-------|
| **{APP_ID}** | 학습 된 LUIS 앱의 응용 프로그램 ID입니다. |
| **{APP_VERSION}** | 학습 된 LUIS 앱의 응용 프로그램 버전입니다. |
| **{AUTHORING_KEY}** | 게시된 LUIS 앱에 대한 LUIS 계정의 작성 키입니다.<br/>LUIS 포털의 **사용자 설정** 페이지에서 작성 키를 가져올 수 있습니다. |
| **{AZURE_REGION}** | 해당 Azure 지역:<br/><br/>`westus` - 미국 서부<br/>`westeurope` - 서유럽<br/>`australiaeast` - 오스트레일리아 동부 |

버전 관리 패키지를 다운로드 하려면 여기에 있는 [API 설명서][download-versioned-package]를 참조 하세요. 성공적으로 다운로드 되 면 응답은 LUIS 패키지 파일입니다. 컨테이너의 입력 탑재에 대한 지정된 스토리지 위치에 파일을 저장합니다.

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](luis-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* 이 예제에서는 드라이브의 디렉터리를 사용 하 여 `C:` Windows에서 사용 권한 충돌을 방지 합니다. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다.
* Docker 컨테이너에 대해 잘 모르는 경우에는 인수의 순서를 변경 하지 마십시오.
* 다른 운영 체제를 사용 하는 경우 시스템에 올바른 콘솔/터미널, 탑재를 위한 폴더 구문 및 줄 연속 문자를 사용 합니다. 이 예에서는 Windows 콘솔에 줄 연속 문자가 있는 것으로 가정 `^` 합니다. 컨테이너는 Linux 운영 체제 이므로 대상 탑재는 Linux 스타일 폴더 구문을 사용 합니다.

이 명령은 다음을 수행합니다.

* LUIS 컨테이너 이미지에서 컨테이너를 실행합니다.
* 컨테이너 호스트에 있는 *C:\input*의 입력 탑재에서 LUIS 앱을 로드 합니다.
* CPU 코어 두 개 및 4GB 메모리를 할당합니다.
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 컨테이너 호스트에 있는 *C:\output*의 출력 탑재에 컨테이너 및 LUIS 로그를 저장 합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

`docker run` 명령의 자세한 [예제](luis-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.
> ApiKey 값은 LUIS 포털에 있는 **Azure 리소스** 페이지의 **키** 이며 azure 리소스 키 페이지 에서도 사용할 수 있습니다 `Cognitive Services` .

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>컨테이너에서 지 원하는 끝점 Api

API의 V2 및 [V3](luis-migration-api-v3.md) 버전은 모두 컨테이너에서 사용할 수 있습니다.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 게시 된 (스테이징 또는 프로덕션) 앱의 끝점은 버전이 지정 된 앱의 끝점과 _다릅니다_ .

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

# <a name="v3-prediction-endpoint"></a>[V3 예측 엔드포인트](#tab/v3)

|패키지 유형|HTTP 동사|경로|쿼리 매개 변수|
|--|--|--|--|
|게시 날짜|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|바뀔|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

쿼리 매개 변수는 쿼리 응답에 반환되는 방법 및 내용을 구성합니다.

|쿼리 매개 변수|형식|목적|
|--|--|--|
|`query`|문자열|사용자의 발화입니다.|
|`verbose`|boolean|예측 된 모델에 대 한 모든 메타 데이터를 반환할지 여부를 나타내는 부울 값입니다. 기본값은 false입니다.|
|`log`|boolean|[활성 학습](luis-how-to-review-endpoint-utterances.md)에 대해 나중에 사용할 수 있는 로그 쿼리입니다. 기본값은 false입니다.|
|`show-all-intents`|boolean|모든 의도를 반환할지 아니면 상위 점수 매기기 의도만 반환할지를 나타내는 부울 값입니다. 기본값은 false입니다.|

# <a name="v2-prediction-endpoint"></a>[V2 예측 엔드포인트](#tab/v2)

|패키지 유형|HTTP 동사|경로|쿼리 매개 변수|
|--|--|--|--|
|게시 날짜|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|바뀔|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

쿼리 매개 변수는 쿼리 응답에 반환되는 방법 및 내용을 구성합니다.

|쿼리 매개 변수|형식|목적|
|--|--|--|
|`q`|문자열|사용자의 발화입니다.|
|`timezoneOffset`|number|timezoneOffset으로 미리 작성된 엔터티 datetimeV2에서 사용하는 [표준 시간대를 변경](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)할 수 있습니다.|
|`verbose`|boolean|True로 설정하는 경우 모든 의도 및 점수를 반환합니다. 기본값은 False이며, 최상위 의도만 반환합니다.|
|`staging`|boolean|True로 설정하면 스테이징 환경 결과에서 쿼리를 반환합니다. |
|`log`|boolean|[활성 학습](luis-how-to-review-endpoint-utterances.md)에 대해 나중에 사용할 수 있는 로그 쿼리입니다. 기본값은 true입니다.|

***

### <a name="query-the-luis-app"></a>LUIS 앱 쿼리

게시된 앱에 대한 컨테이너를 쿼리하는 CURL 명령 예입니다.

# <a name="v3-prediction-endpoint"></a>[V3 예측 엔드포인트](#tab/v3)

슬롯에서 모델을 쿼리하려면 다음 API를 사용 합니다.

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

**스테이징** 환경에 대 한 쿼리를 만들려면 `production` 경로에서을로 바꿉니다 `staging` .

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

버전이 지정 된 모델을 쿼리하려면 다음 API를 사용 합니다.

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 예측 엔드포인트](#tab/v2)

슬롯에서 모델을 쿼리하려면 다음 API를 사용 합니다.

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
**스테이징** 환경에 대한 쿼리를 만들려면 **스테이징** 쿼리 문자열 매개 변수 값을 True로 변경합니다.

`staging=true`

버전이 지정 된 모델을 쿼리하려면 다음 API를 사용 합니다.

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
버전 이름은 최대 10자이며 URL에 허용되는 문자만 포함합니다.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>활성 학습에 대한 엔드포인트 로그 가져오기

LUIS 컨테이너에 대 한 출력 탑재를 지정 하면 앱 쿼리 로그 파일이 출력 디렉터리에 저장 됩니다 `{INSTANCE_ID}` . 여기서는 컨테이너 ID입니다. 앱 쿼리 로그는 LUIS 컨테이너에 제출된 각 예측 쿼리에 대한 쿼리, 응답 및 타임스탬프를 포함합니다.

다음 위치는 컨테이너의 로그 파일에 대한 중첩 된 디렉터리 구조를 보여줍니다.
```
/output/luis/{INSTANCE_ID}/
```

LUIS 포털에서 앱을 선택한 다음, **엔드포인트 로그 가져오기**를 선택하여 이 로그를 업로드합니다.

![활성 학습에 대한 컨테이너의 로그 파일 가져오기](./media/luis-container-how-to/upload-endpoint-log-files.png)

로그를 업로드한 후 LUIS 포털에서 [엔드포인트 발화](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)를 검토합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 종료하려면 컨테이너를 실행하는 명령줄 환경에서 **Ctrl + C**를 누릅니다.

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](luis-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

LUIS 컨테이너는 Azure 계정의 _Cognitive Services_ 리소스를 사용 하 여 azure로 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](luis-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 LUIS(Language Understanding) 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* LUIS(language Understanding)는 발화의 엔드포인트 쿼리를 예측하는 Docker용 Linux 컨테이너 하나를 제공합니다.
* 컨테이너 이미지는 Microsoft 컨테이너 레지스트리(MCR)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정함으로써 REST API를 사용하여 컨테이너 엔드포인트를 쿼리할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정에 대 한 [컨테이너 구성](luis-container-configuration.md) 을 검토 합니다.
* 알려진 기능 제한에 대 한 [LUIS 컨테이너 제한 사항](luis-container-limitations.md) 을 참조 하세요.
* LUIS 기능과 관련된 문제를 해결하려면 [문제 해결](troubleshooting.md)을 참조하세요.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
