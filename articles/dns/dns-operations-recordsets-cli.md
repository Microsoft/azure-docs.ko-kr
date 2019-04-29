---
title: Azure CLI를 사용하여 Azure DNS에서 DNS 레코드 관리 | Microsoft Docs
description: Azure DNS에서 도메인을 호스트하는 경우 Azure DNS에서 DNS 레코드 집합 및 레코드를 관리합니다.
services: dns
documentationcenter: na
author: WenJason
manager: digimobile
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
origin.date: 05/15/2018
ms.date: 04/15/2019
ms.author: v-jay
ms.openlocfilehash: 4864a46b91b4e243ce6a2ae3d9d36df28fe74d8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293357"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure DNS에서 DNS 레코드 및 레코드 집합 관리

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

이 문서에서는 Windows, Mac 및 Linux용으로 제공되는 플랫폼 간 Azure CLI를 사용하여 DNS 영역에 대한 DNS 레코드를 관리하는 방법을 설명합니다. [Azure PowerShell](dns-operations-recordsets.md) 또는 [Azure Portal](dns-operations-recordsets-portal.md)을 사용하여 DNS 레코드를 관리할 수도 있습니다.

이 문서의 예제에서는 이미 [Azure CLI를 설치했고, 로그인했고, DNS 영역을 만들었다](dns-operations-dnszones-cli.md)고 가정합니다.

## <a name="introduction"></a>소개

