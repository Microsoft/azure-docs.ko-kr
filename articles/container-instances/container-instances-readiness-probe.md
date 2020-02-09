---
title: 컨테이너 인스턴스에서 준비 프로브 설정
description: Azure Container Instances 컨테이너가 준비 된 경우에만 요청을 수신 하도록 프로브를 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935690"
---
# <a name="configure-readiness-probes"></a>준비 상태 프로브 구성

트래픽을 처리 하는 컨테이너 화 된 응용 프로그램의 경우 컨테이너가 들어오는 요청을 처리할 준비가 되었는지 확인 하는 것이 좋습니다. Azure Container Instances는 특정 조건에서 컨테이너에 액세스할 수 없도록 구성을 포함 하는 준비 프로브를 지원 합니다. 준비 상태 프로브는 [Kubernetes 준비 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)처럼 동작 합니다. 예를 들어 컨테이너 앱은 시작 하는 동안 대량 데이터 집합을 로드 해야 할 수 있으며이 시간 동안 요청을 수신 하지 않으려고 합니다.

이 문서에서는 프로브가 성공 했을 때 컨테이너가 트래픽을 수신할 수 있도록 준비 프로브를 포함 하는 컨테이너 그룹을 배포 하는 방법을 설명 합니다.

Azure Container Instances은 비정상 컨테이너가 자동으로 다시 시작 되도록 구성할 수 있는 [선거의 프로브](container-instances-liveness-probe.md)도 지원 합니다.

> [!NOTE]
> 현재는 가상 네트워크에 배포 된 컨테이너 그룹에서 준비 프로브를 사용할 수 없습니다.

## <a name="yaml-configuration"></a>YAML 구성

예를 들어 준비 프로브를 포함 하는 다음 코드 조각을 사용 하 여 `readiness-probe.yaml` 파일을 만듭니다. 이 파일은 작은 웹 앱을 실행 하는 컨테이너로 구성 된 컨테이너 그룹을 정의 합니다. 앱이 공용 `mcr.microsoft.com/azuredocs/aci-helloworld` 이미지에서 배포 됩니다. 이 컨테이너 화 된 앱은 Azure CLI 및 기타 빠른 시작을 [사용 하 여 Azure에 컨테이너 인스턴스 배포](container-instances-quickstart.md) 에도 설명 되어 있습니다.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>시작 명령

배포에는 컨테이너를 처음 실행 하기 시작할 때 실행 되는 시작 명령을 정의 하는 `command` 속성이 포함 되어 있습니다. 이 속성은 문자열 배열을 허용 합니다. 이 명령은 웹 앱이 실행 되는 시간을 시뮬레이션 하지만 컨테이너가 준비 되지 않습니다. 

먼저 셸 세션을 시작 하 고 `node` 명령을 실행 하 여 웹 앱을 시작 합니다. 또한 240 초 동안 절전 모드를 중지 하는 명령을 시작 합니다. 그 후에는 `/tmp` 디렉터리 내 `ready` 라는 파일을 만듭니다.

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>준비 명령

이 YAML 파일은 준비 검사의 역할을 하는 `exec` 준비 명령을 지 원하는 `readinessProbe`를 정의 합니다. 이 예제 준비 명령 예제는 `/tmp` 디렉터리에 `ready` 파일이 있는지 테스트 합니다.

`ready` 파일이 없으면 준비 명령이 0이 아닌 값으로 종료 됩니다. 컨테이너가 계속 실행 되지만 액세스할 수 없습니다. 종료 코드 0으로 명령이 성공적으로 종료 되 면 컨테이너에 액세스할 준비가 된 것입니다. 

`periodSeconds` 속성은 준비 명령이 5 초 마다 실행 되도록 지정 합니다. 준비 상태 프로브는 컨테이너 그룹의 수명 동안 실행 됩니다.

## <a name="example-deployment"></a>예제 배포

다음 명령을 실행 하 여 이전 YAML 구성의 컨테이너 그룹을 배포 합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>준비 검사 보기

이 예제에서는 처음 240 초 동안 준비 명령이 `ready` 파일의 존재 여부를 확인 하는 데 실패 합니다. 반환 된 상태 코드는 컨테이너가 준비 되지 않았다는 신호를 보냅니다.

이러한 이벤트는 Azure Portal 또는 Azure CLI에서 볼 수 있습니다. 예를 들어, 포털은 준비 명령이 실패 하는 경우 트리거되는 `Unhealthy` 형식의 이벤트를 표시 합니다. 

![포털 비정상 이벤트][portal-unhealthy]

## <a name="verify-container-readiness"></a>컨테이너 준비 상태 확인

컨테이너를 시작한 후 처음에는 액세스할 수 없는지 확인할 수 있습니다. 프로 비전 한 후 컨테이너 그룹의 IP 주소를 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

준비 검색이 실패 하는 동안 사이트에 액세스를 시도 합니다.

```bash
wget <ipAddress>
```

출력은 사이트가 처음에 액세스할 수 없는 것을 보여 줍니다.
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 초 후 준비 명령이 성공 하 고 컨테이너에 대한 신호가 준비 됩니다. 이제 `wget` 명령을 실행 하면 성공 합니다.

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

컨테이너가 준비 되 면 웹 브라우저를 사용 하 여 IP 주소를 검색 하 여 웹 앱에 액세스할 수도 있습니다.

> [!NOTE]
> 준비 프로브는 컨테이너 그룹의 수명 동안 계속 실행 됩니다. 준비 명령이 나중에 실패 하는 경우 컨테이너에 다시 액세스할 수 없게 됩니다. 
> 

## <a name="next-steps"></a>다음 단계

준비 프로브는 종속 컨테이너로 구성 된 다중 컨테이너 그룹과 관련 된 시나리오에서 유용할 수 있습니다. 다중 컨테이너 시나리오에 대한 자세한 내용은 [Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)을 참조 하세요.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
