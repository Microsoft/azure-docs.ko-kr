---
title: Azure Instance Metadata Service | Microsoft Docs
description: Windows VM의 계산, 네트워크 및 예정된 유지 관리 이벤트에 대한 정보를 가져오는 RESTful 인터페이스입니다.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: cc2a78dd547681a4b20fea14cd8cd7f4fd9c2df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711271"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Azure Instance Metadata Service는 가상 머신을 관리 및 구성하는 데 사용할 수 있는 가상 머신 인스턴스를 실행하는 방법에 대한 정보를 제공합니다.
여기에는 SKU, 네트워크 구성, 예정된 유지 관리 이벤트 등의 정보가 포함됩니다. 사용 가능한 정보 유형에 대한 자세한 내용은 [메타데이터 범주](#instance-metadata-data-categories)를 참조하세요.

Azure의 Instance Metadata Service는 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)를 통해 생성된 모든 IaaS VM에 액세스할 수 있는 REST 엔드포인트입니다.
이 엔드포인트는 VM 내부에서만 액세스할 수 있는 잘 알려진 라우팅이 불가능한 IP 주소(`169.254.169.254`) 에서 사용할 수 있습니다.

> [!IMPORTANT]
> 이 서비스는 모든 Azure 지역에서 **일반 공급**됩니다.  정기적으로 업데이트를 받아 가상 컴퓨터 인스턴스에 대한 새 정보를 노출합니다. 이 페이지에는 사용 가능한 최신 [데이터 범주](#instance-metadata-data-categories)가 반영되어 있습니다.

## <a name="service-availability"></a>서비스 가용성

이 서비스는 일반 공급되는 Azure 지역에서 사용할 수 있습니다. 모든 API 버전을 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다.

영역                                        | 가용성                                 | 지원되는 버전
-----------------------------------------------|-----------------------------------------------|-----------------
[일반 공급되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/)     | 일반 공급   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 일반 공급 | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure 중국](https://www.azure.cn/)                                                           | 일반 공급 | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure 독일](https://azure.microsoft.com/overview/clouds/germany/)                    | 일반 공급 | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01

이 표는 서비스 업데이트가 있거나 지원되는 새 버전을 사용할 수 있을 때 업데이트됩니다.

Instance Metadata Service를 평가하려면 위 지역의 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 또는 [Azure Portal](https://portal.azure.com)에서 VM을 만들고 아래 예제를 따릅니다.

## <a name="usage"></a>사용

### <a name="versioning"></a>버전 관리

인스턴스 메타데이터 서비스에는 버전이 있습니다. 버전은 필수이며 글로벌 Azure에서 최신 버전은 `2018-10-01`입니다. 현재 지원되는 버전은 (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01)입니다.

새로운 버전이 추가되더라도 스크립트가 특정 데이터 형식에 종속성이 있는 경우 이전 버전에 여전히 액세스할 수 있습니다.

버전이 지정 된 경우 지원 되는 최신 버전의 목록을 사용 하 여 오류가 반환 됩니다.

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**응답**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>헤더 사용

Instance Metadata Service를 쿼리할 때 요청이 실수로 리디렉션되지 않도록 `Metadata: true` 헤더를 제공해야 합니다.

### <a name="retrieving-metadata"></a>메타데이터 검색

인스턴스 메타데이터는 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)를 사용하여 생성/관리되는 VM을 실행하는 데 사용할 수 있습니다. 다음 요청을 사용하여 가상 머신 인스턴스의 모든 데이터 범주에 액세스합니다.

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> 모든 인스턴스 메타데이터 쿼리는 대/소문자를 구분합니다.

### <a name="data-output"></a>데이터 출력

기본적으로 Instance Metadata Service는 JSON 형식(`Content-Type: application/json`)의 데이터를 반환합니다. 그러나 다른 API는 요청된 경우 다른 형식으로 데이터를 반환합니다.
다음 표는 API에서 지원할 수 있는 다른 데이터 형식에 대한 참조입니다.

API | 기본 데이터 형식 | 다른 형식
--------|---------------------|--------------
/instance | json : | 텍스트
/scheduledevents | json : | 없음
/attested | json : | 없음

기본이 아닌 응답 형식에 액세스하려면 요청된 형식을 요청의 쿼리 문자열 매개 변수로 지정합니다. 예를 들면 다음과 같습니다.

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 리프 노드는 `format=json` 작동 하지 않습니다. 이러한 쿼리에 대해 `format=text` 기본 형식은 json 경우 명시적으로 지정 해야 합니다.

### <a name="security"></a>보안

Instance Metadata Service 엔드포인트는 라우팅이 불가능한 IP 주소에서 실행 중인 가상 머신 인스턴스 내부에서만 액세스할 수 있습니다. 또한 `X-Forwarded-For` 헤더가 포함된 모든 요청은 서비스에 의해 거부됩니다.
실제 요청이 의도치 않은 리디렉션의 일환이 아니라 직접적으로 의도된 것이라는 것을 확인하기 위해 요청에 `Metadata: true` 헤더가 포함되어야 합니다.

### <a name="error"></a>오류

찾을 수 없는 데이터 요소 또는 형식이 잘못된 요청이 있으면 Instance Metadata Service는 표준 HTTP 오류를 반환합니다. 예:

HTTP 상태 코드 | 이유
----------------|-------
200 정상 |
400 잘못된 요청 | 누락 된 `Metadata: true` 헤더 또는 리프 노드를 쿼리할 때 형식을 누락
404 찾을 수 없음 | 요청된 요소가 없음
405 메서드를 사용할 수 없음 | `GET` 및 `POST` 요청만 지원됨
429 요청이 너무 많음 | API는 현재 초당 최대 5개의 쿼리를 지원함
500 서비스 오류     | 잠시 후 다시 시도하세요.

### <a name="examples"></a>예

> [!NOTE]
> 모든 API 응답은 JSON 문자열입니다. 모든 다음 예제 응답은 가독성을 위해 적절히 인쇄 되었습니다.

#### <a name="retrieving-network-information"></a>네트워크 정보 검색

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>공용 IP 주소 검색

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>인스턴스에 대한 모든 메타데이터 검색

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows Virtual Machine에서 메타데이터 검색

**요청**

인스턴스 메타데이터는 `curl` 프로그램을 통해 Windows에서 검색할 수 있습니다.

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

또는 `Invoke-RestMethod` PowerShell cmdlet을 통해:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>인스턴스 메타데이터 데이터 범주

다음 데이터 범주는 Instance Metadata Service를 통해 사용할 수 있습니다.

Data | 설명 | 도입된 버전
-----|-------------|-----------------------
azEnvironment | VM에서 실행 하는 azure 환경 | 2018-10-01
location | VM을 실행하는 Azure 지역 | 2017-04-02
이름 | VM의 이름 | 2017-04-02
제품 | VM 이미지에 대한 정보를 제공합니다. 이 값은 Azure 이미지 갤러리에서 배포된 이미지에 대해서만 표시됩니다. | 2017-04-02
publisher | VM 이미지 게시자 | 2017-04-02
sku | VM 이미지에 해당하는 SKU | 2017-04-02
버전 | VM 이미지의 버전 | 2017-04-02
osType | Linux 또는or Windows | 2017-04-02
platformUpdateDomain |  VM을 실행 중인 [업데이트 도메인](manage-availability.md) | 2017-04-02
platformFaultDomain | VM을 실행 중인 [장애 도메인](manage-availability.md) | 2017-04-02
vmId | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmSize | [VM 크기](sizes.md) | 2017-04-02
subscriptionId | Virtual Machine에 대한 Azure 구독 | 2017-08-01
tags | Virtual Machine에 대한 [태그](../../azure-resource-manager/resource-group-using-tags.md)  | 2017-08-01
resourceGroupName | Virtual Machine에 대한 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
placementGroupId | 가상 머신 확장 집합의 [배치 그룹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
계획 | [계획](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) 에서 VM에 대 한 해당 Azure Marketplace 이미지를 이름, 제품 및 게시자를 포함 합니다. | 2018-04-02
provider | VM의 공급자 | 2018-10-01
publicKeys | [공개 키의 컬렉션](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) VM 및 경로에 할당 | 2018-04-02
vmScaleSetName | 가상 머신 확장 집합의 [Virtual Machine ScaleSet 이름](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
영역 | 가상 머신의 [가용성 영역](../../availability-zones/az-overview.md) | 2017-12-01
ipv4/privateIpAddress | VM의 로컬 IPv4 주소 | 2017-04-02
ipv4/publicIpAddress | VM의 공용 IPv4 주소 | 2017-04-02
subnet/address | VM의 서브넷 주소 | 2017-04-02
subnet/prefix | 서브넷 접두사, 예:24 | 2017-04-02
ipv6/ipaddress | VM의 로컬 IPv6 주소 | 2017-04-02
macAddress | VM MAC 주소 | 2017-04-02
scheduledevents | [예정된 이벤트](scheduled-events.md) 참조 | 2017-08-01
ID | Azure 리소스에 대한 관리 ID입니다. [액세스 토큰 획득](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요. | 2018-02-01
attested | [증명된 데이터](#attested-data) 참조 | 2018-10-01

## <a name="attested-data"></a>증명된 데이터

인스턴스 메타데이터는 169.254.169.254의 http 엔드포인트에서 응답합니다. 인스턴스 메타데이터 서비스에서 처리하는 시나리오 부분은 응답된 데이터가 Azure에서 제공되었음을 보증하는 것입니다. Marketplace 이미지가 Azure에서 실행되는 이미지임을 확인할 수 있도록 이 정보의 일부를 서명합니다.

### <a name="example-attested-data"></a>증명된 데이터 예제

 > [!NOTE]
> 모든 API 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 출력되었습니다.

 **요청**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Api-version은 필수 필드이며, 증명된 데이터에 대해 지원되는 버전은 2018-10-01입니다.
Nonce는 제공된 선택적 10자리 문자열입니다. Nonce는 요청을 추적하는 데 사용할 수 있으며, 제공하지 않을 경우 응답으로 인코드된 문자열에 현재 UTC 타임스탬프가 반환됩니다.

 **응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> 서명 Blob은 [pkcs7](https://aka.ms/pkcs7)으로 서명된 버전의 문서입니다. 여기에는 서명에 사용되는 인증서, 문서의 생성 및 만료에 사용되는 vmId, nonce, timeStamp 등의 VM 세부 정보, 이미지에 대한 플랜 정보가 포함되어 있습니다. 플랜 정보는 Azure Marketplace 이미지에 대해서만 채워집니다. 응답에서 인증서를 추출하고 사용하여 응답이 유효하고 Azure에서 제공되는지 확인할 수 있습니다.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Windows 가상 머신에서 증명된 메타데이터 검색

 **요청**

인스턴스 메타데이터는 Powershell 유틸리티 `curl`을 통해 Windows에서 검색할 수 있습니다.

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 또는 `Invoke-RestMethod` cmdlet을 통해 검색할 수 있습니다.

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Api-version은 필수 필드이며, 증명된 데이터에 대해 지원되는 버전은 2018-10-01입니다.
Nonce는 제공된 선택적 10자리 문자열입니다. Nonce는 요청을 추적하는 데 사용할 수 있으며, 제공하지 않을 경우 응답으로 인코드된 문자열에 현재 UTC 타임스탬프가 반환됩니다.

 **응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> 서명 Blob은 [pkcs7](https://aka.ms/pkcs7)으로 서명된 버전의 문서입니다. 여기에는 서명에 사용되는 인증서, 문서의 생성 및 만료에 사용되는 vmId, nonce, timeStamp 등의 VM 세부 정보, 이미지에 대한 플랜 정보가 포함되어 있습니다. 플랜 정보는 Azure Marketplace 이미지에 대해서만 채워집니다. 응답에서 인증서를 추출하고 사용하여 응답이 유효하고 Azure에서 제공되는지 확인할 수 있습니다.

## <a name="example-scenarios-for-usage"></a>사용법을 위한 예제 시나리오  

### <a name="tracking-vm-running-on-azure"></a>Azure에서 실행 중인 VM 추적

서비스 공급자는 소프트웨어를 실행 중인 VM의 수를 추적하거나 VM의 고유성을 추적해야 하는 에이전트가 있어야 합니다. VM의 고유 ID를 가져오려면 Instance Metadata Service의 `vmId` 필드를 사용합니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**응답**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>장애/업데이트 도메인 기반 컨테이너, 데이터 파티션 배치 

특정 시나리오의 경우 다양한 데이터 복제본 배치가 매우 중요합니다. 예를 들어 [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)를 통한 컨테이너 배치를 위해서는 VM을 실행 중인 `platformFaultDomain` 및 `platformUpdateDomain`을 알아야 합니다.
인스턴스에 대해 [가용성 영역](../../availability-zones/az-overview.md)을 사용하여 이러한 결정을 내릴 수도 있습니다.
Instance Metadata Service를 통해 이 데이터를 직접 쿼리할 수 있습니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**응답**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>지원 사례 중 VM에 대한 자세한 정보 얻기

서비스 공급자는 지원 요청을 받을 수 있으며 이 때 VM에 대해 자세한 정보를 알아야 하는 경우가 있습니다. 고객에게 계산 메타데이터를 공유하도록 요청하면 지원 전문가가 Azure에서 VM의 종류에 대해 알 수 있도록 기본 정보가 제공될 수 있습니다. 

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>VM이 실행되는 Azure 환경 가져오기

Azure에는 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)와 같은 다양한 소버린 클라우드가 있습니다. 경우에 따라 몇 가지 런타임 결정을 내리려면 Azure 환경이 필요합니다. 다음 샘플에서는 이러한 동작을 수행하는 방법을 보여줍니다.

**요청**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**응답**
```bash
AZUREPUBLICCLOUD
```

### <a name="getting-the-tags-for-the-vm"></a>VM에 대 한 태그 가져오기

논리적 구조로 분류를 구성 하 여 Azure VM에 태그 적용 되었습니다. 아래 요청을 사용 하 여 VM에 할당 된 태그를 검색할 수 있습니다.

**요청**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**응답**

```text
Department:IT;Environment:Test;Role:WebRole
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>VM이 Azure에서 실행 중인지 확인

 마켓플레이스 공급업체는 해당 소프트웨어가 Azure에서만 실행되도록 사용이 허가되었는지 확인하려고 합니다. 사용자가 온-프레미스에 VHD를 복사하는 경우 이 사실을 검색하는 방법이 있어야 합니다. Instance Metadata Service를 호출함으로써 마켓플레이스 공급업체는 Azure의 응답임을 보증하는 서명된 데이터를 얻을 수 있습니다.
**요청**
> [!NOTE]
> jq를 설치해야 합니다.

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **응답**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Data | 설명
-----|------------
nonce | 사용자가 요청과 함께 선택적 문자열을 제공했습니다. 요청에 nonce를 제공하지 않은 경우 현재 UTC 타임스탬프가 반환됩니다.
계획 | Azure Marketplace 이미지의 VM에 대한 [플랜](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)에는 이름, 제품 및 게시자가 포함됩니다.
timestamp/createdOn | 첫 번째 서명된 문서가 생성된 타임스탬프
timestamp/expiresOn | 서명된 문서가 만료되는 타임스탬프
vmId |  VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)

#### <a name="verifying-the-signature"></a>서명 확인

위에서 서명을 받은 경우 Microsoft에서 제공한 서명임을 확인할 수 있습니다. 중간 인증서와 인증서 체인을 확인할 수도 있습니다.

> [!NOTE]
> 공용 클라우드와 소버린 클라우드의 인증서는 서로 다릅니다.

 영역 | 인증서
---------|-----------------
[일반 공급되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure 중국](https://www.azure.cn/)                                                           | metadata.azure.cn
[Azure 독일](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

### <a name="failover-clustering-in-windows-server"></a>Windows Server의 장애 조치(failover) 클러스터링

특정 시나리오에서 장애 조치(failover) 클러스터링을 사용하여 Instance Metadata Service를 쿼리할 때 라우팅 테이블에 경로를 추가해야 합니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다.

2. 다음 명령을 실행하고 IPv4 경로 테이블에 네트워크 대상의 인터페이스 주소(`0.0.0.0`)를 기록합니다.

```bat
route print
```

> [!NOTE] 
> 장애 조치(failover) 클러스터를 사용하는 Windows Server VM의 다음 예제 출력에는 간단히 나타내기 위해 IPv4 경로 테이블만 포함되어 있습니다.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. 다음 명령을 실행하고 네트워크 대상의 인터페이스 주소(`0.0.0.0`)를 사용합니다(이 예제에서는 `10.0.1.10`).

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM 내의 서로 다른 언어를 사용하여 메타데이터 서비스를 호출하는 예 

언어 | 예제
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
자바       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>FAQ

1. `400 Bad Request, Required metadata header not specified` 오류가 발생했습니다. 무슨 의미인가요?
   * Instance Metadata Service에서는 `Metadata: true` 헤더를 요청에 포함시켜 전달해야 합니다. REST 호출에서 이 헤더를 전달하면 Instance Metadata Service에 액세스가 허용됩니다.
2. VM에 대한 계산 정보를 구할 수 없는 이유가 무엇인가요?
   * 현재 Instance Metadata Service는 Azure Resource Manager를 사용하여 만든 인스턴스만 지원합니다. 나중에 클라우드 서비스 VM에 대한 지원을 추가할 수 있습니다.
3. 잠시 전에 Azure Resource Manager를 통해 내 Virtual Machine을 만들었습니다. 계산 메타데이터 정보가 왜 표시되지 않나요?
   * 2016년 9월 이후에 생성된 VM의 경우 계산 메타데이터를 표시하려면 [태그](../../azure-resource-manager/resource-group-using-tags.md)를 추가하세요. 이전 VM(2016년 9월 전에 생성된)의 경우 메타데이터를 새로 고치도록 VM에 확장 또는 데이터 디스크를 추가/제거하세요.
4. 새 버전용으로 채워진 데이터 중 일부만 표시됩니다.
   * 2016년 9월 이후에 생성된 VM의 경우 계산 메타데이터를 표시하려면 [태그](../../azure-resource-manager/resource-group-using-tags.md)를 추가하세요. 이전 VM(2016년 9월 전에 생성된)의 경우 메타데이터를 새로 고치도록 VM에 확장 또는 데이터 디스크를 추가/제거하세요.
5. `500 Internal Server Error` 오류가 발생하는 이유가 무엇인가요?
   * 지수 백오프 시스템을 기반으로 요청을 다시 시도하세요. 문제가 지속되면 Azure 지원에 문의하세요.
6. 추가 질문/의견은 어디에 공유하나요?
   * https://feedback.azure.com에 대한 의견을 보내주세요.
7. Virtual Machine Scale Set 인스턴스에 작동하나요?
   * 예, 메타데이터 서비스는 확장 집합 인스턴스에 사용할 수 있습니다.
8. 서비스에 대한 지원을 받으려면 어떻게 하나요?
   * 서비스에 대한 지원을 받으려면 Azure Portal에서 긴 다시 시도 후 메타데이터 응답을 받을 수 없는 VM에 대한 지원 문제를 만듭니다.
9. 서비스를 호출하는 데 요청 시간이 초과됩니다.
   * 메타데이터 호출은 VM의 네트워크 카드에 할당된 기본 IP 주소로부터 수행되어야 하며, 경로를 변경한 경우에는 네트워크 카드에서 169.254.0.0/16 주소에 대한 경로도 있어야 합니다.
10. 가상 머신 확장 집합에서 태그를 업데이트했는데 VM과 달리 인스턴스에 태그가 나타나지 않습니다.
    * 현재 ScaleSets의 경우 태그는 재부팅/이미지로 다시 설치/인스턴스에 대한 디스크 변경이 있을 때만 VM에 표시됩니다.

    ![인스턴스 메타데이터 지원](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>다음 단계

- [예약된 이벤트](scheduled-events.md)에 대해 자세히 알아봅니다.
