---
title: (사용되지 않음) Azure Kubernetes 클러스터 모니터링 - 운영 관리
description: Log Analytics를 사용하여 Azure Container Service에서 Kubernetes 클러스터 모니터링
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: d7370fc14a5ede23744e04ac9d35140f2368e21f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711789"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(사용되지 않음) Log Analytics를 사용하여 Azure Container Service 클러스터 모니터링

> [!TIP]
> Azure Kubernetes Service를 사용하는 이 문서의 업데이트된 버전은 [컨테이너용 Azure Monitor](../../azure-monitor/insights/container-insights-overview.md)를 참조하세요.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.

또한 `az` Azure CLI 및 `kubectl` 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.
또는 사용자를 위해 이미 `az` Azure cli 및 `kubectl` 도구를 설치한 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용할 수 있습니다.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.
```console
$ az acs kubernetes install-cli
```

kubectl 도구에 kubernetes 키를 설치했는지를 테스트하려면 다음을 실행하면 됩니다.
```console
$ kubectl get nodes
```

위의 명령을 실행한 결과 오류가 출력되면 kubernetes 클러스터 키를 kubectl 도구에 설치해야 합니다. 이 작업은 다음 명령을 사용하여 수행할 수 있습니다.
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Log Analytics를 사용하여 컨테이너 모니터링

Log Analytics는 온-프레미스 및 클라우드 인프라를 관리하고 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. Container Solution은 컨테이너 인벤토리, 성능 및 로그를 단일 위치에서 볼 수 있는 Log Analytics의 솔루션입니다. 중앙 위치에서 로그를 확인하여 컨테이너를 감사하고 문제를 해결하며 호스트에서 매우 과도하게 사용되는 컨테이너를 찾을 수 있습니다.

![](media/container-service-monitoring-oms/image1.png)

Container Solution에 대한 자세한 내용은 [Container Solution Log Analytics](../../azure-monitor/insights/containers.md)를 참조하세요.

## <a name="installing-log-analytics-on-kubernetes"></a>Kubernetes에 Log Analytics 설치

### <a name="obtain-your-workspace-id-and-key"></a>작업 영역 ID 및 키 가져오기
Log Analytics 에이전트가 서비스와 통신하려면 작업 영역 ID 및 작업 영역 키로 구성되어야 합니다. 작업 영역 ID 및 키를 가져오려면 <https://mms.microsoft.com>에서 계정을 만들어야 합니다.
다음 단계에 따라 계정을 만듭니다. 계정 만들기를 완료하면 **Log Analytics** 블레이드에서 작업 영역의 이름을 클릭하여 ID 및 키를 가져올 수 있습니다. 그런 다음, 아래와 같이 **고급 설정**, **연결된 원본**, **Linux 서버**에서 필요한 정보를 찾습니다.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>DaemonSet을 사용하여 Log Analytics 에이전트 설치
DaemonSet은 Kubernetes가 클러스터의 각 호스트에서 컨테이너의 단일 인스턴스를 실행하기 위해 사용합니다.
모니터링 에이전트를 실행하는 데 완벽합니다.

다음은 [DaemonSet YAML 파일](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)입니다. `oms-daemonset.yaml`라는 파일로 저장하고 `WSID` 및 `KEY`에 대한 자리 표시자 값을 파일의 작업 영역 ID 및 키로 바꿉니다.

작업 영역 ID와 키를 DaemonSet 구성에 추가한 후 `kubectl` 명령줄 도구를 사용하여 클러스터에 Log Analytics 에이전트를 설치할 수 있습니다.

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Kubernetes 비밀을 사용하여 Log Analytics 에이전트 설치
Log Analytics 작업 영역 ID 및 키를 보호하려면 Kubernetes 암호를 DaemonSet YAML 파일의 일부로 사용하면 됩니다.

- [리포지토리](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)에서 스크립트, 비밀 템플릿 파일 및 DaemonSet YAML 파일을 복사하고 이러한 항목이 같은 디렉터리에 있는지 확인합니다.
  - 비밀 생성 스크립트 - secret-gen.sh
  - 비밀 템플릿 - secret-template.yaml
    - DaemonSet YAML 파일 - omsagent-ds-secrets.yaml
- 스크립트를 실행합니다. 스크립트에서는 Log Analytics 작업 영역 ID 및 기본 키를 요청합니다. 이러한 ID와 키를 삽입하면 스크립트는 사용자가 실행할 수 있도록 비밀 yaml 파일을 만듭니다.
  ```
  #> sudo bash ./secret-gen.sh
  ```

  - 다음을 실행하여 비밀 Pod를 만듭니다. ```kubectl create -f omsagentsecret.yaml```

  - 확인하려면 다음을 실행합니다.

  ```
  root@ubuntu16-13db:~# kubectl get secrets
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - ```kubectl create -f omsagent-ds-secrets.yaml```을 실행하여 omsagent daemon-set 만들기

### <a name="conclusion"></a>결론
이것으로 끝입니다. 몇 분 후 Log Analytics 대시보드로 이동하는 데이터를 볼 수 있습니다.
