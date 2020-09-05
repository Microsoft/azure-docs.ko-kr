---
title: '자습서: Azure Key Vault를 사용하여 Blob 암호화 및 해독'
titleSuffix: Azure Storage
description: Azure Key Vault를 통해 클라이언트 쪽 암호화를 사용하여 Blob을 암호화하고 해독하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 975952a0a084286bdbc19ce02c1192d076cf3600
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001402"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>자습서: Azure Key Vault를 사용하여 Blob 암호화 및 해독

이 자습서에서는 Azure Key Vault와 함께 클라이언트 쪽 스토리지 암호화를 사용하는 방법을 설명합니다. 이러한 기술을 사용하여 콘솔 애플리케이션에서 Blob를 암호화하고 해독하는 방법을 단계별로 안내 합니다.

**예상 완료 시간:** 20분

Azure Key Vault에 대한 개요는 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.

Azure Storage에 대한 클라이언트 쪽 암호화의 개요 정보는 [Microsoft Azure Storage에 대한 클라이언트 쪽 암호화 및 Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* Azure Storage 계정
* Visual Studio 2013 이상
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>클라이언트 쪽 암호화 개요

Azure Storage에 대한 클라이언트 쪽 암호화의 개요는 [Microsoft Storage에 대한 클라이언트 쪽 암호화 및 Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

클라이언트 쪽 암호화의 작동 원리에 대한 간단한 설명은 다음과 같습니다.

1. Azure Storage 클라이언트 SDK는 1회용 대칭 키인 콘텐츠 암호화 키(CEK)를 생성합니다.
2. 고객 데이터는 이 CEK를 사용하여 암호화됩니다.
3. 그런 다음 키 암호화 KEK를 사용하여 CEK를 래핑(암호화)합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 Azure Key Vault에 저장됩니다. Storage 클라이언트 자체는 KEK에 액세스할 수 없습니다. 단지 키 자격 증명 모음에서 제공되는 키 래핑 알고리즘을 호출할 뿐입니다. 고객은 원하는 경우 키 래핑/래핑 해제를 위해 사용자 지정 공급자를 사용하도록 선택할 수 있습니다.
4. 그런 다음 암호화된 데이터를 Azure Storage 서비스에 업로드합니다.

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault 설정

이 자습서를 계속하려면 자습서 [빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/secrets/quick-create-net.md)에 요약된 다음 단계를 수행해야 합니다.

* 키 자격 증명 모음을 만듭니다.
* 키 또는 암호를 키 자격 증명 모음에 추가합니다.
* Azure Active Directory에 애플리케이션을 등록합니다.
* 키 또는 암호를 사용하여 애플리케이션에 권한을 부여합니다.

Azure Active directory를 사용하여 애플리케이션을 등록하는 경우 생성된 ClientID 및 ClientSecret를 메모합니다.

키 자격 증명 모음에 두 키를 모두 만듭니다. 자습서의 나머지 부분에서는 이름 ContosoKeyVault 및 TestRSAKey1을 사용했다고 가정합니다.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>패키지 및 AppSettings를 사용하여 콘솔 애플리케이션 만들기

Visual Studio에서 새 콘솔 애플리케이션을 만듭니다.

패키지 관리자 콘솔에서 필요한 Nuget 패키지를 추가합니다.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

AppSettings를 App.Config에 추가합니다.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

다음 `using` 지시문을 추가하고 System.Configuration에 대한 참조를 프로젝트에 추가해야 합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>콘솔 애플리케이션에 토큰을 가져오는 메서드 추가

다음 메서드는 사용자 키 자격 증명 모음에 대한 액세스를 인증해야 하는 키 자격 증명 모음 클래스에 의해 사용됩니다.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>프로그램에서 Azure Storage 및 Key Vault 액세스

Main() 메서드에서 다음 코드를 추가합니다.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> 키 자격 증명 모음 개체 모델
> 
> 실제로 키 자격 증명 모음 개체 모델 두 가지에 유의해야 합니다. 하나는 REST API(KeyVault 네임스페이스)를 기반으로 하며 다른 하나는 클라이언트 쪽 암호화에 대한 확장입니다.
> 
> 키 자격 증명 모음 클라이언트는 REST API와 상호작용하며 JSON 웹 키 및 키 자격 증명 모음에 포함된 두 종류의 항목에 대한 암호를 인식합니다.
> 
> 키 자격 증명 모음 확장은 Azure Storage에 대한 클라이언트 쪽 암호화를 위해 명시적으로 생성된 것으로 보이는 클래스입니다. 이는 키(IKey) 및 키 확인 프로그램의 개념에 기초한 클래스를 포함하고 있습니다. IKey의 두 구현 RSAKey 및 SymmetricKey를 알아야 합니다. 현재 이들은 우연히 키 자격 증명 모음에 포함된 항목과 일치하지만, 이 시점에서는 독립된 클래스입니다(따라서 키 및 키 자격 증명 모음 클라이언트가 검색한 암호는 IKey를 구현하지 않음).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blob 암호화 및 업로드

Blob을 암호화하고 Azure Storage 계정에 업로드하는 다음과 같은 코드를 추가합니다. 사용되는 **ResolveKeyAsync** 메서드는 IKey를 반환합니다.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> BlobEncryptionPolicy 생성자를 살펴보면 키 및/또는 해결 프로그램을 사용할 수 있다는 것을 알 수 있습니다. 현재 해결 프로그램은 기본 키를 지원하지 않기 때문에 암호화에 사용할 수 없다는 데 유의해야 합니다.

## <a name="decrypt-blob-and-download"></a>Blob 암호 해독 및 다운로드

암호 해독은 실제로 확인 프로그램 클래스가 합리적인 경우입니다. 암호화에 사용되는 키의 ID는 해당 메타 데이터의 Blob과 연결되므로 키를 검색하고 키와 Blob 사이의 연결을 기억할 이유가 없습니다. 다만 키가 키 자격 증명 모음에 남아 있는지 확인하기만 하면 됩니다.   

RSA 키의 프라이빗 키는 키 자격 증명 모음에 남아 있으므로 해독을 실행하려면 CEK를 포함하고 있는 Blob 메타데이터의 암호화 키를 해독하기 위해 키 자격 증명 모음에 보냅니다.

방금 업로드한 Blob을 암호 해독하려면 다음을 추가합니다.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> 키 관리를 더 쉽게 해 주는 다른 종류의 두 확인 프로그램 AggregateKeyResolver 및 CachingKeyResolver가 있습니다.

## <a name="use-key-vault-secrets"></a>키 자격 증명 모음 암호 사용

암호는 기본적으로 대칭 키이므로 SymmetricKey 클래스를 통해 암호를 클라이언트 쪽 암호화와 함께 사용할 수 있습니다. 하지만 위에서 지적했듯이 키 자격 증명 모음의 암호는 SymmetricKey에 정확하게 매핑되지 않습니다. 여기서 이해해야 할 몇 가지 사항이 있습니다.

* SymmetricKey 키의 키는 고정 길이 128, 192, 256, 384 또는 512비트여야 합니다.
* SymmetricKey의 키는 Base64 인코딩이 되어 있어야 합니다.
* SymmetricKey로 사용할 키 자격 증명 모음 암호는 키 자격 증명 모음에 "애플리케이션/옥텟 스트림" 콘텐츠 형식을 가지고 있어야 합니다.

다음은 SymmetricKey로 사용할 수 있는 키 자격 증명 모음의 암호를 만드는 Powershell의 예제입니다.
$key는 하드 코드된 값이며 데모 전용입니다. 사용자 고유의 코드에서 이 키를 생성할 수 있습니다.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

사용자의 콘솔 애플리케이션에서는 이전과 동일한 호출을 사용하여 SymmetricKey로 이 비밀을 검색할 수 있습니다.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

이것으로 끝입니다. 마음껏 즐기세요!

## <a name="next-steps"></a>다음 단계

C#에서 Microsoft Azure Storage 사용에 대한 자세한 내용은 [.NET용 Microsoft Azure Storage Client Library](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하세요.

Blob REST API에 대한 자세한 내용은 [Blob 서비스 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)를 참조하세요.

Microsoft Azure Storage에 관한 최신 정보를 보려면 [Microsoft Azure Storage Team Blog](https://blogs.msdn.com/b/windowsazurestorage/)로 이동하세요.
