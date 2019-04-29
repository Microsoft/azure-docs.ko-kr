---
title: Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해 | Microsoft Docs
description: Azure Digital Twins를 사용하여 사람, 장소 및 디바이스 간 관계 모델링
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e7efe1a8632643e2a299b6c9a1b1407414deee4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925881"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digital Twins 개체 모델 및 공간 인텔리전스 그래프 이해

Azure Digital Twins는 물리적 환경 및 환경과 연결된 디바이스, 센서, 사람의 포괄적인 가상 표현을 가능하게 해주는 Azure IoT 서비스입니다. 도메인별 개념을 유용한 모델로 구성하여 개발을 개선합니다. 그런 다음, 모델은 공간 인텔리전스 그래프 내의 배치합니다. 이러한 개념은 사람, 공간, 디바이스 간의 관계와 상호 작용을 정확하게 모델링합니다.

Digital Twins 개체 모델은 도메인별 개념, 범주 및 속성을 설명합니다. 모델은 특정 요구 사항에 맞게 솔루션을 조정하려는 사용자에 의해 미리 정의됩니다. 이러한 미리 정의된 Digital Twins 개체 모델이 합쳐져서 _온톨로지_를 구성합니다. 스마트 건물의 온톨로지는 지역, 장소, 층, 사무실, 영역, 회의실 및 포커스 룸을 설명합니다. 에너지 그리드 온톨로지는 다양한 발전소, 변전소, 에너지 리소스 및 고객을 설명합니다. Digital Twins 개체 모델과 온톨로지를 통해 다양한 시나리오 및 요구 사항을 사용자 지정할 수 있습니다.

Digital Twins 개체 모델 및 온톨로지가 작동하면 _공간 그래프_를 채울 수 있습니다. 공간 그래프는 IoT 솔루션과 관련된 공간, 디바이스 및 사람 사이의 여러 관계를 가상으로 표현한 것입니다. 이 다이어그램은 스마트 건물의 온톨로지를 사용하는 공간 그래프의 예제를 보여줍니다.

![Digital Twins 공간 그래프 빌드][1]

<a id="model"></a>

공간 그래프는 공간, 디바이스, 센서 및 사용자를 하나로 합칩니다. 각각은 실제 세계를 모델링하는 방식으로 함께 연결됩니다. 이 샘플에서 장소 43은 4개의 층으로 구성되어 있으며 각각 다른 영역이 있습니다. 사용자는 자신의 워크스테이션과 연결되어 있고 그래프 일부에 대한 액세스 권한이 부여됩니다. 관리자는 공간 그래프를 변경하는 권한이 있는 반면 방문자는 특정 건물 데이터만 볼 수 있는 권한이 있습니다.

## <a name="digital-twins-object-models"></a>Digital Twins 개체 모델

Digital Twins 개체 모델은 다음과 같은 주요 범주를 지원합니다.

- **공간**은 가상 또는 실제 위치입니다(예: `Tenant`, `Customer`, `Region` 및 `Venue`).
- **디바이스**는 가상 또는 실제 장비입니다(예: `AwesomeCompany Device` 및 `Raspberry Pi 3`).
- **센서**는 이벤트를 검색하는 개체입니다(예: `AwesomeCompany Temperature Sensor` 및 `AwesomeCompany Presence Sensor`).
- **사용자**는 점유자 및 점유자의 특징을 식별합니다.

그 외에도 다음과 같은 개체 범주가 있습니다.

- **리소스**는 공간에 연결되며, 일반적으로 공간 그래프의 개체가 사용할 Azure 리소스를 나타냅니다(예: `IoTHub`).
- **Blob**은 개체(예: 공간, 디바이스, 센서 및 사용자)에 연결됩니다. mime 형식 및 메타데이터가 있는 파일로 사용됩니다(`maps`, `pictures` 및 `manuals`).
- **확장 유형**은 특정 특성을 사용하여 엔터티를 보강하는 확장 가능한 열거형입니다(예: `SpaceType` 및 `SpaceSubtype`).
- **온톨로지**는 확장된 형식의 집합을 나타냅니다(예: `Default`, `Building`, `BACnet` 및 `EnergyGrid`).
- **속성 키 및 값**은 공간, 디바이스, 센서 및 사용자로 구성되는 사용자 지정 특성입니다. 기본 제공 특성과 함께 사용할 수 있습니다. 예를 들어 키로 `DeltaProcessingRefreshTime`, 값으로 `10`을 사용합니다.
- **역할**은 공간 그래프의 사용자 및 디바이스에 할당된 권한 집합입니다(예: `Space Administrator`, `User Administrator` 및 `Device Administrator`).
- **역할 할당**은 역할과 공간 그래프에서 역할과 개체 간의 연결입니다. 예를 들어, 사용자 또는 서비스 주체에 공간 그래프의 공간을 관리할 수 있는 권한이 부여될 수 있습니다.
- **보안 키 저장소**는 디바이스가 Digital Twins와 안전하게 통신할 수 있도록 특정 공간 개체 아래의 계층 구조에 있는 모든 디바이스를 위한 보안 키를 제공합니다.
- **사용자 정의 함수**(UDF)를 사용하면 공간 그래프 내에서 사용자 지정 가능한 센서 원격 분석 데이터를 처리할 수 있습니다. 예를 들어 UDF는 다음과 같은 작업을 수행할 수 있습니다.
  - 센서 값을 설정합니다.
  - 센서 판독값을 기반으로 하는 사용자 지정 논리를 수행하고 출력을 공간으로 설정합니다.
  - 공간에 메타데이터를 연결합니다.
  - 미리 정의된 조건이 충족되면 알림을 보냅니다. 현재 UDF는 JavaScript로 작성할 수 있습니다.
