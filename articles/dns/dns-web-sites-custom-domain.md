---
title: 자습서 - 웹앱에 대한 사용자 지정 Azure DNS 레코드 만들기
description: 이 자습서에서는 Azure DNS를 사용하여 웹앱에 대한 사용자 지정 도메인 DNS 레코드를 만듭니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 7a250eaeb8ac4d0c8cacfb1748855700c9986bf5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079453"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>자습서: 사용자 지정 도메인에 웹앱에 대한 DNS 레코드 만들기 

웹앱에 대한 사용자 지정 도메인을 호스트하도록 Azure DNS를 구성할 수 있습니다. 예를 들어 Azure 웹앱을 만들고 FQDN(정규화된 도메인 이름)으로 www\. contoso.com 또는 contoso.com을 사용하여 사용자가 액세스하도록 할 수 있습니다.

> [!NOTE]
> Contoso.com은 이 자습서 전체에서 예로 사용됩니다. Contoso.com을 고유한 도메인 이름으로 바꾸세요.

바꾸려면 다음과 같은 세 가지 레코드를 만들어야 합니다.

* contoso.com을 가리키는 루트 "A" 레코드
* 확인을 위한 루트 "TXT" 레코드
* A 레코드를 가리키는 www 이름에 대한 "CNAME" 레코드

Azure에서 웹앱에 대한 A 레코드를 만드는 경우 웹앱의 기본 IP 주소가 변경되면 A 레코드를 수동으로 업데이트해야 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 도메인에 대한 A 및 TXT 레코드 만들기
> * 사용자 지정 도메인에 대한 CNAME 레코드 만들기
> * 새 레코드 테스트
> * 웹앱에 사용자 지정 호스트 이름 추가
> * 사용자 지정 호스트 이름 테스트


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure DNS에서 호스트할 수 있는 도메인 이름으로 테스트할 수 있어야 합니다. 이 도메인에 대한 전체 제어 권한이 있어야 합니다. 전체 제어 권한에는 도메인의 NS(이름 서버) 레코드를 설정하는 권한이 포함됩니다.
* [App Service 앱을 만들거나](../app-service/quickstart-html.md) 다른 자습서에서 만든 앱을 사용합니다.

* Azure DNS에서 DNS 영역을 만들고 등록 기관의 영역을 Azure DNS로 위임합니다.

   1. DNS 영역을 만들려면 [DNS 영역 만들기](dns-getstarted-create-dnszone.md)의 단계를 수행합니다.
   2. 영역을 Azure DNS로 위임하려면 [DNS 도메인 위임](dns-delegate-domain-azure-dns.md)의 단계를 수행합니다.

영역을 만들어서 Azure DNS에 위임한 후에는, 사용자 지정 도메인에 대한 레코드를 만들 수 있습니다.

## <a name="create-an-a-record-and-txt-record"></a>A 레코드 및 TXT 레코드 만들기

A 레코드는 이름을 해당 IP 주소에 매핑하는 데 사용됩니다. 다음 예제에서는 웹앱 IPv4 주소를 사용하여 "\@"을 A 레코드로 할당합니다. \@은 일반적으로 루트 도메인을 나타냅니다.

### <a name="get-the-ipv4-address"></a>IPv4 주소 가져오기

Azure Portal의 App Services 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

![사용자 지정 도메인 메뉴](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인** 페이지에서 앱의 IPv4 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>A 레코드 만들기

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>TXT 레코드 만들기

App Services는 구성 시에만 이 레코드를 사용하여 사용자 지정 도메인을 소유하고 있는지 확인합니다. App Service에서 사용자 지정 도메인의 유효성을 검사하고 구성한 후 이 TXT 레코드를 삭제할 수 있습니다.

> [!NOTE]
> 도메인 이름을 확인하고 프로덕션 트래픽을 웹앱으로 라우팅하지 않으려면 확인 단계에 대한 TXT 레코드만 지정하면 됩니다.  확인에는 TXT 레코드 외에 A 또는 CNAME 레코드가 필요하지 않습니다.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>CNAME 레코드 만들기

Azure DNS에서 이미 도메인을 관리하고 있는 경우( [DNS 도메인 위임](dns-domain-delegation.md)참조), 다음 예제를 사용하여 contoso.azurewebsites.net에 대한 CNAME 레코드를 만들 수 있습니다.

Azure PowerShell을 열고 새 CNAME 레코드를 만듭니다. 이 예제에서는 웹앱 contoso.azurewebsites.net에 대한 별칭을 사용하여 "contoso.com"이라는 DNS 영역에 "TTL(Time to Live)" 600초인 레코드 집합 형식 CNAME을 만듭니다.

### <a name="create-the-record"></a>레코드 만들기

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

다음 예제는 응답입니다.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>새 레코드 테스트

아래와 같이 nslookup으로 "www.contoso.com" 및 "contoso.com"을 쿼리하여 레코드가 올바르게 생성되었는지 확인할 수 있습니다.

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>사용자 지정 호스트 이름 추가

이제 웹앱에 사용자 지정 호스트 이름을 추가할 수 있습니다.

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>사용자 지정 호스트 이름 테스트

브라우저를 열고 `http://www.<your domainname>` 및 `http://<you domain name>`으로 이동합니다.

> [!NOTE]
> `http://` 접두사가 포함되었는지 확인합니다. 포함되지 않으면 브라우저가 URL을 예측하려고 시도할 수 있습니다.

두 URL이 동일한 페이지를 표시할 것입니다. 다음은 그 예입니다.

![Contoso 앱 서비스](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스가 더 이상 필요 없다면 **myresourcegroup** 리소스 그룹을 삭제해도 됩니다.

## <a name="next-steps"></a>다음 단계

Azure DNS 프라이빗 영역을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure DNS 프라이빗 영역 시작](private-dns-getstarted-powershell.md)
