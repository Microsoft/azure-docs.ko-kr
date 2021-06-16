---
title: Azure Front Door에서 사용자 지정 HTTPS를 사용하도록 설정하기 위해 허용된 CA
description: 사용자 고유의 인증서를 통해 Azure Front Door 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하는 경우 허용된 CA(인증 기관)를 사용하여 인증서를 만들어야 합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112003639"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Azure Front Door에서 사용자 지정 HTTPS를 사용하도록 설정하기 위해 허용된 인증 기관

Azure Front Door 사용자 지정 도메인에 대해 [사용자 고유의 인증서를 통해 HTTPS 기능을 사용](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)하도록 설정하는 경우 TLS/SSL 인증서를 만들려면 허용된 CA(인증 기관)가 필요합니다. 그렇지 않고 허용되지 않는 CA 또는 자체 서명 인증서를 사용할 경우 요청이 거부됩니다.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
