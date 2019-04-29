---
title: Azure Cloud Services 정의 LoadBalancerProbe 스키마 | Microsoft Docs
ms.custom: ''
origin.date: 04/14/2015
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613870"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services 정의 LoadBalancerProbe 스키마
부하 분산 장치 프로브는 UDP 엔드포인트와 역할 인스턴스의 엔드포인트에서 고객이 정의한 상태 프로브입니다. `LoadBalancerProbe`는 독립 실행형 요소가 아닙니다. 서비스 정의 파일의 웹 역할 또는 작업자 역할과 결합됩니다. `LoadBalancerProbe`는 하나 이상의 역할에서 사용할 수 있습니다.

서비스 정의 파일의 기본 확장명은 .csdef입니다.

## <a name="the-function-of-a-load-balancer-probe"></a>부하 분산 장치 프로브 함수
Azure Load Balancer는 들어오는 트래픽을 역할 인스턴스로 라우팅합니다. 부하 분산 장치는 해당 인스턴스의 상태를 판단하기 위해 각 인스턴스를 정기적으로 검색함으로써 트래픽을 수신할 수 있는 인스턴스를 결정합니다. 부하 분산 장치는 분당 여러 번 모든 인스턴스를 검색합니다. 부하 분산 장치에 인스턴스 상태를 제공하기 위한 옵션에는 기본 부하 분산 장치 프로브와 .csdef 파일에서 LoadBalancerProbe를 정의하여 구현하는 사용자 지정 부하 분산 장치 프로브, 이렇게 두 가지가 있습니다.

기본 부하 분산 장치 프로브는 수신 대기하여 인스턴스가 Ready 상태인 경우(즉, 인스턴스가 Busy, Recycling, Stopping 등의 상태가 아닌 경우)에만 HTTP 200 OK 응답으로 응답하는 가상 머신 내의 게스트 에이전트를 활용합니다. 게스트 에이전트가 HTTP 200 OK로 응답하지 않으면 Azure Load Balancer에서 인스턴스를 응답하지 않는 것으로 표시하고 해당 인스턴스로 트래픽 전송을 중지합니다. Azure Load Balancer는 인스턴스를 계속 ping하고, 게스트 에이전트가 HTTP 200에 응답하는 경우 Azure Load Balancer가 해당 인스턴스로 다시 트래픽을 보냅니다. 웹 역할을 사용할 때 웹 사이트 코드는 일반적으로 w3wp.exe에서 실행되는데, 이는 Azure 패브릭 또는 게스트 에이전트에서 모니터링되지 않습니다. 즉, w3wp.exe에서 발생한 오류(예: HTTP 500 반응)는 게스트 에이전트로 보고되지 않으며 부하 분산 장치는 해당 인스턴스를 순환에서 제거할 수 없습니다.

사용자 지정 부하 분산 장치 프로브는 기본 게스트 에이전트 프로브를 재정의하고, 사용자가 역할 인스턴스의 상태를 판단할 수 있도록 고유의 사용자 지정 논리를 만들 수 있습니다. 부하 분산 장치는 정기적으로 엔드포인트를 검색(기본적으로 15초 간격)하고, 인스턴스는 시간 제한 기간(기본값은 31초) 내 TCP ACK 또는 HTTP 200으로 응답하는 경우에 순환에 있는 것으로 간주됩니다. 이는 인스턴스가 90% CPU 이상일 경우 200이 아닌 상태를 반환하는 것과 같이 부하 분산 장치 회전에서 인스턴스를 제거하는 사용자 고유의 논리를 구현하는 데 유용할 수 있습니다. w3wp.exe를 사용하는 웹 역할의 경우 웹 사이트 코드에 오류가 있으면 부하 분산 장치 프로브에 200이 아닌 상태가 반환되므로 웹 사이트의 자동 모니터링도 사용할 수 있습니다. .csdef 파일에서 LoadBalancerProbe를 정의하지 않는 경우 기본 부하 분산 장치 동작(앞에서 설명한)이 사용됩니다.

