---
title: cloud-init를 사용하여 Azure에서 Linux VM에 대한 호스트 이름 설정 | Microsoft Docs
description: Azure CLI에서 cloud-init를 사용하여 생성 중인 Linux VM을 사용자 지정하는 방법
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
ms.openlocfilehash: da20c4b30e2708bf7754d025cfbd2c269c3b5c7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648944"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>cloud-init를 사용하여 Azure에서 Linux VM에 대한 호스트 이름 설정
이 문서는 [cloud-init](https://cloudinit.readthedocs.io)를 사용하여 Azure의 프로비전 시간에서 VM(가상 머신) 또는 VMSS(가상 머신 확장 집합)에 특정 호스트 이름을 구성하는 방법을 보여 줍니다. Azure에서 리소스가 프로비전되면 처음 부팅 시 이러한 cloud-init 스크립트가 실행됩니다. 기본적으로 cloud-init가 Azure에서 작동되는 방식과 지원되는 Linux 배포판에 대한 자세한 내용은 [cloud-init 개요](using-cloud-init.md)를 참조하세요.

## <a name="set-the-hostname-with-cloud-init"></a>cloud-init로 호스트 이름 설정
기본적으로 호스트 이름은 Azure에서 새 가상 머신을 만들 때 VM 이름과 동일합니다.  Azure에서 [az vm create](/cli/azure/vm)를 사용하여 VM을 만들 때 cloud-init 스크립트를 실행하여 이 기본 호스트 이름을 변경하려면 cloud-init 파일을 `--custom-data` 스위치로 지정합니다.  

진행 중인 업그레이드 프로세스를 확인하려면 현재 셸에 이름이 *cloud_init_hostname.txt*인 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud_init_hostname.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  

```yaml
#cloud-config
hostname: myhostname
```

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_hostname.txt`로 cloud-init 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

만든 후에는 Azure CLI에 VM 관련 정보가 표시됩니다. VM에 SSH에 대한 `publicIpAddress`를 사용합니다. 사용자 고유의 주소를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

VM 이름을 보려면 다음과 같이 `hostname` 명령을 사용합니다.

```bash
hostname
```

다음 예제 출력에서처럼 VM은 호스트 이름을 cloud-init 파일에 설정된 해당 값으로 보고해야 합니다.

```bash
myhostname
```

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