Azure DNS에 DNS 레코드를 만들기 전에 먼저 Azure DNS에서 DNS 레코드를 DNS 레코드 집합으로 구성하는 방법을 이해해야 합니다.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS의 DNS 레코드에 대한 자세한 내용은 [DNS 영역 및 레코드](dns-zones-records.md)를 참조하세요.

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set <record-type> add-record` 명령을 사용합니다(여기서 `<record-type>`은 a, srv, txt 등의 레코드 형식임). 도움말을 보려면 `az network dns record-set --help`을 참조하세요.

레코드를 만드는 경우 리소스 그룹 이름, 영역 이름, 레코드 집합 이름, 레코드 유형 및 만드는 레코드의 세부 정보를 지정해야 합니다. 레코드 집합 이름은 *상대* 이름이어야 합니다. 즉, 영역 이름을 제외해야 합니다.

레코드 집합이 아직 없는 경우 이 명령은 자동으로 레코드 집합을 만듭니다. 레코드 집합이 이미 있는 경우 이 명령은 지정한 레코드를 기존 레코드 집합에 추가합니다.

새 레코드 집합이 만들어지면 3600의 기본 TTL(Time to Live)이 사용됩니다. 다른 TTL을 사용하는 방법에 대한 지침은 [DNS 레코드 집합 만들기](#create-a-dns-record-set)를 참조하세요.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 *www*라는 A 레코드를 만듭니다. A 레코드의 IP 주소는 *1.2.3.4*입니다.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

영역의 구로에서 레코드 집합을 만들려면(이 경우 "contoso.com"), 따옴표를 포함한 레코드 이름 "\@"를 사용합니다.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>DNS 레코드 집합 만들기

위의 예제에서는 DNS 레코드가 기존 레코드 집합에 추가되거나 레코드 집합이 *명시적*으로 생성되었습니다. 레코드를 추가하기 전에 레코드 집합을 *명시적*으로 만들 수도 있습니다. Azure DNS는 DNS 레코드를 만들기 전에 DNS 이름을 예약하는 자리 표시자 역할을 수행할 수 있는 '빈' 레코드 집합도 지원합니다. 빈 레코드 집합은 Azure DNS 제어 평면에 표시되어 있지만 Azure DNS 이름 서버에 나타나지 않습니다.

`az network dns record-set <record-type> create` 명령을 사용하여 레코드 집합을 만듭니다. 도움말을 보려면 `az network dns record-set <record-type> create --help`을 참조하세요.

레코드 집합을 명시적으로 만들면 [TTL(Time to Live)](dns-zones-records.md#time-to-live) 및 메타데이터와 같은 레코드 집합 속성을 지정할 수 있습니다. [레코드 집합 메타데이터](dns-zones-records.md#tags-and-metadata)는 키-값 쌍의 형태로 각 레코드 집합과 애플리케이션 특정 데이터를 연결하는 데 사용할 수 있습니다.

다음 예제에서는 `--ttl` 매개 변수(약식 `-l`)를 사용하여 60초 TTL이 있는 비어 있는 'A' 형식의 레코드 집합을 만듭니다.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

다음 예제에서는 `--metadata` 매개 변수를 사용하여 "dept=finance" 및 "environment=production"라는 두 개의 메타데이터 항목을 가진 레코드 집합을 만듭니다.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

비어 있는 레코드 집합을 만들었으므로 [DNS 레코드 만들기](#create-a-dns-record)에 설명된 대로 `azure network dns record-set <record-type> add-record`를 사용하여 레코드를 추가할 수 있습니다.

## <a name="create-records-of-other-types"></a>다른 형식의 레코드 만들기

지금까지 'A' 레코드를 만드는 방법에 대해 자세히 살펴보았으며, 다음 예제에서는 Azure DNS에서 지원하는 다른 레코드 형식의 레코드를 만드는 방법을 보여 줍니다.

레코드 데이터를 지정하는 데 사용되는 매개 변수는 레코드 유형에 따라 다릅니다. 예를 들어 "A" 유형의 레코드의 경우 `--ipv4-address <IPv4 address>` 매개 변수로 IPv4 주소를 지정합니다. 각 레코드 형식에 대한 매개 변수는 `az network dns record-set <record-type> add-record --help`를 사용하여 나열할 수 있습니다.

각각의 경우에 단일 레코드를 만드는 방법을 보여줍니다. 레코드는 기존 레코드 집합 또는 암시적으로 생성된 레코드 집합에 추가됩니다. 레코드 집합 생성 및 레코드 집합 매개 변수를 명시적으로 정의하는 방법은 [DNS 레코드 집합 만들](#create-a-dns-record-set)를 참조하세요.

SOA가 각 DNS 영역과 함께 만들어지고 삭제되며 별도로 만들어지거나 삭제될 수 없기 때문에 SOA 레코드 집합을 만드는 예제를 제공하지 않습니다. 그러나 [뒷부분의 예제에 표시된 대로 SOA를 수정할 수 있습니다](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>AAAA 레코드 만들기

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>CAA 레코드 만들기

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>CNAME 레코드 만들기

> [!NOTE]
> DNS 표준은 영역의 apex(`--Name "@"`)에서 CNAME 레코드를 허용하거나 둘 이상의 레코드를 포함하는 레코드 집합을 허용하지 않습니다.
> 
> 자세한 내용은 [CNAME 레코드](dns-zones-records.md#cname-records)를 참조하세요.

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>MX 레코드 만들기

이 예제에서는 레코드 집합 이름을 "\@"로 사용하여 영역 구로에 MX 레코드를 만듭니다(이 경우 "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS 레코드 만들기

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR 레코드 만들기

이 경우에 'my-arpa-zone.com'은 IP 범위를 나타내는 ARPA 영역을 나타냅니다. 이 영역의 각 PTR 레코드 집합은 IP 범위 내의 IP 주소에 해당합니다.  레코드 이름 '10'은 이 레코드에서 나타내는 이 IP 범위 내에서 IP 주소의 마지막 옥텟입니다.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>SRV 레코드 만들기

[SRV 레코드 집합](dns-zones-records.md#srv-records)을 만들 경우 레코드 집합 이름에 *\_서비스* 및 *\_프로토콜*을 지정합니다. 영역 apex에 SRV 레코드 집합을 만드는 경우 레코드 집합 이름에 "\@"을 포함할 필요가 없습니다.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>TXT 레코드 만들기

다음 예제에서는 TXT 레코드를 만드는 방법을 보여 줍니다. TXT 레코드에서 지원되는 최대 문자열 길이에 대한 자세한 내용은 [TXT 레코드](dns-zones-records.md#txt-records)를 참조하세요.

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>레코드 집합 가져오기

기존 레코드 집합을 가져오려면, `az network dns record-set <record-type> show`를 사용합니다. 도움말을 보려면 `az network dns record-set <record-type> show --help`을 참조하세요.

레코드 또는 레코드 집합을 만들 때와 마찬가지로, 레코드 집합 이름은 *상대* 이름이어야 합니다. 즉, 영역 이름을 제외해야 합니다. 레코드 형식, 레코드 집합을 포함하는 영역 및 영역을 포함하는 리소스 그룹을 지정해야 합니다.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에서 *www*라는 A 형식의 레코드를 검색합니다.

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>레코드 집합 나열

`az network dns record-set list` 명령을 사용하여 DNS 영역에 있는 모든 레코드를 나열할 수 있습니다. 도움말을 보려면 `az network dns record-set list --help`을 참조하세요.

이 예제는 이름이나 레코드 형식에 관계없이 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 모든 레코드 집합을 반환합니다.

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

이 예제는 지정된 레코드 형식(이 경우 'A' 레코드)과 일치하는 모든 레코드 집합을 반환합니다.

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>기존 레코드 집합에 레코드 추가

`az network dns record-set <record-type> add-record`를 사용하여 새 레코드 집합에 레코드를 만들거나 기존 레코드 집합에 레코드를 추가할 수 있습니다.

자세한 내용은 위의 [DNS 레코드 만들기](#create-a-dns-record) 및 [다른 형식의 레코드 만들기](#create-records-of-other-types)를 참조하세요.

## <a name="remove-a-record-from-an-existing-record-set"></a>기존 레코드 집합에서 레코드를 제거합니다.

기존 레코드 집합에서 DNS 레코드를 제거하려면 `az network dns record-set <record-type> remove-record`를 사용합니다. 도움말을 보려면 `az network dns record-set <record-type> remove-record -h`을 참조하세요.

이 명령은 레코드 집합에서 DNS 레코드를 삭제합니다. 레코드 집합에서 마지막 레코드가 삭제되면 레코드 집합 자체도 삭제됩니다. 대신, 빈 레코드 집합을 유지하려면 `--keep-empty-record-set` 옵션을 사용합니다.

`az network dns record-set <record-type> add-record`를 사용하여 레코드를 만들 때와 동일한 매개 변수를 사용하여 삭제할 레코드와 레코드를 삭제할 영역을 지정해야 합니다. 이러한 매개 변수는 위의 [DNS 레코드 만들기](#create-a-dns-record) 및 [다른 형식의 레코드 만들기](#create-records-of-other-types)에 설명됩니다

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 *www*라는 레코드 집합에서 값이 '1.2.3.4'인 A 레코드를 삭제합니다.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>기존 레코드 집합 수정

각 레코드 집합에는 [TTL(Time to Live)](dns-zones-records.md#time-to-live), [메타데이터](dns-zones-records.md#tags-and-metadata) 및 DNS 레코드가 포함됩니다. 다음 섹션에서는 이러한 각 속성을 수정하는 방법을 설명합니다.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>A, AAAA, CAA, MX, NS, PTR, SRV 또는 TXT 레코드를 수정하려면

A, AAAA, CAA, MX, NS, PTR, SRV 또는 TXT 형식의 기존 레코드를 수정하려면 먼저 새 레코드를 추가한 후 기존 레코드를 삭제해야 합니다. 레코드를 삭제 및 추가하는 방법에 대한 자세한 내용은 이 문서의 이전 섹션을 참조하세요.

다음 예제에서는 IP 주소 1.2.3.4~IP 주소 5.6.7.8 범위에서 'A' 레코드를 수정하는 방법을 보여 줍니다.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

영역 루트(인용 부호를 포함하는 `--Name "@"`)에 자동으로 생성된 NS 레코드 집합에서 레코드를 추가, 제거 또는 수정할 수는 없습니다. 이 레코드 집합의 경우 레코드 집합 TTL 및 메타데이터를 수정하는 변경 작업만 허용됩니다.

### <a name="to-modify-a-cname-record"></a>CNAME 레코드를 수정하려면

대부분의 다른 레코드 형식과 달리 CNAME 레코드 집합은 단일 레코드만 포함할 수 있습니다.  따라서 다른 레코드 형식의 경우과 달리, 새 레코드를 추가하고 기존 레코드를 제거하여 현재 값을 바꿀 수 없습니다.

대신 CNAME 레코드를 수정하려면 `az network dns record-set cname set-record`를 사용합니다. 도움말을 보려면 `az network dns record-set cname set-record --help`를 참조하세요.

이 예에서는 기존 값 대신 'www.fabrikam.net'을 가리키도록 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에서 *www*라는 CNAME 레코드 집합을 수정합니다.

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>SOA 레코드를 수정하려면

대부분의 다른 레코드 형식과 달리 CNAME 레코드 집합은 단일 레코드만 포함할 수 있습니다.  따라서 다른 레코드 형식의 경우과 달리, 새 레코드를 추가하고 기존 레코드를 제거하여 현재 값을 바꿀 수 없습니다.

대신 SOA 레코드를 수정하려면 `az network dns record-set soa update`를 사용합니다. 도움말을 보려면 `az network dns record-set soa update --help`을 참조하세요.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에 SOA 레코드의 'email' 속성을 설정하는 방법을 보여 줍니다.

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>영역 루트의 NS 레코드를 수정하려면

각 DNS 영역에 영역 루트의 NS 레코드 집합이 자동으로 만들어집니다. 여기에는 영역에 할당된 Azure DNS 이름 서버의 이름이 포함됩니다.

이 NS 레코드 집합에 추가 이름 서버를 추가하여 DNS 공급자가 2개 이상 있는 공동 호스팅 도메인을 지원할 수 있습니다. 또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다.또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다. 그러나 미리 채워진 Azure DNS 이름 서버를 제거 또는 수정할 수 없습니다.

이는 영역 루트에 있는 NS 레코드 집합에만 적용됩니다. 영역의 다른 NS 레코드 집합은 제약 없이 수정할 수 있습니다(자식 영역을 위임하는 데 사용되므로).

다음 예제에서는 영역 루트의 NS 레코드 집합에 추가 이름 서버를 추가하는 방법을 보여 줍니다.

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.cn
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>기존 레코드 집합의 TTL을 수정하려면

기존 레코드 집합의 TTL을 수정하려면 `azure network dns record-set <record-type> update`을 사용합니다. 도움말을 보려면 `azure network dns record-set <record-type> update --help`을 참조하세요.

다음 예제에서는 레코드 집합 TTL(이 경우 60초)을 수정하는 방법을 보여 줍니다.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>기존 레코드 집합의 메타데이터를 수정하려면

[레코드 집합 메타데이터](dns-zones-records.md#tags-and-metadata)는 키-값 쌍의 형태로 각 레코드 집합과 애플리케이션 특정 데이터를 연결하는 데 사용할 수 있습니다. 기존 레코드 집합의 메타데이터를 수정하려면 `az network dns record-set <record-type> update`을 사용합니다. 도움말을 보려면 `az network dns record-set <record-type> update --help`을 참조하세요.

다음 예제에서는 "dept=finance" 및 "environment=production"라는 두 개의 메타데이터 항목을 가진 레코드 집합을 수정하는 방법을 보여 줍니다. 기존 메타데이터는 지정된 값으로 *대체*됩니다.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>레코드 집합 삭제

`az network dns record-set <record-type> delete` 명령을 사용하여 레코드 집합을 삭제할 수 있습니다. 도움말을 보려면 `azure network dns record-set <record-type> delete --help`을 참조하세요. 레코드 집합을 삭제하면 레코드 집합 내에서 모든 레코드가 삭제됩니다.

> [!NOTE]
> 영역 apex(`--name "@"`)에서 SOA 및 NS 레코드 집합을 삭제할 수 없습니다 .  이러한 항목은 영역을 만들 때 자동으로 만들어지고 영역을 삭제할 때 자동으로 삭제됩니다.

다음 예제에서는 *MyResourceGroup* 리소스 그룹의 *contoso.com* 영역에서 *www*라는 A 형식의 레코드 집합을 삭제합니다.

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

삭제 작업을 확인하라는 메시지가 표시됩니다. 이 프롬프트를 표시하지 않으려면 `--yes` 스위치를 사용합니다.

## <a name="next-steps"></a>다음 단계

[Azure DNS의 영역 및 레코드](dns-zones-records.md)에 대해 자세히 알아봅니다.
<br>
Azure DNS를 사용하는 경우 [영역 및 레코드를 보호](dns-protect-zones-recordsets.md)하는 방법에 대해 알아봅니다.