사용자 지정 부하 분산 장치 프로브를 사용하는 경우 사용자의 논리가 RoleEnvironment.OnStop 메서드를 이용하도록 해야 합니다. 기본 부하 분산 장치 프로브를 사용할 때는 인스턴스가 OnStop 호출에 앞서 순환에서 제거되지만, 사용자 지정 부하 분산 장치 프로브는 OnStop 이벤트를 사용하는 동안 계속해서 200 OK를 반환할 수 있습니다. OnStop 이벤트를 사용하여 캐시를 정리하고 서비스를 중지하거나, 서비스의 런타임 동작에 영향을 미칠 수 있는 변경을 수행한 경우, 사용자 지정 부하 분산 장치 프로브 논리가 인스턴스를 회전에서 제거하도록 해야 합니다.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>부하 분산 장치 프로브에 대한 기본 서비스 정의 스키마
 부하 분산 장치 프로브를 포함하는 서비스 정의 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>스키마 요소
서비스 정의 파일의 `LoadBalancerProbes` 요소에는 다음과 같은 요소가 포함됩니다.

- [LoadBalancerProbes 요소](#LoadBalancerProbes)
- [LoadBalancerProbe 요소](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes 요소
`LoadBalancerProbes` 요소는 부하 분산 장치 프로브의 컬렉션을 설명합니다. 이 요소는 [LoadBalancerProbe 요소](#LoadBalancerProbe)의 부모 요소입니다. 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe 요소
`LoadBalancerProbe` 요소는 모델에 대한 상태 프로브를 정의합니다. 여러 부하 분산 장치 프로브를 정의할 수 있습니다. 

다음 표에서는 `LoadBalancerProbe` 요소의 특성을 설명합니다.

|특성|Type|설명|
| ------------------- | -------- | -----------------|
| `name`              | `string` | 필수 사항입니다. 부하 분산 장치 프로브의 이름입니다. 고유한 이름이어야 합니다.|
| `protocol`          | `string` | 필수 사항입니다. 끝점의 프로토콜을 지정합니다. 가능한 값은 `http` 또는 `tcp`입니다. `tcp`가 지정된 경우 프로브가 성공하려면 수신된 ACK가 필요합니다. `http`가 지정된 경우 프로브가 성공하려면 지정한 URI로부터의 200 OK 응답이 필요합니다.|
| `path`              | `string` | VM에서 상태를 요청하는 데 사용되는 URI입니다. `protocol`이 `http`로 설정된 경우 `path`가 필요합니다. 그렇지 않은 경우 허용되지 않습니다.<br /><br /> 기본값은 없습니다.|
| `port`              | `integer` | 선택 사항입니다. 프로브 통신을 위한 포트입니다. 이는 동일한 포트가 프로브에 사용되므로 모든 엔드포인트에서 선택 사항입니다. 검색을 위해 다른 포트를 구성할 수도 있습니다. 가능한 값 범위는 1에서 65535 사이입니다.<br /><br /> 기본값은 엔드포인트에 의해 설정됩니다.|
| `intervalInSeconds` | `integer` | 선택 사항입니다. 상태에 대한 엔드포인트를 검색하는 빈도에 대한 간격(초)입니다. 일반적으로 간격은 인스턴스가 순환에서 제거되기 전에 전체 두 프로브를 허용하는 할당된 시간 제한 기간(초)의 절반보다 약간 더 짧습니다.<br /><br /> 기본값은 15이며 최소값은 5입니다.|
| `timeoutInSeconds`  | `integer` | 선택 사항입니다. 시간 제한 기간(초)은 응답이 없으면 엔드포인트에 향후 트래픽을 전달하지 않는 프로브에 적용됩니다. 이 값을 사용하면 엔드포인트를 Azure에서 사용되는 일반적인 시간에 비해 더 빠르거나 더 느리게 순환에서 제거할 수 있습니다.<br /><br /> 기본값은 31, 최소 값은 11입니다.|

## <a name="see-also"></a>관련 항목
[Cloud Service(클래식) 정의 스키마](schema-csdef-file.md)