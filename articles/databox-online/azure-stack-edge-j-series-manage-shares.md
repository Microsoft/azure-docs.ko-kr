---
title: Azure Stack Edge GPU 공유 관리 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Stack에 지 GPU의 공유를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6448ac002482751052310532989278e3b88b5915
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268845"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-stack-edge"></a>Azure Portal를 사용 하 여 Azure Stack에 지에 대 한 공유를 관리 합니다.

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge에서 공유를 관리 하는 방법을 설명 합니다. Azure Portal 또는 로컬 웹 UI를 통해 Azure Stack Edge를 관리할 수 있습니다. Azure Portal을 사용하여 공유와 연결된 스토리지 계정의 스토리지 키를 동기화하거나 공유를 추가, 삭제, 새로 고칠 수 있습니다.

## <a name="about-shares"></a>공유에 대한 정보

Azure로 데이터를 전송 하려면 Azure Stack Edge에 공유를 만들어야 합니다. Azure Stack Edge 장치에 추가 하는 공유는 클라우드에 데이터를 푸시하는 로컬 공유 또는 공유 일 수 있습니다.

 - **로컬 공유**: 장치에서 데이터를 로컬로 처리 하려면 이러한 공유를 사용 합니다.
 - **공유**: 클라우드의 저장소 계정에 장치 데이터를 자동으로 푸시 하려면 이러한 공유를 사용 합니다. **새로 고침** 및 **스토리지 키 동기화**와 같은 모든 클라우드 기능이 공유에 적용됩니다.


## <a name="add-a-share"></a>공유 추가

