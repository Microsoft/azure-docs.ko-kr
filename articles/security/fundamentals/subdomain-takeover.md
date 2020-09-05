---
title: Azure DNS 별칭 레코드 및 Azure App Service의 사용자 지정 도메인 확인을 사용 하 여 하위 도메인 takeovers 방지
description: 하위 도메인 인수의 일반적인 높은 심각도 위협을 방지 하는 방법을 알아봅니다.
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890827"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>현 수 DNS 항목을 방지 하 고 하위 도메인 인수 방지

이 문서에서는 하위 도메인 인수의 일반적인 보안 위협에 대해 설명 하 고이를 완화 하기 위해 수행할 수 있는 단계를 설명 합니다.


## <a name="what-is-subdomain-takeover"></a>하위 도메인 인수 무엇 인가요?

하위 도메인 takeovers은 정기적으로 많은 리소스를 만들고 삭제 하는 조직에 대 한 일반적이 고 높은 심각도 위협입니다. 하위 도메인 인수는 프로 비전 해제 Azure 리소스를 가리키는 DNS 레코드가 있는 경우에 발생할 수 있습니다. 이러한 DNS 레코드는 "현 수 DNS" 항목으로도 알려져 있습니다. CNAME 레코드는 특히이 위협에 취약 합니다.

하위 도메인 인수 일반적인 시나리오는 다음과 같습니다.

1. 웹 사이트가 만들어집니다. 

    이 예제에서 해당 이름은 `app-contogreat-dev-001.azurewebsites.net`입니다.

1. CNAME 항목이 웹 사이트를 가리키는 DNS에 추가 됩니다. 

    이 예제에서는 다음과 같은 이름을 `greatapp.contoso.com` 만들었습니다.

1. 몇 개월 후에는 해당 DNS 항목을 삭제 **하지 않고** 사이트가 더 이상 필요 하지 않습니다. 

    CNAME DNS 항목은 이제 "현 수"입니다.

1. 사이트를 삭제 한 직후에는 위협 행위자가 누락 된 사이트를 검색 하 고에서 자체 웹 사이트를 만듭니다 `app-contogreat-dev-001.azurewebsites.net` .

    이제에 사용 되는 트래픽은 `greatapp.contoso.com` 위협 행위자의 Azure 사이트로 이동 하 고, 위협 행위자는 표시 되는 콘텐츠를 제어 합니다. 

    현 수 DNS가 악용 되었으며 Contoso의 하위 도메인 "GreatApp"가 하위 도메인 인수의 공격을 받았습니다. 

