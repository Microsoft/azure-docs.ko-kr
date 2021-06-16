---
title: Azure PowerShell을 사용하여 Azure DNS의 DNS 레코드 관리 | Microsoft Docs
description: Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다. 레코드 집합 및 레코드 작업에 대한 모든 PowerShell 명령입니다.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 2aaa8ff93d0e334bf8b9104b4f82b0e35ccf36c1
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705041"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure 클래식 CLI](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

이 문서는 Azure PowerShell을 사용하여 DNS 영역에 대한 DNS 레코드를 관리하는 방법을 보여줍니다. 크로스 플랫폼인 [Azure CLI](dns-operations-recordsets-cli.md) 또는 [Azure Portal](dns-operations-recordsets-portal.md)을 사용하여 DNS 레코드를 관리할 수도 있습니다.

이 문서의 예제에서는 이미 [Azure PowerShell을 설치했고, 로그인했고, DNS 영역을 만들었다](dns-operations-dnszones.md)고 가정합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>소개

Azure DNS에 DNS 레코드를 만들기 전에 먼저 Azure DNS에서 DNS 레코드를 DNS 레코드 집합으로 구성하는 방법을 이해해야 합니다.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS의 DNS 레코드에 대한 자세한 내용은 [DNS 영역 및 레코드](dns-zones-records.md)를 참조하세요.


## <a name="create-a-new-dns-record"></a>새 DNS 레코드 만들기

