---
title: Azure 서비스에 대한 역방향 DNS-Azure DNS
description: 이 학습 경로를 사용 하 여 Azure에서 호스트 되는 서비스에 대한 역방향 DNS 조회 구성을 시작 합니다.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932364"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서는 Azure에서 호스트되는 서비스에 대해 역방향 DNS 조회를 구성하는 방법을 설명합니다.

Azure의 서비스는 Azure에서 할당하고 Microsoft가 소유하는 IP 주소를 사용합니다. 이러한 역방향 DNS 레코드(PTR 레코드)는 해당 Microsoft 소유의 역방향 DNS 조회 영역에서 만들어야 합니다. 이 문서에서는 이 방법을 설명합니다.

이 시나리오를 [Azure DNS의 할당된 IP 범위에 대한 역방향 DNS 조회 영역 호스트](dns-reverse-dns-hosting.md) 기능과 혼동하면 안 됩니다. 이 경우 역방향 조회 영역으로 표시되는 IP 범위가 일반적으로 ISP에 의해 조직에 할당되어야 합니다.

이 문서를 읽기 전에 이 [Azure의 역방향 DNS 및 지원 개요](dns-reverse-dns-overview.md)에 익숙해지는 것이 좋습니다.

Azure DNS에서 컴퓨팅 리소스(예: 가상 머신, 가상 머신 확장 집합 또는 Service Fabric 클러스터)는 PublicIpAddress 리소스를 통해 노출됩니다. 역방향 DNS 조회는 PublicIpAddress의 'ReverseFqdn' 속성을 사용하여 구성됩니다.


역방향 DNS는 현재 Azure App Service에 대해 지원되지 않습니다.

## <a name="validation-of-reverse-dns-records"></a>역방향 DNS 레코드의 유효성 검사

타사에서는 DNS 도메인에 대한 Azure 서비스 매핑을 위해 역방향 DNS 레코드를 만들지 않아야 합니다. 이를 방지하기 위해 Azure는 역방향 DNS 레코드에 지정된 도메인 이름이 동일한 Azure 구독의 PublicIpAddress 또는 클라우드 서비스에 대한 DNS 이름 또는 IP 주소와 같거나 이러한 항목으로 확인되는 경우에만 역방향 DNS 레코드 만들기를 허용합니다.

이 유효성 검사는 역방향 DNS 레코드를 설정하거나 수정하는 경우에만 수행됩니다. 유효성 재검사는 정기적으로 수행되지 않습니다.

예를 들어 PublicIpAddress 리소스의 DNS 이름이 contosoapp1.northus.cloudapp.azure.com이고 IP 주소가 23.96.52.53이라고 가정해 보겠습니다. PublicIpAddress에 대한 ReverseFqdn을 다음과 같이 지정할 수 있습니다.
* PublicIpAddress, contosoapp1.northus.cloudapp.azure.com의 DNS 이름
* 같은 구독의 다른 PublicIpAddress에 대한 DNS 이름(예: Contosoapp2.westus.cloudapp.azure.com)
* 베니티 DNS 이름(예: app1.contoso.com). 이 이름은 contosoapp1.northus.cloudapp.azure.com 또는 동일한 구독의 다른 PublicIpAddress에 대한 CNAME으로 구성된 *최초* 이름이어야 합니다.
* 베니티 DNS 이름(예: app1.contoso.com). 이 이름은 IP 주소 23.96.52.53 또는 동일한 구독의 다른 PublicIpAddress에 대한 IP 주소의 A 레코드로 구성된 *최초* 이름이어야 합니다.

동일한 제약 조건이 Cloud Services에 대한 역방향 DNS에도 적용됩니다.


## <a name="reverse-dns-for-publicipaddress-resources"></a>PublicIpAddress 리소스에 대한 역방향 DNS

이 섹션에서는 Azure PowerShell, Azure 클래식 CLI 또는 Azure CLI를 사용하여 Resource Manager 배포 모델에서 PublicIpAddress 리소스에 대한 역방향 DNS를 구성하는 방법에 대한 자세한 지침을 제공합니다. 현재 Azure Portal에서는 PublicIpAddress 리소스에 대한 역방향 DNS를 구성할 수 없습니다.

Azure는 현재 IPv4 PublicIpAddress 리소스에 대해서만 역방향 DNS를 지원합니다. IPv6에 대해서는 지원되지 않습니다.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>기존 PublicIpAddresses에 역방향 DNS 추가

#### <a name="powershell"></a>PowerShell

역방향 DNS를 기존 PublicIpAddress 업데이트 하려면 다음을 수행 합니다.

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

DNS 이름이 아직 없는 기존 PublicIpAddress에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다.

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure 클래식 CLI

기존 PublicIpAddresses에 역방향 DNS를 추가하려면

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