![프로 비전 해제 웹 사이트에서 하위 도메인 인수](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>하위 도메인의 위험 인수

DNS 레코드를 사용할 수 없는 리소스를 가리키는 경우에는 DNS 영역에서 레코드 자체가 제거 되어야 합니다. 삭제 되지 않은 경우 "현 DNS" 레코드 이며 하위 도메인 인수의 가능성을 만듭니다.

현 수 DNS 항목을 통해 위협 행위자는 악성 웹 사이트 또는 서비스를 호스트 하기 위해 연결 된 DNS 이름을 제어할 수 있습니다. 조직의 하위 도메인에 있는 악의적인 페이지와 서비스는 다음과 같은 결과를 얻을 수 있습니다.

- 하위 **도메인의 콘텐츠에 대 한 제어 손실** -부정은 조직의 콘텐츠를 보호할 수 없는 기능 뿐만 아니라 브랜드 손상과 신뢰의 손실을 방지 합니다.

- **의심 되는 방문자 로부터 쿠키를 수집** 합니다. 웹 앱은 세션 쿠키를 하위 도메인 (*. contoso.com)에 노출 하는 것이 일반적 이므로 모든 하위 도메인에서 액세스할 수 있습니다. 위협 행위자는 하위 도메인 인수를 사용 하 여 인증 된 페이지를 빌드하고, 사용자가 방문 하 여 방문 하 고, 쿠키 (보안 쿠키)를 수집할 수 있습니다. 일반적인 오해 SSL 인증서를 사용 하 여 사이트 및 사용자의 쿠키를 인수에서 보호 한다는 것입니다. 그러나 위협 행위자는 하이재킹 된 하위 도메인을 사용 하 여 유효한 SSL 인증서를 적용 하 고 받을 수 있습니다. 유효한 SSL 인증서를 통해 보안 쿠키에 대 한 액세스 권한을 부여 하 고 악의적인 사이트의 인식 된 적법성을 더욱 늘릴 수 있습니다.

- **피싱 캠페인** -인증-하위 도메인을 피싱 캠페인에서 사용할 수 있습니다. 악의적인 사이트와 위협 행위자가 알려진 안전한 브랜드의 합법적인 하위 도메인으로 주소가 지정 된 메일을 받을 수 있도록 허용 하는 MX 레코드의 경우에도 마찬가지입니다.

- **추가 위험** -악성 사이트를 사용 하 여 XSS, CSRF, CORS 바이패스 등의 다른 클래식 공격으로 에스컬레이션 할 수 있습니다.



## <a name="preventing-dangling-dns-entries"></a>현 수 DNS 항목 방지

조직에서 현 수 DNS 항목을 방지 하는 프로세스를 구현 하 고 결과 하위 도메인 takeovers은 보안 프로그램의 중요 한 부분입니다.

현재 사용할 수 있는 예방 조치는 아래에 나열 되어 있습니다.


### <a name="use-azure-dns-alias-records"></a>Azure DNS 별칭 레코드 사용

Azure DNS의 [별칭 레코드](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) 는 DNS 레코드의 수명 주기를 Azure 리소스와 결합 하 여 현 수 참조를 방지할 수 있습니다. 공용 IP 주소 또는 Traffic Manager 프로필을 가리키는 별칭 레코드로 정규화된 DNS 레코드를 예로 들어보겠습니다. 이러한 기본 리소스를 삭제 하면 DNS 별칭 레코드가 빈 레코드 집합이 됩니다. 삭제 된 리소스를 더 이상 참조 하지 않습니다. 별칭 레코드로 보호할 수 있는 항목에는 제한이 있다는 점에 유의 해야 합니다. 현재이 목록은 다음으로 제한 됩니다.

- Azure Front Door
- Traffic Manager 프로필
- CDN (Azure Content Delivery Network) 끝점
- 공용 IP

현재 제한 된 서비스 제공에도 불구 하 고 가능 하면 항상 하위 도메인에 대해 인수 하는 별칭 레코드를 사용 하는 것이 좋습니다.

Azure DNS의 별칭 레코드 기능에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) .



### <a name="use-azure-app-services-custom-domain-verification"></a>Azure App Service의 사용자 지정 도메인 확인 사용

Azure App Service에 대 한 DNS 항목을 만들 때 asuid를 만듭니다. 도메인이 도메인 확인 ID를 사용 하는 TXT 레코드입니다. 이러한 TXT 레코드가 있는 경우 다른 Azure 구독은 사용자 지정 도메인의 유효성을 검사할 수 없습니다. 

이러한 레코드는 다른 사용자가 CNAME 항목에 있는 것과 동일한 이름을 사용 하 여 Azure App Service를 만들 수 있도록 방지 하지 않습니다. 도메인 이름의 소유권을 증명 하는 기능이 없으면 위협 행위자는 트래픽을 수신 하거나 콘텐츠를 제어할 수 없습니다.

Azure App Service에 기존 사용자 지정 DNS 이름을 매핑하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) .



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>위협을 완화 하는 프로세스 빌드 및 자동화

일반적으로는 현 수의 DNS 위협을 방지 하기 위해 정리 프로세스를 실행 하는 개발자 및 운영 팀이 될 것입니다. 아래의 방법으로 조직에서이 위협 으로부터 발생 하지 않도록 하는 데 도움이 됩니다. 

- **예방 절차 만들기:**

    - 리소스를 삭제할 때마다 응용 프로그램 개발자에 게 주소를 다시 라우팅하는 것을 교육 합니다.

    - 서비스의 서비스를 해제할 때 필수 검사 목록에 "DNS 항목 제거"를 입력 합니다.

    - 사용자 지정 DNS 항목이 있는 모든 리소스에 대 한 [삭제 잠금을](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) 설정 합니다. 삭제 잠금은 리소스가 프로 비전 해제 되기 전에 매핑을 제거 해야 함을 나타내는 표시기 역할을 합니다. 이와 같은 측정값은 내부 교육 프로그램과 함께 사용할 경우에만 작동할 수 있습니다.

