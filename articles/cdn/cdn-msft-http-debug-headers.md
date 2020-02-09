---
title: Microsoft의 Azure CDN에 대한 HTTP 헤더 디버그 | Microsoft Docs
description: 디버그 캐시 요청 헤더는 요청 된 자산에 적용 되는 캐시 정책에 대한 추가 정보를 제공 합니다. 이러한 헤더는 Microsoft의 Azure CDN에만 적용 됩니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814078"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대한 디버그 HTTP 헤더
디버그 응답 헤더 `X-Cache`는 콘텐츠가 제공 된 CDN 스택의 계층에 대한 세부 정보를 제공 합니다. 이 헤더는 Microsoft의 Azure CDN에만 적용 됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

헤더 | Description
-------|------------
X 캐시: TCP_HIT | 이 헤더는 CDN에 지 캐시에서 콘텐츠를 제공할 때 반환 됩니다. 
X 캐시: TCP_REMOTE_HIT | 이 헤더는 CDN 지역 캐시 (원본 방패 계층)에서 콘텐츠를 제공할 때 반환 됩니다.
X 캐시: TCP_MISS | 캐시 누락이 있는 경우이 헤더가 반환 되 고 원본에서 콘텐츠가 제공 됩니다. 


