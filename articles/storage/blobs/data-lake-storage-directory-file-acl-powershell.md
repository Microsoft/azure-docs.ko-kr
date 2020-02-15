---
title: Acl & 파일에 대 한 Azure Data Lake Storage Gen2 PowerShell (미리 보기)
description: PowerShell cmdlet을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: a5ad4cbd11d6468413a43576e2156ee807067aa8
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153352"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>PowerShell을 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 Acl 관리 (미리 보기)

이 문서에서는 PowerShell을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 문서에서 설명 하는 PowerShell 모듈은 현재 공개 미리 보기로 제공 됩니다.

[Gen1 To Gen2 mapping](#gen1-gen2-map) | [사용자 의견 제공](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>필수 조건

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.
> * .NET Framework 4.7.2 이상 설치 되어 있습니다. [다운로드 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)를 참조 하세요.
> * PowerShell 버전 `5.1` 이상

## <a name="install-powershell-modules"></a>PowerShell 모듈 설치

1. 다음 명령을 사용 하 여 설치 된 PowerShell 버전이 `5.1` 이상 인지 확인 합니다. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 를 참조 하세요.
    
2. 최신 **PowershellGet** 모듈을 설치 합니다. 그런 다음 Powershell 콘솔을 닫았다가 다시 엽니다.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  설치 **Az. Storage** preview module.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) 를 참조 하세요.

## <a name="connect-to-the-account"></a>계정에 연결

Windows PowerShell 명령 창을 열고 `Connect-AzAccount` 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 디렉터리를 만들고 관리 하려는 저장소 계정의 구독으로 설정 합니다. 이 예제에서는 `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

그런 다음 명령을 통해 저장소 계정에 대 한 권한 부여를 가져오는 방법을 선택 합니다. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>옵션 1: Azure Active Directory (AD)를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 사용자 계정에 적절 한 RBAC (역할 기반 액세스 제어) 할당 및 ACL 권한이 있는지 확인 합니다. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>옵션 2: 저장소 계정 키를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 RBAC 또는 ACL 사용 권한을 확인 하지 않습니다.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. `New-AzDatalakeGen2FileSystem` cmdlet을 사용 하 여 만들 수 있습니다. 

이 예에서는 `my-file-system`이라는 파일 시스템을 만듭니다.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>디렉터리 만들기

`New-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리 참조를 만듭니다. 

이 예제에서는 `my-directory` 라는 디렉터리를 파일 시스템에 추가 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

이 예제에서는 동일한 디렉터리를 추가 하지만 사용 권한, umask, 속성 값 및 메타 데이터 값도 설정 합니다. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

이 예제에서는 `Get-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리를 가져온 다음 속성 값을 콘솔에 출력 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`Move-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리 이름을 바꾸거나 이동 합니다.

이 예에서는 `my-directory` 이름에서 `my-new-directory`이름으로 디렉터리의 이름을 바꿉니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

이 예제에서는 이름이 `my-directory` 인 디렉터리를 `my-subdirectory`라는 `my-directory-2`의 하위 디렉터리로 이동 합니다. 또한이 예제에서는 하위 디렉터리에 umask를 적용 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>디렉터리 삭제

`Remove-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리를 삭제 합니다.

이 예에서는 `my-directory`라는 디렉터리를 삭제 합니다. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

`-Force` 매개 변수를 사용 하 여 프롬프트 없이 파일을 제거할 수 있습니다.

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

`Get-AzDataLakeGen2ItemContent` cmdlet을 사용 하 여 디렉터리에서 파일을 다운로드 합니다.

이 예제에서는 이름이 `my-directory`인 디렉터리에서 `upload.txt` 파일을 다운로드 합니다. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`Get-AzDataLakeGen2ChildItem` cmdlet을 사용 하 여 디렉터리의 내용을 나열 합니다.

이 예에서는 이름이 `my-directory`인 디렉터리의 내용을 나열 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

이 예에서는 `ACL`, `Permissions`, `Group`및 `Owner` 속성에 대 한 값을 반환 하지 않습니다. 이러한 값을 얻으려면 `-FetchPermission` 매개 변수를 사용 합니다. 

다음 예에서는 동일한 디렉터리의 내용을 나열 하지만 `-FetchPermission` 매개 변수를 사용 하 여 `ACL`, `Permissions`, `Group`및 `Owner` 속성의 값을 반환 합니다. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

파일 시스템의 내용을 나열 하려면 명령에서 `-Path` 매개 변수를 생략 합니다.

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

`New-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리에 파일을 업로드 합니다.

이 예제에서는 이름이 `upload.txt` 인 파일을 `my-directory`라는 디렉터리에 업로드 합니다. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

이 예에서는 동일한 파일을 업로드 하지만 대상 파일의 사용 권한, umask, 속성 값 및 메타 데이터 값을 설정 합니다. 또한이 예제에서는 이러한 값을 콘솔에 출력 합니다.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>파일 속성 표시

이 예제에서는 `Get-AzDataLakeGen2Item` cmdlet을 사용 하 여 파일을 가져온 다음 속성 값을 콘솔에 출력 합니다.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>파일 삭제

