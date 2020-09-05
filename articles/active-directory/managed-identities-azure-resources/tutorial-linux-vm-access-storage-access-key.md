---
title: 자습서`:` 관리 ID를 사용하여 액세스 키를 통해 Azure Storage에 액세스 - Linux - Azure AD
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Storage에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c00c723c23e2f0d962009d33a6abe1008c734a5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266275"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 액세스 키를 통해 Azure Storage에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Linux VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 스토리지 계정 액세스 키를 검색하는 방법을 보여줍니다. Storage SDK를 사용하는 등의 스토리지 작업을 수행할 때 일반적인 방식으로 스토리지 액세스 키를 사용할 수 있습니다. 이 자습서의 경우 Azure CLI를 사용하여 Blob을 업로드하고 다운로드합니다. 다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * 리소스 관리자의 스토리지 계정 액세스 키에 대한 VM 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져와서 리소스 관리자에서 스토리지 액세스 키를 검색하는 데 사용  

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>스토리지 계정 만들기 

스토리지 계정이 아직 없는 경우 이제 하나 만들게 됩니다.  또한 이 단계를 건너뛰고 기존 스토리지 계정의 키에 대한 VM 시스템 할당 관리 ID 액세스 권한을 부여할 수 있습니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **스토리지**를 클릭하면 **스토리지 계정**, 새 “스토리지 계정 만들기” 패널이 표시됩니다.
3. 나중에 사용할 스토리지 계정에 대한 **이름**을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 각각 “리소스 관리자” 및 “범용”으로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 스토리지 계정 만들기](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>스토리지 계정에 Blob 컨테이너 만들기

나중에 새 스토리지 계정에 파일을 업로드 및 다운로드합니다. 파일은 Blob Storage가 필요하기 때문에 파일을 저장할 Blob 컨테이너를 만들어야 합니다.

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. 왼쪽에서 “Blob service” 아래에 있는 **컨테이너** 링크를 클릭합니다.
3. 페이지 맨 위에 있는 **+ 컨테이너**를 클릭하면 “새 컨테이너” 패널이 나타납니다.
4. 컨테이너에 이름을 지정하고 액세스 수준을 선택한 다음 **확인**을 클릭합니다. 지정한 이름은 이 자습서의 뒷부분에 사용됩니다. 

    ![스토리지 컨테이너 만들기](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>스토리지 계정 액세스 키를 사용하도록 VM의 시스템 할당 관리 ID 액세스 부여

이 단계에서 스토리지 계정의 키에 대한 VM의 시스템 할당 관리 ID 액세스 권한을 부여합니다.   

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. 왼쪽 패널의 **액세스 제어(IAM)** 링크를 클릭합니다.  
3. 페이지의 위쪽에서 **+ 역할 할당 추가**를 클릭하여 VM에 대한 새 역할 할당을 추가합니다.
4. 페이지 오른쪽에서 **역할**을 &quot;스토리지 계정 키 운영자 서비스 역할&quot;로 설정합니다. 
5. 다음 드롭다운에서 **다음에 대한 액세스 할당**을 “Virtual Machine” 리소스로 설정합니다.  
6. 다음으로 적절한 구독이 **구독** 드롭다운에 나열되는지 확인하고 **리소스 그룹**을 "모든 리소스 그룹"으로 설정합니다.  
7. 마지막으로 **선택** 드롭다운에서 Linux Virtual Machine을 선택하고 **저장**을 클릭합니다. 

    ![대체 이미지 텍스트](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/install-win10)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다. 
2. SSH 클라이언트를 사용하여 VM에 연결합니다.  
3. 그리고 나면 **Linux VM**을 만들 때 추가했던 **암호**를 입력하라는 메시지가 표시됩니다. 이제 정상적으로 로그인되어야 합니다.  
4. CURL을 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져옵니다.  

    액세스 토큰에 대한 CURL 요청 및 응답은 다음과 같습니다.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > 이전 요청에서 "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    > 다음 응답에서는 간단히 나타내기 위해 access_token 요소를 축약했습니다.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>스토리지 호출을 위해 Azure Resource Manager에서 스토리지 계정 액세스 키 가져오기  

이제 CURL을 사용하여 이전 섹션에서 검색한 액세스 토큰을 사용하여 리소스 관리자를 호출하여 스토리지 액세스 키를 검색합니다. 스토리지 액세스 키가 있으면 스토리지 업로드/다운로드 작업을 호출할 수 있습니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<STORAGE ACCOUNT NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다.

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 이전 URL의 텍스트는 대/소문자를 구분하므로 리소스 그룹을 적절하게 반영하는 대/소문자를 사용하는지 확인하세요. 또한 이 요청은 GET 요청이 아닌 POST 요청임을 기억해야 하며, -d(NULL일 수 있음)를 사용하여 길이 제한을 캡처하는 값을 전달해야 합니다.  

CURL 응답에서는 키 목록을 제공합니다.  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Blob Storage 컨테이너에 업로드할 샘플 Blob 파일을 만듭니다. Linux VM에서는 다음 명령을 사용하여 이 작업을 수행할 수 있습니다. 

```bash
echo "This is a test file." > test.txt
```

다음으로, 스토리지 액세스 키를 사용하여 CLI `az storage` 명령을 통해 인증을 받고 Blob 컨테이너에 파일을 업로드합니다. 이 단계에서는 VM에 [최신 Azure CLI를 설치](/cli/azure/install-azure-cli)해야 합니다(아직 없는 경우).
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

응답: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

또한 Azure CLI를 사용하여 파일을 다운로드하고 스토리지 액세스 키를 사용하여 인증할 수 있습니다. 

요청: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

응답: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux VM 시스템 할당 관리 ID를 사용하여 액세스 키를 통해 Azure Storage에 액세스하는 방법을 알아보았습니다.  Azure Storage 액세스 키에 대한 자세한 내용은 다음 리소스를 참조하세요.

> [!div class="nextstepaction"]
>[스토리지 액세스 키 관리](../../storage/common/storage-account-create.md)