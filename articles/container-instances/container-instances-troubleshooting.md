---
title: Azure Container Instances 문제 해결
description: Azure Container Instances 관련 문제 해결 방법 알아보기
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bf783c988c0163fe562669a8331c332dbf8d535e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61067333"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances에서 일반적인 문제 해결

이 아티클에서는 컨테이너를 관리하거나 Azure Container Instances에 배포하는 경우 일반적인 문제를 해결하는 방법을 보여줍니다.

## <a name="naming-conventions"></a>명명 규칙

컨테이너 사양을 정의할 때 특정 매개 변수에는 명명 제한 사항을 준수해야 합니다. 컨테이너 그룹 속성에 대한 특정 요구 사항이 포함된 테이블은 다음과 같습니다. Azure 명명 규칙에 대한 자세한 내용은 Azure 아키텍처 센터에서 [명명 규칙][azure-name-restrictions]을 참조하세요.

| 범위 | 길이 | 대/소문자 구분 | 유효한 문자 | 제안된 패턴 | 예 |
| --- | --- | --- | --- | --- | --- |
| 컨테이너 그룹 이름 | 1-64 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 컨테이너 이름 | 1-64 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 컨테이너 포트 | 1에서 65535 사이 |정수  |1에서 65535 사이의 정수 |`<port-number>` |`443` |
| DNS 이름 레이블 | 5-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 하이픈 |`<name>` |`frontend-site1` |
| 환경 변수 | 1-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 영숫자 및 밑줄(_) |`<name>` |`MY_VARIABLE` |
| 볼륨 이름 | 5-63 |대/소문자 구분하지 않음 |첫 번째 또는 마지막 문자를 제외한 모든 위치의 소문자, 숫자 및 하이픈 두 개 연속 하이픈을 포함할 수 없습니다. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>지원되지 않는 이미지 OS 버전

Azure Container Instances에서 지원되지 않는 이미지를 지정하면 `OsVersionNotSupported` 오류가 반환됩니다. 이 오류는 다음과 비슷하며, `{0}`에는 배포하려고 시도한 이미지의 이름이 들어갑니다.

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

이 오류는 SAC(반기 채널) 릴리스 기반의 Windows 이미지를 배포할 때 가장 자주 발생합니다. 예를 들어 Windows 1709 및 1803 버전은 SAC 릴리스이고, 배포 시 이 오류를 생성합니다.

Azure Container Instances는 현재 **Windows Server 2016 LTSC(장기 서비스 채널)** 릴리스 기반의 Windows 이미지만 지원합니다. Windows 컨테이너를 배포할 때 항상 Windows Server 2016(LTSC) 기반 이미지를 배포하면 이 문제를 완화할 수 있습니다. Windows Server 2019(LTSC) 기반 이미지는 지원되지 않습니다.

Windows LTSC 및 SAC 버전에 대한 자세한 내용은 [Windows Server 반기 채널 개요][windows-sac-overview]를 참조하세요.

## <a name="unable-to-pull-image"></a>이미지를 풀링할 수 없음

Azure Container Instances는 초기에 이미지를 풀링할 수 없는 경우 당분간 풀링을 재시도합니다. 이미지 풀링 작업이 계속 실패하면 ACI는 결국 배포에 실패하게 되고 `Failed to pull image` 오류가 발생합니다.

이 문제를 해결하려면 컨테이너 인스턴스를 삭제하고 배포를 다시 시도하세요. 이미지가 레지스트리에 있는지, 이미지 이름을 올바르게 입력했는지 확인합니다.

이미지를 풀링할 수 없는 경우 다음과 같은 이벤트가 [az container show][az-container-show] 명령의 출력에 표시됩니다.

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>컨테이너가 계속 종료되고 다시 시작함(장기 실행 프로세스가 없음)

컨테이너 그룹의 기본 [재시작 정책](container-instances-restart-policy.md)이 **항상**이므로, 실행 완료 후 컨테이너 그룹의 컨테이너는 항상 다시 시작합니다. 작업 기반 컨테이너를 실행하려면 이를 **실패 시**(OnFailure) 또는 **Never**(안 함)로 변경해야 합니다. **OnFailure**를 지정해도 컨테이너가 계속 다시 시작되면 컨테이너에서 실행된 애플리케이션이나 스크립트에 문제가 있을 수 있습니다.

장기 실행 프로세스가 없는 컨테이너 그룹을 실행하면 Ubuntu 또는 Alpine과 같은 이미지와 함께 종료와 재시작이 반복될 수 있습니다. [EXEC](container-instances-exec.md)을 통해 연결하는 작업은 컨테이너에서 활성 상태로 유지되지 않으므로 작동하지 않습니다. 이 문제를 해결 하려면 실행 중인 컨테이너를 유지 하려면 컨테이너 그룹 배포를 사용 하 여 다음과 같은 시작 명령이 포함 됩니다.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2016
 --command-line "ping -t localhost"
