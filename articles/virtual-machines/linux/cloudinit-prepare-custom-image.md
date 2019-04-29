---
title: cloud-init와 함께 사용하기 위해 Azure VM 준비 | Microsoft Docs
description: cloud-init를 사용하여 배포를 위해 기존 Azure VM 이미지를 준비하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/27/2019
ms.author: danis
ms.openlocfilehash: da539a5bebc1613115f89a7b47c513ce486b5e3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627937"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>cloud-init와 함께 사용하기 위해 기존 Linux Azure VM 이미지 준비
이 문서는 기존 Azure 가상 머신을 사용하고 다시 배포하고 cloud-init를 사용할 수 있도록 준비하는 방법을 보여 줍니다. 결과 이미지는 새 가상 머신이나 가상 머신 확장 집합을 배포하는 데 사용할 수 있습니다. 그런 다음 가상 머신 또는 가상 머신 확장 집합은 배포 시 cloud-init에 의해 더 사용자 지정될 수 있습니다.  Azure에서 리소스가 프로비전되면 처음 부팅 시 이러한 cloud-init 스크립트가 실행됩니다. 기본적으로 cloud-init가 Azure에서 작동되는 방식과 지원되는 Linux 배포판에 대한 자세한 내용은 [cloud-init 개요](using-cloud-init.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 지원되는 버전의 Linux 운영 체제를 실행하는 실행 중인 Azure 가상 머신이 이미 있다고 가정합니다. 필요에 맞도록 컴퓨터를 이미 구성했고, 모든 필수 모듈을 설치했고, 모든 필수 업데이트를 처리했으며 요구 사항을 충족하는지 테스트했습니다. 

## <a name="preparing-rhel-76--centos-76"></a>준비 7.6 RHEL / CentOS 7.6
Linux VM으로 SSH하고 cloud-init를 설치하기 위해 다음 명령을 실행해야 합니다.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

`/etc/cloud/cloud.cfg`의 `cloud_init_modules` 섹션을 업데이트하여 다음 모듈을 포함합니다.
```bash
- disk_setup
- mounts
```

다음은 범용 `cloud_init_modules` 섹션이 표시되는 샘플입니다.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
사용 후 삭제 디스크 프로비전 및 처리와 관련된 다양한 작업은 `/etc/waagent.conf`에서 업데이트되어야 합니다. 다음 명령을 실행하여 적절한 설정을 업데이트합니다. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cp /lib/systemd/system/waagent.service /etc/systemd/system/waagent.service
sed -i 's/After=network-online.target/WantedBy=cloud-init.service\\nAfter=network.service systemd-networkd-wait-online.service/g' /etc/systemd/system/waagent.service
systemctl daemon-reload
cloud-init clean
```
다음 줄로 선택한 편집기를 사용하여 새 파일 `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg`를 만들어 Azure Linux 에이전트에 대한 데이터 원본으로 Azure만을 허용합니다.

```bash
# Azure Data Source config
datasource_list: [ Azure ]
datasource:
   Azure:
     agent_command: [systemctl, start, waagent, --no-block]
```

스왑 파일이 구성된 기존 Azure 이미지가 있고 cloud-init를 사용하는 새 이미지의 스왑 파일 구성을 변경하려는 경우 기존 스왑 파일을 제거해야 합니다.

Red Hat 기반 이미지의 경우 - [스왑 파일 제거](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file) 방법을 설명하는 다음 Red Hat 문서의 지침을 따릅니다.

스왑 파일이 활성화된 CentOS 이미지의 경우 다음 명령을 실행하여 스왑 파일을 해제할 수 있습니다.
```bash
sudo swapoff /mnt/resource/swapfile
```

스왑 파일 참조가 `/etc/fstab`에서 제거되었는지 확인합니다. 다음 출력과 같은 내용이 나타납니다.
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

공간을 절약하고 스왑 파일을 제거하기 위해 다음 명령을 실행할 수 있습니다.
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>cloud-init 준비 이미지에 대한 추가 단계
> [!NOTE]
> 이미지가 이전에 **cloud-init** 준비되었고 이미지를 구성한 경우 다음 단계를 수행해야 합니다.

다음 세 가지 명령은 특수화된 새 원본 이미지가 되도록 사용자 지정하는 VM이 이전에 cloud-init로 프로비전된 경우에만 사용됩니다.  이미지가 Azure Linux 에이전트를 사용하여 구성된 경우 이를 실행할 필요가 없습니다.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux 에이전트 설정 종료 
모든 Azure 플랫폼 이미지는 cloud-init으로 구성되었는지 여부와 상관 없이 Azure Linux 에이전트가 설치되었습니다.  다음 명령을 실행하여 Linux 컴퓨터에서 사용자 프로비전 해제를 완료합니다. 

```bash
sudo waagent -deprovision+user -force
```

Azure Linux 에이전트 프로비전 해제 명령에 대한 자세한 내용은 [Azure Linux 에이전트](../extensions/agent-linux.md)를 참조하세요.

SSH 세션을 종료한 다음 bash 셸에서 다음 AzureCLI 명령을 실행하여 할당을 취소하고, 새 Azure VM 이미지를 일반화하고 만듭니다.  `myResourceGroup` 및 `sourceVmName`을 사용자 sourceVM을 반영하는 적절한 정보로 바꿉니다.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
