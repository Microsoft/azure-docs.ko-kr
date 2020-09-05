---
title: Azure Stack Edge와 공유할 데이터를 전송하는 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure Stack Edge에서 Azure로 데이터를 전송할 수 있도록 Azure Stack Edge 디바이스의 공유를 추가하고 연결하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: fc47497eeceff20e8357195226c2d44cea64fa88
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926113"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge"></a>자습서: Azure Stack Edge를 사용하여 데이터 전송

이 자습서에서는 Azure Stack Edge 디바이스에 공유를 추가하고 연결하는 방법에 대해 설명합니다. 공유가 추가되면 Azure Stack Edge에서 Azure로 데이터를 전송할 수 있습니다.

이 절차를 완료하는 데 약 10분이 걸릴 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 공유 추가
> * 공유에 연결

 
## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge에 공유를 추가하기 전에 다음 사항을 확인합니다.

- [Azure Stack Edge 설치](azure-stack-edge-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.

- [Azure Stack Edge 연결, 설정 및 활성화](azure-stack-edge-deploy-connect-setup-activate.md)에서 설명한 대로 물리적 디바이스를 활성화했습니다.


## <a name="add-a-share"></a>공유 추가

공유를 만들려면 다음 절차를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Stack Edge 리소스를 선택한 다음, **개요**로 이동합니다. 디바이스는 온라인 상태여야 합니다.

   ![디바이스 온라인 상태](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. 디바이스 명령 모음에서 **+ 공유 추가**를 선택합니다.

   ![공유 추가](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. **공유 추가** 창에서 다음 절차를 수행합니다.

    a. **이름** 상자에서 공유에 대한 고유한 이름을 제공합니다.  
    공유 이름에는 소문자, 숫자 및 하이픈만 있을 수 있습니다. 3-63자여야 하고 문자 또는 숫자로 시작해야 합니다. 하이픈 앞에는 문자 또는 숫자가 나와야 합니다.
    
    b. 공유에 대한 **유형**을 선택합니다.  
    유형은 **SMB** 또는 **NFS**이며, SMB가 기본값입니다. SMB는 Windows 클라이언트에 대한 표준이며, NFS는 Linux 클라이언트에 사용됩니다.  
    SMB 또는 NFS 공유를 선택하는지 여부에 따라 나머지 옵션이 약간 다릅니다. 

    다. 공유가 상주할 스토리지 계정을 입력합니다. 

    > [!IMPORTANT]
    > Azure Stack Edge 또는 Data Box Gateway 디바이스에서 사용하는 경우 사용하는 Azure Storage 계정에 불변성 정책을 설정하지 않았는지 확인합니다. 자세한 내용은 [Blob 스토리지에 대한 불변성 정책 설정 및 관리](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)를 참조하세요.
    
    d. **스토리지 서비스** 드롭다운 목록에서 **블록 Blob**, **페이지 Blob** 또는 **파일**을 선택합니다.  
    선택하는 서비스 유형은 Azure에서 사용하려는 데이터 형식에 따라 달라집니다. 이 예제에서는 데이터를 블록 Blo 으로 Azure에 저장하므로 **블록 Blob**을 선택합니다. **페이지 Blob**을 선택하는 경우 데이터가 512바이트로 정렬되어 있는지 확인합니다. 예를 들어 VHDX는 항상 512바이트로 정렬됩니다.

    e. 새 Blob 컨테이너를 만들거나 드롭다운 목록의 기존 항목을 사용합니다. Blob 컨테이너를 만드는 경우 컨테이너 이름을 제공합니다. 컨테이너가 아직 없으면 스토리지 계정에 새로 만든 공유 이름으로 만들어집니다.
   
    f. SMB 공유 또는 NFS 공유를 만들었는지 여부에 따라 다음 단계 중 하나를 수행합니다. 
     
    - **SMB 공유**: **모든 권한 로컬 사용자**  아래에서 **새로 만들기** 또는 **기존 항목 사용**을 선택합니다. 새 로컬 사용자를 만드는 경우 사용자 이름과 암호를 입력한 다음, 해당 암호를 확인합니다. 이 작업은 로컬 사용자에게 권한을 할당합니다. 여기서 권한을 할당한 후에 파일 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.

        이 공유 데이터에 대해 **읽기 작업만 허용** 확인란을 선택하면 읽기 전용 사용자를 지정할 수 있습니다.

        ![SMB 공유 추가](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 공유**: 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다.

        ![NFS 공유 추가](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. **만들기**를 선택하여 공유를 만듭니다.
    
    공유 만들기가 진행 중이라는 알림이 표시됩니다. 지정한 설정으로 공유가 만들어지면 새 공유를 반영하도록 **공유** 타일이 업데이트됩니다.
    

## <a name="connect-to-the-share"></a>공유에 연결

이제 마지막 단계에서 만든 하나 이상의 공유에 연결할 수 있습니다. SMB 공유 또는 NFS 공유인지 여부에 따라 단계가 다를 수 있습니다.

### <a name="connect-to-an-smb-share"></a>SMB 공유에 연결

Azure Stack Edge 디바이스에 연결된 Windows Server 클라이언트에서 다음 명령을 입력하여 SMB 공유에 연결합니다.


1. 명령 창에서 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. 이 작업을 요청하는 메시지가 표시되면 공유에 대한 암호를 입력합니다.  
   이 명령의 출력 샘플은 다음과 같습니다.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. 키보드에서 Windows+R을 선택합니다.

4. **실행** 창에서 `\\<device IP address>`를 지정한 다음, **확인**을 선택합니다.  
   파일 탐색기가 열립니다. 이제 폴더로 만든 공유가 표시됩니다. 파일 탐색기에서 공유(폴더)를 두 번 클릭하여 콘텐츠를 봅니다.
 
    ![SMB 공유에 연결](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    공유가 생성되면 데이터가 이러한 공유에 기록되고, 디바이스에서 해당 데이터를 클라우드로 푸시합니다.

### <a name="connect-to-an-nfs-share"></a>NFS 공유에 연결

Azure Stack Edge 디바이스에 연결된 Linux 클라이언트에서 다음 절차를 수행합니다.

1. 클라이언트에 NFSv4 클라이언트가 설치되어 있는지 확인합니다. NFS 클라이언트를 설치하려면 다음 명령을 사용합니다.

   `sudo apt-get install nfs-common`

    자세한 내용을 알아보려면 [NFSv4 클라이언트 설치](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)로 이동하세요.

2. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Azure Stack Edge 디바이스에서 만든 NFS 공유를 탑재합니다.

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > 공유 탑재 시 대규모 파일의 전송 속도가 개선될 경우 `sync` 옵션을 사용합니다.
    > 공유를 탑재하기 전에 로컬 컴퓨터에서 탑재 지점으로 작동할 디렉터리가 이미 만들어져 있는지 확인합니다. 이러한 디렉터리에는 파일 또는 하위 폴더가 없어야 합니다.

    다음 예제에서는 NFS를 통해 Azure Stack Edge 디바이스의 공유에 연결하는 방법을 보여 줍니다. 디바이스 IP는 `10.10.10.60`입니다. `mylinuxshare2` 공유는 ubuntuVM에 탑재됩니다. 공유 탑재 지점은 `/home/databoxubuntuhost/edge`입니다.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> 다음은 이 릴리스에 적용되는 주의 사항입니다.
> - 공유에 파일이 만들어지면 파일 이름 바꾸기가 지원되지 않습니다. 
> - 공유에서 파일을 삭제해도 스토리지 계정의 항목은 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Azure Stack Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 공유 추가
> * 공유에 연결

Azure Stack Edge를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge를 사용하여 데이터 변환](./azure-stack-edge-deploy-configure-compute.md)


