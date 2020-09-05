---
title: Azure Key Vault용 클라이언트 라이브러리 | Microsoft Docs
description: Azure Key Vault용 클라이언트 라이브러리
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263790"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault용 클라이언트 라이브러리

Azure Key Vault용 클라이언트 라이브러리를 사용하면 .NET, Python, Java 및 Javascript를 비롯한 다양한 언어에서 Key Vault 기능에 프로그래밍 방식으로 액세스할 수 있습니다.

## <a name="client-libraries-per-language-and-object"></a>언어 및 개체당 클라이언트 라이브러리

각 SDK에는 아래 표에 따라 비밀, 키 및 인증서에 대한 별도의 클라이언트 라이브러리가 있습니다.

| 언어 | 비밀 | 구성 | 인증서 |
|--|--|--|--|
| .NET | - [API 참조](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [빠른 시작](../secrets/quick-create-net.md) | - [API 참조](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 참조](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API 참조](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-python.md) |- [API 참조](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [빠른 시작](../keys/quick-create-python.md) | - [API 참조](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [빠른 시작](../certificates/quick-create-python.md) |
| Java | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-java.md) |- [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API 참조](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-node.md) |- [API 참조](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 참조](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
- [Azure Key Vault용 관리 ID](managed-identity.md)에 대해 자세히 알아보기