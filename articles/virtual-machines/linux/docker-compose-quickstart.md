---
title: Azure에서 Linux VM에 대한 Docker Compose 사용 | Microsoft Docs
description: Azure CLI를 사용하여 Linux 가상 머신에 Docker 및 Compose를 설치하고 사용하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474084"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Azure에서 다중 컨테이너 애플리케이션 정의 및 실행을 위해 Docker 및 Compose 시작
[Compose](https://github.com/docker/compose)를 사용하면 간단한 텍스트 파일을 사용하여 여러 Docker 컨테이너로 구성된 애플리케이션을 정의할 수 있습니다. 그런 다음, 정의된 환경을 배포하도록 모든 작업을 수행하는 단일 명령으로 애플리케이션을 스핀업합니다. 그 예로, 이 문서에서는 Ubuntu VM의 백 엔드 MariaDB SQL Database로 WordPress 블로그를 신속하게 설정하는 방법을 보여주지만 Compose를 사용하여 좀더 복잡한 애플리케이션을 설정할 수도 있습니다.

이 문서는 2019년 2월 14일에 [Azure Cloud Shell](https://shell.azure.com/bash) 및 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 버전 2.0.58을 사용하여 마지막으로 테스트되었습니다.

## <a name="create-docker-host-with-azure-cli"></a>Azure CLI를 사용하여 Docker 호스트 만들기
최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.

먼저 [az group create](/cli/azure/group)를 사용하여 Docker 환경에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

*cloud-init.txt*라는 파일을 만들고 다음 구성을 붙여넣습니다. `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. 

```yaml
#include https://get.docker.com
```

이제 [az vm create](/cli/azure/vm#az-vm-create)로 VM을 만듭니다. `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init.txt* 구성의 전체 경로를 제공합니다. 다음 예제에서는 *myDockerVM*이라는 VM을 만들고 웹 트래픽에 대해 포트 80을 엽니다.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

VM을 만들고 패키지를 설치하고 앱을 시작하는 데 몇 분 정도 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. VM이 만들어지면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 

                 

## <a name="install-compose"></a>Compose 설치


새 Docker 호스트 VM에 대해 SSH를 수행합니다. 사용자 고유의 IP 주소를 입력합니다.

```bash
ssh azureuser@10.10.111.11
```

VM에 Compose를 설치합니다.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>docker-compose.yml 구성 파일 만들기
VM에서 실행할 Docker 컨테이너를 정의하는 `docker-compose.yml` 구성 파일을 만듭니다. 파일은 각 컨테이너에서 실행되는 이미지, 필요한 환경 변수 및 종속성, 포트, 컨테이너 간 링크를 지정합니다. yml 파일 구문에 대한 세부 정보는 [Compose 파일 참조](https://docs.docker.com/compose/compose-file/)를 참조하세요.

*docker-compose.yml* 파일을 만듭니다. 원하는 텍스트 편집기를 사용하여 파일에 데이터를 추가합니다. 다음 예제에서는 `sensible-editor`에 대한 프롬프트를 통해 사용하려는 편집기를 선택하는 파일을 만듭니다.

```bash
sensible-editor docker-compose.yml
```

다음 예제를 Docker Compose 파일로 붙여넣습니다. 이 구성은 [DockerHub 레지스트리](https://registry.hub.docker.com/_/wordpress/) 의 이미지를 사용하여 WordPress(오픈 소스 블로깅 및 콘텐츠 관리 시스템) 및 연결된 백 엔드 MariaDB SQL 데이터베이스를 설치합니다. 사용자 고유의 *MYSQL_ROOT_PASSWORD*를 입력합니다.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Compose를 사용하여 컨테이너 시작
*docker-compose.yml* 파일과 동일한 디렉터리에서 다음 명령을 실행합니다(환경에 따라 `sudo`를 사용하여 `docker-compose`를 실행해야 할 수 있음).

```bash
sudo docker-compose up -d
```

이 명령은 *docker-compose.yml*에 지정된 Docker 컨테이너를 시작합니다. 이 단계를 완료하려면 1~2분 정도 걸립니다. 다음과 같은 출력이 표시됩니다.

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


컨테이너가 동작하는지 확인하려면 `sudo docker-compose ps`를 입력합니다. 다음과 유사한 결과가 표시됩니다.

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

이제는 포트 80에서 VM에서 직접 WordPress에 연결할 수 있습니다. 웹 브라우저를 열고 VM의 IP 주소 이름을 입력합니다. 이제 WordPress 시작 화면이 표시되면 이 화면에서 설치를 완료하고 애플리케이션을 시작할 수 있습니다.

![WordPress 시작 화면](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>다음 단계
* 다중 컨테이너 앱 빌드 및 배포의 추가 예제는 [Compose 명령줄 참조](https://docs.docker.com/compose/reference/) 및 [사용자 가이드](https://docs.docker.com/compose/)를 확인합니다.
* Azure 리소스 관리자 템플릿, 사용자 자신의 템플릿 또는 [커뮤니티](https://azure.microsoft.com/documentation/templates/)에서 배포된 템플릿을 사용하여, Azure VM을 Docker로 배포하고 Compose로 애플리케이션을 설정합니다. 예를 들어 [Docker를 사용한 WordPress 블로그 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) 템플릿은 Docker 및 Compose를 사용하여 Ubuntu VM에 MySQL 백 엔드와 함께 WordPress를 신속하게 배포합니다.
* Docker Swarm 클러스터와 Docker Compose 통합을 시도합니다. 시나리오의 경우 [Swarm으로 Compose 사용](https://docs.docker.com/compose/swarm/) 을 참조하세요.

