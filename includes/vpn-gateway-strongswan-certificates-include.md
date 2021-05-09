---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "70961609"
---
CA 인증서를 생성합니다.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

CA 인증서를 base64 형식으로 인쇄합니다. Azure에서 지원하는 형식입니다. 이 인증서를 [P2S 구성 단계](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)의 일부로 Azure에 업로드합니다.

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