---
title: Azure CLI를 사용하여 Linux VM에 데이터 디스크 추가 | Microsoft Docs
description: Azure CLI를 사용하여 Linux VM에 영구 데이터 디스크를 추가하는 방법 알아보기
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 1915051380a514cf8143d43bddc0d0d475f43c07
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765841"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM에 디스크 추가
이 문서에서는 유지 관리 또는 크기 조정으로 인해 VM이 다시 프로비전되더라도 데이터를 유지할 수 있도록 VM에 영구 디스크를 연결하는 방법을 보여 줍니다.


## <a name="attach-a-new-disk-to-a-vm"></a>VM에 새 디스크 연결

VM에 새 빈 데이터 디스크를 추가하려는 경우 [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) 명령에 `--new` 매개 변수를 사용합니다. VM이 가용성 영역에 있는 경우 VM과 동일한 영역에 디스크가 자동으로 생성됩니다. 자세한 내용은 [가용성 영역 개요](../../availability-zones/az-overview.md)를 참조하세요. 다음 예제에서는 크기가 50GB이고 이름이 *myDataDisk*인 디스크를 만듭니다.

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>기존 디스크 연결

기존 디스크를 연결하려면 디스크 ID를 찾아 [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) 명령에 ID를 전달합니다. 다음 예제에서는 *myResourceGroup*에서 이름이 *myDataDisk*인 디스크를 쿼리한 다음 이름이 *myVM*인 VM에 연결합니다.

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM에 연결하여 새 디스크 탑재

Linux VM에서 사용할 수 있도록 새 디스크를 분할, 포맷 및 탑재하려면 VM에 SSH합니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](mac-create-ssh-keys.md)을 참조하세요. 다음 예제에서는 사용자 이름 *azureuser*를 사용하여 공용 DNS 항목이 *mypublicdns.westus.cloudapp.azure.com*인 VM에 연결합니다.

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

VM에 연결하고 나면 디스크를 연결할 준비가 되었습니다. 먼저 `dmesg`를 사용하여 디스크를 찾습니다(새 디스크를 찾는 데 사용하는 방법은 다를 수 있습니다). 다음 예제에서는 dmesg를 사용하여 *SCSI* 디스크를 필터링합니다.

```bash
dmesg | grep SCSI
```

다음 예제와 유사하게 출력됩니다.

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

여기서 *sdc*는 원하는 디스크입니다. `parted`를 사용하여 디스크를 분할합니다. 디스크 크기가 2테비바이트(TiB) 이상이면 GPT 분할을 사용해야 하고, 2TiB 미만이면 MBR 또는 GPT 분할을 사용하면 됩니다. MBR 분할을 사용하는 경우 `fdisk`를 사용할 수 있습니다. 파티션 1에 기본 디스크를 만들고, 나머지는 기본값을 적용합니다. 다음 예제에서는 */dev/sdc*에서 `fdisk` 프로세스를 시작합니다.

```bash
sudo fdisk /dev/sdc
```

새 파티션을 추가하려면 `n` 명령을 사용합니다. 이 예제에서는 주 파티션에 대해 `p`를 선택하고 기본 값의 나머지를 적용합니다. 다음 예제와 유사하게 출력됩니다.

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

`p`를 입력하여 파티션 테이블을 인쇄한 다음, `w`를 사용하여 디스크에 테이블을 쓰고 종료합니다. 출력은 다음 예제와 비슷해야 합니다.

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
사용 된 커널 업데이트를 알아보려면 아래 명령의:
```
partprobe 
```

이제 `mkfs` 명령을 사용하여 파티션에 파일 시스템을 씁니다. 파일 시스템 형식 및 디바이스 이름을 지정합니다. 다음 예제에서는 이전 단계에서 만든 */dev/sdc1* 파티션에 *ext4* 파일 시스템을 만듭니다.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

다음 예제와 유사하게 출력됩니다.

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

이제 `mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다. 다음 예제에서는 */datadrive*에 디렉터리를 만듭니다.

```bash
sudo mkdir /datadrive
```

`mount`를 사용하여 파일 시스템을 탑재합니다. 다음 예제에서는 */dev/sdc1* 파티션을 */datadrive* 탑재 지점에 탑재합니다.

```bash
sudo mount /dev/sdc1 /datadrive
```

다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 */etc/fstab* 파일에 추가해야 합니다. 또한 */etc/fstab*에 UUID(Universally Unique IDentifier)를 사용하여 디바이스 이름(예: */dev/sdc1*) 대신 드라이브를 가리키는 것이 좋습니다. 부팅하는 동안 OS에서 디스크 오류를 검색하는 경우 UUID를 사용하여 지정된 위치에 탑재되어 있는 잘못된 디스크를 회피합니다. 그런 다음, 남아 있는 데이터 디스크를 동일한 디바이스 ID에 할당합니다. 새 드라이브의 UUID를 찾으려면 `blkid` 유틸리티를 사용합니다.

```bash
sudo blkid
```

출력은 다음 예제와 유사합니다.

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

텍스트 편집기에서 다음과 같이 */etc/fstab* 파일을 엽니다.

```bash
sudo vi /etc/fstab
```

이 예제에서는 이전 단계에서 만든 */dev/sdc1* 디바이스의 UUID 값과 탑재 지점 */datadrive*를 사용합니다. */etc/fstab* 파일의 끝에 다음 줄을 추가합니다.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> 나중에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 대부분의 배포는 *nofail* 및/또는 *nobootwait* fstab 옵션을 제공합니다. 이러한 옵션을 사용하면 디스크가 부팅 시 탑재되지 않더라도 시스템을 부팅할 수 있습니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.
>
> *nofail* 옵션은 파일 시스템이 손상되었거나 디스크가 부팅 시 존재하지 않더라도 VM이 시작되도록 합니다. 이 옵션이 없으면 [FSTAB 오류로 인해 Linux에 SSH를 사용할 수 없음](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)(영문)에 설명되어 있는 동작이 발생할 수 있습니다.
>
> Azure VM의 직렬 콘솔에서 부팅 오류가 발생 했습니다 fstab을 수정 하는 경우 VM에 대 한 콘솔 액세스에 사용할 수 있습니다. 자세한 내용은 합니다 [직렬 콘솔 설명서](https://docs.microsoft.com/en-us/azure/virtual-machines/troubleshooting/serial-console-linux)합니다.

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure에서 Linux에 대한 TRIM/UNMAP 지원
일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이 기능은 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 Azure에 알리기 위해 표준 저장소에서 주로 유용하며, 큰 파일을 만들고 삭제하는 경우 비용을 절약할 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

* */etc/fstab*에 `discard` 탑재 옵션을 사용합니다. 예:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* 경우에 따라 `discard` 옵션을 사용하면 성능이 저하될 수 있습니다. 또는 `fstrim` 명령을 명령줄에서 수동으로 실행하거나, 또는 정기적으로 실행하기 위해 crontab에 추가할 수 있습니다.

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>다음 단계

* Linux VM을 올바르게 구성했는지 확인하려면 [Linux 컴퓨터 성능 최적화](optimization.md) 권장 사항을 검토합니다.
* 디스크를 추가하여 저장소 용량을 확장하고 추가 성능이 필요할 경우 [RAID를 구성](configure-raid.md)합니다.
