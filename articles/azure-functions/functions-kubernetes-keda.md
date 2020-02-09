---
title: KEDA를 사용 하 여 Kubernetes에서 Azure Functions
description: Kubernetes 기반 이벤트 기반 자동 크기 조정을 사용 하 여 클라우드 또는 온-프레미스에서 Kubernetes의 Azure Functions를 실행 하는 방법에 대해 알아봅니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 83c57b27c1cd1d524805a92381a1ba9eb2e1fbd6
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549039"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA를 사용 하 여 Kubernetes에서 Azure Functions

Azure Functions 런타임은 원하는 위치와 방법을 호스팅할 수 있는 유연성을 제공 합니다.  [Keda](https://keda.sh) (Kubernetes 기반 이벤트 기반 자동 크기 조정)는 Azure Functions 런타임과 도구를 사용 하 여 Kubernetes에서 이벤트 중심 크기 조정을 제공 합니다.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes 기반 함수의 작동 방식

Azure Functions 서비스는 런타임 및 크기 조정 컨트롤러의 두 가지 주요 구성 요소로 구성 됩니다.  함수 런타임은 코드를 실행 하 고 실행 합니다.  런타임에는 함수 실행을 트리거, 로그 및 관리 하는 방법에 대한 논리가 포함 되어 있습니다.  Azure Functions 런타임은 *어디서 나*실행할 수 있습니다.  다른 구성 요소는 크기 조정 컨트롤러입니다.  크기 조정 컨트롤러는 함수를 대상으로 하는 이벤트의 비율을 모니터링 하 고 앱을 실행 하는 인스턴스 수를 사전에 확장 합니다.  자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

Kubernetes 기반 함수는 KEDA를 통해 이벤트 기반 크기 조정을 사용 하 여 [Docker 컨테이너](functions-create-function-linux-custom-image.md) 에서 함수 런타임을 제공 합니다.  KEDA는 이벤트가 발생 하지 않는 경우 0 개 인스턴스 및 최대 *n* 개의 인스턴스로 확장할 수 있습니다. Kubernetes autoscaler (수평 Pod Autoscaler)에 대한 사용자 지정 메트릭을 노출 하 여이를 수행 합니다.  KEDA에서 함수 컨테이너를 사용 하면 Kubernetes 클러스터에서 서버를 사용 하지 않는 함수 기능을 복제할 수 있습니다.  서버 리스 인프라의 [AKS (Azure Kubernetes Services) 가상 노드](../aks/virtual-nodes-cli.md) 기능을 사용 하 여 이러한 함수를 배포할 수도 있습니다.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes에서 KEDA 및 함수 관리

Kubernetes 클러스터에서 함수를 실행 하려면 KEDA 구성 요소를 설치 해야 합니다. [Azure Functions Core Tools](functions-run-local.md)를 사용 하 여이 구성 요소를 설치할 수 있습니다.

### <a name="installing-with-helm"></a>투구를 사용 하 여 설치

투구를 포함 하 여 Kubernetes 클러스터에 KEDA를 설치 하는 다양 한 방법이 있습니다.  배포 옵션은 [Keda 사이트](https://keda.sh/deploy/)에 설명 되어 있습니다.

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes에 함수 앱 배포

Kubernetes를 실행 하는 모든 함수 앱을 KEDA를 실행 하는 클러스터에 배포할 수 있습니다.  함수는 Docker 컨테이너에서 실행 되므로 프로젝트에 `Dockerfile`필요 합니다.  아직 없는 경우 함수 프로젝트의 루트에서 다음 명령을 실행 하 여 Dockerfile을 추가할 수 있습니다.

```cli
func init --docker-only
```

이미지를 빌드하고 함수를 Kubernetes에 배포 하려면 다음 명령을 실행 합니다.

> [!NOTE]
> 핵심 도구는 docker CLI를 활용 하 여 이미지를 빌드하고 게시 합니다. Docker를 이미 설치 하 고 `docker login`를 사용 하 여 계정에 연결 해야 합니다.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`은 함수 앱 이름으로 바꿉니다.

그러면 `local.settings.json` 파일에서 가져온 환경 변수를 포함 하는 Kubernetes `Deployment` 리소스, `ScaledObject` 리소스 및 `Secrets`만들어집니다.

### <a name="deploying-a-function-app-from-a-private-registry"></a>개인 레지스트리에서 함수 앱 배포

위의 흐름은 개인 레지스트리 에서도 작동 합니다.  개인 레지스트리에서 컨테이너 이미지를 끌어오는 경우 `func kubernetes deploy`를 실행할 때 개인 레지스트리 자격 증명을 포함 하는 Kubernetes 암호를 참조 하는 `--pull-secret` 플래그를 포함 합니다.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes에서 함수 앱 제거

배포한 후에는 생성 된 `Secrets` 연결 된 `Deployment``ScaledObject`제거 하 여 함수를 제거할 수 있습니다.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes에서 KEDA 제거

KEDA를 제거 하는 단계는 [keda 사이트에](https://keda.sh/deploy/)설명 되어 있습니다.

## <a name="supported-triggers-in-keda"></a>KEDA에서 지원 되는 트리거

KEDA는 다음 Azure Function 트리거를 지원 합니다.

* [Azure Storage 큐](functions-bindings-storage-queue.md)
* [Azure Service Bus 큐](functions-bindings-service-bus.md)
* [Azure 이벤트/IoT 허브](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ 큐](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 트리거 지원

HTTP 트리거를 노출 하는 Azure Functions를 사용할 수 있지만 KEDA는 직접 관리 하지 않습니다.  KEDA 프로메테우스 트리거를 활용 하 여 [HTTP Azure Functions를 1에서 *n* 인스턴스로 확장할](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)수 있습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [사용자 지정 이미지를 사용 하 여 함수 만들기](functions-create-function-linux-custom-image.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](functions-develop-local.md)
* [Azure 함수 소비 계획의 작동 원리](functions-scale.md)