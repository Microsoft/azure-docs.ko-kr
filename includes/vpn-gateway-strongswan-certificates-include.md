---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 01/16/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766190"
---
다음 컴퓨터 구성이 아래 단계에 사용되었습니다.

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |종속성| strongSwan |

#### <a name="1-install-strongswan"></a>1. strongSwan 설치

다음 명령을 사용하여 필요한 strongSwan 구성을 설치합니다.

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. 키 및 인증서 생성

CA 인증서를 생성합니다.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

CA 인증서를 base64 형식으로 인쇄합니다. Azure에서 지원하는 형식입니다. 나중에 이것을 P2S 구성의 일부로 Azure에 업로드합니다.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

사용자 인증서를 생성합니다.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

사용자 인증서를 포함하는 p12 번들을 생성합니다. 이 번들은 클라이언트 구성 파일을 사용할 때 다음 단계에서 사용됩니다.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```