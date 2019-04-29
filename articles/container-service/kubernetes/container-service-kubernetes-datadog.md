---
title: (사용되지 않음) Datadog을 사용하여 Azure Kubernetes 클러스터 모니터링
description: Datadog을 사용하여 Azure Container Service에서 Kubernetes 클러스터 모니터링
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712341"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(사용되지 않음) Datadog을 사용하여 Azure Container Service 클러스터 모니터링

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 `az` Azure CLI 및 `kubectl` 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog은 Azure 컨테이너 서비스 클러스터 내의 컨테이너에서 모니터링 데이터를 수집하는 모니터링 서비스입니다. Datadog에는 컨테이너 내에서 특정 메트릭을 볼 수 있는 Docker 통합 대시보드가 있습니다. 컨테이너에서 수집된 메트릭은 CPU, 메모리, 네트워크 및 I/O로 구성됩니다. Datadog은 메트릭을 컨테이너와 이미지로 분할합니다.

먼저 [계정을 만들어야 합니다](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>DaemonSet에 Datadog 에이전트 설치
DaemonSet은 Kubernetes가 클러스터의 각 호스트에서 컨테이너의 단일 인스턴스를 실행하기 위해 사용합니다.
모니터링 에이전트를 실행하는 데 완벽합니다.

Datadog에 로그인한 후 [Datadog 지침](https://app.datadoghq.com/account/settings#agent/kubernetes)에 따라 DaemonSet를 사용하여 클러스터에 Datadog 에이전트를 설치할 수 있습니다.

## <a name="conclusion"></a>결론
이것으로 끝입니다. 에이전트가 작동 및 실행되면 몇 분 내에 콘솔에 데이터가 표시됩니다. 통합 [kubernetes 대시보드](https://app.datadoghq.com/screen/integration/kubernetes)를 방문하여 클러스터에 대한 요약을 볼 수 있습니다.