- **검색을 위한 프로시저 만들기:**

    - DNS 레코드를 정기적으로 검토 하 여 하위 도메인이 모두 Azure 리소스에 매핑되는지 확인 합니다.

        - 존재-Azure 하위 도메인 (예: *. azurewebsites.net 또는 *. cloudapp.azure.com)을 가리키는 리소스에 대 한 DNS 영역을 쿼리 합니다 ( [이 참조 목록](azure-domains.md)참조).
        - 사용자는 DNS 하위 도메인에서 대상으로 하는 모든 리소스를 소유 하 고 있는지 확인 합니다.

    - Azure FQDN (정규화 된 도메인 이름) 끝점과 응용 프로그램 소유자의 서비스 카탈로그를 유지 관리 합니다. 서비스 카탈로그를 빌드하려면 다음 Azure 리소스 그래프 쿼리 스크립트를 실행 합니다. 이 스크립트는 액세스할 수 있는 리소스의 FQDN 끝점 정보를 프로젝트 하 고 CSV 파일로 출력 합니다. 테 넌 트에 대 한 모든 구독에 대 한 액세스 권한이 있는 경우 스크립트는 다음 샘플 스크립트와 같이 모든 해당 구독을 고려 합니다. 결과를 특정 구독 집합으로 제한 하려면 스크립트를 다음과 같이 편집 합니다.

        >[!IMPORTANT]
        > **권한** -모든 Azure 구독에 대 한 액세스 권한이 있는 사용자로 쿼리를 실행 합니다. 
        >
        > **제한 사항** -Azure 리소스 그래프에는 azure 환경이 클 경우 고려해 야 하는 제한 및 페이징 제한이 있습니다. Azure 리소스 데이터 집합을 사용 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) . 다음 샘플 스크립트는 구독 일괄 처리를 사용 하 여 이러한 제한 사항을 방지 합니다.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        `FQDNProperty`이전 리소스 그래프 쿼리에 지정 된 형식 및 해당 값의 목록입니다.

        |리소스 이름  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|속성. cName|
        |Azure Blob Storage|microsoft.storage/storageaccounts|속성. primaryEndpoints. blob|
        |Azure CDN|microsoft.cdn/profiles/endpoints|속성. 호스트 이름|
        |공용 IP 주소|microsoft.network/publicipaddresses|dnsSettings. fqdn|
        |Azure Traffic Manager|microsoft.network/trafficmanagerprofiles|속성. dnsConfig. fqdn|
        |Azure Container Instance|microsoft.containerinstance/containergroups|속성. ipAddress. fqdn|
        |Azure API Management|microsoft.apimanagement/service|hostnameConfigurations. hostName|
        |Azure App Service|microsoft.web/sites|defaultHostName|
        |Azure App Service-슬롯|microsoft.web/sites/slots|defaultHostName|


- **수정 절차 만들기:**
    - 현 수 DNS 항목이 발견 되 면 팀에서 손상이 발생 했는지 여부를 조사 해야 합니다.
    - 리소스를 서비스 해제할 때 주소가 다시 라우팅되지 않는 이유를 조사 합니다.
    - 더 이상 사용 하지 않는 경우 DNS 레코드를 삭제 하거나 조직에서 소유한 올바른 Azure 리소스 (FQDN)를 가리킵니다.
 

## <a name="next-steps"></a>다음 단계

하위 도메인 인수 대해 보호 하는 데 사용할 수 있는 관련 서비스 및 Azure 기능에 대해 자세히 알아보려면 다음 페이지를 참조 하세요.

- [Azure DNS 사용자 지정 도메인에 대 한 별칭 레코드 사용 지원](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Azure App Service에서 사용자 지정 도메인을 추가할 때 도메인 확인 ID 사용](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [빠른 시작: Azure PowerShell을 사용하여 첫 번째 Resource Graph 쿼리 실행](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
