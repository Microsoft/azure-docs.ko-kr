---
title: Azure Application Gateway의 SSL 정책 개요 | Microsoft Docs
description: Azure Application Gateway에 대해 자세히 알게 되면 SSL 정책을 구성할 수 있습니다.
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
origin.date: 08/03/2017
ms.date: 02/26/2019
ms.author: v-junlch
ms.openlocfilehash: 46a823e4e230656b53a93a97f195d0879fd08bf2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122237"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway SSL 정책 개요

Azure Application Gateway를 사용하여 SSL 인증서 관리를 중앙 집중화하고 백 엔드 서버 팜에서 암호화 및 암호 해독 오버헤드를 줄일 수 있습니다. 또한 이러한 중앙 집중식 SSL 처리를 통해 조직의 보안 요구 사항에 적합한 중앙 SSL 정책을 지정할 수 있습니다. 그러면 보안 지침과 권장되는 정보 및 규정 준수 요구 사항을 충족할 수 있습니다.

SSL 정책은 SSL 프로토콜 버전 및 SSL 핸드셰이크 도중 암호 그룹과 암호화 사용 순서를 제어합니다. Application Gateway는 SSL 정책을 제어하기 위한 두 가지 메커니즘을 제공합니다. 미리 정의된 정책이나 사용자 지정 정책을 사용할 수 있습니다.

## <a name="predefined-ssl-policy"></a>미리 정의된 SSL 정책

Application Gateway에는 세 가지 미리 정의된 보안 정책이 있습니다. 적절한 보안 수준을 얻을 수 있도록 이러한 정책을 사용하여 게이트웨이를 구성할 수 있습니다. 정책 이름은 구성된 연도 및 월로 주석 처리됩니다. 각 정책은 다른 SSL 프로토콜 버전 및 암호 그룹을 제공합니다. SSL 보안을 극대화되도록 최신 SSL 정책을 사용하는 것이 좋습니다.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|자산  |값  |
|---|---|
|이름     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|기본값| True(미리 정의된 정책이 지정되지 않은 경우) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|자산  |값  |
|   ---      |  ---       |
|이름     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|기본값| 거짓 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|자산  |값  |
|---|---|
|이름     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|기본값| 거짓 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>SSL 정책 사용자 지정

요구 사항에 대해 미리 정의된 SSL 정책을 구성해야 하는 경우 고유의 사용자 지정 SSL 정책을 정의해야 합니다. 사용자 지정 SSL 정책을 통해, 지원되는 암호 그룹 및 해당 우선 순위 순서를 지원하려면 최소 SSL 프로토콜 버전을 완전히 제어해야 합니다.
 
### <a name="ssl-protocol-versions"></a>SSL 프로토콜 버전

- SSL 2.0 및 3.0은 모든 Application Gateway에 대해 기본적으로 사용되지 않습니다. 이러한 프로토콜 버전을 구성할 수 없습니다.
- 사용자 지정 SSL 정책을 게이트웨이의 최소 SSL 프로토콜 버전으로 다음 세 가지 프로토콜 중 하나를 선택 하는 옵션이 있습니다. 게 TLSv1_0, TLSv1_1 및 TLSv1_2 중에서 선택 합니다.
- SSL 정책을 하나도 정의하지 않으면 3개 프로토콜(TLSv1_0, TLSv1_1, TLSv1_2) 모두 활성화됩니다.

### <a name="cipher-suites"></a>암호 그룹

Application Gateway는 사용자 지정 정책을 선택할 수 있는 다음과 같은 암호 그룹을 지원합니다. 암호 그룹의 순서는 SSL 협상 중 우선 순위 순서를 결정합니다.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>다음 단계

SSL 정책 구성에 대해 자세히 알아보려면 [Application Gateway에 SSL 정책 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.

<!-- Update_Description: wording update -->