`Remove-AzDataLakeGen2Item` cmdlet을 사용 하 여 파일을 삭제 합니다.

이 예에서는 `upload.txt`라는 파일을 삭제 합니다. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

`-Force` 매개 변수를 사용 하 여 프롬프트 없이 파일을 제거할 수 있습니다.

## <a name="manage-access-permissions"></a>액세스 권한 관리

디렉터리 및 파일의 액세스 권한을 가져오고 설정 하 고 업데이트할 수 있습니다.

> [!NOTE]
> Azure Active Directory (Azure AD)를 사용 하 여 명령에 권한을 부여 하는 경우 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

### <a name="get-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 가져오기

`Get-AzDataLakeGen2Item`cmdlet을 사용 하 여 디렉터리 또는 파일의 ACL을 가져옵니다.

이 예에서는 **디렉터리**의 acl을 가져온 다음 해당 acl을 콘솔에 출력 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

이 예제에서는 **파일** 의 acl을 가져온 다음 해당 acl을 콘솔에 출력 합니다.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

다음 이미지는 디렉터리의 ACL을 가져온 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

이 예제에서 소유 하는 사용자에 게는 읽기, 쓰기 및 실행 권한이 있습니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="set-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 설정

`New-AzDataLakeGen2ItemAclObject` cmdlet을 사용 하 여 소유 하 고 있는 사용자, 소유 그룹 또는 기타 사용자에 대 한 ACL을 만듭니다. 그런 다음 `Update-AzDataLakeGen2Item` cmdlet을 사용 하 여 ACL을 커밋합니다.

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 **디렉터리** 의 acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 **파일** 에 acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

다음 이미지는 파일의 ACL을 설정한 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

이 예제에서 소유 하는 사용자 및 소유 그룹에는 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에 게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="update-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 업데이트

`Get-AzDataLakeGen2Item` cmdlet을 사용 하 여 디렉터리 또는 파일의 ACL을 가져옵니다. 그런 다음 `New-AzDataLakeGen2ItemAclObject` cmdlet을 사용 하 여 새 ACL 항목을 만듭니다. `Update-AzDataLakeGen2Item` cmdlet을 사용 하 여 새 ACL을 적용 합니다.

이 예에서는 사용자에 게 디렉터리에 대 한 쓰기 및 실행 권한을 제공 합니다.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the directory ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew  

```

이 예에서는 사용자에 게 파일에 대 한 쓰기 및 실행 권한을 제공 합니다.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the file ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $aclnew 

```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>파일 시스템의 모든 항목에 대 한 사용 권한 설정

`Get-AzDataLakeGen2Item` 및 `-Recurse` 매개 변수와 `Update-AzDataLakeGen2Item` cmdlet을 함께 사용 하 여 파일 시스템에 있는 모든 디렉터리와 파일의 ACL을 재귀적으로 설정할 수 있습니다. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchPermission | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 to Gen2 Mapping

다음 표에서는 Data Lake Storage Gen1에 사용 되는 cmdlet이 Data Lake Storage Gen2 cmdlet에 매핑되는 방법을 보여 줍니다.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| 참고 |
|--------|---------|-----|
|AzDataLakeStoreChildItem|AzDataLakeGen2ChildItem|기본적으로 AzDataLakeGen2ChildItem cmdlet은 첫 번째 수준의 자식 항목만 나열 합니다. -재귀 매개 변수는 자식 항목을 재귀적으로 나열 합니다. |
|AzDataLakeStoreItem<br>AzDataLakeStoreItemAclEntry<br>AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission|AzDataLakeGen2Item|AzDataLakeGen2Item cmdlet의 출력 항목에는 Acl, Owner, Group, Permission 속성이 있습니다.|
|AzDataLakeStoreItemContent|AzDataLakeGen2FileContent|AzDataLakeGen2FileContent cmdlet은 파일 콘텐츠를 로컬 파일에 다운로드 합니다.|
|AzDataLakeStoreItem|AzDataLakeGen2Item||
|AzDataLakeStoreItem|AzDataLakeGen2Item|이 cmdlet은 로컬 파일에서 새 파일 콘텐츠를 업로드 합니다.|
|AzDataLakeStoreItem|AzDataLakeGen2Item||
|AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission<br>AzDataLakeStoreItemAcl|업데이트-AzDataLakeGen2Item|AzDataLakeGen2Item cmdlet은 단일 항목만 업데이트 하 고 재귀적으로 업데이트 하지 않습니다. 재귀적으로 업데이트 하려는 경우 AzDataLakeStoreChildItem cmdlet을 사용 하 여 항목을 나열 하 고 파이프라인을 AzDataLakeGen2Item cmdlet으로 파이프라인 합니다.|
|AzDataLakeStoreItem|AzDataLakeGen2Item|항목이 존재 하지 않는 경우 AzDataLakeGen2Item cmdlet에서 오류를 보고 합니다.|



## <a name="see-also"></a>참고 항목

* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure Storage와 함께 Azure PowerShell 사용](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [저장소 PowerShell cmdlet](/powershell/module/az.storage).

