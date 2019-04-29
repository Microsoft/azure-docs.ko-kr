---
title: Azure CLI를 사용하여 도메인 영역 파일을 Azure DNS에 가져오기 및 내보내기 | Microsoft Docs
description: Azure CLI를 사용하여 Azure DNS에 DNS 영역 파일을 가져오고 내보내는 방법을 알아봅니다
services: dns
author: WenJason
ms.service: dns
origin.date: 4/3/2019
ms.date: 04/15/2019
ms.author: v-jay
ms.openlocfilehash: 25445415141372e1f231549c5b8f8575a89363c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293079"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Azure CLI를 사용하여 DNS 영역 파일 가져오기 및 내보내기

이 문서에서는 Azure CLI를 사용하여 Azure DNS에 대한 DNS 영역 파일 가져오고 내보내는 과정을 설명합니다.

## <a name="introduction-to-dns-zone-migration"></a>DNS 영역 마이그레이션 소개

DNS 영역 파일은 영역의 모든 DNS(도메인 이름 시스템) 레코드의 세부 정보를 포함하는 텍스트 파일입니다. 표준 형식을 따르며 이는 DNS 시스템 간에 DNS 레코드를 전송하는 데 적합하도록 만듭니다. 영역 파일을 사용하는 작업은 DNS 영역을 Azure DNS으로 전송할 수 있는 신뢰할 수 있는 빠르고 편리한 방법입니다.

Azure DNS는 Azure CLI(명령줄 인터페이스I)를 사용하여 영역 파일 가져오기 및 내보내기를 지원합니다. 영역 파일 가져오기는 현재 Azure PowerShell 또는 Azure Portal을 통해 지원되지 **않습니다**.

