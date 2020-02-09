---
title: Consul 개요
description: Consul 개요 얻기
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7ffbd2386cd0e50cec8976e6bdd6c5778aa014d7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928435"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>개요

[Consul][consul] 은 런타임 플랫폼 간에 서비스를 연결 하 고 보호 하는 다중 데이터 센터 인식 서비스 네트워킹 솔루션입니다. [Connect][consul-features] 는 서비스 메시 기능을 제공 하는 구성 요소입니다.

## <a name="architecture"></a>아키텍처

Consul은 기본적으로 [엔보이][envoy-proxy]기반 [사이드카][consul-sidecar] 구성 된 데이터 평면을 제공 합니다. Consul에는 플러그형 프록시 아키텍처가 있습니다. 이러한 지능형 프록시는 사용자가 선택한 앱 및 워크 로드에서 모든 네트워크 트래픽을 제어 합니다.

컨트롤 평면은 다음 [구성 요소][consul-architecture]를 통해 구성과 정책을 관리 합니다.

- **서버** -서버 모드에서 실행 되는 서버 모드에서 실행 되는 Consul 에이전트-클러스터 상태를 유지 관리 합니다.

- **클라이언트** -경량 클라이언트 모드에서 실행 되는 Consul 에이전트입니다. 각 계산 노드에는 클라이언트 에이전트가 실행 중 이어야 합니다. 이 클라이언트는 워크 로드와 Consul 구성 간의 브로커 구성과 정책을 구성 합니다. 

다음 아키텍처 다이어그램에서는 데이터 평면 및 컨트롤 평면 내의 여러 구성 요소가 상호 작용 하는 방식을 보여 줍니다.

![Consul 구성 요소 및 아키텍처에 대한 개요입니다.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

워크 로드에 대한 Consul을 평가할 때 다음 영역을 이해 하 고 고려해 야 합니다.

- [Consul 원칙](#consul-principles)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="consul-principles"></a>Consul 원칙

다음 원칙은 Consul 프로젝트를 [안내][consul-principles] 합니다.

- **API 구동** -모든 구성 및 정책을 분류 합니다.

- **언제 어디서 나 실행 및 연결** -런타임 플랫폼 (Kubernetes, Vm, 서버 리스)에서 워크 로드를 연결 합니다.

- 인프라를 통해 안전 하 게 작업 **을 확장 하 고 통합** 합니다.


### <a name="capabilities"></a>Capabilities

Consul은 다음과 같은 기능 집합을 제공 합니다.

- **메시** – 게이트웨이 (다중 데이터 센터), 가상 머신 (클러스터 노드 외부), 서비스 동기화, 기본 제공 디버깅 옵션

- **프록시** – 엔보이, 기본 제공 프록시, 플러그형, Windows 작업에 사용할 수 있는 l4 프록시

- **트래픽 관리** – 라우팅, 분할, 해결

- **정책** – 의도, acl

- **보안** – 권한 부여, 인증, 암호화, SPIFFE 기반 id, 외부 CA (자격 증명 모음), 인증서 관리 및 회전

- **관찰성** – 메트릭, ui 대시보드, 프로메테우스, grafana


### <a name="scenarios"></a>시나리오

Consul은 다음과 같은 시나리오에 적합 하 고 제안 됩니다.

- 기존 연결 작업 확장

- 인증서 관리와 관련 한 규정 준수 요구 사항

- 다중 클러스터 서비스 메시

- 서비스 메시에 포함 될 VM 기반 작업



## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Azure Kubernetes 서비스 (AKS)에서 Consul을 설치 하는 방법을 설명 합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes 서비스 (AKS)에서 Consul 설치][consul-install]

또한 다음과 같은 기능 및 아키텍처를 추가로 탐색할 수 있습니다.

- [Consul 기능][consul-features]
- [Consul 아키텍처][consul-architecture]
- [Consul-연결이 작동 하는 방식][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
