---
title: Linux용 OpenSSL을 구성하는 방법
titleSuffix: Azure Cognitive Services
description: Linux 용 OpenSSL을 구성 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119989"
---
# <a name="configure-openssl-for-linux"></a>Linux용 OpenSSL 구성

1\.9.0 이전에 Speech SDK 버전을 사용 하는 경우 [OpenSSL](https://www.openssl.org) 는 호스트 시스템 버전으로 동적으로 구성 됩니다. 이후 버전의 Speech SDK에서는 OpenSSL (버전 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html))이 speech sdk의 핵심 라이브러리에 정적으로 연결 되어 있습니다.

연결을 보장 하려면 OpenSSL 인증서가 시스템에 설치 되어 있는지 확인 합니다. 다음 명령을 실행 합니다.
```bash
openssl version -d
```

Ubuntu/Debian 기반 시스템의 출력은 다음과 같아야 합니다.
```
OPENSSLDIR: "/usr/lib/ssl"
```

OPENSSLDIR 아래에 `certs` 하위 디렉터리가 있는지 확인 합니다. 위의 예제에서는 `/usr/lib/ssl/certs`합니다.

* `/usr/lib/ssl/certs` 있고 개별 인증서 파일 (`.crt` 또는 `.pem` 확장 포함)이 많은 경우 추가 작업이 필요 하지 않습니다.

* OPENSSLDIR이 `/usr/lib/ssl` 아닌 다른 경우 또는 여러 개별 파일 대신 단일 인증서 번들 파일이 있는 경우 인증서를 찾을 수 있는 위치를 나타내는 적절 한 SSL 환경 변수를 설정 해야 합니다.

## <a name="examples"></a>예

- OPENSSLDIR이 `/opt/ssl`됩니다. `.crt` 또는 `.pem` 파일이 많은 `certs` 하위 디렉터리가 있습니다.
Speech SDK를 사용 하는 프로그램을 실행 하기 전에 `/opt/ssl/certs`를 가리키도록 환경 변수 `SSL_CERT_DIR`를 설정 합니다. 다음은 그 예입니다.
```bash
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR이 `/etc/pki/tls`됩니다. `ca-bundle.pem` 또는 `ca-bundle.crt`와 같은 인증서 번들 파일이 있습니다.
Speech SDK를 사용 하는 프로그램을 실행 하기 전에 `/etc/pki/tls/ca-bundle.pem`를 가리키도록 환경 변수 `SSL_CERT_FILE`를 설정 합니다. 다음은 그 예입니다.
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)
