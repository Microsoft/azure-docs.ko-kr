---
title: Azure Linux VM에 대한 액세스 다시 설정 | Microsoft Docs
description: VMAccess 확장 및 Azure CLI를 사용하여 관리 사용자를 관리하고 Linux VM에 대한 액세스를 다시 설정하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 71aecc1748e70e2119b1f54c21a0f705afc5d5d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800066"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Azure CLI에서 VMAccess 확장을 사용하여 관리 사용자, SSH를 관리하고 Linux VM의 디스크를 검사 또는 복구
## <a name="overview"></a>개요
Linux VM의 디스크에 오류가 표시되어 있습니다. 사용자가 Linux VM의 루트 암호를 재설정했거나 SSH 개인 키를 실수로 삭제했습니다. 데이터 센터를 사용할 때는 이러한 경우 데이터 센터로 직접 가서 KVM을 열어 서버 콘솔에 액세스해야 했습니다. Azure VMAccess 확장을 콘솔에 액세스하여 Linux에 대한 액세스 권한을 재설정하거나 디스크 수준 유지 관리를 수행할 수 있는 이 KVM 스위치로 생각하세요.

이 문서에서는 Azure VM 액세스 확장을 사용하여 Azure Resource Manager 가상 머신으로 실행되는 Linux에서 디스크를 검사 또는 복구하거나, 사용자 액세스를 다시 설정하거나, 관리 사용자 계정을 관리하거나, SSH 구성을 업데이트하는 방법에 대해 설명합니다. 클래식 가상 머신을 관리해야 하는 경우 [클래식 VM 설명서](../linux/classic/reset-access-classic.md)에 있는 지침을 따르면 됩니다. 
 
> [!NOTE]
> AAD 로그인 확장을 설치한 후에 VMAccess 확장을 사용하여 VM의 암호를 재설정하는 경우 AAD 로그인 확장을 다시 실행하여 머신에 대해 AAD 로그인을 다시 사용하도록 설정해야 합니다.

## <a name="prerequisites"></a>필수 조건
### <a name="operating-system"></a>운영 체제

VM 액세스 확장은 다음 Linux 배포판에 대해 실행할 수 있습니다.

| 배포 | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS 및 12.04 LTS |
| Debian | Debian 7.9 이상, 8.2 이상 |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4 이상, 7.0 이상 |
| Suse | 11 및 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3 이상, 7.0 이상 |
| CoreOS | 494.4.0 이상 |

## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess 확장을 사용하는 방법
Linux VM에서 두 가지 방법으로 VMAccess 확장을 사용할 수 있습니다.

* Azure CLI 및 필수 매개 변수 사용
* [VMAccess 확장을 처리하고 관련 작업을 수행하는 원시 JSON 파일을 사용](#use-json-files-and-the-vmaccess-extension)합니다.

다음 예에서는 [az vm user](/cli/azure/vm/user) 명령을 사용합니다. 이러한 단계를 수행하려면 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다.

## <a name="update-ssh-key"></a>SSH 키 업데이트
다음 예제에서는 VM `myVM`에서 사용자 `azureuser`에 대한 SSH 키를 업데이트합니다.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **참고:** `az vm user update` 명령은 VM의 관리 사용자에 대한 `~/.ssh/authorized_keys` 파일에 새 공개 키 텍스트를 추가합니다. 기존 SSH 키를 대체하거나 제거하지 않습니다. 배포 시 설정된 이전 키 또는 VMAccess 확장을 통한 후속 업데이트를 제거하지 않습니다.

## <a name="reset-password"></a>암호 재설정
다음 예제에서는 VM `myVM`에서 사용자 `azureuser`에 대한 암호를 다시 설정합니다.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH 다시 시작
다음 예제에서는 SSH 디먼을 다시 시작하고 SSH 구성을 `myVM`이라는 VM에서 기본값으로 다시 설정합니다.

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>관리/sudo 사용자 만들기
다음 예제에서는 **sudo** 권한을 가진 `myNewUser`라는 사용자를 만듭니다. 계정은 `myVM`이라는 VM에서 인증을 위해 SSH 키를 사용합니다. 이 메서드는 현재 자격 증명을 분실하거나 잊어버린 상황에서 VM에 대한 액세스 권한을 다시 얻을 수 있도록 설계되었습니다. 모범 사례로, **sudo** 권한이 있는 계정은 제한되어야 합니다.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>사용자 삭제
다음 예제에서는 VM `myVM`에서 사용자 `myNewUser`을 삭제합니다.

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON 파일 및 VMAccess 확장 사용
다음 예제에서는 원시 JSON 파일을 사용합니다. [az vm extension set](/cli/azure/vm/extension)을 사용하여 JSON 파일을 호출합니다. 이러한 JSON 파일은 Azure 템플릿에서도 호출할 수 있습니다. 

### <a name="reset-user-access"></a>사용자 액세스 다시 설정
Linux VM의 루트에 액세스할 수 없게 된 경우 VMAccess 스크립트를 시작하여 사용자의 SSH 키 또는 암호를 업데이트할 수 있습니다.

사용자의 SSH 공개 키를 업데이트하려면 파일 `update_ssh_key.json`을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `ssh_key` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

사용자 암호를 다시 설정하려면 파일 `reset_user_password.json`을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `password` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH 다시 시작
SSH 디먼을 다시 시작하고 SSH 구성을 기본값으로 다시 설정하려면 파일 `reset_sshd.json`을 만듭니다. 다음 내용을 추가합니다.

```json
{
  "reset_ssh": true
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>관리 사용자 관리

인증을 위해 SSH 키를 사용하고 **sudo** 권한이 있는 사용자를 만들려면 `create_new_user.json`이라는 파일을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `ssh_key` 매개 변수에 대해 고유한 값으로 대체합니다. 이 메서드는 현재 자격 증명을 분실하거나 잊어버린 상황에서 VM에 대한 액세스 권한을 다시 얻을 수 있도록 설계되었습니다. 모범 사례로, **sudo** 권한이 있는 계정은 제한되어야 합니다.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

사용자를 삭제하려면 파일 `delete_user.json`을 만들고 다음 콘텐츠를 추가합니다. `remove_user` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "remove_user":"myNewUser"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>디스크 확인 또는 복구
VMAccess를 사용하여 Linux VM에 추가한 디스크를 확인하고 복구할 수도 있습니다.

디스크를 확인한 다음 복구하려면 파일 `disk_check_repair.json`을 만들고 다음 형식으로 설정을 추가합니다. `repair_disk`의 이름에 대해 고유한 값으로 대체합니다.

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