DNS 이름이 아직 없는 기존 PublicIpAddress에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다.

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

기존 PublicIpAddresses에 역방향 DNS를 추가하려면

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

DNS 이름이 아직 없는 기존 PublicIpAddress에 역방향 DNS를 추가하려면 DNS 이름도 지정해야 합니다.

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>역방향 DNS를 사용하여 공용 IP 주소 만들기

역방향 DNS 속성이 이미 지정된 새 PublicIpAddress를 만들려면

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure 클래식 CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>기존 PublicIpAddresses의 역방향 DNS 보기

기존 PublicIpAddress에 대해 구성된 값을 보려면

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure 클래식 CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>기존 공용 IP 주소에서 역방향 DNS 제거

기존 PublicIpAddress에서 역방향 DNS 속성을 제거하려면

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure 클래식 CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Cloud Services에 대한 역방향 DNS 구성

이 섹션에서는 Azure PowerShell을 사용하여 클래식 배포 모델에서 Cloud Services에 대한 역방향 DNS를 구성하는 방법에 대한 자세한 지침을 제공합니다. Azure Portal, Azure 클래식 CLI 또는 Azure CLI를 통해 Cloud Services용 역방향 DNS를 구성할 수는 없습니다.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>기존 Cloud Services에 역방향 DNS 추가

기존 클라우드 서비스에 역방향 DNS 레코드를 추가하려면

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>역방향 DNS를 사용하여 클라우드 서비스 만들기

역방향 DNS 속성이 이미 지정된 새 클라우드 서비스를 만들려면

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>기존 Cloud Services에 대한 역방향 DNS 보기

기존 클라우드 서비스에 대한 역방향 DNS 속성을 보려면

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>기존 Cloud Services에서 역방향 DNS 제거

기존 클라우드 서비스에서 역방향 DNS 속성을 제거하려면

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>역방향 DNS 레코드의 비용은 얼마인가요?

무료입니다.  역방향 DNS 레코드 또는 쿼리에 대한 추가 비용은 없습니다.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>인터넷에서 내 역방향 DNS 레코드가 확인되나요?

예. Azure 서비스에 대해 역방향 DNS 속성을 설정하면 역방향 DNS 레코드를 모든 인터넷 사용자에 대해 확인하는 데 필요한 DNS 영역 및 DNS 위임을 Azure에서 모두 관리합니다.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>내 Azure 서비스에 대해 기본 역방향 DNS 레코드가 생성되나요?

아닙니다. 역방향 DNS는 옵트인(opt in) 기능입니다. 역방향 레코드를 구성하지 않으면 기본 역방향 DNS 레코드가 만들어지지 않습니다.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>FQDN(정규화된 도메인 이름)의 형식은 무엇인가요?

FQDN은 정방향 순서로 지정되고 점으로 끝나야 합니다(예: "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>내가 지정한 역방향 DNS의 유효성 검사가 실패하면 어떻게 되나요?

역방향 DNS 유효성 검사가 실패하는 경우 역방향 DNS 레코드를 구성하는 작업이 실패합니다. 필요에 따라 역방향 DNS 값을 수정하고 다시 시도하세요.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Azure App Service에 대한 역방향 DNS를 구성할 수 있나요?

아닙니다. 역방향 DNS는 Azure App Service에 대해 지원되지 않습니다.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>내 Azure 대해 다중 역방향 DNS 레코드를 구성할 수 있나요?

아닙니다. Azure는 각 Azure Cloud Service 또는 PublicIpAddress에 대해 단일 역방향 DNS 레코드를 지원합니다.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>IPv6 PublicIpAddress 리소스에 대한 역방향 DNS를 구성할 수 있나요?

아닙니다. Azure는 현재 IPv4 PublicIpAddress 리소스 및 Cloud Services에 대해서만 역방향 DNS를 지원합니다.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Azure Compute 서비스에서 외부 도메인으로 이메일을 보낼 수 있나요?

Azure 배포에서 직접 이메일을 전송하는 기술 능력은 구독 유형에 따라 다릅니다. 구독 유형에 관계 없이 Microsoft는 신뢰할 수 있는 메일 릴레이 서비스를 사용하여 나가는 메일을 전송할 것을 권장합니다. 자세한 내용은 [이메일 전송을 위해 향상된 Azure 보안 – 2017년 11월 업데이트](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/)를 참조합니다.

## <a name="next-steps"></a>다음 단계

역방향 DNS에 대한 자세한 내용은 [Wikipedia에서 역방향 DNS 조회](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)를 참조하세요.
<br>
[Azure DNS에서 ISP 할당 IP 범위에 대한 역방향 조회 영역 호스트](dns-reverse-dns-for-azure-services.md) 방법을 알아봅니다.

