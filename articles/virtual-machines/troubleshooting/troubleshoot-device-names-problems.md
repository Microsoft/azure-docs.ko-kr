---
title: Azure에서 Linux VM 디바이스 이름 변경 문제 해결 | Microsoft Docs
description: Linux VM 디바이스 이름이 변경되는 이유와 이 문제를 해결하는 방법을 설명합니다.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 31f64a504156134b1d622705d5301d9cd5a5f5b1
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756829"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Linux VM 디바이스 이름 변경 문제 해결

이 문서에서는 Linux VM을 다시 시작하거나 데이터 디스크를 다시 연결한 후 디바이스 이름이 변경되는 이유를 설명합니다. 또한 이 문서에서는 이 문제에 대한 해결 방법을 제공합니다.

## <a name="symptoms"></a>증상
Microsoft Azure에서 Linux VM을 실행하는 경우 다음과 같은 문제가 발생할 수 있습니다.

- VM에서 다시 시작한 후 부팅에 실패합니다.
- 데이터 디스크가 분리되었다가 다시 연결되는 경우 디스크 디바이스 이름이 변경됩니다.
- 디바이스 이름이 변경되었으므로 디바이스 이름을 사용하여 디스크를 참조하는 애플리케이션 또는 스크립트가 실패합니다.

## <a name="cause"></a>원인

Linux의 디바이스 경로는 다시 시작에 대해 일관되도록 보장되지 않습니다. 디바이스 이름은 주 번호(문자) 및 보조 번호로 구성됩니다. Linux 스토리지 디바이스 드라이버에서 새 디바이스를 검색하는 경우 드라이버는 사용할 수 있는 범위의 주 및 보조 번호를 디바이스에 할당합니다. 디바이스가 제거되는 경우 나중에 사용할 수 있게 해당 디바이스 번호가 회수됩니다.

SCSI 하위 시스템에서 예약된 Linux에서 검색하는 디바이스가 비동기적으로 발생하므로 문제가 발생합니다. 결과적으로 다시 시작할 때마다 디바이스 경로 이름이 달라질 수 있습니다.

## <a name="solution"></a>솔루션

이 문제를 해결하려면 영구 이름 지정을 사용합니다. 영구적으로 이름을 지정하는 네 가지 방법에는 파일 시스템 레이블별, UUID별, ID별 및 경로별이 있습니다. Azure Linux VM에 대해 파일 시스템 레이블 또는 UUID를 사용하는 것이 좋습니다.

대부분의 배포는 `fstab` **nofail** 또는 **nobootwait** 매개 변수를 제공합니다. 이러한 매개 변수를 사용하면 디스크가 시작 시 탑재되지 않을 때 시스템을 부팅할 수 있습니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요. 데이터 디스크를 추가할 때 UUID를 사용하도록 Linux VM을 구성하는 방법에 대한 내용은 [Linux VM에 연결하여 새 디스크 탑재](../linux/add-disk.md#format-and-mount-the-disk)를 참조하세요.

Azure Linux 에이전트는 VM에 설치될 때 Udev 규칙을 사용하여 /dev/disk/azure 경로 아래에 기호 링크의 집합을 만듭니다. 애플리케이션 및 스크립트는 Udev 규칙을 사용하여 VM에 연결된 디스크와 디스크 유형 및 디스크 LUN을 식별합니다.

VM이 부팅 되지 않고 VM으로 SSH 할 수 없는 방식으로 fstab를 이미 편집한 경우 [Vm 직렬 콘솔](./serial-console-linux.md) 을 사용 하 여 [단일 사용자 모드로](./serial-console-grub-single-user-mode.md) 전환 하 고 fstab를 수정할 수 있습니다.

### <a name="identify-disk-luns"></a>디스크 LUN 식별

애플리케이션은 LUN을 사용하여 연결된 모든 디스크를 찾고 기호 링크를 생성합니다. Azure Linux 에이전트에는 LUN에서 디바이스로 기호 링크를 설정하는 Udev 규칙이 포함됩니다.

```console
$ tree /dev/disk/azure

/dev/disk/azure
├── resource -> ../../sdb
├── resource-part1 -> ../../sdb1
├── root -> ../../sda
├── root-part1 -> ../../sda1
└── scsi1
    ├── lun0 -> ../../../sdc
    ├── lun0-part1 -> ../../../sdc1
    ├── lun1 -> ../../../sdd
    ├── lun1-part1 -> ../../../sdd1
    ├── lun1-part2 -> ../../../sdd2
    └── lun1-part3 -> ../../../sdd3
```

Linux 게스트 계정의 LUN 정보는 `lsscsi` 또는 유사한 도구를 사용하여 검색합니다.

```console
$ sudo lsscsi

[1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

[2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

[3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

[5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

[5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd
```

게스트 LUN 정보는 Azure Storage에서 파티션 데이터를 포함하는 VHD를 찾기 위해 Azure 구독 메타데이터와 함께 사용됩니다. 예를 들어 다음 `az` CLI를 사용할 수 있습니다.

```azurecli
$ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
[
{
"caching": "None",
  "createOption": "empty",
"diskSizeGb": 1023,
  "image": null,
"lun": 0,
"managedDisk": null,
"name": "testVM-20170619-114353",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
}
},
{
"caching": "None",
"createOption": "empty",
"diskSizeGb": 512,
"image": null,
"lun": 1,
"managedDisk": null,
"name": "testVM-20170619-121516",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
  }
  }
]
```

### <a name="discover-filesystem-uuids-by-using-blkid"></a>blkid를 사용하여 파일 시스템 UUID 검색

애플리케이션 또는 스크립트는 `blkid`의 출력 또는 유사한 원본의 정보를 읽고 사용하기 위해 /dev 경로에서 기호 링크를 생성할 수 있습니다. 출력에는 VM에 연결된 모든 디스크의 UUID와 관련 디바이스 파일이 표시됩니다.

```console
$ sudo blkid -s UUID

/dev/sr0: UUID="120B021372645f72"
/dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
/dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
/dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"
```

Azure Linux 에이전트 Udev 규칙은 /dev/disk/azure 경로 아래에 기호 링크의 집합을 생성합니다.

```console
$ ls -l /dev/disk/azure

total 0
lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1
```

애플리케이션은 링크를 사용하여 부팅 디스크 디바이스 및 리소스(임시) 디스크를 식별할 수 있습니다. Azure에서 애플리케이션은 /dev/disk/azure/root-part1 또는 /dev/disk/azure-resource-part1 경로를 조회하여 이러한 파티션을 검색해야 합니다.

`blkid` 목록의 추가 파티션이 데이터 디스크에 상주합니다. 애플리케이션에서 이러한 파티션에 대한 UUID를 유지 관리하고 경로를 사용하여 런타임 시 디바이스 이름을 검색합니다.

```console
$ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1
```


### <a name="get-the-latest-azure-storage-rules"></a>최신 Azure Storage 규칙 가져오기

최신 Azure Storage 규칙을 가져오려면 다음 명령을 실행합니다.

```console
# sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
# sudo udevadm trigger --subsystem-match=block
```

## <a name="see-also"></a>참고 항목

자세한 내용은 다음 아티클을 참조하세요.

- [Ubuntu: UUID 사용](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: 영구 이름 지정](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: UUID에서 수행할 수 있는 작업](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: 최신 Linux 시스템에서 디바이스 관리 소개](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

