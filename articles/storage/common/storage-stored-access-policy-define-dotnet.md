---
title: .NET을 사용 하 여 저장 된 액세스 정책 만들기
titleSuffix: Azure Storage
description: Azure Storage 및 .NET을 사용 하 여 저장 된 액세스 정책을 만듭니다. 서버에서 서비스 수준 공유 액세스 서명에 대 한 제어 수준을 추가로 실행 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010752"
---
# <a name="create-a-stored-access-policy-with-net"></a>.NET을 사용 하 여 저장 된 액세스 정책 만들기

저장 된 액세스 정책은 서버 쪽의 서비스 수준 SAS (공유 액세스 서명)에 대 한 추가 제어 수준을 제공 합니다. 저장 된 액세스 정책을 정의 하는 것은 공유 액세스 서명을 그룹화 하 고 정책에 의해 바인딩되는 공유 액세스 서명에 대 한 추가 제한 사항을 제공 하는 데 사용 됩니다. 저장 된 액세스 정책을 사용 하 여 SAS의 시작 시간, 만료 시간 또는 사용 권한을 변경 하거나 발급 된 후에 취소할 수 있습니다.
  
다음 Azure Storage 리소스는 저장 된 액세스 정책을 지원 합니다.  
  
- Blob 컨테이너  
- 파일 공유  
- 큐  
- 테이블  
  
> [!NOTE]
> 컨테이너에 대 한 저장 된 액세스 정책은 컨테이너 자체 나 컨테이너에 포함 된 blob에 대 한 권한을 부여 하는 공유 액세스 서명과 연결 될 수 있습니다. 마찬가지로, 파일 공유에 대 한 저장 된 액세스 정책은 공유 액세스에 권한을 부여 하는 공유 액세스 서명 또는 공유 액세스에 포함 된 파일에 연결 될 수 있습니다.  
>
> 저장 된 액세스 정책은 서비스 SAS에만 지원 됩니다. 계정 SAS 또는 사용자 위임 SAS에 대해 저장 된 액세스 정책이 지원 되지 않습니다.  

저장 된 액세스 정책에 대 한 자세한 내용은 [저장 된 액세스 정책 정의](/rest/api/storageservices/define-stored-access-policy)를 참조 하세요.

## <a name="create-a-stored-access-policy"></a>저장된 액세스 정책 만들기

저장 된 액세스 정책을 만드는 기본 REST 작업은 [컨테이너 ACL로 설정](/rest/api/storageservices/set-container-acl)됩니다. 연결 문자열에서 계정 액세스 키를 사용 하 여 공유 키를 통해 저장 된 액세스 정책을 만들도록 작업에 권한을 부여 해야 합니다. Azure AD 자격 증명을 사용 하 여 **컨테이너 ACL 설정** 작업에 권한을 부여 하는 것은 지원 되지 않습니다. 자세한 내용은 [blob 및 큐 데이터 작업 호출에 대 한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조 하세요.

다음 코드 예제에서는 컨테이너에 저장 된 액세스 정책을 만듭니다. 액세스 정책을 사용하여 컨테이너나 해당 Blob에 서비스 SAS에 대한 제약 조건을 지정할 수 있습니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Azure Storage 용 .NET 클라이언트 라이브러리 버전 12의 컨테이너에 저장 된 액세스 정책을 만들려면 다음 방법 중 하나를 호출 합니다.

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

다음 예에서는 1 일 동안 적용 되 고 읽기/쓰기 권한을 부여 하는 저장 된 액세스 정책을 만듭니다.

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Azure Storage 용 .NET 클라이언트 라이브러리 버전 12의 컨테이너에 저장 된 액세스 정책을 만들려면 다음 방법 중 하나를 호출 합니다.

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

다음 예에서는 1 일 동안 적용 되 고 읽기, 쓰기 및 나열 권한을 부여 하는 저장 된 액세스 정책을 만듭니다.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>참고 항목

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](storage-sas-overview.md)
- [저장된 액세스 정책 정의](/rest/api/storageservices/define-stored-access-policy)
- [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)
