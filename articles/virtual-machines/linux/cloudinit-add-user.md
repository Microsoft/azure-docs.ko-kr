---
title: cloud-init를 사용하여 Azure에서 Linux VM에 사용자 추가 | Microsoft Docs
description: Azure CLI에서 cloud-init를 사용하여 생성 중인 Linux VM에 사용자를 추가하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 51de92eb64e9879b769baf7e574ee1dca9355040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650355"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>cloud-init를 사용하여 Azure에서 Linux VM에 사용자 추가
이 문서는 [cloud-init](https://cloudinit.readthedocs.io)를 사용하여 Azure의 프로비전 시간에서 VM(가상 머신) 또는 VMSS(가상 머신 확장 집합)에 사용자를 추가하는 방법을 보여 줍니다. Azure에서 리소스가 프로비전되면 처음 부팅 시 이 cloud-init 스크립트가 실행됩니다. 기본적으로 cloud-init가 Azure에서 작동되는 방식과 지원되는 Linux 배포판에 대한 자세한 내용은 [cloud-init 개요](using-cloud-init.md)를 참조하세요.

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>cloud-init를 사용하여 VM에 사용자 추가
모든 새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 *root* 사용을 방지하기 위해 자신에 대한 추가 사용자를 추가하는 것입니다. SSH 키는 보안 및 가용성의 모범 사례입니다. 키는 이 cloud-init 스크립트를 통해 *~/.ssh/authorized_keys* 파일에 추가됩니다.

Linux VM에 사용자를 추가하려면 현재 셸에 이름이 *cloud_init_add_user.txt*인 파일을 만들고 다음 구성을 붙여 넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud_init_add_user.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  `ssh-authorized-keys:` 값에 대해 고유한 공개 키(예: *~/.ssh/id_rsa.pub*의 콘텐츠)를 제공해야 합니다. 여기에서는 예제를 단순화하기 위해 줄였습니다.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #cloud-config 파일은 포함된 `- default` 매개 변수를 포함합니다. 프로비전 중에 만든 기존 관리 사용자에 사용자를 추가합니다. `- default` 매개 변수 없이 사용자를 만드는 경우 Azure 플랫폼에서 만든 자동 생성된 관리 사용자를 덮어쓸 수 있습니다. 

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_add_user.txt`로 cloud-init 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 **publicIpAddress**를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

VM 및 지정된 그룹에 사용자가 추가되었는지 확인하려면 다음과 같이 */etc/group* 파일의 내용을 확인합니다.

```bash
cat /etc/group
```

다음 예제 출력에서는 *cloud_init_add_user.txt* 파일의 사용자가 VM과 적합한 그룹에 추가되었음을 나타냅니다.

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
