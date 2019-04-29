---
title: Azure Service Fabric 네트워킹 소개 | Microsoft Docs
description: Service Fabric Mesh에서 네트워크, 게이트웨이 및 인텔리전트 트래픽 라우팅에 대해 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811068"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric Mesh 애플리케이션의 네트워킹 소개
이 문서에서는 다양한 유형의 부하 분산 장치, 게이트웨이가 사용자 애플리케이션을 사용하는 네트워크를 다른 네트워크에 연결하는 방법 및 애플리케이션에서 서비스 간에 트래픽이 라우팅되는 방법을 설명합니다.

## <a name="layer-4-vs-layer-7-load-balancers"></a>계층 4와 계층 7 부하 분산 장치 비교
부하 분산 장치는 네크워킹에 대한 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)의 다양한 계층, 주로 계층 4(L4) 및 계층 7(L7)에서 수행할 수 있습니다.  일반적으로 두 가지 유형의 부하 분산 장치가 있습니다.

- L4 부하 분산 장치는 패킷의 내용에 관계 없이 패킷을 배달하는 네트워킹 전송 계층에서 작동합니다. 분산 기준은 IP 주소 및 포트를 제한하므로 부하 분산 장치에서 패킷 헤더만 검사합니다. 예를 들어 클라이언트는 부하 분산 장치에 TCP 연결을 만듭니다. 부하 분산 장치는 연결(SYN에 직접 응답) 연결을 종료하고 백 엔드를 선택하며 새 TCP 연결을 백 엔드(새 SYN 전송)에 만듭니다. 일반적으로 L4 로드 분산 장치는 L4 TCP/UDP 연결 또는 세션의 수준에서만 작동합니다. 따라서 부하 분산 장치에서는 바이트를 리디렉션하고 동일한 세션에서 전송된 바이트가 동일한 백 엔드에서 끝나는지 확인합니다. L4 부하 분산 장치는 이동 중인 바이트의 애플리케이션 세부 정보를 인식하지 못합니다. 바이트는 애플리케이션 프로토콜일 수 있습니다.

- L7 부하 분산 장치는 각 패킷의 콘텐츠를 처리하는 애플리케이션 계층에서 작동합니다. HTTP, HTTPS 또는 WebSocket과 같은 프로토콜을 이해하기 때문에 패킷 콘텐츠를 검사합니다. 이렇게 하면 부하 분산 장치에서 고급 라우팅을 수행할 수 있습니다. 예를 들어 클라이언트는 부하 분산 장치에 단일HTTP/2 TCP 연결을 만듭니다. 그다음 부하 분산 장치는 두 개의 백 엔드 연결을 계속해서 만듭니다. 클라이언트가 부하 분산 장치에 두 개의 HTTP/2 스트림을 보내면 스트림 1은 백 엔드 1에 보내고 스트림 2는 백 엔드 2에 보냅니다. 따라서 아주 다른 요청 부하가 있는 멀티플렉싱 클라이언트 조차도 백 엔드에 효과적으로 분산될 수 있습니다. 

## <a name="networks-and-gateways"></a>네트워크 및 게이트웨이
[Service Fabric 리소스 모델](service-fabric-mesh-service-fabric-resources.md)에서 네트워크 리소스는 종속성을 가지는 애플리케이션 또는 서비스 리소스에 관계없이 리소스를 개별적으로 배포할 수 있습니다. 인터넷에 개방된 애플리케이션에 대한 네트워크를 만드는 데 사용됩니다. 다른 애플리케이션의 다중 서비스가 동일한 네트워크에 속할 수 있습니다. 비공개네트워크는 Service Fabric에서 만들고 관리되며 Azure Virtual Network(VNET)가 아닙니다. 애플리케이션은 네트워크 리소스에서 동적으로 추가하고 제거하여 VNET 연결을 사용하고 사용하지 않을 수 있습니다. 

게이트웨이는 두 네트워크를 연결하는 데 사용됩니다. 게이트웨이 리소스는 프로토콜에 대한 L4 라우팅 및 고급 HTTP(S) 애플리케이션 라우팅에 대한 L7 라우팅을 제공하는 [Envoy 프록시](https://www.envoyproxy.io/)를 배포합니다. 게이트웨이가 외부 네트워크에서 사용자 네트워크로 트래픽을 라우팅하고 트래픽을 라우팅할 서비스를 결정합니다.  외부 네트워크는 다른 Azure 애플리케이션 및 리소스에 연결할 수 있는 개방형 네트워크(즉 공용 인터넷) 또는 Azure Virtual Network일 수 있습니다. 

![네트워크 및 게이트웨이][Image1]

`ingressConfig`를 사용하여 네트워크 리소스를 만드는 경우 공용 IP는 네트워크 리소스에 할당됩니다. 공용 IP는 네트워크 리소스의 수명에 연결됩니다.

메시 애플리케이션을 만들 때 기존 네트워크 리소스를 참조해야 합니다. 새 공용 포트를 추가하거나 수신 구성에서 기존 포트를 제거할 수 있습니다. 애플리케이션 리소스에서 참조하고 있는 경우 네트워크 리소스에 대한 삭제는 실패하게 됩니다. 애플리케이션이 삭제되면 네트워크 리소스가 제거됩니다.

## <a name="next-steps"></a>다음 단계 
Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png