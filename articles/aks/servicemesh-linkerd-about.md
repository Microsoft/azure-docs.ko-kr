---
title: Linkerd 개요
description: Linkerd 개요 얻기
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 9d0a65f2cf557120ec5a551494e697c876ad5c59
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530213"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>개요

[Linkerd][linkerd] 는 사용 하기 쉽고 간단한 서비스 메시입니다.

## <a name="architecture"></a>건축

Linkerd는 ultralight [linkerd][linkerd-proxy] 다루고 proxy 사이드카로 구성 된 데이터 평면을 제공 합니다. 이러한 지능형 프록시는 사용자가 선택한 앱 및 워크 로드에서 모든 네트워크 트래픽을 제어 합니다. 또한 프록시는 [프로메테우스][prometheus] 메트릭 엔드포인트을 통해 메트릭을 노출 합니다.

컨트롤 평면은 다음과 같은 [구성 요소][linkerd-architecture]를 통해 구성 및 집계 된 원격 분석을 관리 합니다.

- **컨트롤러** -Linkerd CLI 및 대시보드를 구동 하는 api를 제공 합니다. 프록시에 대한 구성을 제공 합니다.

- 요청 및 응답에 대한 실시간 감시를 **탭** 하 여 설정 합니다.

- **Id** -서비스 간 mTLS를 허용 하는 id 및 보안 기능을 제공 합니다.

- **웹** -Linkerd 대시보드를 제공 합니다.


다음 아키텍처 다이어그램에서는 데이터 평면 및 컨트롤 평면 내의 여러 구성 요소가 상호 작용 하는 방식을 보여 줍니다.


![Linkerd 구성 요소 및 아키텍처에 대한 개요입니다.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

작업에 대해 Linkerd를 평가할 때 다음 영역을 이해 하 고 고려해 야 합니다.

- [디자인 원칙](#design-principles)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="design-principles"></a>디자인 원칙

다음 디자인 원칙은 Linkerd 프로젝트를 [안내][design-principles] 합니다.

- **간단** 하 게 유지-쉽게 사용 하 고 이해 해야 합니다.

- **리소스 요구 사항 최소화** -최소한의 성능 및 리소스 비용을 부과 합니다.

- **단지 작업** -기존 응용 프로그램을 중단 하지 않으며 복잡 한 구성이 필요 하지 않습니다.


### <a name="capabilities"></a>기능

Linkerd는 다음과 같은 기능 집합을 제공 합니다.

- **메시** – 기본 제공 디버깅 옵션

- **트래픽 관리** – 분할, 시간 제한, 다시 시도, 수신

- **보안** – 암호화 (mTLS), 인증서 autorotated 24 시간 마다

- **관찰성** – 골든 메트릭, 탭, 추적, 서비스 프로필 및 경로 메트릭 별, 토폴로지 그래프가 포함 된 웹 대시보드, 프로메테우스, grafana


### <a name="scenarios"></a>시나리오

Linkerd는 다음과 같은 시나리오에 적합 하 고 제안 됩니다.

- 필수 기능 요구 사항 집합만 사용 하는 것이 간단 합니다.

- 관찰성 및 간단한 트래픽 관리에 집중 된 낮은 대기 시간, 낮은 오버 헤드


## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Azure Kubernetes 서비스 (AKS)에 Linkerd를 설치 하는 방법에 대해 설명 합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes 서비스 (AKS)에 Linkerd 설치][linkerd-install]

또한 Linkerd 기능과 아키텍처를 추가로 탐색할 수 있습니다.

- [Linkerd 기능][linkerd-features]
- [Linkerd 아키텍처][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md