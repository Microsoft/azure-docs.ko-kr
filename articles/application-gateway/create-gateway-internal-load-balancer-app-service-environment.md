---
title: Azure에서 Application Gateway 문제 해결 – ILB ASE | Microsoft Docs
description: Azure에서 App Service Environment와 함께 내부 Load Balancer를 사용하여 애플리케이션 게이트웨이의 문제를 해결하는 방법을 알아봅니다.
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: baed2b23a321c53a614303d3085fbb3a4bf6ad0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831098"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>백 엔드 서버 인증서는 App Service Environment와 함께 내부 Load Balancer를 사용하는 애플리케이션 게이트웨이에 대한 허용 목록에 없습니다.

이 문서에서는 다음 문제를 해결합니다. Azure에서 엔드투엔드 SSL을 사용하는 경우 백 엔드에서 ASE(App Service Environment)와 함께 ILB(내부 Load Balancer)를 사용하여 애플리케이션 게이트웨이를 만들 때 인증서가 허용 목록에 없습니다.

## <a name="symptoms"></a>증상

백 엔드에서 ASE와 함께 ILB를 사용하여 애플리케이션 게이트웨이를 만들 때 백 엔드 서버는 비정상이 될 수 있습니다. 이 문제는 애플리케이션 게이트웨이의 인증 인증서가 백 엔드 서버에서 구성된 인증서와 일치하지 않는 경우 발생합니다. 예제로 다음 시나리오를 참조하세요.

**Application Gateway 구성:**

- **수신기:** 다중 사이트
- **Port:** 443
- **호스트 이름:** test.appgwtestase.com
- **SSL 인증서:** CN=test.appgwtestase.com
- **백 엔드 풀:** IP 주소 또는 FQDN
- **IP 주소:**: 10.1.5.11
- **HTTP 설정:** HTTPS
- **포트:**: 443
- **사용자 지정 프로브:** 호스트 이름 - test.appgwtestase.com
- **인증 인증서:** test.appgwtestase.com의 .cer
- **백 엔드 상태:** 비정상 - 백 엔드 서버 인증서는 Application Gateway에서 허용 목록에 없습니다.

**ASE 구성:**

- **ILB IP:** 10.1.5.11
- **도메인 이름:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL 바인딩:** SNI SSL – CN=test.appgwtestase.com

애플리케이션 게이트웨이에 액세스하는 경우 백 엔드 서버가 비정상이므로 다음과 같은 오류 메시지를 받습니다.

**502 - 웹 서버에서 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 수신했습니다.**

## <a name="solution"></a>해결 방법

HTTPS 웹 사이트에 액세스하는 데 호스트 이름을 사용하지 않으면 SNI를 사용하지 않는 경우 백 엔드 서버는 기본 웹 사이트에서 구성된 인증서를 반환합니다. ILB ASE의 경우 기본 인증서는 ILB 인증서에서 제공됩니다. ILB에 대해 구성된 인증서가 없는 경우 인증서는 ASE 앱 인증서에서 제공됩니다.

FQDN(정규화된 도메인 이름)을 사용하여 ILB에 액세스하는 경우 백 엔드 서버는 HTTP 설정에 업로드되는 올바른 인증서를 반환합니다. 그렇지 않은 경우 다음과 같은 옵션을 고려하세요.

- 애플리케이션 게이트웨이의 백 엔드 풀에서 FQDN을 사용하여 ILB의 IP 주소를 가리킵니다. 이 옵션은 사설 DNS 영역 또는 구성된 사용자 지정 DNS가 있는 경우에만 작동합니다. 그렇지 않으면 공용 DNS에 대한 "A" 레코드를 만들어야 합니다.

- ILB에서 업로드된 인증서 또는 HTTP 설정에서 기본 인증서(ILB 인증서)를 사용합니다. 애플리케이션 게이트웨이는 프로브에 대한 ILB의 IP에 액세스할 때 인증서를 가져옵니다.

- ILB 및 백 엔드 서버에서 와일드카드 인증서를 사용하므로 모든 웹 사이트에 대해 인증서는 일반적입니다. 단, 이 해결 방법은 하위 도메인의 경우에만 가능하며 각 웹 사이트에 다른 호스트 이름이 필요한 경우에는 적용되지 않습니다.

- ILB의 IP 주소를 사용하는 경우 애플리케이션 게이트웨이에 대해 **App Service에 사용** 옵션을 선택 취소합니다.

오버헤드를 줄이기 위해 HTTP 설정에 ILB 인증서를 업로드하여 프로브 경로 작업을 만들 수 있습니다. (이 단계는 허용 목록에 대한 것입니다. SSL 통신에 사용되지 않습니다.) HTTPS의 사용자 브라우저에서 해당 IP 주소로 ILB에 액세스한 다음, Base-64로 인코딩된 CER 형식으로 SSL 인증서를 내보내고 해당 HTTP 설정에서 인증서를 업로드하여 ILB 인증서를 검색할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
