---
title: Azure IoT Edge 모듈 기술 자산 만들기 | Microsoft Docs
description: IoT Edge 모듈에 대한 기술 자산을 만듭니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916211"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge 모듈 기술 자산 준비

이 문서에서는 IoT Edge 모듈 기술 자산을 Azure Marketplace에 게시하기 전에 충족해야 하는 요구 사항을 설명합니다.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>IoT Edge 모듈 이해 및 시작

IoT Edge 모듈은 IoT Edge 디바이스에서 실행하도록 만들어진 Docker 호환 컨테이너입니다.

- IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)를 참조하세요.
- IoT Edge 모듈 개발을 시작하려면 [IoT Edge 모듈 개발을 위한 요구 사항 및 도구](https://docs.microsoft.com/azure/iot-edge/module-development)를 참조하세요.

## <a name="technical-requirements"></a>기술적인 요구 사항

IoT Edge 모듈을 인증하고 Azure Marketplace에 게시하기 위해 다음 기술 요구 사항이 충족되어야 합니다.

### <a name="platform-support"></a>플랫폼 지원

IoT Edge 모듈은 다음 플랫폼 옵션 중 하나를 지원해야 합니다.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼

IoT Edge에서 지원되는 모든 계층 1 플랫폼을 지원합니다([Azure IoT Edge 지원](https://docs.microsoft.com/azure/iot-edge/support)에 기록된 대로). 더 나은 고객 경험을 제공하기 때문에 이 옵션을 권장합니다. 이 조건을 충족하는 모듈이 소개됩니다. 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- GitHub [매니페스트 도구](https://github.com/estesp/manifest-tool)를 사용하여 빌드된 매니페스트 태그인 `latest` 태그 및 버전 태그(예: `1.0.1`)를 제공합니다.
- [Marketplace 탭](./cpp-marketplace-tab.md)을 사용하여 [호환 IoT Edge 인증 디바이스](https://aka.ms/iot-edge-certified)에 링크를 추가합니다. 이 링크는 고객이 인증된 디바이스를 찾거나 검색할 수 있는 웹 사이트인 `https://aka.ms/iot-edge-certified`로 이동합니다. 이 웹 사이트는 [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) 디바이스 카탈로그라고도 합니다.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼의 하위 집합
  
IoT Edge에서 지원되는 계층 1 플랫폼의 하위 집합(하나 이상)을 지원합니다([Azure IoT Edge 지원](https://docs.microsoft.com/azure/iot-edge/support)에 기록된 대로). 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- 둘 이상의 플랫폼이 지원되는 경우 GitHub [매니페스트 도구](https://github.com/estesp/manifest-tool)를 사용하여 빌드된 매니페스트 태그인 `latest` 태그 및 버전 태그(예: `1.0.1`)를 제공합니다. 하나의 플랫폼이 지원되는 경우에만 매니페스트 태그는 선택 사항입니다.
- [Marketplace 탭](./cpp-marketplace-tab.md)을 사용하여 [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) 디바이스 카탈로그의 하나 이상의 IoT Edge 디바이스에 링크를 제공합니다.

### <a name="device-dimensions"></a>디바이스 차원

대상 IoT Edge 디바이스의 IoT Edge 모듈 차원(CPU/RAM/저장소/GPU/등)은 다음 요구 사항을 충족해야 합니다.

- 모듈은 [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) 디바이스 카탈로그에서 **하나 이상의 IoT Edge 인증** 디바이스를 사용하여 작업해야 합니다.
- **최소 하드웨어 요구 사항**은 제품의 설명에서 마지막 단락으로 문서화되어야 합니다([Marketplace 탭](./cpp-marketplace-tab.md) 아래). 필요에 따라 크게 다를 경우 권장되는 하드웨어 요구 사항을 나열할 수도 있습니다. 예를 들어 제품 설명의 끝에 다음 섹션을 추가합니다.

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>구성

또한 IoT Edge 디바이스에 가능한 간편하게 배포할 수 있도록 하는 기본 구성 설정을 포함합니다. 이 컨테이너는 Edge 허브 및 IoT Hub와 통신할 수 있도록 하는 IoT Edge 모듈 SDK를 포함할 수도 있습니다.

#### <a name="default-configuration"></a>기본 구성

IoT Edge 모듈은 [Cloud 파트너 포털의 SKU 탭](./cpp-skus-tab.md)에 제공된 기본 설정으로 시작할 수 있어야 합니다. 다음 기본 설정을 사용할 수 있습니다.

- 기본 **경로**
- 기본 **쌍 desired 속성**
- 기본 **환경 변수**
- 기본 **createOptions**

기본값에 필요한 매개 변수가 적합하지 않은 시나리오에서(예: 고객 서버의 IP 주소) 매개 변수를 기본값으로 추가합니다. 이 값은 대문자로 대괄호로 묶입니다. 이 예제의 경우 다음 기본 환경 변수를 설정할 수 있습니다.

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>구성 문서

IoT Edge 모듈의 모든 구성 설정은 명확하게 문서화되어야 합니다(해당 경로, 쌍 desired 속성, 환경 변수, createOptions 등을 사용하는 방법). 해당 설명서에 대한 링크를 제공하거나 설명서는 제품/SKU 설명의 일부여야 합니다.

### <a name="tags-and-versioning"></a>태그 및 버전 관리

고객은 모듈을 쉽게 배포하고 마켓플레이스에서 업데이트를 자동으로 가져올 수 있어야 합니다(개발자 시나리오에서). 테스트한 정확한 버전을 사용 및 고정할 수도 있어야 합니다(프로덕션 시나리오에서).

이러한 고객의 기대를 충족하고 마켓플레이스에 게시하기 위해 IoT Edge 모듈은 다음 요구 사항을 충족해야 합니다.

- 지원되는 모든 플랫폼에서 최신 버전을 가리키는 매니페스트 `latest` 태그를 포함합니다.
- 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다.
- 지원되는 모든 플랫폼에서 특정 버전을 가리키는 `1.0.1`과 같은 "버전" 태그를 포함합니다.
- `1.0.1`과 같은 "버전" 태그는 변경하지 않아야 하므로 업데이트하지 마십시오.

>[!Note]
>필요에 따라 버전 관리는 `2.0` 및 `1.0`과 같은 "롤링 버전" 태그를 포함할 수 있습니다. 이는 동시에 여러 주 버전 유지 관리를 지원합니다.

### <a name="telemetry"></a>원격 분석

IoT 모듈 SDK를 사용하는 모듈은 원격 분석을 위해 `PublisherId.OfferId.SkuId`에 고유 모듈 식별자를 설정해야 합니다. 고유 식별자를 통해 Azure Marketplace에서 실행되는 모듈 인스턴스의 번호를 식별할 수 있습니다.

 IoT 모듈 SDK에서 다음 메서드를 사용하여 `ProductInfo`를 이 식별자로 설정합니다.

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

IoT 모듈 SDK를 사용하지 않는 모듈의 경우 Cloud 파트너 포털을 통해 다운로드 횟수와 같은 덜 정확한 인사이트를 얻을 수 있습니다.

### <a name="security"></a>보안

IoT Edge 모듈은 가능한 호스트에 대한 최소한의 권한 있는 액세스를 요청해야 합니다. [권한 있는 모듈](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)을 피해야 합니다.

### <a name="module-iot-sdk"></a>모듈 IoT SDK

IoT 모듈 SDK를 포함하는 것은 인증을 위한 필수 조건이 아닙니다. 그러나 IoT 모듈 SDK가 포함되면 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 라우팅 또는 클라우드에 메시지를 보낼 수 있습니다.

IoT 모듈 SDK는 실행되는 모듈 인스턴스의 번호에 대한 원격 분석 데이터를 가져와야 합니다.


## <a name="recertification-process"></a>재인증 프로세스

<!-- Add legal time windows-->
파트너와 같은 해당 모듈에 영향을 주는 주요 변경 될 때마다 라는 알림을 받게 됩니다.

- IoT Edge에서 지원되는 계층 1 운영 체제/아키텍처 지원 매트릭스
- IoT 모듈 SDK
- IoT Edge 런타임
- IoT Edge 모듈 인증 지침

파트너는 해당 모듈을 업데이트하고 Cloud 파트너 포털 도구를 사용하여 다시 인증해야 합니다.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Azure Container Registry에서 IoT Edge 모듈 호스팅

Cloud 파트너 포털에 IoT Edge 모듈을 업로드하려면 먼저 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)(ACR)에서 호스트해야 합니다. 모듈은 매니페스트 태그에서 참조하는 이미지 태그를 포함하여 게시하려는 모든 태그를 포함해야 합니다.


## <a name="next-steps"></a>다음 단계

- [IoT Edge 모듈 제품 만들기](./cpp-create-offer.md)
