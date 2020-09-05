---
title: Azure Data Lake Storage Gen2에 대 한 Acl을 재귀적으로 설정 Microsoft Docs
description: 부모 디렉터리의 기존 자식 항목에 대 한 액세스 제어 목록을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 71c470bd1bb71b55d6643ac6305a054f1c934948
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229042"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 대 한 Acl (액세스 제어 목록)을 재귀적으로 설정

부모 디렉터리 아래에 생성 된 새 자식 항목에는 ACL 상속이 이미 사용할 수 있습니다. 이제 각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 대해 Acl을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다.

> [!NOTE]
> 액세스 목록을 재귀적으로 설정 하는 기능은 공개 미리 보기 상태 이며 모든 지역에서 사용할 수 있습니다.  

[라이브러리](#libraries)  |  [샘플](#code-samples)  |  모범 [사례](#best-practice-guidelines)  |  [사용자 의견 제공](#provide-feedback)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](data-lake-storage-quickstart-create-account.md)을 수행합니다.

- 재귀 ACL 프로세스를 실행할 수 있는 올바른 권한입니다. 올바른 사용 권한에는 다음 중 하나가 포함 됩니다. 

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 프로 비전 된 AD (Azure Active Directory) [보안 주체](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 입니다.   

  - 재귀 ACL 프로세스를 적용 하려는 대상 컨테이너 또는 디렉터리를 소유 하는 사용자입니다. 여기에는 대상 컨테이너 또는 디렉터리의 모든 자식 항목이 포함 됩니다. 

- 디렉터리 및 파일에 Acl을 적용 하는 방법을 이해 합니다. [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조 하세요. 

PowerShell, .NET SDK 및 Python SDK에 대 한 설치 지침을 보려면이 문서의 **프로젝트 설정** 섹션을 참조 하세요.

## <a name="set-up-your-project"></a>프로젝트 설정

필요한 라이브러리를 설치 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET framework가 설치 되어 있는지 확인 합니다. [다운로드 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)를 참조 하세요.
 
2. 다음 명령을 사용 하 여 설치한 PowerShell 버전이 이상 인지 확인 `5.1` 합니다.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 를 참조 하세요.
    
3. PowershellGet 모듈의 최신 버전을 설치 합니다.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. PowerShell 콘솔을 닫았다가 다시 엽니다.

5. 설치 **Az. Storage** preview module.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) 를 참조 하세요.

### <a name="net"></a>[.NET](#tab/dotnet)

1. 명령 창을 엽니다 (예: Windows PowerShell).

2. 프로젝트 디렉터리에서 명령을 사용 하 여 DataLake 미리 보기 패키지를 설치 합니다. `dotnet add package`

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.3.0-dev.20200811.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 이러한 using 문을 코드 파일의 맨 위에 추가 합니다.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. [Python 용 Azure Data Lake Storage 클라이언트 라이브러리](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)를 다운로드 합니다.

2. [Pip](https://pypi.org/project/pip/)를 사용 하 여 다운로드 한 라이브러리를 설치 합니다.

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>계정에 연결

AD (Azure Active Directory)를 사용 하거나 계정 키를 사용 하 여 연결할 수 있습니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Windows PowerShell 명령 창을 열고 명령을 사용 하 여 Azure 구독에 로그인 하 `Connect-AzAccount` 고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 디렉터리를 만들고 관리 하려는 저장소 계정의 구독으로 설정 합니다. 이 예에서는 `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

그런 다음 명령을 통해 저장소 계정에 대 한 권한 부여를 가져오는 방법을 선택 합니다. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>옵션 1: Azure Active Directory (AD)를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 사용자 계정에 적절 한 RBAC (역할 기반 액세스 제어) 할당 및 ACL 권한이 있는지 확인 합니다. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

다음 표에서는 지원 되는 각 역할과 해당 ACL 설정 기능을 보여 줍니다.

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>옵션 2: 저장소 계정 키를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 RBAC 또는 ACL 사용 권한을 확인 하지 않습니다.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[.Net 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

패키지를 설치한 후에는이 using 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure.Identity;
```

클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 가져옵니다. 이렇게 하려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조 하세요. 해당 프로세스의 일부로 보안 주체에 다음의 [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 역할 중 하나를 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

이 방법은 계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> 더 많은 예제는 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조 하세요.

### <a name="python"></a>[Python](#tab/python)

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[Python 용 azure identity client 라이브러리](https://pypi.org/project/azure-identity/) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요. 해당 프로세스의 일부로 보안 주체에 다음의 [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 역할 중 하나를 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 더 많은 예제는 [Python 용 Azure id 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조 하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key`자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

---

## <a name="set-an-acl-recursively"></a>재귀적으로 ACL 설정

Acl을 재귀적으로 설정할 수 있습니다.  

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlet을 사용 하 여 재귀적으로 ACL을 설정 `Set-AzDataLakeGen2AclRecursive` 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient SetAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL을 재귀적으로 설정 합니다. 이 메서드를 [Pathaccesscontrolitems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1) 에 전달 합니다. 각 [Pathaccesscontrolitems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 는 ACL 항목을 정의 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

**Set_access_control_recursive DataLakeDirectoryClient** 메서드를 호출 하 여 ACL을 재귀적으로 설정 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>재귀적으로 ACL 업데이트

기존 ACL을 재귀적으로 업데이트할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 ACL을 재귀적으로 업데이트 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient UpdateAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

**Update_access_control_recursive DataLakeDirectoryClient** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>ACL 항목을 재귀적으로 제거

하나 이상의 ACL 항목을 재귀적으로 제거할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 ACL 항목을 제거 합니다. 

이 예에서는 컨테이너의 루트 디렉터리에서 ACL 항목을 제거 합니다.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient RemoveAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL 항목을 제거 합니다. 

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

**Remove_access_control_recursive DataLakeDirectoryClient** 메서드를 호출 하 여 ACL 항목을 제거 합니다. 

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>오류에서 복구

런타임 또는 사용 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작 합니다. 보안 주체에 수정 되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 없으면 권한 오류가 발생할 수 있습니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 오류 지점에서 프로세스를 다시 시작 하거나 프로세스를 처음부터 다시 시작 하도록 선택 합니다. 처음부터 다시 시작 하는 것을 선호 하는 경우 연속 토큰을 사용할 필요가 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

결과를 변수에 반환 합니다. 형식이 지정 된 테이블에 실패 한 항목을 파이프 합니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

테이블의 출력에 따라 모든 사용 권한 오류를 수정한 다음 연속 토큰을 사용 하 여 실행을 다시 시작할 수 있습니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 ``null`` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, null, continuationToken: continuationToken);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 ``None`` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>리소스

이 섹션에는 라이브러리 및 코드 샘플에 대 한 링크가 포함 되어 있습니다.

#### <a name="libraries"></a>라이브러리

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>코드 샘플

- PowerShell: [추가 정보](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [샘플](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [추가 정보](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [추가 정보](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [샘플](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>모범 사례 지침

이 섹션에서는 Acl을 재귀적으로 설정 하는 몇 가지 모범 사례 지침을 제공 합니다. 

#### <a name="handling-runtime-errors"></a>런타임 오류 처리

런타임 오류는 여러 가지 이유로 발생할 수 있습니다 (예: 중단 또는 클라이언트 연결 문제). 런타임 오류가 발생 하는 경우 재귀 ACL 프로세스를 다시 시작 합니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="handling-permission-errors-403"></a>권한 오류 처리 (403)

재귀 ACL 프로세스를 실행 하는 동안 액세스 제어 예외가 발생 하는 경우 AD [보안 주체](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 에 게 디렉터리 계층 구조에 있는 하나 이상의 자식 항목에 ACL을 적용 하는 데 충분 한 권한이 없을 수 있습니다. 권한 오류가 발생 하면 프로세스가 중지 되 고 연속 토큰이 제공 됩니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 나머지 데이터 집합을 처리 합니다. 이미 성공적으로 처리 된 디렉터리와 파일은 다시 처리 하지 않아도 됩니다. 재귀 ACL 프로세스를 다시 시작 하도록 선택할 수도 있습니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="credentials"></a>자격 증명 

대상 저장소 계정 또는 컨테이너의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 Azure AD 보안 주체를 프로 비전 하는 것이 좋습니다. 

#### <a name="performance"></a>성능 

대기 시간을 줄이려면 storage 계정과 동일한 지역에 있는 Azure VM (가상 머신)에서 재귀 ACL 프로세스를 실행 하는 것이 좋습니다. 

#### <a name="acl-limits"></a>ACL 제한

디렉터리 또는 파일에 적용할 수 있는 최대 Acl 수는 32 액세스 Acl 및 32 기본 Acl입니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요.

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>사용자 의견을 제공 하거나 문제를 보고 합니다.

에서 사용자 의견을 제공 하거나 문제를 보고할 수 있습니다  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [알려진 문제](data-lake-storage-known-issues.md)


