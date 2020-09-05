---
title: 자습서`:` 관리 ID를 통해 SAS 자격 증명을 사용하여 Azure Storage에 액세스 - Azure AD
description: Windows VM 시스템 할당 관리 ID를 사용하여 스토리지 계정 액세스 키 대신 SAS 자격 증명으로 Azure Storage에 액세스하는 방법을 보여주는 자습서입니다.
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
ms.date: 01/24/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa04247aca777612c05a7531dc5b36e7af40e60e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255837"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 SAS 자격 증명으로 Azure Storage에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 ID를 사용하여 SAS(공유 액세스 서명) 자격 증명을 얻는 방법을 보여줍니다. 특히 [서비스 SAS 자격 증명](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)에 대한 내용을 다룹니다. 

서비스 SAS는 계정 액세스 키를 노출하지 않고 제한된 시간 동안 특정 서비스(이 경우 Blob service)에 대해 스토리지 계정의 개체에 대해 제한된 액세스 권한을 부여하는 기능을 제공합니다. Storage SDK를 사용하는 등의 Storage 작업을 수행할 때 일반적인 방식으로 SAS 자격 증명을 사용할 수 있습니다. 이 자습서에서는 Azure Storage PowerShell을 사용하여 Blob을 업로드 및 다운로드하는 과정을 보여 줍니다. 다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * Resource Manager의 스토리지 계정 SAS에 대한 VM 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져오고 리소스 관리자에서 SAS를 검색하는 데 사용 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>스토리지 계정 만들기 

스토리지 계정이 아직 없는 경우 이제 하나 만들게 됩니다. 또한 이 단계를 건너뛰고 기존 스토리지 계정의 SAS 자격 증명에 대한 VM의 시스템 할당 관리 ID 액세스 권한을 부여할 수 있습니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **스토리지**를 클릭하면 **스토리지 계정**, 새 “스토리지 계정 만들기” 패널이 표시됩니다.
3. 나중에 사용할 스토리지 계정에 대한 이름을 입력합니다.  
4. **배포 모델** 및 **계정 종류**는 각각 “리소스 관리자” 및 “범용”으로 설정해야 합니다. 
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.
6. **만들기**를 클릭합니다.

    ![새 스토리지 계정 만들기](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>스토리지 계정에 Blob 컨테이너 만들기

나중에 새 스토리지 계정에 파일을 업로드 및 다운로드합니다. 파일은 Blob Storage가 필요하기 때문에 파일을 저장할 Blob 컨테이너를 만들어야 합니다.

1. 새로 만든 스토리지 계정으로 다시 이동합니다.
2. 왼쪽 패널에서 “Blob service” 아래에 있는 **컨테이너** 링크를 클릭합니다.
3. 페이지 맨 위에 있는 **+ 컨테이너**를 클릭하면 “새 컨테이너” 패널이 나타납니다.
4. 컨테이너에 이름을 지정하고 액세스 수준을 선택한 다음 **확인**을 클릭합니다. 지정한 이름은 이 자습서의 뒷부분에 사용됩니다. 

    ![스토리지 컨테이너 만들기](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>스토리지 SAS를 사용하도록 VM의 시스템 할당 관리 ID 액세스 부여 

Azure Storage는 Azure AD 인증을 기본적으로 지원하지 않습니다.  그러나 관리 ID를 사용하여 Resource Manager에서 스토리지 SAS를 검색한 다음, 해당 SAS를 사용하여 스토리지에 액세스할 수 있습니다.  이 단계에서 스토리지 계정 SAS에 대한 VM의 시스템 할당 관리 ID 액세스 권한을 부여합니다.   

1. 새로 만든 스토리지 계정으로 다시 이동합니다.   
2. 왼쪽 패널의 **액세스 제어(IAM)** 링크를 클릭합니다.  
3. 페이지의 위쪽에서 **+ 역할 할당 추가**를 클릭하여 VM에 대한 새 역할 할당을 추가합니다.
4. 페이지 오른쪽에서 **역할**을 &quot;스토리지 계정 참가자&quot;로 설정합니다.  
5. 다음 드롭다운에서 **다음에 대한 액세스 할당**을 “Virtual Machine” 리소스로 설정합니다.  
6. 다음으로 적절한 구독이 **구독** 드롭다운에 나열되는지 확인하고 **리소스 그룹**을 "모든 리소스 그룹"으로 설정합니다.  
7. 마지막으로 **선택** 드롭다운에서 Windows Virtual Machine을 선택하고 **저장**을 클릭합니다. 

    ![대체 이미지 텍스트](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용 

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

이 부분에서는 Azure Resource Manager PowerShell cmdlet을 사용해야 합니다.  설치하지 않은 경우 계속하기 전에 [최신 버전을 다운로드](/powershell/azure/)합니다.

1. Azure Portal에서 **Virtual Machines**, Windows 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다.
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다. 
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 PowerShell을 엽니다. 
4. Powershell의 Invoke-WebRequest를 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 “Content” 요소를 추출합니다. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>스토리지 호출을 위해 Azure Resource Manager에서 SAS 자격 증명 가져오기 

이제 PowerShell을 사용하여 이전 섹션에서 검색한 액세스 토큰으로 리소스 관리자를 호출하고 스토리지 SAS 자격 증명을 만듭니다. SAS 자격 증명이 있으면 스토리지 작업을 호출할 수 있습니다.

이 요청을 위해 다음 HTTP 요청 매개 변수를 사용하여 SAS 자격 증명을 만듭니다.

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

이러한 매개 변수는 SAS 자격 증명에 대한 요청 POST 본문에 포함됩니다. SAS 자격 증명을 만들기 위한 매개 변수에 대한 자세한 내용은 [서비스 SAS REST 목록 참조](/rest/api/storagerp/storageaccounts/listservicesas)를 참조하세요.

가장 먼저 매개 변수를 JSON으로 변환한 다음 스토리지 `listServiceSas` 엔드포인트를 호출하여 SAS 자격 증명을 만듭니다.

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자를 사용해야 하며, “resourceGroups”와 같이 대문자 “G”를 사용해야 합니다. 

이제 응답에서 SAS 자격 증명을 추출할 수 있습니다.

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

SAS 자격 증명을 검사하는 경우 다음과 같은 표시가 나타납니다.

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

그런 다음 “test.txt”라는 파일을 만듭니다. 그런 다음 SAS 자격 증명을 사용하여 `New-AzStorageContent` cmdlet으로 인증하고 파일을 blob 컨테이너로 업로드한 다음 파일을 다운로드합니다.

```bash
echo "This is a test text file." > test.txt
```

먼저 `Install-Module Azure.Storage`를 사용하여 Azure Storage cmdlet을 설치해야 합니다. `Set-AzStorageBlobContent` PowerShell cmdlet을 사용하여 방금 만든 Blob을 업로드합니다.

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

응답:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

`Get-AzStorageBlobContent` PowerShell cmdlet을 사용하여 방금 업로드한 Blob을 다운로드할 수 있습니다.

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

응답:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows VM의 시스템 할당 관리 ID를 사용하여 SAS 자격 증명을 통해 Azure Storage에 액세스하는 방법을 알아보았습니다.  Azure Storage SAS에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[SAS(공유 액세스 서명) 사용](../../storage/common/storage-sas-overview.md)