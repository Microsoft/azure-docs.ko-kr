---
title: cloud-init를 사용하여 Azure의 Linux VM에서 bash 스크립트 실행 | Microsoft Docs
description: Azure CLI에서 cloud-init를 사용하여 생성 중인 Linux VM에서 bash 스크립트를 실행하는 방법
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
ms.openlocfilehash: 4f65ebfd2e1ce508c5cf9b224871102a35b55fe0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627846"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>cloud-init를 사용하여 Azure의 Linux VM에서 bash 스크립트 실행
이 문서는 [cloud-init](https://cloudinit.readthedocs.io)를 사용하여 Azure의 프로비전 시간에서 Linux VM(가상 머신) 또는 VMSS(가상 머신 확장 집합)에서 기존 bash 스크립트를 실행하는 방법을 보여 줍니다. Azure에서 리소스가 프로비전되면 처음 부팅 시 이러한 cloud-init 스크립트가 실행됩니다. 기본적으로 cloud-init가 Azure에서 작동되는 방식과 지원되는 Linux 배포판에 대한 자세한 내용은 [cloud-init 개요](using-cloud-init.md)를 참조하세요.

## <a name="run-a-bash-script-with-cloud-init"></a>cloud-init를 사용하여 bash 스크립트 실행
cloud-init를 사용하면 기존 스크립트를 cloud-config로 변환할 필요가 없습니다. cloud-init는 여러 입력 형식을 허용합니다. 그 중 하나는 bash 스크립트입니다.

Linux 사용자 지정 스크립트 Azure 확장을 사용하여 스크립트를 실행하는 경우 cloud-init를 사용하도록 마이그레이션할 수 있습니다. 그러나 Azure 확장은 스크립트 오류에 대해 경고하도록 보고를 통합했으며 cloud-init 이미지 배포는 스크립트가 실패할 경우 실패하지 않습니다.

진행 중인 이 기능을 보려면 테스트를 위해 간단한 bash 스크립트를 만듭니다. cloud-init `#cloud-config` 파일과 마찬가지로 이 스크립트는 가상 머신을 프로비전하기 위해 AzureCLI 명령을 실행할 로컬이어야 합니다.  이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor simple_bash.sh`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data simple_bash.sh`로 bash 스크립트 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>bash 스크립트가 실행되었는지 확인
이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 **publicIpAddress**를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

**/tmp** 디렉터리를 변경하고 myScript.txt 파일이 있고 내부에 적절한 텍스트가 있는지 확인합니다.  그렇지 않은 경우 자세한 내용은 **/var/log/cloud-init.log**를 확인할 수 있습니다.  다음 항목을 검색합니다.

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
