---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770857"
---
Azure에서 Windows VM(가상 머신)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 애플리케이션 업데이트가 실패하여 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서는 가상 하드 디스크를 다른 VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure Portal을 사용하는 방법을 설명합니다.


## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 문제가 발생하는 VM을 삭제하지만, 가상 하드 디스크는 유지합니다.
2. 문제 해결을 위해 가상 하드 디스크를 다른 VM에 연결하고 탑재합니다.
3. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 오류를 수정합니다.
4. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
5. 원래 가상 하드 디스크를 사용하여 VM을 만듭니다.

## <a name="delete-the-original-vm"></a>원본 VM 삭제
가상 하드 디스크와 VM은 Azure의 두 가지 별개의 리소스입니다. 가상 하드 디스크에는 운영 체제, 애플리케이션 및 구성이 저장됩니다. VM은 크기 또는 위치를 정의하고 가상 하드 디스크 또는 가상 NIC(네트워크 인터페이스 카드)와 같은 리소스를 참조하는 메타데이터일 뿐입니다. 각 가상 하드 디스크에는 VM에 연결할 때 임대가 할당됩니다. VM을 실행하는 동안에도 데이터 디스크를 연결하고 분리할 수 있지만, VM 리소스를 삭제하지 않는 한 OS 디스크를 분리할 수 없습니다. 해당 VM이 중지 및 할당 취소된 상태에 있을 때에도 임대는 OS 디스크와 VM을 계속 연결합니다.

VM을 복구하는 첫 번째 단계는 자체 VM 리소스를 삭제하는 것입니다. VM을 삭제하면 가상 하드 디스크는 저장소 계정에 남게 됩니다. VM을 삭제한 후 가상 하드 디스크를 다른 VM에 연결하여 문제와 오류를 해결할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 왼쪽 메뉴에서 **Virtual Machines(클래식)** 를 클릭합니다.
3. 문제가 있는 VM을 선택하고 **디스크**를 클릭한 후 가상 하드 디스크의 이름을 식별합니다. 
4. OS 가상 하드 디스크를 선택하고 **위치**를 확인하여 가상 하드 디스크가 있는 저장소 계정을 식별합니다. 다음 예제에서는 “.blob.core.windows.net” 앞의 문자열이 저장소 계정 이름입니다.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![VM의 위치에 대한 이미지](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. VM을 마우스 오른쪽 단추로 클릭한 다음 **삭제**를 선택합니다. VM을 삭제하는 경우 디스크가 선택되어 있지 않아야 합니다.
6. 새 복구 VM을 만듭니다. 이 VM은 문제 VM과 동일한 지역 및 리소스 그룹(클라우드 서비스)에 있어야 합니다.
7. 복구 VM을 선택한 다음 **디스크** > **기존 연결**을 선택합니다.
8. 기존 가상 하드 디스크를 선택하려면 **VHD 파일**을 클릭합니다.

    ![기존 VHD 찾아보기](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. 저장소 계정 > VHD 컨테이너 > 가상 하드 디스크를 선택하고 **선택** 단추를 클릭하여 선택한 내용을 확인합니다.

    ![기존 VHD 선택](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. 이제 VHD를 선택하였으므로 **확인**을 선택하여 기존 가상 하드 디스크를 연결합니다.
11. 몇 초 후에 VM의 **디스크** 창에서는 데이터 디스크로 연결된 기존 가상 하드 디스크가 표시됩니다.

    ![데이터 디스크로 연결된 기존 가상 하드 디스크](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 탑재 해제하고 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 함께 사용할 수 없습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 왼쪽 메뉴에서 **Virtual Machines(클래식)** 를 선택합니다.
3. 복구를 VM을 찾습니다. 디스크를 선택하고 디스크를 마우스 오른쪽 단추로 선택한 다음 **분리**를 선택합니다.

## <a name="create-a-vm-from-the-original-hard-disk"></a>원래 하드 디스크에서 VM 만들기

원래 가상 하드 디스크에서 VM을 만들려면 [Azure Portal](https://portal.azure.com)을 사용합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 포털의 왼쪽 맨 위에서 **리소스 만들기** > **계산** > **Virtual Machine** > **갤러리에서**를 차례로 선택합니다.
3. **이미지 선택** 섹션에서 **내 디스크**를 선택한 후 원래 가상 하드 디스크를 선택합니다. 위치 정보를 확인합니다. VM을 배포해야 하는 지역입니다. [다음] 단추를 선택합니다.
4. **가상 머신 구성** 섹션에서 VM 이름을 입력하고 VM 크기를 선택합니다.