- **검사기**는 지정된 원격 분석 메시지에 대해 실행할 UDF를 결정하는 개체입니다.
- **엔드포인트**는 원격 분석 메시지 및 Digital Twins 이벤트를 라우팅할 수 있는 위치입니다(예: `Event Hub`, `Service Bus` 및 `Event Grid`).

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>공간 인텔리전스 그래프

공간 그래프는 Digital Twins 개체 모델에 정의된 공간, 디바이스 및 사람으로 구성되는 계층 그래프입니다. 공간 그래프는 상속, 필터링, 트래버스, 확장성 및 연장성을 지원합니다. REST API 컬렉션을 사용하여 공간 그래프를 관리하고 상호 작용할 수 있습니다.

구독에 Digital Twins 서비스를 배포하면 루트 노드의 글로벌 관리자가 됩니다. 그런 다음, 전체 구조에 대한 모든 액세스 권한이 자동으로 부여됩니다. Space API를 사용하여 그래프에 공간을 프로비저닝합니다. Sensor API를 사용하여 Device API 및 센서를 통해 서비스를 프로비저닝합니다. [오픈 소스 도구](https://github.com/Azure-Samples/digital-twins-samples-csharp)를 사용하여 그래프를 대량으로 프로비저닝할 수도 있습니다.

**그래프 상속**. 상속은 부모 노드에서 해당 부모 노드에 속한 모든 노드로 전달되는 권한 및 속성에 적용됩니다. 예를 들어 특정 노드의 사용자에게 역할이 할당되면 사용자는 지정된 노드와 해당 노드에 속한 모든 노드에 대한 해당 역할의 권한을 가지게 됩니다. 지정된 노드에 대해 정의된 각 속성 키 및 확장 유형은 해당 노드 아래에 있는 모든 노드에 상속됩니다.

**그래프 필터링**. 필터링은 요청 결과를 좁히는 데 사용됩니다. ID, 이름, 유형, 하위 유형, 부모 공간 및 연결된 공간별로 필터링할 수 있습니다. 센서 데이터 형식, 속성 키와 값, *트래버스*, *minLevel*, *maxLevel* 및 기타 OData 필터 매개 변수를 기준으로 필터링할 수도 있습니다.

**그래프 트래버싱**. 해당 깊이와 너비를 통해 공간 그래프를 트래버스할 수 있습니다. 깊이의 경우 매개 변수인 *트래버스*, *minLevel* 및 *maxLevel*을 사용하여 그래프를 하향식 또는 상향식으로 트래버스할 수 있습니다. 그래프를 트래버스하여 형제 노드를 부모 공간 또는 너비에 대한 해당 하위 항목 중 하나에 직접 연결되도록 합니다. 개체를 쿼리할 때 GET API의 *includes* 매개 변수를 사용하여 해당 개체와 관계가 있는 모든 관련 개체를 가져올 수 있습니다.

**그래프 확장성**. Digital Twins는 그래프 확장성을 보장하므로 실제 워크로드를 처리할 수 있습니다. Digital Twins를 사용하여 부동산, 인프라, 디바이스, 센서, 원격 분석 등의 대규모 포트폴리오를 나타낼 수 있습니다.

**그래프 확장성**. 확장성을 사용하여 새 형식과 온톨로지로 기본 Digital Twins 개체 모델을 사용자 지정할 수 있습니다. 또한 확장 가능한 속성 및 값을 사용하여 Digital Twins의 데이터를 보강할 수 있습니다.

### <a name="spatial-intelligence-graph-management-apis"></a>공간 인텔리전스 그래프 관리 API

[Azure Portal](https://portal.azure.com)에서 Digital Twins를 배포하면 관리 API의 [Swagger](https://swagger.io/tools/swagger-ui/) URL이 자동으로 생성됩니다. **개요** 섹션의 Azure Portal에 다음 형식으로 표시됩니다.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| 이름 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_INSTANCE_NAME | Digital Twins 인스턴스의 이름 |
| YOUR_LOCATION | 인스턴스를 호스팅하는 서버 지역 |

 전체 URL 형식은 이 이미지에 표시됩니다.

![Digital Twins 포털 관리 API][2]

공간 인텔리전스 그래프를 사용하는 방법에 대한 자세한 내용은 Azure Digital Twins 관리 API 미리 보기를 방문하세요.

> [!TIP]
> Swagger 미리 보기는 API 기능 집합을 설명하기 위해 제공됩니다.
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)에서 호스팅됩니다.

[Swagger 사용 방법](how-to-use-swagger.md)에 대해 자세히 알아보세요.

[OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)를 사용하여 모든 API 호출을 인증해야 합니다. API는 [Microsoft REST API 지침 규칙](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)을 따릅니다. 컬렉션을 반환하는 대부분의 API는 [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) 시스템 쿼리 옵션을 지원합니다.

## <a name="next-steps"></a>다음 단계

- 디바이스 연결 및 Digital Twins에 원격 분석 메시지를 보내는 방법에 대한 자세한 내용은 [Azure Digital Twins 디바이스 연결 및 원격 분석 데이터 수신](concepts-device-ingress.md)을 참조하세요.

- 관리 API 제한에 대한 자세한 내용은 [Azure Digital Twins API 관리 및 제한](concepts-service-limits.md)을 참조하세요.

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
