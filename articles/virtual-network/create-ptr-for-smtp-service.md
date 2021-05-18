---
title: SMTP 배너 검사를 위한 역방향 조회 영역 구성
titlesuffix: Azure Virtual Network
description: Azure에서 SMTP 배너 검사를 위한 역방향 조회 영역을 구성하는 방법을 설명합니다.
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135064"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>SMTP 배너 검사를 위한 역방향 조회 영역 구성

이 문서에서는 Azure DNS에서 역방향 영역을 사용하여 SMTP 배너 검사를 위한 역방향 DNS(PTR) 레코드를 만드는 방법을 설명합니다.

## <a name="symptom"></a>증상

Microsoft Azure에서 SMTP 서버를 호스트하는 경우 원격 메일 서버에서 메시지를 보내거나 받을 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

**554: PTR 레코드 없음**

## <a name="solution"></a>솔루션

Azure의 가상 IP 주소의 경우, 역방향 레코드가 사용자 정의 도메인 영역이 아닌 Microsoft 소유의 도메인 영역에 생성됩니다.

Microsoft 소유의 영역에 PTR 레코드를 구성하려면 PublicIpAddress 리소스에서 -ReverseFqdn 속성을 사용합니다. 자세한 내용은 [Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](../dns/dns-reverse-dns-for-azure-services.md)을 참조하세요.

PTR 레코드를 구성할 때 IP 주소 및 역방향 FQDN이 구독의 소유여야 합니다. 구독에 속하지 않는 역방향 FQDN을 설정하려고 시도하면 다음과 같은 오류 메시지가 나타납니다.

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

기본 역방향 FQDN을 매칭하기 위해 SMTP 배너를 수동으로 변경하는 경우 원격 메일 서버는 SMTP 배너 호스트가 도메인의 MX 레코드와 일치할 것으로 예상하기 때문에 계속 실패할 수 있습니다.