```

컨테이너 인스턴스 API 및 Azure Portal에는 `restartCount` 속성이 포함됩니다. 컨테이너에 대한 다시 시작 횟수를 확인하기 위해 Azure CLI에서 [az container show][az-container-show] 명령을 사용할 수 있습니다. 다음 예제 출력(간단히 하기 위해 일부 자름)에서는 출력 마지막에 `restartCount` 속성이 있습니다.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 배포판에 대한 대부분 컨테이너 이미지는 기본 명령으로 Bash와 같은 셸을 설정합니다. 자체 셸은 장기 실행 서비스이므로, 기본값 **Always** 다시 시작 정책이 구성되었을 때 이러한 컨테이너는 즉시 종료된 후 다시 시작 루프를 시작합니다.

## <a name="container-takes-a-long-time-to-start"></a>컨테이너는 시작하는 데 오래 걸림

Azure Container Instances에서 컨테이너 시작 시간에 영향을 주는 두 가지 주요 요인은 다음과 같습니다.

* [이미지 크기](#image-size)
* [이미지 위치](#image-location)

Windows 이미지에는 [추가 고려 사항](#cached-windows-images)이 있습니다.

### <a name="image-size"></a>이미지 크기

컨테이너가 시작하는 데 오래 걸리지만, 결과적으로 성공할 경우 먼저 컨테이너 이미지의 크기를 살펴 보세요. Azure Container Instances가 요청 시 컨테이너 이미지를 풀링하므로, 표시되는 시작 시간은 크기와 직접 관련이 있습니다.

Docker CLI에서 `docker images` 명령을 사용하여 컨테이너 이미지의 크기를 볼 수 있습니다.

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

이미지 크기를 작게 유지하는 핵심 요소는 최종 이미지에 런타임 시 필요하지 않은 것은 아무 것도 포함하지 않는 것입니다. 이 작업을 수행하는 한 가지 방법은 [다단계 빌드][docker-multi-stage-builds]를 사용하는 것입니다. 다단계 빌드를 통해 간편하게 최종 이미지에 빌드 시간에 필요한 추가 콘텐츠가 아닌 애플리케이션에 필요한 아티팩트만 포함하도록 할 수 있습니다.

### <a name="image-location"></a>이미지 위치

컨테이너 시작 시간에 이미지 풀의 영향을 줄이는 다른 방법은 Container Instances를 배포하려는 곳과 동일한 지역의 [Azure Container Registry](/azure/container-registry/)에서 컨테이너 이미지를 호스팅하는 것입니다. 이를 통해 컨테이너 이미지가 이동해야 하는 네트워크 경를 단축하여 다운로드 시간을 크게 줄여 줍니다.

### <a name="cached-windows-images"></a>캐시된 Windows 이미지

Azure Container Instances는 일반적인 Windows 및 Linux 이미지를 기반으로 하는 이미지에 대 한 컨테이너 시작 시간 속도를 캐싱 메커니즘을 사용 합니다. 자세한 목록을 캐시 된 이미지 및 태그를 사용 합니다 [캐시 된 이미지 나열] [ list-cached-images] API.

Windows 컨테이너 시작 시간을 단축하려면 다음과 같은 **두 개의 이미지**에서 **세 개의 최근** 버전 중 하나를 기본 이미지로 사용합니다.

* [Windows Server Core 2016] [ docker-hub-windows-core] (LTSC에만 해당)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows 컨테이너 느린 네트워크 준비

Windows 컨테이너를 처음 만들면 최대 30초(또는 이상, 드문 경우에서) 동안 인바운드 또는 아웃바운드 연결이 설정되지 않을 수 있습니다. 컨테이너 애플리케이션에 인터넷 연결이 필요한 경우 인터넷 연결을 설정하는 데 30초를 허용하도록 지연 및 다시 시도 논리를 추가합니다. 초기 설치 후에 컨테이너 네트워킹은 적절하게 다시 시작해야 합니다.

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류

Azure에서 다양한 지역별 리소스 부하로 인해 컨테이너 인스턴스 배포를 시도할 때 다음 오류가 발생할 수 있습니다.

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

이 오류는 배포하려는 지역에서 과부하로 인해 컨테이너에 대해 지정된 리소스를 해당 시간에 할당할 수 없음을 나타냅니다. 다음 위험 완화 단계 중 하나 이상을 사용하여 문제를 해결합니다.

* [Azure Container Instances에 대한 지역 가용성](container-instances-region-availability.md)에 정의된 매개 변수 내에 컨테이너 배포 설정이 속하는지 확인합니다.
* 컨테이너에 대해 낮은 CPU 및 메모리 설정 지정
* 다른 Azure 지역에 배포
* 나중에 배포

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>기본 Docker API에 연결하거나 권한 있는 컨테이너를 실행할 수 없음

Azure Container Instances는 컨테이너 그룹을 호스트하는 기본 인프라에 대한 직접 액세스를 노출하지 않습니다. 여기에는 컨테이너의 호스트에서 실행되고 권한 있는 컨테이너를 실행하는 Docker API에 대한 액세스가 포함됩니다. Docker 상호 작용이 필요한 경우 [REST 참조 설명서](https://aka.ms/aci/rest)에서 ACI API가 무엇을 지원하는지 확인하세요. 빠진 부분이 있는 경우 [ACI 피드백 포럼](https://aka.ms/aci/feedback)에서 요청을 제출하세요.

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>일치하지 않는 포트로 인해 IP에 액세스하지 못할 수 있습니다.

Azure Container Instances는 현재 일반 docker 구성과 같은 포트 매핑을 지원하지 않지만 수정 내용이 로드맵에 나와 있습니다. 액세스할 수 있어야 하는 데 액세스할 수 없는 IP가 있으면 `ports` 속성을 사용하여 컨테이너 그룹에 노출한 것과 동일한 포트를 수신하도록 컨테이너 이미지를 구성해야 합니다.

## <a name="next-steps"></a>다음 단계

에 대해 알아봅니다 하는 방법 [컨테이너 로그 및 이벤트를 검색할](container-instances-get-logs.md) 컨테이너를 디버깅 하는 데 있습니다.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
