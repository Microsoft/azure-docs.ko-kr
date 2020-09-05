---
title: 빠른 시작 - .NET용 Azure Key Vault 클라이언트 라이브러리(v4)
description: .NET 클라이언트 라이브러리(v4)를 사용하여 Azure Key Vault에서 암호를 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cf9c34dc2dde5c41edb7991451aebf5d90104196
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003255"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>빠른 시작: .NET용 Azure Key Vault 클라이언트 라이브러리(SDK v4)

.NET용 Azure Key Vault 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다.

Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. .NET 용 Key Vault 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

- 키 및 암호에 대한 보안과 제어를 강화합니다.
- 몇 분 안에 암호화 키를 만들고 가져옵니다.
- 클라우드 규모와 글로벌 중복성을 통해 대기 시간을 줄입니다.
- TLS/SSL 인증서 작업을 간소화하고 자동화합니다.
- FIPS 140-2 수준 2 유효성이 검사된 HSM을 사용합니다.

[API 참조 설명서](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/)

이 빠른 시작에서는 Windows 터미널(예: [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 또는 [Azure Cloud Shell](https://shell.azure.com/))에서 `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 및 Windows 명령을 실행한다고 가정합니다.

## <a name="setting-up"></a>설치

### <a name="create-new-net-console-app"></a>새 .NET 콘솔 앱 만들기

콘솔 창에서 `dotnet new` 명령을 사용하여 `key-vault-console-app`이라는 새 .NET 콘솔 앱을 만듭니다.

```console
dotnet new console -n key-vault-console-app
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>패키지 설치

콘솔 창에서 .NET용 Azure Key Vault 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

이 빠른 시작에서는 다음 패키지도 설치해야 합니다.

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>서비스 주체 만들기

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 서비스 주체에 부여

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>환경 변수 설정

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>개체 모델

.NET용 Azure Key Vault 클라이언트 라이브러리를 사용하면 키 및 관련 자산(예: 인증서 및 비밀)을 관리할 수 있습니다. 아래의 코드 샘플에서는 클라이언트를 만들고, 비밀을 설정, 검색 및 삭제하는 방법을 보여 줍니다.

전체 콘솔 앱은 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app 에서 사용할 수 있습니다.

## <a name="code-examples"></a>코드 예제

### <a name="add-directives"></a>지시문 추가

다음 지시문을 코드 위쪽에 추가합니다.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

Key Vault 인증을 받고 Key Vault 클라이언트를 생성하는 작업은 위에 나오는 [환경 변수 설정](#set-environmental-variables) 단계의 환경 변수에 따라 달라집니다. 키 자격 증명 모음의 이름은 "https://\<your-key-vault-name\>.vault.azure.net" 형식의 키 자격 증명 모음 URI로 확장됩니다. 아래 코드는 액세스 토큰을 검색하는 환경 변수를 읽는 키 자격 증명 모음에 대한 인증에 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet)을 사용하고 있습니다. 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>비밀 저장

이제 애플리케이션이 인증되었으므로 [client.SetSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)를 사용하여 Key Vault에 비밀을 넣을 수 있습니다. 이 작업을 위해 비밀의 이름이 필요하며, 이 샘플에서는 "mySecret"을 사용하고 있습니다.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 설정되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>비밀 검색

이제 [client.GetSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)를 사용하여 이전에 설정한 값을 검색할 수 있습니다.

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

이제 비밀이 `secret.Value`로 저장됩니다.

### <a name="delete-a-secret"></a>비밀 삭제

마지막으로 [client.DeleteSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)를 사용하여 Key Vault에서 비밀을 삭제해 보겠습니다.

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 삭제되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 키 자격 증명 모음 및 해당 리소스 그룹을 제거할 수 있습니다.

### <a name="delete-a-key-vault"></a>Key Vault 삭제
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Key Vault 제거
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>리소스 그룹 삭제

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>예제 코드

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고, 비밀을 저장하고, 해당 비밀을 검색했습니다. [GitHub의 전체 콘솔 앱](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)을 참조하세요.

Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토
