---
title: Azure PowerShell을 사용하여 Azure DNS 하위 도메인 위임
description: Azure PowerShell을 사용하여 Azure DNS 하위 도메인을 위임하는 방법을 알아봅니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 40b2a4d98e6269d9740856ba44c1043af75ce1b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791187"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure DNS 하위 도메인 위임

Azure PowerShell을 사용하여 DNS 하위 도메인을 위임할 수 있습니다. 예를 들어 contoso.com 도메인을 소유하고 있으면 *engineering*이라는 하위 도메인을 contoso.com 영역과 별도로 관리할 수 있는 별도의 다른 영역에 위임할 수 있습니다.

원하는 경우 [Azure Portal](delegate-subdomain.md)을 사용하여 하위 도메인을 위임할 수 있습니다.

> [!NOTE]
> Contoso.com은 이 문서 전체에서 예로 사용됩니다. Contoso.com을 고유한 도메인 이름으로 바꾸세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>필수 조건

Azure DNS 하위 도메인을 위임하려면 먼저 공용 도메인을 Azure DNS에 위임해야 합니다. 위임에 대한 이름 서버를 구성하는 방법에 대한 지침은 [Azure DNS에 도메인 위임](./dns-delegate-domain-azure-dns.md)을 참조하세요. 도메인이 Azure DNS 영역에 위임되면 하위 도메인을 위임할 수 있습니다.

## <a name="create-a-zone-for-your-subdomain"></a>하위 도메인에 대한 영역 만들기

먼저 **engineering** 하위 도메인에 대한 영역을 만듭니다.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>이름 서버 기록

engineering 하위 도메인에서 4개의 이름 서버를 적어 둡니다.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>테스트 레코드 만들기

engineering 영역에 테스트에 사용할 **A** 레코드를 만듭니다.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>NS 레코드 만들기

다음으로, contoso.com 영역의 **engineering** 영역에 대한 NS(이름 서버) 레코드를 만듭니다.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>위임 테스트

nslookup을 사용하여 위임을 테스트합니다.

1. PowerShell 창을 엽니다.
2. 명령 프롬프트에서 `nslookup www.engineering.contoso.com.`을 입력합니다.
3. **10.10.10.10** 주소를 보여 주는 신뢰할 수 없는 응답을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](dns-reverse-dns-for-azure-services.md) 방법에 대해 알아봅니다.