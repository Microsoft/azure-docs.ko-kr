---
title: 관리 id를 사용 하 여 데이터에 대 한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure 리소스에 관리 되는 id를 사용 하 여 Azure Vm, 함수 앱 등에서 실행 되는 응용 프로그램에서 blob 및 큐 데이터 액세스에 권한을 부여 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2087e5f8ec397123df504e9d30d351a0ba79b4a5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018759"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 id를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한 부여

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에 대 한 관리 되는 id는 azure Vm (가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 blob 및 큐 데이터 액세스 권한을 부여할 수 있습니다. Azure 리소스에 대 한 관리 되는 id를 Azure AD 인증과 함께 사용 하 여 클라우드에서 실행 되는 응용 프로그램에 자격 증명을 저장 하지 않을 수 있습니다.  

이 문서에서는 Azure 리소스에 대 한 관리 id를 사용 하 여 Azure VM에서 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다. 개발 환경에서 코드를 테스트 하는 방법에 대해서도 설명 합니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 blob 및 큐에 대 한 액세스 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.

## <a name="authenticate-with-the-azure-identity-library"></a>Azure Id 라이브러리를 사용 하 여 인증

Azure Id 클라이언트 라이브러리는 azure [SDK](https://github.com/Azure/azure-sdk)에 대 한 azure AD 토큰 인증 지원을 제공 합니다. .NET, Java, Python 및 JavaScript 용 Azure Storage 클라이언트 라이브러리의 최신 버전은 Azure Id 라이브러리와 통합 되어 Azure Storage 요청에 대 한 권한 부여를 위한 OAuth 2.0 토큰을 획득 하는 간단 하 고 안전한 방법을 제공 합니다.

Azure Id 클라이언트 라이브러리의 장점은 응용 프로그램이 개발 환경 또는 Azure에서 실행 되 고 있는지 여부를 인증 하는 데 동일한 코드를 사용할 수 있다는 것입니다. .NET 용 Azure Id 클라이언트 라이브러리는 보안 주체를 인증 합니다. 코드가 Azure에서 실행 되는 경우 보안 주체는 Azure 리소스에 대 한 관리 되는 id입니다. 개발 환경에서 관리 id가 존재 하지 않으므로 클라이언트 라이브러리는 테스트 목적으로 사용자 또는 서비스 주체를 인증 합니다.

인증 후에 Azure Id 클라이언트 라이브러리는 토큰 자격 증명을 가져옵니다. 그런 다음이 토큰 자격 증명은 Azure Storage에 대해 작업을 수행 하기 위해 만드는 서비스 클라이언트 개체에 캡슐화 됩니다. 라이브러리는 적절 한 토큰 자격 증명을 가져와이를 원활 하 게 처리 합니다.

.NET 용 Azure Id 클라이언트 라이브러리에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)를 참조 하세요. Azure Id 클라이언트 라이브러리에 대 한 참조 설명서는 [Azure Id 네임 스페이스](/dotnet/api/azure.identity)를 참조 하세요.

### <a name="assign-azure-roles-for-access-to-data"></a>데이터 액세스를 위한 Azure 역할 할당

Azure AD 보안 주체가 blob 또는 큐 데이터에 액세스 하려는 경우 해당 보안 주체에 게 리소스에 대 한 권한이 있어야 합니다. 보안 주체가 Azure에서 관리 되는 id이 든, 개발 환경에서 코드를 실행 하는 Azure AD 사용자 계정 인지 여부에 관계 없이 보안 주체는 Azure Storage의 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 Azure 역할에 할당 되어야 합니다. RBAC를 통해 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 사용 하 여 azure blob 및 큐에](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)대 한 액세스 권한 부여에서 **액세스 권한에 대 한 azure 역할 할당** 섹션을 참조 하세요.

### <a name="authenticate-the-user-in-the-development-environment"></a>개발 환경에서 사용자 인증

개발 환경에서 코드가 실행 되는 경우 인증을 자동으로 처리 하거나 사용 중인 도구에 따라 브라우저 로그인이 필요할 수 있습니다. 예를 들어 Microsoft Visual Studio는 SSO (Single Sign-On)를 지원 하므로 활성 Azure AD 사용자 계정이 인증에 자동으로 사용 됩니다. SSO에 대 한 자세한 내용은 [응용 프로그램에 대 한 Single sign-on](../../active-directory/manage-apps/what-is-single-sign-on.md)을 참조 하세요.

다른 개발 도구는 웹 브라우저를 통해 로그인 하 라는 메시지를 표시할 수 있습니다.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>개발 환경에서 서비스 주체 인증

개발 환경에서 웹 브라우저를 통해 Single Sign-On 또는 로그인을 지원 하지 않는 경우 서비스 주체를 사용 하 여 개발 환경에서 인증할 수 있습니다.

#### <a name="create-the-service-principal"></a>서비스 주체 만들기

Azure CLI를 사용 하 여 서비스 주체를 만들고 Azure 역할을 할당 하려면 [az ad sp create-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 호출 합니다. 새 서비스 사용자에 게 할당할 Azure Storage 데이터 액세스 역할을 제공 합니다. 또한 역할 할당에 대 한 범위를 제공 합니다. Azure Storage에 대해 제공 되는 기본 제공 역할에 대 한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

서비스 주체에 역할을 할당할 수 있는 권한이 없는 경우 계정 소유자 또는 관리자에 게 역할 할당을 수행 하도록 요청 해야 할 수 있습니다.

다음 예에서는 Azure CLI를 사용 하 여 새 서비스 주체를 만들고 계정 범위를 사용 하 여 **저장소 Blob 데이터 읽기 권한자** 역할을 할당 합니다.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

이 `az ad sp create-for-rbac` 명령은 JSON 형식의 서비스 사용자 속성 목록을 반환 합니다. 이러한 값을 복사 하 여 다음 단계에서 필요한 환경 변수를 만들 수 있습니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="set-environment-variables"></a>환경 변수 설정

Azure Id 클라이언트 라이브러리는 런타임에 세 가지 환경 변수에서 값을 읽어서 서비스 사용자를 인증 합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명 합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|서비스 사용자의 앱 ID
|`AZURE_TENANT_ID`|서비스 주체의 Azure AD 테 넌 트 ID
|`AZURE_CLIENT_SECRET`|서비스 사용자에 대해 생성 된 암호입니다.

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용 하는 경우 새 환경 변수를 등록 하기 위해 개발 환경을 다시 시작 해야 할 수 있습니다.

자세한 내용은 [포털에서 Azure 앱에 대 한 Id 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

`using`Azure id 및 Azure Storage 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

코드에서 Azure Storage 요청에 권한을 부여 하는 데 사용할 수 있는 토큰 자격 증명을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 다음 코드 예제에서는 인증 된 토큰 자격 증명을 가져와서이를 사용 하 여 서비스 클라이언트 개체를 만든 다음 서비스 클라이언트를 사용 하 여 새 blob을 업로드 하는 방법을 보여 줍니다.

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD를 사용 하 여 blob 또는 큐 데이터에 대 한 요청에 권한을 부여 하려면 해당 요청에 대해 HTTPS를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [RBAC를 사용 하 여 저장소 데이터에 대 한 액세스 권한을 관리](storage-auth-aad-rbac.md)합니다.
- [저장소 응용 프로그램과 함께 AZURE AD를 사용](storage-auth-aad-app.md)합니다.
- [AZURE AD 자격 증명을 사용 하 여 Azure CLI 또는 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스](authorize-active-directory-powershell.md)합니다.
