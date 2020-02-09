---
title: Istio 개요
description: Istio 개요 얻기
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3db2a3d75df08e7c668b1983ad9b4493446475df
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530512"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>개요

[Istio][istio] 는 전체 기능을 갖춘 세울 확장 가능한 서비스 메시입니다.

## <a name="architecture"></a>건축

Istio는 [엔보이][envoy-proxy]기반 사이드카 구성 된 데이터 평면을 제공 합니다. 이러한 지능형 프록시는 사용자가 선택한 앱 및 워크 로드에서 모든 네트워크 트래픽을 제어 합니다.

컨트롤 평면은 다음 [구성 요소][what-is-istio]를 통해 구성, 정책 및 원격 분석을 관리 합니다.

- **Mixer** -액세스 제어 및 사용 정책을 적용 합니다. [프로메테우스][prometheus]로 푸시되는 프록시에서 원격 분석을 수집 합니다.

- **파일럿** -프록시에 대한 서비스 검색 및 트래픽 관리 정책/구성을 제공 합니다.

- **성채** -서비스 간 mTLS를 허용 하는 id 및 보안 기능을 제공 합니다.

- **교정쇄** -구성 요소에 대한 구성을 추상화 하 고 제공 합니다.

다음 아키텍처 다이어그램에서는 데이터 평면 및 컨트롤 평면 내의 여러 구성 요소가 상호 작용 하는 방식을 보여 줍니다.


![Istio 구성 요소 및 아키텍처에 대한 개요입니다.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

작업에 대해 Istio를 평가할 때 다음 영역을 이해 하 고 고려해 야 합니다.

- [디자인 목표](#design-goals)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="design-goals"></a>설계 목표

다음 디자인 목표는 Istio 프로젝트를 [안내][design-goals] 합니다.

- **투명도 최대화** -시스템에서 실제 가치를 얻기 위해 최소 작업량을 사용 하 여 채택할 수 있습니다.

- **확장성** -변화 하는 요구에 따라 확장 및 조정할 수 있어야 합니다.

- **이식성** -클라우드, 온-프레미스에서 다양 한 종류의 환경에서 쉽게 실행 됩니다.

- **정책 일관성** -다양 한 리소스의 정책 정의에 일관성이 있습니다.


### <a name="capabilities"></a>기능

Istio는 다음과 같은 기능 집합을 제공 합니다.

- **메시** – 게이트웨이 (다중 클러스터), 가상 컴퓨터 (메시 확장)

- **트래픽 관리** – 라우팅, 분할, 시간 제한, 회로 차단기, 다시 시도, 수신, 송신

- **정책** -액세스 제어, 요금 제한, 할당량, 사용자 지정 정책 어댑터

- **보안** – 인증 (jwt), 권한 부여, 암호화 (mTLS), 외부 CA (HashiCorp 자격 증명 모음)

- **관찰성** – 골든 메트릭, 미러, 추적, 사용자 지정 어댑터, 프로메테우스, grafana

### <a name="scenarios"></a>시나리오

Istio는 다음과 같은 시나리오에 적합 하 고 제안 됩니다.

- 확장성 및 풍부한 기능 집합 필요

- VM 기반 워크 로드를 포함 하는 메시 확장

- 다중 클러스터 서비스 메시

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Azure Kubernetes 서비스 (AKS)에 Istio를 설치 하는 방법에 대해 설명 합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes 서비스 (AKS)에 Istio 설치][istio-install]

또한 Istio 개념 및 추가 배포 모델을 추가로 탐색할 수 있습니다.

- [Istio 개념][what-is-istio]
- [Istio 배포 모델][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
