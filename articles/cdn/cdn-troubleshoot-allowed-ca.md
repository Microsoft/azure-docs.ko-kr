---
title: Azure CDN에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관 | Microsoft Docs
description: 고유의 인증서를 사용하여 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하려면 허용되는 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 8ce141fbf3d767159d16495bff5a93d791224c17
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331893"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Azure CDN에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관

**Microsoft Azure CDN Standard** 엔드포인트의 Azure CDN(Content Delivery Network) 사용자 지정 도메인의 경우 [고유의 인증서를 사용하여 HTTPS를 활성화](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)하려면 허용되는 CA(인증 기관)를 사용하여 SSL 인증서를 만들어야 합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

> [!NOTE]
> 고유한 인증서를 사용하여 사용자 지정 HTTPS를 활성화하는 옵션은 **Microsoft Azure CDN Standard** 프로필에만 사용할 수 있습니다. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

