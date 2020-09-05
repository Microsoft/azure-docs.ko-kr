---
title: 자습서`:` 관리 ID를 사용하여 Azure Storage에 액세스 - Linux - Azure AD
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b61d3ed21d053fc7166b47c94a9ec61e355d199
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263164"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스하는 방법을 보여줍니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * 스토리지 계정에 Blob 컨테이너 만들기
> * Azure Storage 컨테이너에 대한 Linux VM의 관리 ID 액세스 권한 부여
> * 액세스 토큰 가져오기 및 액세스 토큰을 사용하여 Azure Storage 호출

> [!NOTE]
> Azure Storage에 대한 Azure Active Directory 인증은 공개 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 **사용해 보세요** 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](/cli/azure/install-azure-cli)합니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기 

이 섹션에서는 스토리지 계정을 만듭니다. 

1. Azure Portal의 왼쪽 위에 있는 **+ 리소스 만들기** 단추를 클릭합니다.
2. **스토리지**를 클릭한 다음, **스토리지 계정 - Blob, 파일, 테이블, 큐**를 클릭합니다.
3. **이름** 아래에서 스토리지 계정의 이름을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 **리소스 관리자** 및 **스토리지(범용 v1)** 로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 스토리지 계정 만들기](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Blob 컨테이너 만들기 및 스토리지 계정에 파일 업로드

파일에 Blob Storage가 필요하므로 파일을 저장할 Blob 컨테이너를 만들어야 합니다. 그런 다음, 새 스토리지 계정에서 Blob 컨테이너에 파일을 업로드합니다.

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. **Blob Service**에서 **컨테이너**를 클릭합니다.
3. 페이지 맨 위에서 **+ 컨테이너**를 클릭합니다.
4. **새 컨테이너** 아래에서 컨테이너에 대한 이름을 입력하고 **공용 액세스 수준** 아래에서 기본값을 유지합니다.

    ![스토리지 컨테이너 만들기](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 선택한 편집기를 사용하여 로컬 컴퓨터에서 *hello world.txt*라는 파일을 만듭니다.  파일을 열고 "Hello world! :)"라는 텍스트(따옴표 제외)를 추가한 다음, 저장합니다. 

6. 컨테이너 이름을 클릭한 다음, **업로드**를 클릭하여 새로 만든 컨테이너에 파일을 추가합니다.
7. **Blob 업로드** 창의 **파일** 아래에서 폴더 아이콘을 클릭하고 로컬 컴퓨터에서 **hello_world.txt**라는 파일을 찾고, 파일을 선택한 다음, **업로드**를 클릭합니다.

    ![텍스트 파일 업로드](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>VM에 Azure Storage 컨테이너에 대한 액세스 권한 부여 

VM의 관리 ID를 사용하여 Azure Storage Blob에서 데이터를 검색할 수 있습니다.   

1. 새로 만든 스토리지 계정으로 다시 이동합니다.  
2. 왼쪽 패널의 **액세스 제어(IAM)** 링크를 클릭합니다.  
3. 페이지의 위쪽에서 **+ 역할 할당 추가**를 클릭하여 VM에 대한 새 역할 할당을 추가합니다.
4. **역할**의 드롭다운에서 **Storage Blob 데이터 판독기**를 선택합니다. 
5. 다음 드롭다운의 **다음에 대한 액세스 할당** 아래에서 **가상 머신**을 선택합니다.  
6. 다음으로 적절한 구독이 **구독** 드롭다운에 나열되는지 확인한 다음, **리소스 그룹**을 **모든 리소스 그룹**으로 설정합니다.  
7. **선택** 아래에서 VM을 선택한 다음, **저장**을 클릭합니다.

    ![권한 할당](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>액세스 토큰 가져오기 및 액세스 토큰을 사용하여 Azure Storage 호출

Azure Storage는 기본적으로 Azure AD 인증을 지원하므로 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. 이 방법은 Azure Storage를 Azure AD와 통합하는 작업의 일부로, 연결 문자열에서 자격 증명을 제공하는 것과는 다릅니다.

다음 단계를 완료하려면 앞에서 작성한 VM에서 작업해야 하며 연결을 위한 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](~/articles/virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지에서 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다.
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결**합니다. 
3. 터미널 창에서 CURL을 사용하여 로컬 관리 ID 엔드포인트에 대한 요청을 만들어서 Azure Storage에 대한 액세스 토큰을 가져옵니다.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. 이제 액세스 토큰을 사용하여 Azure Storage에 액세스합니다. 예를 들어 앞에서 컨테이너에 업로드한 샘플 파일의 내용을 읽습니다. `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` 및 `<FILE NAME>` 값을 앞에서 지정한 값으로 바꾸고, `<ACCESS TOKEN>`을 이전 단계에서 반환된 토큰으로 바꿉니다.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   응답에는 파일의 내용이 포함됩니다.

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux VM 시스템 할당 관리 ID를 활성화하여 Azure Storage에 액세스하는 방법을 알아보았습니다.  Azure Storage에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)