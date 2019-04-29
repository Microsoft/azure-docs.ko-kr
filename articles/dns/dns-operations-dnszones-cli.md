---
title: Azure DNS에서 DNS 영역 관리 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 DNS 영역을 관리할 수 있습니다. 이 문서에서는 Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법을 설명합니다.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: df741b34e1268c547723af87401760197d395780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293832"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure DNS에서 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [포털](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


이 가이드는 Windows, Mac 및 Linux에서 사용할 수 있는 플랫폼 간 Azure CLI를 사용하여 DNS 영역을 관리하는 방법을 보여 줍니다. [Azure PowerShell](dns-operations-dnszones.md) 또는 Azure Portal을 사용하여 DNS 영역을 관리할 수도 있습니다.

이 가이드에서는 특히 공용 DNS 영역을 다룹니다. Azure CLI를 사용한 Azure DNS에서 Private Zones 관리에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure DNS Private Zones 시작](private-dns-getstarted-cli.md)을 참조하세요.

## <a name="introduction"></a>소개

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Azure DNS용 Azure CLI 설치

### <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

* Windows, Linux 또는 MAC용 최신 버전의 Azure CLI를 설치합니다. 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-az-cli2)를 참조하세요.

### <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

콘솔 창을 열고 자격 증명을 사용하여 인증합니다. 자세한 내용은 [Azure CLI에서 Azure에 연결](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조하세요.

```
az login
```

### <a name="select-the-subscription"></a>구독 선택

계정에 대한 구독을 확인합니다.

```
az account list
```

사용할 Azure 구독을 선택합니다.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>선택 사항: Azure DNS 사설 영역 기능(공개 미리 보기)을 설치/사용하려면
Azure DNS 사설 영역 기능은 Azure CLI 확장을 통해 공개 미리 보기로 출시되었습니다. “dns” Azure CLI 확장 설치 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>도움말 보기

Azure DNS와 관련된 모든 Azure CLI 명령은 `az network dns`로 시작합니다. `--help` 옵션(약식 `-h`)을 사용하여 각 명령에 대한 도움말을 볼 수 있습니다.  예를 들면 다음과 같습니다.

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 도움말을 보려면 `az network dns zone create -h`을 참조하세요.

다음 예제에서는 *MyResourceGroup*이라는 리소스 그룹에 *contoso.com*이라는 DNS 영역을 만듭니다.

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>태그를 사용하여 DNS 영역을 만들려면

다음 예제에서는 두 [Azure Resource Manager 태그](dns-zones-records.md#tags), *project = demo* 및 *env = test*와 함께 `--tags` 매개 변수(짧은 양식 `-t`)를 사용하여 DNS 영역을 만드는 방법을 보여 줍니다.

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS 영역 가져오기

DNS 영역을 가져오려면 `az network dns zone show`를 사용합니다. 도움말을 보려면 `az network dns zone show --help`을 참조하세요.

다음 예제에서는 DNS 영역 *contoso.com* 및 해당 관련 데이터를 리소스 그룹 *MyResourceGroup*에서 반환합니다. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

다음 예제는 응답입니다.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

DNS 레코드는 `az network dns zone show`에서 반환되지 않습니다. DNS 레코드를 나열하려면 `az network dns record-set list`를 사용합니다.


## <a name="list-dns-zones"></a>DNS 영역 나열

DNS 영역을 열거하려면 `az network dns zone list`를 사용합니다. 도움말을 보려면 `az network dns zone list --help`을 참조하세요.

리소스 그룹을 지정하면 리소스 그룹 내의 해당 영역만 나열합니다.

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

리소스 그룹을 생략하면 구독의 모든 영역이 나열됩니다.

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS 영역 업데이트

`az network dns zone update`를 사용하여 DNS 영역 리소스를 변경할 수 있습니다. 도움말을 보려면 `az network dns zone update --help`을 참조하세요.

이 명령은 영역 내의 DNS 레코드 집합을 업데이트하지 않습니다([DNS 레코드를 관리하는 방법](dns-operations-recordsets-cli.md) 참조). 영역 리소스 자체의 속성을 업데이트하는 데만 사용됩니다. 이러한 속성은 현재 영역 리소스에 대한 [Azure Resource Manager '태그'](dns-zones-records.md#tags)로 제한됩니다.

다음 예제에서는 DNS 영역에서 태그를 업데이트하는 방법을 보여 줍니다. 기존 태그는 지정된 값으로 대체됩니다.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

`az network dns zone delete`를 사용하여 DNS 영역을 삭제할 수 있습니다. 도움말을 보려면 `az network dns zone delete --help`을 참조하세요.

> [!NOTE]
> DNS 영역을 삭제하면 영역 내의 모든 DNS 레코드도 삭제됩니다. 이 작업은 취소할 수 없습니다. DNS 영역을 사용 중인 경우 영역이 삭제되면 영역을 사용하는 서비스가 실패합니다.
>
>실수로 영역이 삭제되는 것을 방지하려면 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요.

이 명령은 확인 메시지를 표시합니다. 선택적 `--yes` 스위치는 이 프롬프트를 표시하지 않습니다.

다음 예제는 리소스 그룹 *MyResourceGroup*에서 *contoso.com* 영역을 삭제하는 방법을 보여 줍니다.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>다음 단계

DNS 영역에서 [레코드 집합 및 레코드 관리](dns-getstarted-create-recordset-cli.md) 방법을 알아봅니다.

[Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.

