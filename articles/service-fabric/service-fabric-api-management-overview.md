---
title: Azure Service Fabric 및 API Management 개요 | Microsoft Docs
description: 이 문서에서는 Service Fabric 애플리케이션에 대한 게이트웨이로 Azure API Management를 사용하는 것을 소개합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 0dac2730bcc13b979de6a8faaaa53c0aaf15e902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621890"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric 및 API Management 개요

일반적으로 클라우드 애플리케이션에는 사용자, 장치 또는 기타 애플리케이션 수신을 위한 단일 지점을 제공하는 프런트 엔드 게이트웨이가 필요합니다. Service Fabric에서 게이트웨이는 [ASP.NET Core 애플리케이션](service-fabric-reliable-services-communication-aspnetcore.md), 트래픽 수신을 위해 설계된 기타 서비스(예: [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), [Azure API Management](https://docs.microsoft.com/azure/api-management/))와 같은 상태 비저장 서비스일 수 있습니다.

이 문서에서는 Service Fabric 애플리케이션에 대한 게이트웨이로 Azure API Management를 사용하는 것을 소개합니다. API Management는 Service Fabric과 직접 통합되므로 다양한 라우팅 규칙 집합을 사용하여 백 엔드 Service Fabric 서비스에 API를 게시할 수 있습니다. 

## <a name="availability"></a>가용성

> [!IMPORTANT]
> 이 기능은 필수 가상 네트워크 지원으로 인해 API Management의 **프리미엄** 및 **개발자** 계층에서 사용할 수 있습니다.

## <a name="architecture"></a>아키텍처

공통 Service Fabric 아키텍처는 HTTP API를 노출하는 백 엔드 서비스에 HTTP 호출을 수행하는 단일 페이지 웹 애플리케이션을 사용합니다. [Service Fabric 시작 애플리케이션 예제](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)는 이 아키텍처의 예를 보여 줍니다.

이 시나리오에서는 상태 비저장 웹 서비스가 Service Fabric 애플리케이션에 대한 게이트웨이로 작동합니다. 이 방법에서는 다음 다이어그램에 표시된 것처럼 HTTP 요청을 백 엔드 서비스로 프록시할 수 있는 웹 서비스를 작성해야 합니다.

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-web-app-stateless-gateway]

애플리케이션이 복잡해짐에 따라 다양한 백 엔드 서비스 앞에 API를 제공해야 하는 게이트웨이도 복잡해집니다. Azure API Management는 최소한의 작업으로 라우팅 규칙, 액세스 제어, 속도 제한, 모니터링, 이벤트 로깅 및 응답 캐싱 등 복잡한 API를 처리하기 위해 설계되었습니다. Azure API Management는 Service Fabric 서비스 검색, 파티션 확인, 요청을 Service Fabric의 백 엔드 서비스에 지능적으로 직접 라우팅할 복제본 선택을 지원하므로 사용자 고유의 상태 비저장 API 게이트웨이를 작성할 필요가 없습니다. 

이 시나리오에서 웹 UI는 여전히 웹 서비스를 통해 제공되지만 다음 다이어그램처럼 HTTP API 호출은 API Management를 통해 관리 및 라우팅됩니다.

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-apim-web-app]

## <a name="application-scenarios"></a>애플리케이션 시나리오

Service Fabric의 서비스는 상태 비저장 또는 상태 저장일 수 있으며 단일 항목, int-64 범위 및 이름 지정(named)의 3가지 체계 중 하나를 사용하여 분할될 수 있습니다. 서비스 엔드포인트 확인을 사용하려면 특정 서비스 인스턴스의 특정 파티션을 식별해야 합니다. 서비스 엔드포인트를 확인할 때는 서비스 인스턴스 이름(예: `fabric:/myapp/myservice`)과 서비스의 특정 파티션을 지정해야 합니다(단, 단일 파티션인 경우는 제외).

상태 비저장 서비스, 상태 저장 서비스 및 모든 파티션 체계를 조합하여 Azure API Management를 사용할 수 있습니다.

## <a name="send-traffic-to-a-stateless-service"></a>트래픽을 상태 비저장 서비스로 보내기

가장 간단한 경우, 트래픽은 상태 비저장 서비스 인스턴스로 전달됩니다. 이를 위해 API Management 작업에는 Service Fabric 백 엔드에서 특정 상태 비저장 서비스 인스턴스에 매핑되는 Service Fabric 백 엔드가 있는 인바운드 처리 정책이 포함됩니다. 해당 서비스에 전송된 요청은 상태 비저장 서비스 인스턴스의 임의 복제본으로 전송됩니다.

#### <a name="example"></a>예
다음 시나리오에서 Service Fabric 애플리케이션에는 내부 HTTP API를 노출하는 `fabric:/app/fooservice` 이름의 상태 비저장 서비스가 포함됩니다. 서비스 인스턴스 이름은 잘 알려져 있으며 API Management 인바운드 처리 정책에 직접 하드 코딩될 수 있습니다. 

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>트래픽을 상태 저장 서비스로 보내기

상태 비저장 서비스 시나리오와 마찬가지로 트래픽을 상태 저장 서비스 인스턴스에 전달할 수 있습니다. 이 경우 API Management 작업에는 요청을 특정 *상태 저장* 서비스 인스턴스의 특정 파티션에 매핑하는 Service Fabric 백 엔드가 있는 인바운드 처리 정책이 포함됩니다. 각 요청을 매핑할 파티션은 들어오는 HTTP 요청에서 입력(예: URL 경로의 값)을 사용하는 람다 메서드를 통해 계산됩니다. 요청을 주 복제본에만 또는 읽기 작업을 위해 임의 복제본으로 보내도록 정책을 구성할 수 있습니다.

