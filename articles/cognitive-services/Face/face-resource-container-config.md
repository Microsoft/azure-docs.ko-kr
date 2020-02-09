---
title: 컨테이너 구성-얼굴
titleSuffix: Azure Cognitive Services
description: Face 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용 하 여 구성 됩니다. 필수 및 선택적 설정이 모두 있습니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 3c78c9eb85c3a8be236be5c3a24bd877db204b6c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167972"
---
# <a name="configure-face-docker-containers"></a>Face Docker 컨테이너 구성

**Face** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다. 

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](face-how-to-install-containers.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 그 값은 [ `Billing` ](#billing-configuration-setting) 구성 설정에 대해 지정된 _Cognitive Services_ 리소스를 위한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 계량하기 위해 사용되는 Azure _Cognitive Services_ 리소스의 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며 그 값은 Azure _Cognitive Services_ 리소스를 위한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Cognitive Services** 개요, 레이블 `Endpoint`

예제에 표시된 대로 엔드포인트 URI로 _얼굴_ 라우팅을 추가해야 합니다. 

|필수| 이름 | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대 한 자세한 내용은 [필수 매개 변수 수집](face-how-to-install-containers.md#gathering-required-parameters)을 참조 하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI 구성 설정

`CloudAI` 섹션의 구성 설정은 컨테이너에 고유한 컨테이너 관련 옵션을 제공합니다. `CloudAI` 섹션에서 Face 컨테이너에 대해 지원되는 설정과 개체는 다음과 같습니다.

| 이름 | 데이터 형식 | Description |
|------|-----------|-------------|
| `Storage` | 개체 | Face 컨테이너에서 사용되는 스토리지 시나리오입니다. `Storage` 개체에 대한 스토리지 시나리오 및 관련 설정에 대한 자세한 내용은 [스토리지 시나리오 설정](#storage-scenario-settings)을 참조하세요. |

### <a name="storage-scenario-settings"></a>스토리지 시나리오 설정

Face 컨테이너는 저장되는 콘텐츠에 따라 Blob, 캐시, 메타데이터 및 큐 데이터를 저장합니다. 예를 들어 대규모 사용자 그룹에 대한 학습 인덱스 및 결과는 Blob 데이터로 저장됩니다. Face 컨테이너에서 이러한 형식의 데이터와 상호 작용하고 저장할 때 제공하는 별도의 두 가지 스토리지 시나리오는 다음과 같습니다.

* 메모리  
  네 가지 형식의 데이터는 모두 메모리에 저장됩니다. 이러한 데이터는 배포되지도 않고 유지되지도 않습니다. Face 컨테이너가 중지되거나 제거되면 해당 컨테이너에 대한 스토리지에 있는 모든 데이터가 삭제됩니다.  
  이는 Face 컨테이너에 대한 기본 스토리지 시나리오입니다.
* Azure  
  Face 컨테이너는 Azure Storage와 Azure Cosmos DB를 사용하여 이러한 네 가지 형식의 데이터를 영구 스토리지에 배포합니다. Blob 및 큐 데이터는 Azure Storage에서 처리됩니다. 메타데이터 및 캐시 데이터는 Azure Cosmos DB에서 처리됩니다. Face 컨테이너가 중지되거나 제거되면 해당 컨테이너에 대한 스토리지에 있는 모든 데이터가 Azure Storage 및 Azure Cosmos DB에 저장되어 유지됩니다.  
  Azure Storage 시나리오에서 사용되는 리소스에는 다음과 같은 추가 요구 사항이 있습니다.
  * Azure Storage 리소스는 StorageV2 계정 종류를 사용해야 합니다.
  * Azure Cosmos DB 리소스는 Azure Cosmos DB의 MongoDB API를 사용해야 합니다.

스토리지 시나리오 및 관련 구성 설정은 `CloudAI` 구성 섹션의 `Storage` 개체에서 관리됩니다. `Storage` 개체에서 사용할 수 있는 구성 설정은 다음과 같습니다.

| 이름 | 데이터 형식 | Description |
|------|-----------|-------------|
| `StorageScenario` | String | 컨테이너에서 지원되는 스토리지 시나리오입니다. 사용할 수 있는 값은 다음과 같습니다.<br/>`Memory` - 기본값입니다. 일시적인 단일 노드 사용을 위해 컨테이너에서 비영구, 비분산 및 메모리 내 스토리지를 사용합니다. 컨테이너가 중지되거나 제거되면 해당 컨테이너의 스토리지가 삭제됩니다.<br/>`Azure` - 컨테이너에서 스토리지에 대한 Azure 리소스를 사용합니다. 컨테이너가 중지되거나 제거되더라도 해당 컨테이너의 스토리지가 유지됩니다.|
| `ConnectionStringOfAzureStorage` | String | 컨테이너에서 사용되는 Azure Storage 리소스에 대한 연결 문자열입니다.<br/>`Azure`가 `StorageScenario` 구성 설정으로 지정된 경우에만 이 설정이 적용됩니다. |
| `ConnectionStringOfCosmosMongo` | String | 컨테이너에서 사용되는 Azure Cosmos DB 리소스에 대한 MongoDB 연결 문자열입니다.<br/>`Azure`가 `StorageScenario` 구성 설정으로 지정된 경우에만 이 설정이 적용됩니다. |

예를 들어 다음 명령은 Azure Storage 시나리오를 지정하고, Face 컨테이너에 대한 데이터를 저장하는 데 사용되는 Azure Storage 및 Cosmos DB 리소스에 대한 연결 문자열 샘플을 제공합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

스토리지 시나리오는 입력 탑재 및 출력 탑재와는 별도로 처리됩니다. 단일 컨테이너에 대해 이러한 기능의 조합을 지정할 수 있습니다. 예를 들어 다음 명령은 호스트 머신의 `D:\Output` 폴더에 대한 Docker 바인딩 탑재를 출력 탑재로 정의한 다음, Face 컨테이너 이미지에서 컨테이너를 인스턴스화하여 JSON 형식의 로그 파일을 출력 탑재에 저장합니다. 또한 이 명령은 Azure Storage 시나리오를 지정하고, Face 컨테이너에 대한 데이터를 저장하는 데 사용되는 Azure Storage 및 Cosmos DB 리소스에 대한 연결 문자열 샘플을 제공합니다.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Face 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용하는 권한과 호스트 탑재 위치 권한이 충돌하여 [호스트 컴퓨터](face-how-to-install-containers.md#the-host-computer)의 탑재 위치에 액세스하지 못할 수도 있습니다. 

|선택 사항| 이름 | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | Face 컨테이너에는 사용되지 않습니다.|
|선택 사항| `Output` | String | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제 

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](face-how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션의 Docker 명령은 백슬래시 (`\`)를 줄 연속 문자로 사용 합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: Docker 컨테이너에 대해 잘 알고 있지 않으면 인수의 순서를 변경 하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Face` 키 페이지에 있는 `Face` 리소스의 엔드포인트 키입니다. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 청구 엔드포인트 값은 Azure의 `Face` 개요 페이지에서 사용 가능합니다.| 명시적 예제에 대 한 [필수 매개 변수 수집](face-how-to-install-containers.md#gathering-required-parameters) 을 참조 하세요. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](face-how-to-install-containers.md#billing)를 참조하세요.
> ApiKey 값은 Azure `Cognitive Services` 리소스 [키] 페이지의 **키**입니다. 

## <a name="face-container-docker-examples"></a>Face 컨테이너 Docker 예제

아래에는 Face 컨테이너용 Docker 예제가 나와 있습니다. 

### <a name="basic-example"></a>기본 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>로깅 예 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](face-how-to-install-containers.md)을 리뷰합니다.
