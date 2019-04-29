---
title: (사용되지 않음) Docker API를 사용하여 Azure Swarm 클러스터 관리
description: Azure Container Service에서 Docker Swarm 클러스터에 컨테이너 배포
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 04cc9048271d653bd77fd7f2707c8f510ea8c29f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456568"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>(사용되지 않음) Docker Swarm을 사용하여 컨테이너 관리

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Docker Swarm은 풀링된 Docker 호스트 집합에 컨테이너화된 워크로드를 배포하는 환경을 제공합니다. Docker Swarm은 네이티브 Docker API를 사용합니다. Docker Swarm에서 컨테이너를 관리하는 워크플로는 단일 컨테이너 호스트에서 컨테이너를 관리하는 워크플로와 거의 동일합니다. 이 문서는 Docker Swarm의 Azure 컨테이너 서비스 인스턴스에서 컨테이너화된 워크로드를 배포하는 간단한 예제를 제공합니다. Docker Swarm에 대한 보다 심층적인 설명서는 [Docker.com의 Docker Swarm](https://docs.docker.com/swarm/)을 참조하세요.

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

이 문서의 연습을 위한 필수 구성 요소

[Azure 컨테이너 서비스에서 Swarm 클러스터 만들기](container-service-deployment.md)

[Azure 컨테이너 서비스에서 Swarm 클러스터에 연결](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>새 컨테이너 배포
Docker Swarm에 새 컨테이너를 만들려면 `docker run` 명령을 사용합니다(위의 필수 구성 요소처럼 SSH 터널을 마스터에 열었는지 확인함). 이 예제는 `yeasy/simple-web` 이미지에서 컨테이너를 만듭니다.

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

컨테이너를 만든 후에는 `docker ps` 를 사용하여 컨테이너에 대한 정보를 반환합니다. 컨테이너를 호스팅하는 Swarm 에이전트가 나열되었습니다.

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

이제 Swarm 에이전트 부하 분산 장치의 공개 DNS 이름을 통해 이 컨테이너에서 실행되고 있는 애플리케이션에 액세스할 수 있습니다. 이 정보는 Azure 포털에서 찾을 수 있습니다.  

![실제 방문 결과](./media/container-service-docker-swarm/real-visit.jpg)  

기본적으로 부하 분산 장치에는 포트 80, 8080 및 443이 열려 있습니다. 다른 포트에 연결하려는 경우 에이전트 풀에 대한 Azure Load Balancer에서 해당 포트를 열어야 합니다.

## <a name="deploy-multiple-containers"></a>여러 컨테이너 배포
여러 컨테이너가 시작되기 때문에 'docker 실행'을 여러 번 수행하여 컨테이너가 실행되고 있는 호스트를 확인하도록 `docker ps` 명령을 사용할 수 있습니다. 아래 예제에서는 세 개의 컨테이너가 세 개의 Swarm 에이전트 고르게 분포되어 있습니다.  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Docker Compose를 사용하여 컨테이너 배포
Docker Compose를 사용하여 여러 컨테이너의 배포 및 구성을 자동화할 수 있습니다. 그러려면 SSH(Secure Shell) 터널이 생성되어 있고 DOCKER_HOST 변수가 설정되었는지 확인합니다(위의 필수 구성 요소를 참조).

로컬 시스템에 docker-compose.yml 파일을 만듭니다. 이렇게 하려면 이 [샘플](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)을 사용합니다.

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

`docker-compose up -d` 를 실행하여 컨테이너 배포를 시작합니다.

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

마지막으로 실행 중인 컨테이너의 목록이 반환됩니다. 이 목록에는 Docker Compose를 사용하여 배포된 컨테이너가 표시됩니다.

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

물론 `docker-compose ps`을 사용하여 `compose.yml` 파일에 정의된 컨테이너를 검사할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Docker Swarm에 대해 자세히 알아보기](https://docs.docker.com/swarm/)

