---
title: Azure Application Gateway 리디렉션 개요
description: Azure Application Gateway의 리디렉션 기능에 대해 알아봅니다.
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/19/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715818"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway 리디렉션 개요

트래픽을 리디렉션할 응용 프로그램 게이트웨이 사용할 수 있습니다.  다른 수신기로 또는 외부 사이트에 하나의 수신기에서 수신 트래픽을 리디렉션할 수 있는 일반적인 리디렉션 메커니즘에 이를 통해 애플리케이션 구성이 간소화되고, 리소스 사용이 최적화되고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.

여러 웹 응용 프로그램에 대 한 일반적인 리디렉션 시나리오는 응용 프로그램과 사용자 간의 모든 통신은 암호화 된 경로 통해 발생 되도록 자동 HTTP-HTTPS 리디렉션을 지원 하기 위해서입니다. 이전에는 HTTP에서 수신하는 요청을 HTTPS로 리디렉션하는 것이 유일한 목적인 전용 백 엔드 풀 만들기와 같은 기술을 고객이 사용했습니다. Application Gateway에서 리디렉션 지원을 통해 라우팅 규칙을 새 리디렉션 구성을 추가 하 고 대상 수신기로 HTTPS 프로토콜을 사용 하 여 다른 수신기를 지정 하 여 간단히 수행할 수 있습니다.

다음과 같은 유형의 리디렉션 지원 됩니다.

- 301 영구 리디렉션
- 302 있음
- 다른 303 참조
- 307 임시 리디렉션

Application Gateway 리디렉션 지원에서는 다음과 같은 기능을 제공합니다.

-  **전역 리디렉션**

   게이트웨이의 한 수신기에서 다른 수신기로의 리디렉션. 한 사이트에서 HTTP - HTTPS 리디렉션이 가능합니다.
- **경로 기반 리디렉션**

   이러한 종류의 리디렉션에서는 /cart/*로 표시되는 쇼핑 카트 영역과 같이 특정 사이트 영역에서만 HTTP - HTTPS 리디렉션이 가능합니다.
- **외부 사이트로의 리디렉션**

![리디렉션](./media/redirect-overview/redirect.png)

이러한 변경으로 인해 고객은 리디렉션하려는 대상 수신기 또는 외부 사이트를 지정하는 새 리디렉션 구성 개체를 만들어야 합니다. 구성 요소도 리디렉션된 URL에 URI 경로 및 쿼리 문자열을 추가할 수 있는 옵션을 지원합니다. 또한 리디렉션 형식을 선택할 수 있습니다. 이 리디렉션 구성이 만들어지면 새 규칙을 통해 원본 수신기에 첨부됩니다. 기본 규칙을 사용하는 경우 리디렉션 구성이 원본 수신기와 연결되며 전역 리디렉션이 됩니다. 경로 기반 규칙을 사용할 경우 리디렉션 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다.

### <a name="next-steps"></a>다음 단계

[애플리케이션 게이트웨이에서 URL 리디렉션 구성](tutorial-url-redirect-powershell.md)

<!-- Update_Description: wording update -->