새 레코드 집합을 만들려면 기존 레코드와 다른 이름과 형식이 있어야 합니다. 새 레코드가 기존 레코드와 이름 및 형식이 똑같은 경우 [기존 레코드 집합에 추가](#add-a-record-to-an-existing-record-set)해야 합니다.

### <a name="create-a-records-in-a-new-record-set"></a>새 레코드 집합에서 ‘A’ 레코드 만들기

`New-AzDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 레코드 집합을 만들 때, 레코드 집합 이름, 영역, TTL(Time-to-Live), 레코드 형식 및 만들 레코드를 지정해야 합니다.

레코드 집합에 레코드를 추가하기 위한 매개 변수는 레코드 집합 형식에 따라 달라집니다. 예를 들어 'A' 형식의 레코드 집합을 사용하는 경우 `-IPv4Address` 매개 변수를 사용하여 IP 주소를 지정해야 합니다. 서로 다른 레코드 형식에는 추가 매개 변수가 있습니다.

다음 예제에서는 DNS 영역 `contoso.com`에 상대적 이름 `www`가 포함된 레코드 집합을 만듭니다. 레코드 집합의 정규화된 이름은 `www.contoso.com`입니다. 레코드 형식은 'A'이고 TTL은 3600초입니다. 레코드 집합은 '1.2.3.4' IP 주소를 가진 단일 레코드를 포함합니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

영역의 'apex'에서 레코드 집합을 만들려면(이 경우 'contoso.com'), 레코드 집합 이름 '\@'(따옴표 제외)을 사용합니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

둘 이상의 레코드를 포함하는 레코드 집합을 만들어야 하는 경우 먼저 로컬 배열을 만들고 레코드를 추가한 후에 다음과 같이 `New-AzDnsRecordSet`에 전달합니다.

```azurepowershell-interactive
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[레코드 집합 메타데이터](dns-zones-records.md#tags-and-metadata)는 키-값 쌍의 형태로 각 레코드 집합과 애플리케이션 특정 데이터를 연결하는 데 사용할 수 있습니다. 다음 예제에서는 "dept=finance" 및 "environment=production"라는 두 개의 메타데이터 항목을 가진 레코드 집합을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS는 DNS 레코드를 만들기 전에 DNS 이름을 예약하는 자리 표시자 역할을 수행할 수 있는 '빈' 레코드 집합을 지원합니다. 빈 레코드 집합은 Azure DNS 제어 평면에 표시되어 있지만 Azure DNS 이름 서버에도 나타납니다. 아래 예제에서는 빈 레코드 집합을 만듭니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>다른 형식의 레코드 만들기

지금까지 'A' 레코드를 만드는 방법에 대해 자세히 살펴보았으며, 다음 예제에서는 Azure DNS에서 지원하는 다른 레코드 형식의 레코드를 만드는 방법을 보여 줍니다.

각각의 경우에 단일 레코드를 포함하는 레코드 집합을 만드는 방법을 보여줍니다. 'A' 레코드에 대한 이전 예제는 메타데이터와 여러 레코드를 포함하는 다른 형식의 레코드 집합을 만들거나 빈 레코드 집합을 만드는 데 적용될 수 있습니다.

각 DNS 영역에 SOA가 생성 및 삭제되므로 SOA 레코드 집합을 만드는 예제는 없습니다. SOA 레코드는 별도로 생성 또는 삭제할 수 없습니다. 그러나 [뒷부분의 예제에 표시된 대로 SOA를 수정할 수 있습니다](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>단일 레코드가 포함된 AAAA 레코드 집합 만들기

```azurepowershell-interactive
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>단일 레코드가 포함된 CAA 레코드 집합 만들기

```azurepowershell-interactive
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>단일 레코드가 포함된 CNAME 레코드 집합 만들기

> [!NOTE]
> DNS 표준은 영역의 apex(`-Name '@'`)에서 CNAME 레코드를 허용하거나 둘 이상의 레코드를 포함하는 레코드 집합을 허용하지 않습니다.
> 
> 자세한 내용은 [CNAME 레코드](dns-zones-records.md#cname-records)를 참조하세요.


```azurepowershell-interactive
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>단일 레코드가 포함된 MX 레코드 집합 만들기

이 예제에서는 레코드 집합 이름을 '\@'으로 사용하여 영역 구로에 MX 레코드를 만듭니다(이 경우 'contoso.com').


```azurepowershell-interactive
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>단일 레코드가 포함된 NS 레코드 집합 만들기

```azurepowershell-interactive
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>단일 레코드가 포함된 PTR 레코드 집합 만들기

이 경우에 'my-arpa-zone.com'은 IP 범위를 나타내는 ARPA 역방향 조회 영역을 나타냅니다. 이 영역의 각 PTR 레코드 집합은 IP 범위 내의 IP 주소에 해당합니다. 레코드 이름 '10'은 이 레코드에서 나타내는 이 IP 범위 내에서 IP 주소의 마지막 옥텟입니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>단일 레코드가 포함된 SRV 레코드 집합 만들기

[SRV 레코드 집합](dns-zones-records.md#srv-records)을 만들 경우 레코드 집합 이름에 *\_서비스* 및 *\_프로토콜* 을 지정합니다. 영역 apex에 SRV 레코드 집합을 만드는 경우 레코드 집합 이름에 ‘\@’을 포함할 필요가 없습니다.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>단일 레코드가 포함된 TXT 레코드 집합 만들기

다음 예제에서는 TXT 레코드를 만드는 방법을 보여 줍니다. TXT 레코드에서 지원되는 최대 문자열 길이에 대한 자세한 내용은 [TXT 레코드](dns-zones-records.md#txt-records)를 참조하세요.

```azurepowershell-interactive
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>레코드 집합 가져오기

기존 레코드 집합을 가져오려면, `Get-AzDnsRecordSet`를 사용합니다. 이 cmdlet은 Azure DNS에서 레코드 집합을 나타내는 로컬 개체를 반환합니다.

`New-AzDnsRecordSet`와 마찬가지로, 레코드 집합 이름은 *상대* 이름이어야 합니다. 즉, 영역 이름을 제외해야 합니다. 레코드 형식 및 레코드 집합을 포함하는 영역을 지정해야 합니다.

다음 예제에서는 레코드 집합을 검색하는 방법을 보여 줍니다. 이 예제에서는 `-ZoneName` 및 `-ResourceGroupName` 매개 변수를 사용하여 영역을 지정합니다.

```azurepowershell-interactive
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

대신 `-Zone` 매개 변수를 사용하여 전달된 영역 개체를 사용하는 영역도 지정할 수 있습니다.

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>레코드 집합 나열

`-Name` 또는 `-RecordType` 매개 변수를 생략하여 영역에 있는 레코드 집합을 나열하도록 `Get-AzDnsZone`을 사용할 수도 있습니다.

다음 예제에서는 영역에 있는 모든 레코드 집합을 반환합니다.

```azurepowershell-interactive
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

다음 예제에서는 레코드 집합 이름을 생략하는 동시에 레코드 형식을 지정하여 모든 지정된 형식의 레코드 집합을 검색할 수 있는 방법을 보여 줍니다.

```azurepowershell-interactive
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

레코드 형식에서 지정된 이름의 모든 레코드 집합을 검색하려면 모든 레코드 집합을 검색한 다음 결과를 필터링해야 합니다.

```azurepowershell-interactive
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

위의 모든 예제에서 영역은 `-ZoneName` 및 `-ResourceGroupName` 매개 변수를 사용하거나 영역 개체를 지정하여 지정할 수 있습니다.

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>기존 레코드 집합에 레코드 추가

기존 레코드 집합에 레코드를 추가하려면 다음 세 단계를 수행합니다.

1. 기존 레코드 집합 가져오기

    ```azurepowershell-interactive
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

1. 로컬 레코드 집합에 새 레코드 추가

    ```azurepowershell-interactive
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS 서비스에 반영되도록 변경 내용을 업데이트합니다. 

    ```azurepowershell-interactive
    Set-AzDnsRecordSet -RecordSet $rs
    ```

을 사용하여 Azure DNS의 기존 레코드 집합 및 포함된 모든 레코드를 지정된 레코드 집합으로 `Set-AzDnsRecordSet` *바꿉니다*. [Etag 검사](dns-zones-records.md#etags)를 사용하여 동시 변경 내용을 덮어쓰지 않도록 합니다. 선택적 `-Overwrite` 스위치를 사용하여 이러한 검사를 무시할 수 있습니다.

이 작업 시퀀스를 *파이프* 할 수도 있습니다. 즉, 레코드 집합 개체를 매개 변수로 전달하는 대신 파이프를 통해 전달합니다.

```azurepowershell-interactive
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

위의 예제에서는 'A' 형식의 기존 레코드 집합에 'A' 레코드를 추가하는 방법을 보여 줍니다. 비슷한 작업 시퀀스를 사용하여 다른 형식의 레코드 집합에 레코드를 추가하면 `Add-AzDnsRecordConfig`의 `-Ipv4Address` 매개 변수를 각 레코드 형식에 특정된 다른 매개 변수로 대체합니다. 각 레코드 형식의 매개 변수는 위의 다른 레코드 형식 예제에 표시된 대로 `New-AzDnsRecordConfig` cmdlet의 경우와 동일합니다.

'CNAME' 또는 'SOA' 형식의 레코드 집합은 둘 이상의 레코드를 포함할 수 없습니다. 이 제약 조건은 DNS 표준에서 발생합니다. Azure DNS의 제한 사항이 아닙니다.

## <a name="remove-a-record-from-an-existing-record-set"></a>기존 레코드 집합에서 레코드 제거

레코드 집합에서 레코드를 제거하는 프로세스는 기존 레코드 집합에 레코드를 추가하는 프로세스와 비슷합니다.

1. 기존 레코드 집합 가져오기

    ```azurepowershell-interactive
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. 로컬 레코드 집합 개체에서 레코드를 제거합니다. 제거되는 레코드는 모든 매개 변수가 기존 레코드와 정확히 일치해야 합니다.

    ```azurepowershell-interactive
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS 서비스에 변경 내용 커밋 선택적 `-Overwrite` 스위치를 사용하여 동시 변경에 대한 [Etag 검사](dns-zones-records.md#etags)를 무시합니다.

    ```azurepowershell-interactive
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

레코드 집합에서 마지막 레코드를 제거하는 위의 시퀀스를 사용하여 레코드 집합을 삭제하지 않습니다. 오히려 빈 레코드 집합을 유지합니다. 레코드 집합을 완전히 제거하려면 [레코드 집합 삭제](#delete-a-record-set)를 참조하세요.

마찬가지로 레코드 집합에 레코드를 추가하려면 레코드 집합을 제거하는 작업 시퀀스는 파이핑될 수도 있습니다.

```azurepowershell-interactive
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

적절한 형식 특정 매개 변수를 `Remove-AzDnsRecordSet`에 전달하여 다른 레코드 형식을 지원합니다. 각 레코드 형식의 매개 변수는 위의 다른 레코드 형식 예제에 표시된 대로 `New-AzDnsRecordConfig` cmdlet의 경우와 동일합니다.


## <a name="modify-an-existing-record-set"></a>기존 레코드 집합 수정

기존 레코드 집합을 수정하기 위한 단계는 레코드 집합에서 레코드를 추가하거나 제거할 때 수행하는 단계와 비슷합니다.

1. `Get-AzDnsRecordSet`을 사용하여 기존 레코드를 가져옵니다.
2. 다음을 통해 로컬 레코드 집합 개체를 수정합니다.
    * 레코드 추가 또는 제거
    * 기존 레코드의 매개 변수 변경
    * 레코드 집합 메타데이터 및 TTL(Time To Live) 변경
3. `Set-AzDnsRecordSet` cmdlet을 사용하여 변경 내용을 커밋합니다. 그러면 Azure DNS의 기존 레코드 집합이 지정된 레코드 집합으로 *바뀝니다*.

`Set-AzDnsRecordSet`를 사용하는 경우 [Etag 검사](dns-zones-records.md#etags)를 통해 동시 변경 내용을 덮어쓰지 않도록 합니다. 선택적 `-Overwrite` 스위치를 사용하여 이러한 검사를 무시할 수 있습니다.

### <a name="to-update-a-record-in-an-existing-record-set"></a>기존 레코드 집합의 레코드를 업데이트하려면

이 예제에서는 기존 'A' 레코드의 IP 주소를 변경합니다.

```azurepowershell-interactive
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>SOA 레코드를 수정하려면

영역 루트(인용 부호를 포함한 `-Name "@"`)에 설정된 자동으로 생성된 SOA 레코드 집합에서 레코드를 추가 또는 제거할 수는 없습니다. 그러나 SOA 레코드 내의 매개 변수("Host" 제외) 및 레코드 집합 TTL을 수정할 수 있습니다.

다음 예제에서는 SOA 레코드의 *Email* 속성을 변경하는 방법을 보여 줍니다.

```azurepowershell-interactive
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>영역 루트의 NS 레코드를 수정하려면

각 DNS 영역에 영역 루트의 NS 레코드 집합이 자동으로 만들어집니다. 여기에는 영역에 할당된 Azure DNS 이름 서버의 이름이 포함됩니다.

이 NS 레코드 집합에 추가 이름 서버를 추가하여 DNS 공급자가 2개 이상 있는 공동 호스팅 도메인을 지원할 수 있습니다. 또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수도 있습니다. 그러나 미리 채워진 Azure DNS 이름 서버는 제거 또는 수정할 수 없습니다.

이 제한은 영역 루트에 있는 NS 레코드 집합에만 적용됩니다. 영역의 다른 NS 레코드 집합은 제약 없이 수정할 수 있습니다(자식 영역을 위임하는 데 사용되므로).

다음 예제에서는 영역 루트의 NS 레코드 집합에 다른 이름 서버를 추가하는 방법을 보여 줍니다.

```azurepowershell-interactive
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>레코드 집합 메타데이터를 수정하려면

[레코드 집합 메타데이터](dns-zones-records.md#tags-and-metadata)는 키-값 쌍의 형태로 각 레코드 집합과 애플리케이션 특정 데이터를 연결하는 데 사용할 수 있습니다.

다음 예제에서는 기존 레코드 집합의 메타데이터를 수정하는 방법을 보여 줍니다.

```azurepowershell-interactive
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>레코드 집합 삭제

`Remove-AzDnsRecordSet` cmdlet을 사용하여 레코드 집합을 삭제할 수 있습니다. 레코드 집합을 삭제하면 레코드 집합 내에서 모든 레코드가 삭제됩니다.

> [!NOTE]
> 영역 apex(`-Name '@'`)에서 SOA 및 NS 레코드 집합을 삭제할 수 없습니다 .  Azure DNS는 영역을 만들 때 자동으로 만들어지고 영역을 삭제할 때 자동으로 삭제됩니다.

다음 예제에서는 레코드 집합을 삭제하는 방법을 보여 줍니다. 이 예제에서는 레코드 집합 이름, 레코드 집합 형식, 영역 이름 및 리소스 그룹을 각각 명시적으로 지정합니다.

```azurepowershell-interactive
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

대신 개체를 사용하여 지정된 이름과 형식 및 영역으로 레코드 집합을 지정할 수 있습니다.

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

세 번째 옵션으로 레코드 집합 자체를 레코드 집합 개체를 사용하여 지정할 수 있습니다.

```azurepowershell-interactive
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

레코드 집합 개체를 사용하여 레코드 집합을 삭제하도록 지정하는 경우 동시 변경 내용이 삭제되지 않도록 [Etag 검사](dns-zones-records.md#etags)를 사용합니다. 선택적 `-Overwrite` 스위치를 사용하여 이러한 검사를 무시할 수 있습니다.

레코드 집합 개체를 매개 변수로 전달하는 대신 파이프할 수도 있습니다.

```azurepowershell-interactive
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>확인 메시지 표시

`New-AzDnsRecordSet`, `Set-AzDnsRecordSet` 및 `Remove-AzDnsRecordSet` cmdlet은 모두 확인 메시지를 표시하도록 지원합니다.

`$ConfirmPreference` PowerShell 기본 설정 변수 값에 `Medium` 이하의 값이 있는 경우 각 cmdlet은 확인 메시지를 표시합니다. `$ConfirmPreference`의 기본 값이 `High`이기 때문에 기본 PowerShell 설정을 사용하는 경우 이러한 프롬프트가 표시되지 않습니다.

`-Confirm` 매개 변수를 사용하여 현재 `$ConfirmPreference` 설정을 재정의할 수 있습니다. `-Confirm` 또는 `-Confirm:$True`를 지정하는 경우 cmdlet은 실행하기 전에 확인을 위한 메시지를 표시합니다. `-Confirm:$False`를 지정하는 경우 cmdlet은 확인을 위한 메시지를 표시하지 않습니다. 

`-Confirm` 및 `$ConfirmPreference`에 대한 자세한 내용은 [기본 설정 변수 정보](/powershell/module/microsoft.powershell.core/about/about_preference_variables)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure DNS의 영역 및 레코드](dns-zones-records.md)에 대해 자세히 알아봅니다.
<br>
Azure DNS를 사용하는 경우 [영역 및 레코드를 보호](dns-protect-zones-recordsets.md)하는 방법에 대해 알아봅니다.
<br>
[Azure DNS PowerShell 참조 설명서](/powershell/module/az.dns)를 검토합니다.
