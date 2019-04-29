---
title: Docker Machine을 사용하여 Azure에서 Linux 호스트 만들기
description: Docker Machine을 사용하여 Azure에서 Docker 호스트를 만드는 방법에 대해 설명합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: da0e393dc2ae0d93ecc49745a42ffac4669ed74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386942"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Docker Machine을 사용하여 Azure에서 호스트를 만드는 방법
이 문서는 [Docker Machine](https://docs.docker.com/machine/)을 사용하여 Azure에서 호스트를 만드는 방법을 자세히 설명합니다. `docker-machine` 명령은 Azure에서 Linux VM(가상 머신)을 만든 다음 Docker를 설치합니다. 동일한 로컬 도구 및 워크플로를 사용하여 Azure에서 Docker 호스트를 관리할 수 있습니다. Windows 10에서 Docker-컴퓨터를 사용하려면 Linux bash를 사용해야 합니다.

## <a name="create-vms-with-docker-machine"></a>Docker Machine으로 VM 만들기
먼저, 다음과 같이 [az 계정 표시](/cli/azure/account)로 Azure 구독 ID를 가져옵니다.

```azurecli
sub=$(az account show --query "id" -o tsv)
```

드라이버로 *azure*를 지정하여 `docker-machine create`로 Azure에서 Docker 호스트 VM을 만듭니다. 자세한 내용은 [Docker Azure 드라이버 설명서](https://docs.docker.com/machine/drivers/azure/)를 참조하세요.

다음 예제에서는 *myVM*이라는 VM을 만들고 "표준 D2 v2" 계획에 따라 *azureuser*라는 사용자 계정을 만들고 호스트 VM에서 포트 *80*을 엽니다. 프롬프트를 따라 Azure 계정에 로그인하고 리소스를 만들고 관리하도록 Docker Machine에 사용 권한을 부여합니다.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
    myvm
```

출력은 다음 예제와 유사합니다.

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Docker 셸 구성
Azure에서 Docker 호스트에 연결하려면 적절한 연결 설정을 정의합니다. 출력의 끝에서 설명했듯이 다음과 같이 Docker 호스트에 대한 연결 정보를 확인합니다. 

```bash
docker-machine env myvm
```

다음 예제와 유사하게 출력됩니다.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

연결 설정을 정의하기 위해 제안된 구성 명령(`eval $(docker-machine env myvm)`)을 실행하거나 환경 변수를 직접 설정할 수 있습니다. 

## <a name="run-a-container"></a>컨테이너 실행
작업에서 컨테이너를 확인하려면 기본 NGINX 웹 서버를 실행합니다. `docker run`으로 컨테이너를 만들고 다음과 같이 웹 트래픽에 대해 포트 80을 노출합니다.

```bash
docker run -d -p 80:80 --restart=always nginx
```

다음 예제와 유사하게 출력됩니다.

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

`docker ps`를 사용하여 실행 중인 컨테이너를 봅니다. 다음 예제 출력은 노출된 포트 80으로 실행 중인 NGINX 컨테이너를 보여 줍니다.

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>컨테이너 테스트
다음과 같이 Docker 호스트의 공용 IP 주소를 가져옵니다.


```bash
docker-machine ip myvm
```

작업에서 컨테이너를 보려면 웹 브라우저를 열고 이전 명령의 출력에 나와 있는 공용 IP 주소를 입력합니다.

![ngnix 컨테이너 실행](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>다음 단계
Docker Compose 사용에 대한 예제는 [Azure에서 Docker 및 Compose 시작](docker-compose-quickstart.md)을 참조하세요.