공유를 만들려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **게이트웨이 > 공유**로 이동 합니다. 명령 모음에서 **+ 공유 추가**를 선택합니다.

    ![공유 추가 선택](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. **공유 추가**에서 공유 설정을 지정합니다. 공유에 대한 고유한 이름을 제공합니다.
    
    공유 이름에는 숫자, 소문자 및 하이픈만 포함될 수 있습니다. 공유 이름은 3 -63자이며 문자 또는 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다.

3. 공유에 대한 **유형**을 선택합니다. 유형은 **SMB** 또는 **NFS**이며, SMB가 기본값입니다. SMB는 Windows 클라이언트에 대한 표준이며, NFS는 Linux 클라이언트에 사용됩니다. SMB 공유 또는 NFS 공유 중에서 어느 것을 선택하는지에 따라 제공되는 옵션이 약간 다릅니다.

4. 공유가 상주할 **스토리지 계정**을 제공합니다. 컨테이너가 아직 없으면 스토리지 계정에 공유 이름이 포함된 컨테이너가 만들어집니다. 컨테이너가 이미 있으면 기존 컨테이너가 사용됩니다.

5. 드롭다운 목록의 블록 Blob, 페이지 Blob 또는 파일에서 **스토리지 서비스**를 선택합니다. 선택하는 서비스 유형은 데이터를 Azure에 저장할 형식에 따라 달라집니다. 예를 들어, 이 인스턴스에서는 Azure에서 데이터가 블록 Blob으로 존재하기를 원하므로 **블록 Blob**을 선택합니다. **페이지 Blob**을 선택 하는 경우 데이터를 512 바이트로 맞춰야 합니다. 항상 512바이트로 정렬되는 VHD 또는 VHDX에는 **페이지 Blob**을 사용합니다.

6. 이 단계는 SMB 공유 또는 NFS 공유 중에서 어느 것을 만드는지에 따라 달라집니다.
    - **SMB 공유를 만드는 경우** - **모든 권한 로컬 사용자** 필드에서 **새로 만들기** 또는 **기존 항목 사용**을 선택합니다. 새 로컬 사용자를 만드는 경우 **사용자 이름**, **암호**를 입력한 다음, 암호를 확인합니다. 이렇게 하면 로컬 사용자에게 해당 권한이 할당됩니다. 여기에서 권한을 할당한 후에 파일 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.

        ![SMB 공유 추가](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        이 공유 데이터에 대해 [읽기 작업만 허용]을 선택하면 읽기 전용 사용자만 지정할 수 있습니다.
    - **NFS 공유를 만드는 경우** - 공유에 액세스할 수 있도록 **허용된 클라이언트의 IP 주소**를 제공해야 합니다.

        ![NFS 공유 추가](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. Edge 컴퓨팅 모듈에서 공유에 손쉽게 액세스하려면 로컬 탑재 지점을 사용합니다. 공유가 생성된 후 자동으로 탑재될 수 있도록 **Edge 컴퓨팅과 공유 사용**을 선택합니다. 이 옵션을 선택하면 Edge 모듈이 로컬 탑재 지점과도 컴퓨팅을 사용할 수 있습니다.

8. **만들기**를 클릭하여 공유를 만듭니다. 공유 만들기가 진행 중이라는 알림이 표시됩니다. 지정한 설정으로 공유가 만들어지면 **공유** 블레이드를 업데이트하여 새 공유가 반영됩니다.

## <a name="add-a-local-share"></a>로컬 공유 추가

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **게이트웨이 > 공유**로 이동 합니다. 명령 모음에서 **+ 공유 추가**를 선택합니다.

    ![공유 추가 선택](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. **공유 추가**에서 공유 설정을 지정합니다. 공유에 대한 고유한 이름을 제공합니다.
    
    공유 이름에는 숫자, 소문자 및 대문자 및 하이픈만 사용할 수 있습니다. 공유 이름은 3 -63자이며 문자 또는 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다.

3. 공유에 대한 **유형**을 선택합니다. 유형은 **SMB** 또는 **NFS**이며, SMB가 기본값입니다. SMB는 Windows 클라이언트에 대한 표준이며, NFS는 Linux 클라이언트에 사용됩니다. SMB 공유 또는 NFS 공유 중에서 어느 것을 선택하는지에 따라 제공되는 옵션이 약간 다릅니다.

   > [!IMPORTANT]
   > Azure Stack Edge 또는 Data Box Gateway 디바이스에서 사용하는 경우 사용하는 Azure Storage 계정에 불변성 정책을 설정하지 않았는지 확인합니다. 자세한 내용은 [Blob 스토리지에 대한 불변성 정책 설정 및 관리](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)를 참조하세요.

4. Edge 컴퓨팅 모듈에서 공유에 손쉽게 액세스하려면 로컬 탑재 지점을 사용합니다. Edge 모듈에서 로컬 탑재 지점을 통해 컴퓨팅을 사용할 수 있도록 **Edge 컴퓨팅으로 공유 사용**을 선택합니다.

5. **Edge 로컬 공유로 구성**을 선택합니다. 로컬 공유의 데이터는 디바이스에서 로컬로 유지됩니다. 이 데이터는 로컬에서 처리할 수 있습니다.

6. **모든 권한 로컬 사용자** 필드에서 **새로 만들기** 또는 **기존 항목 사용**을 선택합니다.

7. **만들기**를 선택합니다. 

    ![로컬 공유 만들기](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    공유 만들기가 진행 중이라는 알림이 표시됩니다. 지정한 설정으로 공유가 만들어지면 **공유** 블레이드를 업데이트하여 새 공유가 반영됩니다.

    ![공유 블레이드가 업데이트된 보기](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    이 공유의 Edge 컴퓨팅 모듈에 대한 로컬 탑재 지점을 보려면 공유를 선택합니다.

    ![로컬 공유 세부 정보 보기](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>공유 탑재

Azure Stack Edge 장치에서 계산을 구성 하기 전에 공유를 만든 경우 공유를 탑재 해야 합니다. 공유를 탑재하려면 다음 단계를 수행합니다.


1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **게이트웨이 > 공유**로 이동 합니다. 공유 목록에서 탑재할 공유를 선택합니다. 선택한 공유에 대해 **컴퓨팅에 사용됨** 열에 **사용 안 함**으로 상태가 표시됩니다.

    ![공유 선택](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. **탑재**를 선택합니다.

    ![탑재 선택](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. 확인하라는 메시지가 표시되면 **예**를 선택합니다. 그러면 공유가 탑재됩니다.

    ![탑재 확인](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. 공유가 탑재된 후 공유 목록으로 이동합니다. **컴퓨팅에 사용됨** 열에 공유 상태가 **사용함**으로 표시되는 것을 볼 수 있습니다.

    ![탑재된 공유](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. 공유를 다시 선택하여 공유에 대한 로컬 탑재 지점을 봅니다. Edge 컴퓨팅 모듈은 이 로컬 탑재 지점을 공유에 사용합니다.

    ![공유에 대한 로컬 탑재 지점](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>공유 분리

공유를 분리하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **게이트웨이 > 공유**로 이동 합니다.

    ![공유 선택](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2. 공유 목록에서 분리할 공유를 선택합니다. 분리할 공유가 다른 모듈에서 사용되지 않는지 확인하는 것이 좋습니다. 공유가 모듈에서 사용되면 해당 모듈과 관련된 문제가 표시됩니다. **분리**를 선택 합니다.

    ![분리 선택](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. 확인하라는 메시지가 표시되면 **예**를 선택합니다. 그러면 공유가 분리됩니다.

    ![분리 확인](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. 공유가 분리된 후 공유 목록으로 이동합니다. **컴퓨팅에 사용됨** 열에 공유 상태가 **사용 안 함**으로 표시되는 것을 볼 수 있습니다.

    ![분리된 공유](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>공유 삭제

공유를 삭제하려면 Azure Portal에서 다음 단계를 수행합니다.

1. 공유 목록에서 삭제하려는 공유를 선택하고 클릭합니다.

    ![공유 선택](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. **삭제**를 클릭합니다.

    ![삭제 클릭](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. 확인 메시지가 표시되면 **예**를 클릭합니다.

    ![삭제 확인](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

공유 목록이 업데이트되어 삭제가 반영됩니다.

## <a name="refresh-shares"></a>공유 새로 고침

새로 고침 기능을 사용하면 공유의 콘텐츠를 새로 고칠 수 있습니다. 공유를 새로 고치면 마지막 새로 고침 이후 클라우드에 추가된 Blob과 파일을 비롯한 모든 Azure 개체를 찾기 위해 검색이 시작됩니다. 그런 다음, 이러한 추가 파일이 다운로드되어 디바이스에서 공유의 콘텐츠를 새로 고칩니다.

> [!IMPORTANT]
> - 로컬 공유는 새로 고칠 수 없습니다.
> - 사용 권한 및 ACL(액세스 제어 목록)은 새로 고침 작업에서 유지되지 않습니다. 

공유를 새로 고치려면 Azure Portal에서 다음 단계를 수행합니다.

1.  Azure Portal에서 **공유**로 이동합니다. 새로 고치려는 공유를 선택하고 클릭합니다.

    ![공유 선택](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  **새로 고침**을 클릭합니다. 

    ![새로 고침 클릭](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  확인 메시지가 표시되면 **예**를 클릭합니다. 온-프레미스 공유의 콘텐츠를 새로 고치는 작업이 시작됩니다.

    ![새로 고침 확인](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   새로 고침을 진행하는 동안은 상황에 맞는 메뉴에서 새로 고침 옵션이 회색으로 표시됩니다. 새로 고침 작업 상태를 표시하려면 작업 알림을 클릭합니다.

5.   새로 고침 작업의 소요 시간은 Azure 컨테이너의 파일 수는 물론 디바이스에 있는 파일에 따라 달라집니다. 새로 고침이 완료되면 공유 타임스탬프가 업데이트됩니다. 새로 고침에 부분적인 실패가 있더라도 작업이 성공한 것으로 간주되며 타임스탬프가 업데이트됩니다. 새로 고침 오류 로그도 업데이트됩니다.

![업데이트된 타임스탬프](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
오류가 있으면 경고가 발생합니다. 경고에는 원인 및 문제 해결을 위한 권장 사항이 자세히 설명되어 있습니다. 또한 경고는 업데이트나 삭제에 실패한 파일을 비롯하여 오류에 대한 전체 요약이 포함된 파일로 연결됩니다.

## <a name="sync-pinned-files"></a>고정 된 파일 동기화 

고정 된 파일을 자동으로 동기화 하려면 Azure Portal에서 다음 단계를 수행 합니다.
 
1. 기존 Azure storage 계정을 선택 합니다. 

2. **컨테이너로** 이동 하 고 **+ 컨테이너** 를 선택 하 여 컨테이너를 만듭니다. 이 컨테이너의 이름을 *newcontainer*로 합니다. **공용 액세스 수준을** 컨테이너로 설정 합니다.

    ![고정 된 파일에 대 한 자동화 된 동기화 1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. 컨테이너 이름을 선택 하 고 다음 메타 데이터를 설정 합니다.  

    - 이름 = "고정" 
    - Value = "True" 

    ![고정 된 파일에 대 한 자동화 된 동기화 2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. 장치에 새 공유를 만듭니다. 기존 컨테이너 옵션을 선택 하 여 고정 된 컨테이너에 매핑합니다. 공유를 읽기 전용으로 표시 합니다. 새 사용자를 만들고이 공유의 사용자 이름과 해당 암호를 지정 합니다.  

    ![고정 된 파일에 대 한 자동화 된 동기화 3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. Azure Portal에서 사용자가 만든 컨테이너로 이동 합니다. 메타 데이터가 고정으로 설정 된 newcontainer에 고정 하려는 파일을 업로드 합니다. 

6. 장치에 대 한 Azure Portal에서 **데이터 새로 고침** 을 선택 하 여 특정 Azure Storage 컨테이너에 대 한 고정 정책을 다운로드 합니다.  

    ![고정 된 파일에 대 한 자동화 된 동기화 4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. 장치에서 만든 새 공유에 액세스 합니다. 저장소 계정에 업로드 된 파일은 이제 로컬 공유에 다운로드 됩니다. 

    장치가 연결이 끊어졌거나 다시 연결 되 면 새로 고침을 트리거합니다. 새로 고치면 변경 된 파일만 표시 됩니다. 


## <a name="sync-storage-keys"></a>스토리지 키 동기화

스토리지 계정 키가 순환된 경우 스토리지 액세스 키를 동기화해야 합니다. 동기화를 수행하면 디바이스에 스토리지 계정의 최신 키를 가져올 수 있습니다.

스토리지 액세스 키를 동기화하려면 Azure Portal에서 다음 단계를 수행합니다.

1. 리소스에서 **개요**로 이동합니다. 공유 목록에서 동기화해야 하는 스토리지 계정과 연결된 공유를 선택하여 클릭합니다.

    ![관련 스토리지 계정과 함께 공유 선택](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. **스토리지 키 동기화**를 클릭합니다. 확인 메시지가 표시되면 **예**를 클릭합니다.

     ![스토리지 키 동기화 선택](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. 동기화가 완료되면 대화 상자를 종료합니다.

>[!NOTE]
> 이 작업은 지정된 스토리지 계정에 대해 한 번만 수행하면 됩니다. 동일한 스토리지 계정에 연결된 모든 공유에 대해 이 작업을 반복할 필요가 없습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](azure-stack-edge-j-series-manage-users.md)하는 방법을 알아봅니다.
