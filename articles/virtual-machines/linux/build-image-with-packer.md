---
title: Packer를 사용하여 Linux Azure VM 이미지를 만드는 방법 | Microsoft Docs
description: Azure에서 Packer를 사용하여 Linux 가상 머신의 이미지를 만드는 방법에 대해 알아보기
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/03/2018
ms.author: cynthn
ms.openlocfilehash: 3a7ac2e7a86a135f20f46b03be2c38af330a5367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543111"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Azure에서 Packer를 사용하여 Linux 가상 머신 이미지를 만드는 방법
Azure의 각 VM(가상 컴퓨터)은 Linux 배포판 및 OS 버전을 정의하는 이미지에서 만들어집니다. 이미지는 사전 설치된 애플리케이션 및 구성을 포함할 수 있습니다. Azure Marketplace는 가장 일반적인 배포 및 애플리케이션 환경에 대한 다양한 자사 및 타사 이미지를 제공하거나 사용자 요구에 맞게 사용자 지정 이미지를 만들 수 있습니다. 이 문서에는 오픈 소스 도구 [Packer](https://www.packer.io/)를 사용하여 Azure에서 사용자 지정 이미지를 정의하고 작성하는 방법을 자세히 설명합니다.


## <a name="create-azure-resource-group"></a>Azure 리소스 그룹 만들기
빌드 프로세스 동안 Packer는 원본 VM을 빌드하므로 임시 Azure 리소스를 만듭니다. 이미지로 사용하기 위해 해당 원본 VM을 캡처하려면 리소스 그룹을 정의해야 합니다. Packer 빌드 프로세스의 출력은 이 리소스 그룹에 저장됩니다.

[az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기
Packer는 서비스 사용자를 사용하여 Azure를 인증합니다. Azure 서비스 사용자는 앱, 서비스 및 Packer와 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 사용자가 Azure에서 수행할 수 있는 작업에 대한 사용 권한은 사용자가 제어하고 정의합니다.

[az ad sp create-for-rbac](/cli/azure/ad/sp)를 사용하여 서비스 사용자를 만들고 Packer가 필요로 하는 자격 증명을 출력합니다.

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

이전 명령에서 출력의 예는 다음과 같습니다.

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Azure를 인증하기 위해 [az account show](/cli/azure/account)를 사용하여 Azure 구독 ID를 가져와야 합니다.

```azurecli
az account show --query "{ subscription_id: id }"
```

다음 단계에서 이러한 두 명령의 출력을 사용합니다.


## <a name="define-packer-template"></a>Packer 템플릿 정의
이미지를 작성하려면 JSON 파일로 템플릿을 만듭니다. 템플릿에서 실제 빌드 프로세스를 통해 수행하는 작성기와 프로비저너를 정의합니다. Packer에는 이전 단계에서 만든 서비스 사용자 자격 증명과 같은 Azure 리소스를 정의하도록 허용하는 [Azure용 프로비저너](https://www.packer.io/docs/builders/azure.html)가 있습니다.

*ubuntu.json*이라는 파일을 만들고 다음 콘텐츠를 붙여 넣습니다. 다음에 대해 사용자 고유의 값을 입력합니다.

| 매개 변수                           | 얻을 수 있는 위치 |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `az ad sp` create 명령의 첫 번째 출력 줄 - *appId* |
| *client_secret*                     | `az ad sp` create 명령의 두 번째 출력 줄 - *password* |
| *tenant_id*                         | `az ad sp` create 명령의 세 번째 출력 줄 - *tenant* |
| *subscription_id*                   | `az account show` 명령의 출력 |
| *managed_image_resource_group_name* | 첫 번째 단계에서 만든 리소스 그룹의 이름 |
| *managed_image_name*                | 만들어진 관리되는 디스크 이미지의 이름 |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

이 템플릿은 Ubuntu 16.04 LTS 이미지를 빌드하고 NGINX를 설치한 다음 VM의 프로비전을 해제합니다.

> [!NOTE]
> 이 템플릿을 확장하여 사용자 자격 증명을 프로비전하는 경우 Azure 에이전트의 프로비전을 해제하여 `deprovision+user` 대신 `-deprovision`을 읽는 프로비저너 명령을 조정합니다.
> `+user` 플래그는 원본 VM에서 모든 사용자 계정을 제거합니다.


## <a name="build-packer-image"></a>Packer 이미지 작성
로컬 컴퓨터에 Packer를 아직 설치하지 않은 경우 [Packer 설치 지침을 따릅니다](https://www.packer.io/docs/install/index.html).

다음과 같이 Packer 템플릿 파일을 지정하여 이미지를 작성합니다.

```bash
./packer build ubuntu.json
```

이전 명령에서 출력의 예는 다음과 같습니다.

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer가 VM을 빌드하고 프로비저너를 실행하고 배포를 정리하는 데 몇 분 정도 걸립니다.


## <a name="create-vm-from-azure-image"></a>Azure 이미지에서 VM 만들기
이제 [az vm create](/cli/azure/vm)를 사용하여 이미지에서 VM을 만들 수 있습니다. `--image` 매개 변수를 사용하여 만든 이미지를 지정합니다. 다음 예제에서는 *myPackerImage*에서 *myVM*이라는 VM을 만들고 SSH 키가 아직 없으면 생성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Packer 이미지와 다른 리소스 그룹 또는 지역에서 VM을 만들려는 경우 이미지 이름 대신 이미지 ID를 지정합니다. [az image show](/cli/azure/image#az-image-show)를 사용하여 이미지 ID를 가져올 수 있습니다.

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소는 웹 브라우저를 통해 NGINX 사이트에 액세스할 때 사용됩니다.

웹 트래픽이 VM에 도달하도록 허용하려면 [az vm open-port](/cli/azure/vm)를 사용하여 인터넷에서 포트 80을 엽니다.

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>VM 및 NGINX 테스트
이제 웹 브라우저를 열고 주소 표시줄에 `http://publicIpAddress`를 입력할 수 있습니다. VM 만들기 프로세스에서 사용자 고유의 공용 IP 주소를 제공합니다. 기본 NGINX 페이지는 다음 예제와 같이 표시됩니다.

![NGINX 기본 사이트](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>다음 단계
이 예제에서는 이미 설치된 NGINX를 사용하여 VM 이미지를 만드는 데 Packer를 사용했습니다. Ansible, Chef 또는 Puppet를 사용하여 이미지에서 만든 VM에 앱을 배포하는 것과 같은 기존 배포 워크플로와 함께 이 VM 이미지를 사용할 수 있습니다.

다른 Linux 배포판에 대한 추가 예제 Packer 템플릿은 [이 GitHub 리포지토리](https://github.com/hashicorp/packer/tree/master/examples/azure)를 참조하세요.
