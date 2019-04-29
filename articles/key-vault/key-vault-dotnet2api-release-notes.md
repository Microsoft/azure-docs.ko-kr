---
title: Key Vault .NET 2.x API 릴리스 정보 | Microsoft Docs
description: .NET 개발자는 Azure Key Vault에 대한 코드에 이 API를 사용합니다.
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 3d2543ea2b2ee6261b04396f92d5f3583a89ffb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640704"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - 릴리스 정보 및 마이그레이션 가이드
다음 정보는 C# 및 .NET용 Azure Key Vault 라이브러리 2.0 버전으로 마이그레이션하는 데 도움이됩니다.  이전 버전용으로 작성된 앱은 최신 버전을 지원하도록 업데이트해야 합니다.  이러한 변경 사항은 **Key Vault 인증서**와 같이 새롭고 향상된 기능을 완벽하게 지원하는 데 필요합니다.

## <a name="key-vault-certificates"></a>Key Vault 인증서

Key Vault 인증서는 x509 인증서를 관리하고 다음 동작을 지원합니다.  

* Key Vault 생성 프로세스를 통해 인증서를 만들거나 기존 인증서를 가져옵니다. 여기에는 자체 서명된 인증서 및 CA(인증 기관) 생성 인증서가 모두 포함됩니다.
* 개인 키 자료를 사용하여 상호 작용 없이 x509 인증서 저장소를 안전하게 저장하고 관리합니다.  
* 인증서 수명 주기를 관리하기 위해 Key Vault에게 지시하는 정책을 정의합니다.  
* 만료 경고 및 갱신 알림과 같은 수명 주기 이벤트에 대한 연락처 정보를 제공합니다.  
* 선택한 발급자(Key Vault 파트너 X509 인증서 공급자 및 인증 기관)의 인증서를 자동으로 갱신합니다.* 파트너가 아닌 대체 공급자 및 인증 기관의 인증서도 지원하지만 자동 갱신은 지원하지 않습니다.  

## <a name="net-support"></a>.NET 지원

* **.NET 4.0**은 Azure Key Vault .NET 라이브러리의 2.0 버전에서 지원되지 않습니다.
* **.NET Framework 4.5.2**는 Azure Key Vault .NET 라이브러리의 2.0 버전에서 지원되지 않습니다.
* **.NET Standard 1.4**는 Azure Key Vault .NET 라이브러리의 2.0 버전에서 지원되지 않습니다.

## <a name="namespaces"></a>네임스페이스

* **모델**의 네임스페이스는 **Microsoft.Azure.KeyVault**에서 **Microsoft.Azure.KeyVault.Models**로 변경됩니다.
* **Microsoft.Azure.KeyVault.Internal** 네임스페이스는 삭제됩니다.
* 다음 Azure SDK 종속성 네임스페이스가 있습니다. 

    - **Hyak.Common**은 이제 **Microsoft.Rest**입니다.
    - **Hyak.Common.Internals**는 이제 **Microsoft.Rest.Serialization**입니다.

## <a name="type-changes"></a>유형 변경

* *비밀*을 *SecretBundle*로 변경
* *사전*을 *IDictionary*로 변경
* *목록<T>, string []* 을 *IList<T>* 로 변경
* *NextList*를 *NextPageLink*로 변경

## <a name="return-types"></a>반환 유형

* 이제 **KeyList** 및 **SecretList**는 *ListKeysResponseMessage* 대신 *IPage<T>* 를 반환합니다.
* 생성된 **BackupKeyAsync**는 *값*(백업 blob)을 포함하는 *BackupKeyResult*를 반환합니다. 이전에는 메서드가 래핑되고 해당 값만 반환되었습니다.

## <a name="exceptions"></a>예외

* *KeyVaultClientException*를 *KeyVaultErrorException*로 변경합니다.
* 서비스 오류는 *exception.Error*에서 *exception.Body.Error.Message*로 변경되었습니다.
* **[JsonExtensionData]** 에 대한 오류 메시지에서 추가 정보를 제거합니다.

## <a name="constructors"></a>생성자

* *HttpClient*를 생성자 인수로 적용하는 대신 생성자는 *HttpClientHandler* 또는 *DelegatingHandler*만 허용합니다.

## <a name="downloaded-packages"></a>다운로드한 패키지

클라이언트가 Key Vault 종속성을 처리할 때 다음 패키지가 다운로드됩니다.

### <a name="previous-package-list"></a>이전 패키지 목록

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>현재 패키지 목록

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>클래스 변경

* **UnixEpoch** 클래스가 제거되었습니다.
* **Base64UrlConverter** 클래스 이름을 **Base64UrlJsonConverter**로 변경합니다.

## <a name="other-changes"></a>기타 변경 내용

* 일시적 오류가 발생한 경우 KV 작업 재시도 정책의 구성에 대한 지원은 이 버전의 API에 추가되었습니다.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* *자격 증명 모음*을 반환하는 작업의 경우 반환 형식은 **자격 증명 모음** 속성을 포함하는 클래스였습니다. 반환 유형은 이제 *자격 증명 모음*입니다.
* *PermissionsToKeys* 및 *PermissionsToSecrets*는 이제 *Permissions.Keys* 및 *Permissions.Secrets*입니다.
* 특정 반환 형식 변경 내용은 제어 평면에도 적용됩니다.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 패키지는 암호화 작업에 대해 **Microsoft.Azure.KeyVault.Extensions** 및 **Microsoft.Azure.KeyVault.Cryptography**로 나눠집니다.

