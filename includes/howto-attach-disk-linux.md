---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: da2605e7d6dc8d46aa5c5cb89ec3e6f46076ffd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476286"
---
디스크에 대한 자세한 내용은 [Virtual Machines용 디스크 및 VHD 정보](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>빈 디스크 연결
1. Azure 클래식 CLI를 열고 [Azure 구독에 연결](/cli/azure/authenticate-azure-cli)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.
2. `azure vm disk attach-new`을 입력하여 다음 예와 같이 새 디스크를 만들고 연결합니다. *myVM*을 Linux Virtual Machine의 이름으로 바꾸고 디스크의 크기(GB)를 지정합니다. 이 예제에는 *100GB*입니다.

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. 데이터 디스크를 만들고 연결한 후 `azure vm disk list <virtual-machine-name>` 출력에 다음 예와 같이 나열됩니다.
   
    ```azurecli
    azure vm disk list TestVM
    ```

    다음 예제와 유사하게 출력됩니다.

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>기존 디스크 연결
기존 디스크를 연결하려면 저장소 계정에 사용 가능한 .vhd가 있어야 합니다.

1. Azure 클래식 CLI를 열고 [Azure 구독에 연결](/cli/azure/authenticate-azure-cli)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.
2. 연결하려는 VHD가 Azure 구독에 이미 업로드되었는지 확인합니다.
   
    ```azurecli
    azure vm disk list
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. 사용하려는 디스크를 찾지 못한 경우 `azure vm disk create` 또는 `azure vm disk upload`를 사용하여 구독에 로컬 VHD를 업로드할 수 있습니다. `disk create` 의 예는 다음 예와 같습니다.
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   `azure vm disk upload` 명령을 사용하여 특정 저장소 계정에 VHD를 업로드할 수도 있습니다. Azure 가상 머신의 데이터 디스크를 관리하는 명령에 대한 자세한 내용은 [여기](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)를 참조하세요.

4. 이제 원하는 VHD를 가상 머신에 연결합니다.
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   *myVM*을 가상 머신의 이름으로 바꾸고 *myVHD*를 원하는 VHD로 바꿔야 합니다.

5. 다음과 같이 `azure vm disk list <virtual-machine-name>`명령을 사용하여 디스크가 가상 머신에 연결되어 있는지 확인할 수 있습니다.
   
    ```azurecli
    azure vm disk list myVM
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> 데이터 디스크를 추가한 후 가상 머신이 디스크를 저장소에 사용할 수 있도록 가상 머신에 로그온한 다음 디스크를 초기화해야 합니다(디스크를 초기화하는 실행하는 방법에 대한 자세한 내용은 다음 단계를 참조하세요).
> 
> 

