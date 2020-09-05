---
title: 자습서 - .NET에서 가상 머신과 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션을 구성합니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b4f832750f7a94a6a60cbb1d3ba630925dd4fff2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021734"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>자습서: .NET에서 가상 머신이 있는 Azure Key Vault 사용

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure Key Vault에서 정보를 읽는 콘솔 애플리케이션을 가져오는 방법을 알아봅니다. 애플리케이션은 가상 머신 관리 ID를 사용하여 Key Vault에 인증합니다. 

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 리소스 그룹을 만듭니다.
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀 추가
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure 가상 머신을 만듭니다.
> * Virtual Machine에 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
> * VM ID에 사용 권한을 할당합니다.

시작하기 전에 [Key Vault 기본 개념](basic-concepts.md)을 읽어보세요. 

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Windows, Mac 및 Linux:
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-resources-and-assign-permissions"></a>리소스 만들기 및 권한 할당

코딩을 시작하기 전에 몇 가지 리소스를 만들고, 키 자격 증명 모음에 비밀을 배치하고, 사용 권한을 할당해야 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>비밀로 키 자격 증명 모음 채우기

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
다음 방법 중 하나를 사용하여 Windows 또는 Linux 가상 머신을 만듭니다.

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당
[az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 명령으로 가상 머신에 대한 시스템 할당 ID를 만듭니다.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

다음 코드에 표시되는 시스템 할당 ID를 기록해 둡니다. 위 명령의 출력은 다음과 같습니다. 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID에 사용 권한을 할당합니다.
[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령으로 키 자격 증명 모음에 이전에 만든 ID 사용 권한을 할당합니다.

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>가상 머신에 로그인

가상 머신에 로그인하려면 [Azure Windows 가상 머신에 연결 및 로그인](../../virtual-machines/windows/connect-logon.md) 또는 [Azure Linux 가상 머신에 연결 및 로그인](../../virtual-machines/linux/login-using-aad.md)의 지침을 따르세요.

## <a name="set-up-the-console-app"></a>콘솔 앱 설정

`dotnet` 명령을 사용하여 콘솔 앱을 만들고 필요한 패키지를 설치합니다.

### <a name="install-net-core"></a>.NET Core 설치

.NET Core를 설치하려면 [.NET 다운로드](https://www.microsoft.com/net/download) 페이지로 이동합니다.

### <a name="create-and-run-a-sample-net-app"></a>샘플 .NET 앱 만들기 및 실행

명령 프롬프트를 엽니다.

다음 명령을 실행하여 "Hello World"를 콘솔에 출력할 수 있습니다.

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>패키지 설치

콘솔 창에서 .NET용 Azure Key Vault 비밀 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

이 빠른 시작에서는 다음 ID 패키지를 설치하여 Azure Key Vault에 인증해야 합니다.

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>콘솔 앱 편집

*Program.cs* 파일을 열고 다음 패키지를 추가합니다.

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

다음 줄을 추가하여, 키 자격 증명 모음의 `vaultUri`를 반영하도록 URI를 업데이트합니다. 아래 코드에서는 키 자격 증명 모음에 대한 인증을 위해 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet)을 사용하며, 이는 애플리케이션 관리 ID의 토큰을 사용하여 인증합니다. 또한 키 자격 증명 모음이 제한되는 경우 지수 백오프를 다시 시도에 사용합니다.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

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
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 가상 머신 및 Key Vault를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
