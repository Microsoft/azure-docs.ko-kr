---
title: Linux VM에 데이터 디스크 연결
description: 포털을 사용하여 새 또는 기존 데이터 디스크를 Linux VM에 연결합니다.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a37ed39f3c663f9f77daa1ed8f6946403348edd0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072641"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>포털을 사용하여 데이터 디스크를 Linux VM에 연결 
이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Linux 가상 머신에 연결하는 방법을 보여줍니다. 또한 [Azure Portal에서 Windows VM에 데이터 디스크를 연결](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다. 

VM에 디스크를 연결하기 전에 다음 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 가상 머신에 연결된 디스크는 실제로 Azure에 저장된 .vhd 파일입니다. 자세한 내용은 [관리 디스크 소개](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 디스크를 연결한 후 [Linux VM에 연결하여 새 디스크를 탑재](#connect-to-the-linux-vm-to-mount-the-new-disk)해야 합니다.


## <a name="find-the-virtual-machine"></a>가상 머신 찾기
1. [Azure Portal](https://portal.azure.com/)로 이동하여 VM을 찾습니다. **가상 머신**을 검색하여 선택합니다.
2. 목록에서 VM을 선택합니다.
3. **가상 컴퓨터** 페이지의 **설정**에서 **디스크**를 선택 합니다.


## <a name="attach-a-new-disk"></a>새 디스크 연결

1. **디스크** 창에서 **데이터 디스크**아래에 있는 **새 디스크 만들기 및 연결**을 선택 합니다.

1. 관리되는 디스크에 대한 이름을 입력합니다. 기본 설정을 검토 하 고 필요에 따라 **저장소 유형**, **크기 (GiB)**, **암호화** 및 **호스트 캐싱을** 업데이트 합니다.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="디스크 설정을 검토 합니다.":::


1. 완료 되 면 페이지 맨 위에 있는 **저장** 을 선택 하 여 관리 디스크를 만들고 VM 구성을 업데이트 합니다.


## <a name="attach-an-existing-disk"></a>기존 디스크 연결
1. **디스크** 창의 **데이터 디스크**에서 **기존 디스크 연결**을 선택 합니다.
1. **디스크 이름** 드롭다운 메뉴를 클릭 하 고 사용 가능한 관리 디스크 목록에서 디스크를 선택 합니다. 

1. **저장**을 클릭하여 기존 관리되는 디스크를 연결하고 VM 구성을 업데이트합니다.
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM에 연결하여 새 디스크 탑재
Linux VM에서 사용할 수 있도록 새 디스크를 분할, 포맷 및 탑재하려면 VM에 SSH합니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](mac-create-ssh-keys.md)을 참조하세요. 다음 예제에서는 사용자 이름 *azureuser*를 사용 하 여 *10.123.123.25* 의 공용 IP 주소를 사용 하 여 VM에 연결 합니다. 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>디스크 찾기

VM에 연결 되 면 디스크를 찾아야 합니다. 이 예제에서는를 사용 하 여 `lsblk` 디스크를 나열 합니다. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

다음 예제와 유사하게 출력됩니다.

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

이 예제에서 추가한 디스크는 `sdc` 입니다. 이는 LUN 0 이며 4GB입니다.

더 복잡 한 예는 포털에서 다음과 같은 여러 데이터 디스크가 표시 됩니다.

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="디스크 설정을 검토 합니다.":::

이미지에서 3 개의 데이터 디스크 (LUN 0에 4gb, lun 1에 16GB, LUN 2에 32G)가 있는지 확인할 수 있습니다.

다음은를 사용 하는 것 처럼 보일 수 있는 것입니다 `lsblk` .

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

의 출력에서 `lsblk` lun 0의 4gb 디스크가이 고 `sdc` , lun 1의 16gb 디스크가이 `sdd` 고, Lun 2의 32g 디스크가 인 것을 확인할 수 있습니다 `sde` .

### <a name="partition-a-new-disk"></a>새 디스크 분할

데이터가 포함된 기존 디스크를 사용 중이라면 디스크 탑재 단계로 건너뜁니다. 새 디스크를 연결하는 경우에는 디스크를 분할해야 합니다.

유틸리티를 사용 하 여 `parted` 데이터 디스크를 분할 하 고 형식을 지정할 수 있습니다.

> [!NOTE]
> 배포판에 사용할 수 있는 최신 버전을 사용 하는 것이 좋습니다 `parted` .
> 디스크 크기가 2 tebibytes (TiB) 이상인 경우에는 GPT 분할을 사용 해야 합니다. 디스크 크기가 2 TiB 이면 MBR 또는 GPT 분할 중 하나를 사용할 수 있습니다.  


다음 예제에서는 `parted` `/dev/sdc` 첫 번째 데이터 디스크가 대부분의 vm에서 일반적으로 사용 되는를 사용 합니다. 를 `sdc` 디스크에 대 한 올바른 옵션으로 바꿉니다. [Xfs](https://xfs.wiki.kernel.org/) 파일 시스템을 사용 하 여 형식을 지정 하기도 합니다.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

유틸리티를 사용 [`partprobe`](https://linux.die.net/man/8/partprobe) 하 여 커널이 새 파티션 및 파일 시스템을 인식 하는지 확인 합니다. 을 사용 하지 않으면 `partprobe` blkid 또는 lslbk 명령이 새 파일 시스템에 대해 UUID를 즉시 반환 하지 않을 수 있습니다.

### <a name="mount-the-disk"></a>디스크 탑재

`mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다. 다음 예제에서는 디렉터리를 만듭니다 `/datadrive` .

```bash
sudo mkdir /datadrive
```

`mount`를 사용하여 파일 시스템을 탑재합니다. 다음 예제에서는 */dev/sc1* 파티션을 탑재 지점에 탑재 합니다 `/datadrive` .

```bash
sudo mount /dev/sdc1 /datadrive
```

다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 */etc/fstab* 파일에 추가해야 합니다. 또한 */etc/fstab* 에서 UUID (범용 고유 식별자)를 사용 하 여 장치 이름 (예: */dv/sdc1*)이 아닌 드라이브를 참조 하는 것이 좋습니다. 부팅하는 동안 OS에서 디스크 오류를 검색하는 경우 UUID를 사용하여 지정된 위치에 탑재되어 있는 잘못된 디스크를 회피합니다. 그런 다음, 남아 있는 데이터 디스크를 동일한 디바이스 ID에 할당합니다. 새 드라이브의 UUID를 찾으려면 `blkid` 유틸리티를 사용합니다.

```bash
sudo blkid
```

출력은 다음 예제와 유사합니다.

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

텍스트 편집기에서 다음과 같이 */etc/fstab* 파일을 엽니다.

```bash
sudo nano /etc/fstab
```

이 예제에서는 이전 단계에서 만든 장치에 대해 UUID 값을 사용 하 `/dev/sdc1` 고의 탑재를 사용 합니다 `/datadrive` . 파일의 끝에 다음 줄을 추가 합니다 `/etc/fstab` .

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Nano 편집기를 사용 했으므로 파일 편집이 완료 되 면를 사용 `Ctrl+O` 하 여 파일을 작성 하 고 편집기를 `Ctrl+X` 종료 합니다.

> [!NOTE]
> 나중에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 대부분의 배포는 *nofail* 및/또는 *nobootwait* fstab 옵션을 제공합니다. 이러한 옵션을 사용하면 디스크가 부팅 시 탑재되지 않더라도 시스템을 부팅할 수 있습니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.
> 
> *nofail* 옵션은 파일 시스템이 손상되었거나 디스크가 부팅 시 존재하지 않더라도 VM이 시작되도록 합니다. 이 옵션이 없으면 [FSTAB 오류로 인해 Linux에 SSH를 사용할 수 없음](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)(영문)에 설명되어 있는 동작이 발생할 수 있습니다.


## <a name="verify-the-disk"></a>디스크 확인

이제를 다시 사용 `lsblk` 하 여 디스크 및 탑재 지점을 확인할 수 있습니다.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

출력은 다음과 같이 표시됩니다.

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

`sdc`이제가에 탑재 된 것을 볼 수 있습니다 `/datadrive` .

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure에서 Linux에 대한 TRIM/UNMAP 지원

일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이 기능은 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 Azure에 알리기 위해 표준 스토리지에서 주로 유용하며, 큰 파일을 만들고 삭제하는 경우 비용을 절약할 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

* */etc/fstab*에 `discard` 탑재 옵션을 사용합니다. 예:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
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

## <a name="next-steps"></a>다음 단계
[Azure CLI를 사용해서도 데이터 디스크를 연결](add-disk.md)할 수 있습니다.