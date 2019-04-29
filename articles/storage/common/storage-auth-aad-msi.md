---
title: Blob 및 큐 관리 id 사용 하 여 Azure 리소스-Azure Storage에 대 한 액세스 인증 | Microsoft Docs
description: Azure Blob 및 큐 저장소는 Azure 리소스에 대 한 관리 되는 id 사용 하 여 Azure Active Directory 인증을 지원 합니다. Azure 리소스의 관리 ID를 사용하여 Azure Virtual Machine, 기능 앱, Virtual Machine Scale Set 및 기타 기능에서 실행 중인 애플리케이션의 Blob 및 큐에 대한 액세스를 인증할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9209161f9c9e34320b1388e0e1edbd5069e73727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438293"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 blob 및 Azure Active Directory 및 관리 되는 id를 사용 하 여 큐에 대 한 액세스 인증

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에는 blob에 대 한 액세스 권한을 부여할 수 있습니다 및 큐 데이터를 Azure virtual machines (Vm), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 중인 응용 프로그램에서 Azure AD 자격 증명을 사용 하 여 identities를 관리 합니다. Azure AD 인증와 함께 Azure 리소스에 대 한 관리 되는 id를 사용 하 여 클라우드에서 실행 되는 응용 프로그램을 사용 하 여 자격 증명 저장을 방지할 수 있습니다.  

이 아티클에서 Azure VM에서 관리 되는 id 가진 blob 또는 큐 데이터에 액세스 권한을 부여 하는 방법에 설명 합니다. 

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

VM에서 blob 및 큐에 대 한 액세스 권한을 부여 하려면 Azure 리소스에 대 한 관리 되는 id를 사용 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id을 먼저 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>관리 되는 Azure AD id에 권한 부여

요청 Blob 또는 큐 서비스에 Azure Storage 응용 프로그램에서 관리 되는 id를 인증 하려면 먼저 해당 관리 되는 id에 대 한 역할 기반 액세스 제어 (RBAC) 설정을 구성 합니다. Azure Storage blob 및 큐 데이터에 대 한 사용 권한을 포함 하는 RBAC 역할을 정의 합니다. RBAC 역할은 관리 되는 id에 할당 하는 경우 관리 되는 id에는 적절 한 범위에서 데이터를 blob 또는 큐에 해당 권한이 부여 됩니다. 

RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 다음 문서 중 하나를 참조 하세요.

- [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI를 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell을 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>관리 되는 id 액세스 토큰을 사용 하 여 권한 부여

관리 되는 id 사용 하 여 Blob 및 Queue storage에 대 한 요청에 권한을 부여 하려면 응용 프로그램 또는 스크립트는 OAuth 토큰을 획득 해야 합니다. 합니다 [Microsoft Azure App 인증](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) .NET (미리 보기) 용 클라이언트 라이브러리 획득 및 갱신 코드에서 토큰의 프로세스를 간소화 합니다.

앱 인증 클라이언트 라이브러리는 자동으로 인증을 관리합니다. 라이브러리는 개발자의 자격 증명을 사용 하 여 로컬 개발 동안 인증. 로컬 개발 동안 개발자 자격 증명을 사용하는 것이 Azure AD 자격 증명을 만들거나 개발자 간에 자격 증명을 공유할 필요가 없으므로 더 안전합니다. 나중에 Azure에는 솔루션을 배포한 경우 응용 프로그램 자격 증명을 사용 하 여 라이브러리를 자동으로 전환 합니다.

Azure Storage 응용 프로그램에서 앱 인증 라이브러리를 사용 하려면에서 최신 미리 보기 패키지를 설치 [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), 최신 버전의 뿐만 아니라 합니다 [.NET 용 Azure Storage 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)합니다. 다음을 추가 합니다 **를 사용 하 여** 문을 코드에:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

앱 인증 라이브러리를 제공 합니다 **AzureServiceTokenProvider** 클래스입니다. 이 클래스의 인스턴스는 토큰을 가져오고 다음 만료 되기 전에 토큰을 갱신 하는 콜백에 전달할 수 있습니다.

다음 예제에서는 새 blob을 사용 하 고 동일한 토큰을 사용 하 여 blob을 읽는 토큰을 가져옵니다.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function 
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token, 
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider, 
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), 
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

콜백 메서드는 토큰의 만료 시간을 확인 하 고 필요에 따라 갱신:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

앱 인증 라이브러리에 대 한 자세한 내용은 참조 하세요. [.NET을 사용 하 여 Azure Key Vault에 서비스 간 인증](../../key-vault/service-to-service-authentication.md)합니다. 

액세스 토큰을 획득 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하 여 액세스 토큰을 획득 하는 방법을](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)합니다.

> [!NOTE]
> Azure AD 사용 하 여 데이터를 blob 또는 큐에 대 한 요청에 권한을 부여 하려면 해당 요청에 대 한 HTTPS를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure storage에 대 한 RBAC 역할에 대 한 자세한 내용은 참조 하세요 [RBAC 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.
- 저장소 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure AD 자격 증명을 사용 하 여 Azure CLI 및 PowerShell 명령을 실행 하는 방법에 알아보려면 참조 [blob 또는 큐 데이터에 액세스 하려면 Azure AD 자격 증명을 사용 하 여 실행 하는 Azure CLI 또는 PowerShell 명령을](storage-auth-aad-script.md)합니다.