#### <a name="example"></a>예

다음 시나리오에서 Service Fabric 애플리케이션에는 내부 HTTP API를 노출하는 `fabric:/app/userservice` 이름의 분할된 상태 저장 서비스가 포함됩니다. 서비스 인스턴스 이름은 잘 알려져 있으며 API Management 인바운드 처리 정책에 직접 하드 코딩될 수 있습니다.  

서비스는 두 파티션을 포함하고 키 범위가 `Int64.MinValue` ~ `Int64.MaxValue`인 Int64 파티션 체계를 사용하여 분할됩니다. 여기서 파티션 키 계산을 위해 어떤 알고리즘이라도 사용할 수 있지만, 백 엔드 정책은 URL 요청 경로에 제공된 `id` 값을 64비트 정수로 변환하여 해당 범위 내에서 파티션 키를 계산합니다. 

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>트래픽을 여러 상태 비저장 서비스로 보내기

보다 고급 시나리오에서는 요청을 둘 이상의 서비스 인스턴스로 매핑하는 API Management 작업을 정의할 수 있습니다. 이 경우 각 작업은 요청을 들어오는 HTTP 요청의 값(예: URL 경로 또는 쿼리 문자열, 상태 저장 서비스의 경우 서비스 인스턴스 내에 있는 파티션)에 따라 특정 서비스 인스턴스로 매핑하는 정책을 포함합니다. 

이를 위해 API Management 작업에는 들어오는 HTTP 요청에서 검색된 값에 따라 Service Fabric 백 엔드에서 상태 비저장 서비스 인스턴스에 매핑되는 Service Fabric 백 엔드가 있는 인바운드 처리 정책이 포함됩니다. 서비스 인스턴스에 대한 요청은 서비스 인스턴스의 임의 복제본으로 전송됩니다.

#### <a name="example"></a>예

이 예제에서는 애플리케이션의 각 사용자에 대한 새로운 상태 비저장 서비스 인스턴스가 다음 수식을 사용하여 동적으로 생성된 이름으로 만들어집니다.
 
- `fabric:/app/users/<username>`

  각 서비스에는 고유한 이름이 있지만 사용자 또는 관리자 입력에 대한 응답으로 서비스가 생성되어 APIM 정책이나 라우팅 규칙에 하드 코딩될 수 없기 때문에 이름을 미리 알 수 없습니다. 대신, 요청을 보낼 서비스 이름은 URL 요청 경로에 제공된 `name` 값에서 백 엔드 정책 정의에 생성됩니다. 예를 들면 다음과 같습니다.

  - `/api/users/foo`에 대한 요청은 서비스 인스턴스 `fabric:/app/users/foo`로 라우팅됩니다.
  - `/api/users/bar`에 대한 요청은 서비스 인스턴스 `fabric:/app/users/bar`로 라우팅됩니다.

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>트래픽을 여러 상태 저장 서비스로 보내기

상태 비저장 서비스 예제와 마찬가지로, API Management 작업에서 요청을 둘 이상의 **상태 저장** 서비스 인스턴스로 매핑할 수 있으며 이 경우 각 상태 저장 서비스 인스턴스에 대해 파티션 확인을 수행해야 할 수도 있습니다.

이를 위해 API Management 작업에는 들어오는 HTTP 요청에서 검색된 값에 따라 Service Fabric 백 엔드에서 상태 저장 서비스 인스턴스에 매핑되는 Service Fabric 백 엔드가 있는 인바운드 처리 정책이 포함됩니다. 요청을 특정 서비스 인스턴스에 매핑하는 것 외에도, 요청을 서비스 인스턴스 내의 특정 파티션이나 필요에 따라 해당 파티션 내의 주 복제본 또는 임의 보조 복제본에 매핑할 수도 있습니다.

#### <a name="example"></a>예

이 예제에서는 애플리케이션의 각 사용자에 대한 새로운 상태 저장 서비스 인스턴스가 다음 수식을 사용하여 동적으로 생성된 이름으로 만들어집니다.
 
- `fabric:/app/users/<username>`

  각 서비스에는 고유한 이름이 있지만 사용자 또는 관리자 입력에 대한 응답으로 서비스가 생성되어 APIM 정책이나 라우팅 규칙에 하드 코딩될 수 없기 때문에 이름을 미리 알 수 없습니다. 대신, 요청을 보낼 서비스 이름은 URL 요청 경로에 제공된 `name` 값에서 백 엔드 정책 정의에 생성됩니다. 예를 들면 다음과 같습니다.

  - `/api/users/foo`에 대한 요청은 서비스 인스턴스 `fabric:/app/users/foo`로 라우팅됩니다.
  - `/api/users/bar`에 대한 요청은 서비스 인스턴스 `fabric:/app/users/bar`로 라우팅됩니다.

각 서비스 인스턴스는 두 파티션을 포함하고 키 범위가 `Int64.MinValue` ~ `Int64.MaxValue`인 Int64 파티션 체계를 사용해서도 분할됩니다. 여기서 파티션 키 계산을 위해 어떤 알고리즘이라도 사용할 수 있지만, 백 엔드 정책은 URL 요청 경로에 제공된 `id` 값을 64비트 정수로 변환하여 해당 범위 내에서 파티션 키를 계산합니다. 

![Service Fabric 및 Azure API Management 토폴로지 개요][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>다음 단계

[자습서](service-fabric-tutorial-deploy-api-management.md)에 따라 API Management가 있는 첫 번째 Service Fabric 클러스터를 설정하고 API Management를 통해 서비스로 요청을 전달합니다.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png