---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ae29451e3f7ec263f296e69656a5c66045334687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126722"
---
1. [Azure 클래식 CLI에서 Azure에 연결](/cli/azure/authenticate-azure-cli)에 나열된 단계를 사용하여 Azure 구독에 로그인합니다.

2. 다음과 같이 클래식 배포 모드에 있는지 확인합니다.

    ```azurecli
    azure config mode asm
    ```

3. 다음과 같이 사용 가능한 이미지에서 로드하려는 Linux 이미지를 찾습니다.

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Windows 명령 프롬프트 창에서 grep 대신 **find** 를 사용합니다.
   
4. `azure vm create`을 사용하여 이전 목록의 Linux 이미지로 새 VM을 만듭니다. 이 단계에서는 클라우드 서비스 및 저장소 계정을 만듭니다. 이 VM을 `-c` 옵션을 사용한 기존 클라우드 서비스에서도 연결할 수 있습니다. `-e` 옵션을 사용한 Linux 가상 머신에 로그인할 수 있는 SSH 엔드포인트를 만듭니다. 다음 예제에서는 `Ubuntu-14_04_4-LTS` 이미지를 사용하여 `West US` 위치에 `myVM`이라는 VM을 만들고 사용자 이름 `ops`를 추가합니다.
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    다음 예제와 유사하게 출력됩니다.

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Linux 가상 컴퓨터의 경우 `vm create``-e` 옵션을 제공해야 합니다. 가상 머신을 만든 이후에는 SSH를 사용하도록 설정할 수 없습니다. SSH에 대한 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

5. `azure vm show` 명령을 사용하여 VM의 특성을 확인할 수 있습니다. 다음 예제에서는 `myVM`이라는 VM에 대한 정보를 나열합니다.

    ```azurecli   
    azure vm show myVM
    ```

6. 다음과 같이 `azure vm start` 명령을 사용하여 VM을 시작합니다.

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>다음 단계
모든 Azure 클래식 CLI 가상 머신 명령에 대한 자세한 내용은 [클래식 배포 API에서 Azure 클래식 CLI 사용](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)을 참조하세요.

