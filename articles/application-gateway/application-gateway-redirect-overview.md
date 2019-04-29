---
title: Azure Application Gateway 리디렉션 개요 | Microsoft Docs
description: Azure Application Gateway의 리디렉션 기능에 대해 알아봅니다.
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 07/18/2017
ms.date: 01/15/2019
ms.author: v-junlch
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831766"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway 리디렉션 개요

일반적으로 여러 웹 애플리케이션에서 자동 HTTP - HTTPS 리디렉션을 지원하여 애플리케이션 및 해당 사용자 간 모든 통신이 암호화된 경로를 통해 이루어지도록 합니다. 이전에는 HTTP에서 수신하는 요청을 HTTPS로 리디렉션하는 것이 유일한 목적인 전용 백 엔드 풀 만들기와 같은 기술을 고객이 사용했습니다.  이제 Application Gateway에서 Application Gateway의 트래픽을 리디렉션하는 기능을 지원합니다. 이를 통해 애플리케이션 구성이 간소화되고, 리소스 사용이 최적화되고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다. Application Gateway 리디렉션 지원은 HTTP -> HTTPS 리디렉션으로만 제한되지 않습니다. 일반적인 리디렉션 메커니즘으로, Application Gateway의 한 수신기에서 수신한 트래픽을 다른 수신기로 리디렉션할 수 있습니다. 외부 사이트로의 리디렉션도 지원합니다. Application Gateway 리디렉션 지원에서는 다음과 같은 기능을 제공합니다.

1. Gateway의 한 수신기에서 다른 수신기로의 전역 리디렉션. 한 사이트에서 HTTP - HTTPS 리디렉션이 가능합니다.
2. 경로 기반 리디렉션. 이러한 종류의 리디렉션에서는 /cart/*로 표시되는 쇼핑 카트 영역과 같이 특정 사이트 영역에서만 HTTP - HTTPS 리디렉션이 가능합니다.
3. 외부 사이트로의 리디렉션.

![리디렉션](./media/application-gateway-redirect-overview/redirect.png)

이러한 변경으로 인해 고객은 리디렉션하려는 대상 수신기 또는 외부 사이트를 지정하는 새 리디렉션 구성 개체를 만들어야 합니다. 구성 요소도 리디렉션된 URL에 URI 경로 및 쿼리 문자열을 추가할 수 있는 옵션을 지원합니다. 고객은 리디렉션이 임시 리디렉션(HTTP 상태 코드 302)인지 영구 리디렉션(HTTP 상태 코드 301)인지도 선택할 수 있습니다. 이 리디렉션 구성이 만들어지면 새 규칙을 통해 원본 수신기에 첨부됩니다. 기본 규칙을 사용하는 경우 리디렉션 구성이 원본 수신기와 연결되며 전역 리디렉션이 됩니다. 경로 기반 규칙이 사용되는 경우 리디렉션 구성은 URL 경로 맵에 정의되므로 사이트의 특정 경로 영역에만 적용됩니다.

### <a name="next-steps"></a>다음 단계

[Application Gateway에서 HTTP->HTTPS 리디렉션 구성](redirect-http-to-https-portal.md)

<!-- Update_Description: wording update -->