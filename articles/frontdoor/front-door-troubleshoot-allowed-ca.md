---
title: Azure Front Door Service에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관 | Microsoft Docs
description: 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330742"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Azure Front Door Service에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관

Azure Front Door Service 사용자 지정 도메인의 경우 Microsoft Azure CDN Standard 엔드포인트의 Azure CDN(Content Delivery Network) 사용자 지정 도메인의 경우 [고유의 인증서를 사용하여 HTTPS 기능을 활성화](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)하려면 허용되는 CA(인증 기관)를 사용하여 SSL 인증서를 만들어야 합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
