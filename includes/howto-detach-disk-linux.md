---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 94f662cea5f20485659a7b93549b758fdd7770f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476271"
---
VM(가상 머신)에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. VM에서 디스크를 분리하는 경우 디스크는 저장소에서 제거되지 않습니다. 디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 VM에 다시 연결할 수 있습니다.  

> [!NOTE]
> Azure의 VM은 운영 체제 디스크, 로컬 임시 디스크, 선택적 데이터 디스크 등 다양한 유형의 디스크를 사용합니다. 자세한 내용은 [Virtual Machines용 디스크 및 VHD 정보](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하십시오. 또한 VM을 삭제하지 않는 한 운영 체제 디스크를 분리할 수 없습니다.

## <a name="find-the-disk"></a>디스크 찾기
VM에서 디스크를 분리하기 전에 분리할 디스크에 대한 식별자인 LUN 번호를 확인해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure CLI를 열고 [Azure 구독에 연결](/cli/azure/authenticate-azure-cli)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.
2. 디스크가 VM에 연결되어 있는지 확인합니다. 다음 예제에서는 `myVM`이라는 VM에 대한 디스크를 나열합니다.

    ```azurecli
    azure vm disk list myVM
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. 분리하려는 디스크에 대한 LUN 또는 **논리 단위 번호** 를 확인합니다.

## <a name="remove-operating-system-references-to-the-disk"></a>디스크에 대한 운영 체제 참조 제거
Linux 게스트에서 디스크를 분리하기 전에 디스크의 모든 파티션이 사용되고 있지는 않은지 확인해야 합니다. 다시 부팅한 후에 운영 체제가 파티션을 다시 탑재하려고 하지 않는지 확인합니다. 다음 단계를 수행하면 디스크를 [연결](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 할 때 만들어질 수 있는 구성이 실행 취소됩니다.

1. `lsscsi` 명령을 사용하여 디스크 식별자를 검색합니다. `lsscsi`는 `yum install lsscsi`(Red Hat 기반 배포) 또는 `apt-get install lsscsi`(Debian 기반 배포)를 통해 설치할 수 있습니다. LUN 번호를 사용하여 원하는 디스크 식별자를 찾을 수 있습니다. 각 행의 튜플에 있는 마지막 숫자는 LUN입니다. `lsscsi`의 다음 예제에서 LUN 0은 */dev/sdc*에 매핑됩니다.

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. `fdisk -l <disk>`을 사용하여 분리하려는 디스크와 연결된 파티션을 검색합니다. 다음 예제에서는 `/dev/sdc`에 대한 결과를 보여줍니다.

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. 디스크에 대해 나열된 각 파티션을 분리합니다. 다음 예제는 `/dev/sdc1`의 탑재를 해제합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

4. `blkid` 명령을 사용하여 모든 파티션에 대한 UUID 검색 다음 예제와 유사하게 출력됩니다.

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. **/etc/fstab** 파일에서 디바이스 경로 또는 분리할 디스크에 대한 모든 파티션의 UUID와 연결된 항목을 제거합니다.  이 예제에서는 해당 항목이 다음과 같을 수 있습니다.

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    or
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>디스크 분리
디스크의 LUN 번호를 찾은 후에 운영 체제 참조를 제거했으면 분리할 준비가 된 것입니다.

1. `azure vm disk detach
   <virtual-machine-name> <LUN>` 명령을 실행하여 가상 머신에서 선택된 디스크를 분리합니다. 다음 예제에서는 `myVM`이라는 VM에서 LUN `0`을 분리합니다.
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. `azure vm disk list`을 다시 실행하여 디스크가 분리되었는지 확인할 수 있습니다. 다음 예제에서는 `myVM`이라는 VM을 확인합니다.
   
    ```azurecli
    azure vm disk list myVM
    ```

    출력은 데이터 디스크가 더 이상 연결되어 있지 않음을 보여 주는 다음 예제와 비슷합니다.

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

분리된 디스크가 저장소에 남아 있지만 더 이상 가상 컴퓨터에 연결되지 않습니다.

