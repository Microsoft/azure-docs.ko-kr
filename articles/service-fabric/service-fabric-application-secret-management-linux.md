---
title: Azure Service Fabric Linux 클러스터에서 암호화 인증서 설정 및 비밀 암호화 | Microsoft Docs
description: Linux 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126990"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Linux 클러스터에서 암호화 인증서 설정 및 비밀 암호화
이 문서에서는 Linux 클러스터에서 암호화 인증서를 설정하고 이를 사용하여 비밀을 암호화하는 방법을 알아봅니다. Windows 클러스터의 경우 [Windows 클러스터에서 암호화 인증서 설정 및 비밀 암호화][secret-management-windows-specific-link]를 참조하세요.

## <a name="obtain-a-data-encipherment-certificate"></a>데이터 암호화 인증서 가져오기
데이터 암호화 인증서는 서비스의 Settings.xml에 포함된 [매개 변수][parameters-link] 및 서비스의 ServiceManifest.xml에 있는 [환경 변수][environment-variables-link]를 암호화 및 해독하는 용도로만 엄격하게 사용됩니다. 인증 또는 암호화 텍스트의 서명에는 사용되지 않습니다. 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 인증서 키 사용에는 데이터 암호화(10)가 포함되어야 하며, 서버 인증 또는 클라이언트 인증은 포함되면 안 됩니다.

  예를 들어, OpenSSL을 사용하여 필요한 인증서를 생성하려면 다음 명령을 사용할 수 있습니다.
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>클러스터에 인증서 설치
`/var/lib/sfcerts` 아래에 있는 클러스터의 각 노드에 인증서를 설치해야 합니다. 서비스가 실행 중인 사용자 계정(기본적으로 sfuser)에 설치된 인증서(즉, 현재 예제의 경우 `/var/lib/sfcerts/TestCert.pem`)에 대한 **읽기 권한이 있어야 합니다**.

## <a name="encrypt-secrets"></a>비밀 암호화
다음 코드 조각은 비밀을 암호화하는 데 사용할 수 있습니다. 이 코드 조각은 값만 암호화하며, 암호화 텍스트에 서명하지 **않습니다**. 비밀 값의 암호 텍스트를 생성하려면 클러스터에 설치된 것과 동일한 암호화 인증서를 **사용해야 합니다**.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
그 결과 encrypted.txt에 대한 기본 64로 인코딩된 문자열 출력에는 비밀 암호 텍스트와 암호화에 사용된 인증서에 대한 정보가 모두 포함되어 있습니다. OpenSSL을 사용하여 암호를 해독하여 유효성을 확인할 수 있습니다.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>다음 단계
[애플리케이션에서 암호화된 비밀을 지정하는][secret-management-specify-encrypted-secrets-link] 방법을 알아봅니다.

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
