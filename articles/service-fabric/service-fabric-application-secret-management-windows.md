---
title: Azure Service Fabric Windows 클러스터에서 암호화 인증서 설정 및 비밀 암호화 | Microsoft Docs
description: Windows 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 3d324c54d10433520a73f2bd836c26bd79f1b3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615263"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Windows 클러스터에서 암호화 인증서 설정 및 비밀 암호화
이 문서에서는 Windows 클러스터에서 암호화 인증서를 설정하고 이를 사용하여 비밀을 암호화하는 방법을 알아봅니다. Linux 클러스터의 경우 [Linux 클러스터에서 암호화 인증서 설정 및 비밀 암호화][secret-management-linux-specific-link]를 참조하세요.

[Azure Key Vault][key-vault-get-started]는 인증서에 대한 안전한 저장소 위치이자 Azure의 Service Fabric 클러스터에 설치된 인증서를 가져오는 수단으로 사용됩니다. Azure에 배포하지 않는 경우 서비스 패브릭 애플리케이션의 비밀을 관리하기 위해 주요 자격 증명 모음을 사용할 필요가 없습니다. 하지만 애플리케이션에서 비밀을 *사용* 하는 것은 클라우드 플랫폼에 구애를 받지 않으므로 그 어디에 호스트된 클러스터에도 애플리케이션을 배포할 수 있습니다. 

## <a name="obtain-a-data-encipherment-certificate"></a>데이터 암호화 인증서 가져오기
데이터 암호화 인증서는 서비스의 Settings.xml에 포함된 [매개 변수][parameters-link] 및 서비스의 ServiceManifest.xml에 있는 [환경 변수][environment-variables-link]를 암호화 및 해독하는 용도로만 엄격하게 사용됩니다. 인증 또는 암호화 텍스트의 서명에는 사용되지 않습니다. 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서 키 사용에는 데이터 암호화(10)가 포함되어야 하며, 서버 인증 또는 클라이언트 인증은 포함되면 안 됩니다. 
  
  예를 들어 PowerShell을 사용하여 자체 서명된 인증서를 만들 때 `KeyUsage` 플래그가 `DataEncipherment`로 설정되어야 합니다.
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>클러스터에 인증서 설치
클러스터의 각 노드에 이 인증서를 설치해야 합니다. 설정 지침은 [Azure Resource Manager를 사용하여 클러스터를 만드는 방법][service-fabric-cluster-creation-via-arm]을 참조하세요. 

## <a name="encrypt-application-secrets"></a>애플리케이션 비밀 암호화
다음 PowerShell 명령은 비밀을 암호화하는 데 사용됩니다. 이 명령은 값을 암호화합니다. 암호화 텍스트에 서명하지 **않습니다**. 클러스터에 설치된 것과 동일한 암호화 인증서를 사용하여 비밀 값의 암호 텍스트를 생성해야 합니다.

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

그 결과로 얻게 되는 Base-64 인코딩 문자열에는 암호 텍스트와 암호화에 사용된 인증서에 대한 정보가 모두 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계
[애플리케이션에서 암호화된 비밀을 지정하는][secret-management-specify-encrypted-secrets-link] 방법을 알아봅니다.

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