Azure CLI는 Azure 서비스를 관리하는 데 사용하는 플랫폼 간 명령줄 도구입니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 다운로드하여 Windows, Mac 및 Linux 플랫폼에 사용할 수 있습니다. 가장 일반적인 이름 서버 소프트웨어인 [BIND](https://www.isc.org/downloads/bind/)는 일반적으로 Linux에서 실행하기 때문에 플랫폼 간 지원은 영역 파일 가져오기 및 내보내기에 중요합니다.

## <a name="obtain-your-existing-dns-zone-file"></a>기존 DNS 영역 파일 가져오기

Azure DNS에 DNS 영역 파일을 가져오기 전에 영역 파일의 복사본을 가져와야 합니다. 이 파일의 원본은 DNS 영역이 현재 호스팅되는 위치에 따라 달라집니다.

* DNS 영역이 파트너 서비스에서 호스팅되는 경우(예: 도메인 등록자, 전용 DNS 호스팅 공급자 또는 다른 클라우드 공급자) 해당 서비스는 DNS 영역 파일을 다운로드하는 기능을 제공해야 합니다.
* 사용자의 DNS 영역이 Windows DNS에서 호스팅되는 경우 영역 파일의 기본 폴더는 **%systemroot%\system32\dns**입니다. 또한 각 영역 파일의 전체 경로는 DNS 콘솔의 **일반** 탭에 표시됩니다.
* DNS 영역이 BIND를 사용하여 호스팅되는 경우 각 영역에 대한 영역 파일의 위치는 바인딩 구성 파일 **'named.conf'** 에 지정됩니다.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Azure DNS에 DNS 영역 파일 가져오기

영역이 아직 없는 경우 영역 파일을 가져오면 Azure DNS에서 새 영역을 만듭니다. 영역이 이미 있는 경우 영역 파일의 레코드 집합은 기존 레코드 집합으로 병합되어야 합니다.

### <a name="merge-behavior"></a>병합 동작

* 기존 및 새 레코드 집합은 기본적으로 병합됩니다. 병합된 레코드 집합 내의 동일한 레코드는 중복을 제거합니다.
* 레코드 집합을 병합하는 경우 기존 레코드 집합의 TTL(time to live)이 사용됩니다.
* SOA(Start of Authority) 매개 변수(`host` 제외)는 항상 가져온 영역 파일에서 가져옵니다. 마찬가지로 영역 광선의 이름 서버 레코드 집합의 경우 TTL은 항상 가져온 영역 파일에서 가져옵니다.
* 가져온 CNAME 레코드는 기존 CNAME 레코드를 동일한 이름으로 바꾸지 않습니다.  
* CNAME 레코드와 이름은 같지만 형식이 다른 레코드 간에 충돌이 발생할 경우(기존 또는 새로 만든 것에 관계 없이) 기존 레코드가 보존됩니다. 

### <a name="additional-information-about-importing"></a>가져오기에 대한 추가 정보

다음 정보는 영역 가져오기 프로세스에 대한 추가 기술 세부 정보를 제공합니다.

* `$TTL` 지시어는 선택적이며 지원됩니다. `$TTL` 지시어를 지정하지 않는 경우 기본 TTL 3600초로 설정하고 명시적 TTL이 없는 레코드를 가져올 수 있습니다. 동일한 레코드 집합의 두 레코드가 다른 TTL을 지정하는 경우 낮은 값이 사용됩니다.
* `$ORIGIN` 지시어는 선택적이며 지원됩니다. `$ORIGIN` 을(를) 설정하지 않는 경우 사용된 기본 값은 명령줄에 지정된 영역 이름입니다(그리고 종료하는 ".").
* `$INCLUDE` 및 `$GENERATE` 지시어는 지원되지 않습니다.
* A, AAAA, CAA, CNAME, MX, NS, SOA, SRV 및 TXT 형식의 레코드가 지원됩니다.
* SOA 레코드는 영역이 만들어질 때 Azure DNS에서 자동으로 생성됩니다. 영역 파일을 가져오는 경우 `host` 매개 변수를 *제외한* 모든 SOA 매개 변수는 영역 파일에서 가져옵니다. 이 매개 변수는 Azure DNS에서 제공 되는 값을 사용합니다. 이 매개 변수가 Azure DNS에서 제공하는 기본 이름 서버를 참조해야 하기 때문입니다.
* 또한 영역을 만들 때 역영 광선의 이름 서버 레코드 집합은 Azure DNS에서 자동으로 만들어집니다. 이 레코드 집합의 TTL만을 가져옵니다. 이러한 레코드는 Azure DNS에서 제공하는 이름 서버 이름을 포함합니다. 레코드 데이터를 가져온 영역 파일에 포함된 값으로 덮어쓰지 않습니다.
* 공개 미리 보기 중에 Azure DNS는 단일 문자 TXT 레코드만 지원합니다. 다중 문자열 TXT 레코드는 연결되어 255자로 제한됩니다.

### <a name="cli-format-and-values"></a>CLI 형식 및 값

DNS 영역을 가져오는 Azure CLI 명령 형식은 다음과 같습니다.

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

값

* `<resource group>` 은(는) Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
* `<zone name>` 은(는) 영역의 이름입니다.
* `<zone file name>` 은(는) 가져올 영역 파일의 경로/이름입니다.

이 이름이 있는 영역이 리소스 그룹에 없는 경우 생성됩니다. 영역이 이미 있는 경우 가져온 레코드 집합은 기존 레코드 집합으로 병합됩니다. 

### <a name="step-1-import-a-zone-file"></a>1단계. 영역 파일 가져오기

**contoso.com**영역에 대한 영역 파일을 가져오려면

1. Resource Manager 리소스 그룹이 없는 경우 해당 리소스 그룹을 만들어야 할 수도 있습니다.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. **contoso.com.txt** 파일에서 **myresourcegroup** 리소스 그룹의 새 DNS 영역으로 **contoso.com** 영역을 가져오려면 `az network dns zone import` 명령을 실행합니다.<BR>이 명령은 영역 파일을 로드하여 구문을 분석합니다. 이 명령은 Azure DNS 서비스에서 일련의 명령을 실행하여 영역 및 영역의 모든 레코드 집합을 만듭니다. 이 명령은 모든 오류 또는 경고뿐만 아니라 콘솔 창에 진행률도 보고합니다. 레코드 집합이 계열에 만들어지기 때문에 큰 영역 파일을 가져오는 데 몇 분 정도 걸릴 수 있습니다.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>2단계. 영역 유효성 검사

파일을 가져온 후에 DNS 영역의 유효성을 검사하기 위해서, 다음 방법 중 하나를 사용할 수 있습니다.

* 다음 Azure CLI 명령을 사용하여 레코드를 나열할 수 있습니다.

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Azure CLI 명령 `az network dns record-set ns list`를 사용하여 레코드를 나열할 수 있습니다.
* `nslookup` 을 사용하여 레코드에 대한 이름 확인의 유효성을 검사할 수 있습니다. 영역이 아직 위임되지 않았기 때문에 올바른 Azure DNS 이름 서버를 명시적으로 지정해야 합니다. 다음 샘플은 영역에 할당된 이름 서버 이름을 검색하는 방법을 보여 줍니다. 또한 `nslookup`을 사용하여 "www" 레코드를 쿼리하는 방법을 보여줍니다.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.cn."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.cn."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.cn."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.cn."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.cn

        Server: ns1-01.azure-dns.cn
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>3단계. DNS 위임 업데이트

영역을 올바르게 가져왔는지 확인한 후 Azure DNS 이름 서버를 가리키도록 DNS 위임을 업데이트해야 합니다. 자세한 내용은 [DNS 위임 업데이트](dns-domain-delegation.md)문서를 참조하세요.

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS에서 DNS 영역 파일 내보내기

DNS 영역을 가져오는 Azure CLI 명령 형식은 다음과 같습니다.

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

값

* `<resource group>` 은(는) Azure DNS의 영역에 대한 리소스 그룹의 이름입니다.
* `<zone name>` 은(는) 영역의 이름입니다.
* `<zone file name>` 은(는) 내보낼 영역 파일의 경로/이름입니다.

영역 가져오기와 마찬가지로 먼저 로그인하고 구독을 선택한 다음 리소스 관리자 모드를 사용하도록 Azure CLI를 구성해야 합니다.

### <a name="to-export-a-zone-file"></a>영역 파일을 내보내려면

**myresourcegroup** 리소스 그룹의 기존 **contoso.com** Azure DNS 영역을 현재 폴더의 **contoso.com.txt** 파일로 내보내려면 `azure network dns zone export` 명령을 실행합니다. 이 명령은 Azure DNS 서비스를 호출하여 영역에서 레코드 집합을 열거하고 BIND와 호환 가능한 영역 파일에 결과를 내보냅니다.

```
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>다음 단계

* DNS 영역에서 [레코드 집합 및 레코드 관리](dns-getstarted-create-recordset-cli.md) 방법을 알아봅니다.

* [Azure DNS에 도메인을 위임](dns-domain-delegation.md)하는 방법을 알아봅니다.
