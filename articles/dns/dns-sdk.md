---
title: .NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기
titleSuffix: Azure DNS
description: 이 학습 경로에서 .NET SDK를 사용 하 여 Azure DNS에서 DNS 영역 및 레코드 집합 만들기를 시작 합니다.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 92509ff96af0ed2a7d77799edca8447ff6839ab6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019660"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>.NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기

.NET DNS 관리 라이브러리와 함께 DNS SDK를 사용하여 DNS 영역, 레코드 집합 및 레코드를 만들거나 삭제하거나 업데이트하는 작업을 자동화할 수 있습니다. 전체 Visual Studio 프로젝트는 [여기](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)에서 사용할 수 있습니다.

## <a name="create-a-service-principal-account"></a>서비스 주체 계정 만들기

일반적으로 고유한 사용자 자격 증명 대신 전용 계정을 통해 Azure 리소스에 대한 프로그래밍 방식의 액세스를 부여합니다. 이러한 전용 계정을 '서비스 주체' 계정이라고 합니다. Azure DNS SDK 샘플 프로젝트를 사용하려면 먼저 서비스 주체 계정을 만들고 올바른 사용 권한을 할당해야 합니다.

1. [이러한 지침](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 에 따라 서비스 주체 계정을 만듭니다(Azure DNS SDK 샘플 프로젝트는 암호 기반 인증을 가정함).
2. 리소스 그룹을 만듭니다([방법은 여기에서 확인](../azure-resource-manager/templates/deploy-portal.md)).
3. Azure RBAC를 사용하여 서비스 주체 계정 'DNS 영역 참가자' 권한을 리소스 그룹에 부여합니다([방법은 다음과 같음](../role-based-access-control/role-assignments-portal.md)).
4. Azure DNS SDK 샘플 프로젝트를 사용하는 경우 'program.cs' 파일을 다음과 같이 편집합니다.

   * 1단계에서 사용한 대로 `tenantId`, `clientId`(계정 ID라고도 함), `secret`(서비스 주체 계정 암호) 및 `subscriptionId`에 대한 올바른 값을 삽입합니다.
   * 2단계에서 선택한 리소스 그룹 이름을 입력합니다.
   * 선택한 DNS 영역 이름을 입력합니다.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet 패키지 및 네임스페이스 선언

Azure DNS .NET SDK를 사용하려면 **Azure DNS 관리 라이브러리** NuGet 패키지 및 기타 필수 Azure 패키지를 설치해야 합니다.

1. **Visual Studio**에서 프로젝트 또는 새 프로젝트를 엽니다.
2. **도구** **>** **nuget 패키지 관리자** **>** **솔루션용 NuGet 패키지 관리**...로 이동 합니다.
3. **찾아보기**를 클릭하고 **시험판 포함** 확인란을 사용하도록 설정한 후, 검색 상자에 **Microsoft.Azure.Management.Dns**를 입력합니다.
4. 패키지를 선택하고 **설치** 를 클릭하여 Visual Studio 프로젝트에 추가합니다.
5. 또한 위의 프로세스를 반복하여 **Microsoft.Rest.ClientRuntime.Azure.Authentication** 및 **Microsoft.Azure.Management.ResourceManager** 패키지를 설치합니다.

## <a name="add-namespace-declarations"></a>네임스페이스 선언 추가

다음 네임스페이스 선언 추가

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>DNS 관리 클라이언트 초기화

`DnsManagementClient`에는 DNS 영역 및 레코드 집합 관리에 필요한 메서드 및 속성이 들어 있습니다.  다음 코드는 서비스 주체 계정에 로그인하고 `DnsManagementClient` 개체를 만듭니다.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>DNS 영역 만들기 또는 업데이트

DNS 영역을 만들려면 먼저 "영역" 개체가 DNS 영역 매개 변수를 포함하도록 만듭니다. DNS 영역은 특정 지역에 연결되어 있지 않기 때문에 위치가 '전역'으로 설정됩니다. 이 예제에서는 [Azure Resource Manager '태그'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) 도 영역에 추가합니다.

실제로 Azure DNS에서 영역을 만들거나 업데이트하려면 영역 매개 변수를 포함하는 영역 개체를 `DnsManagementClient.Zones.CreateOrUpdateAsyc` 메서드에 전달해야 합니다.

> [!NOTE]
> DnsManagementClient는 동기('CreateOrUpdate'), 비동기('CreateOrUpdateAsync') 또는 HTTP 응답에 액세스 권한을 가진 비동기('CreateOrUpdateWithHttpMessagesAsync') 등 세 가지 작업 모드를 지원합니다.  애플리케이션 요구 사항에 따라 이러한 모드 중 하나를 선택할 수 있습니다.

Azure DNS는 [Etag](dns-getstarted-create-dnszone.md)라는 낙관적 동시성을 지원합니다. 이 예제에서 'If-None-Match' 헤더에 "*"를 지정하면 DNS 영역이 아직 없는 경우 Azure DNS에서 만들도록 지시합니다.  지정된 이름을 가진 영역이 지정된 리소스 그룹에 이미 있는 경우 호출은 실패합니다.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS 레코드 집합 및 레코드 만들기

DNS 레코드는 레코드 집합으로 관리됩니다. 레코드 집합은 영역 내에서 동일한 이름과 레코드 형식을 가진 레코드의 집합입니다.  레코드 집합 이름은 정규화된 DNS 이름이 아닌 영역 이름에 상대적입니다.

레코드 집합을 만들거나 업데이트하려면 “RecordSet” 매개 변수 개체를 만들어서 `DnsManagementClient.RecordSets.CreateOrUpdateAsync`로 전달합니다. DNS 영역의 경우 동기('CreateOrUpdate'), 비동기('CreateOrUpdateAsync') 또는 HTTP 응답에 액세스 권한을 가진 비동기('CreateOrUpdateWithHttpMessagesAsync') 등 세 가지 작업 모드가 있습니다.

DNS 영역의 경우 레코드 집합에 대한 작업에는 낙관적 동시성에 대한 지원이 포함되어 있습니다.  이 예제에서는 'If-Match' 또는 'If-None-Match'가 지정되지 않았기 때문에 레코드 집합이 항상 만들어집니다.  이 호출은 이 DNS 영역에 있는 동일한 이름과 레코드 형식으로 모든 기존 레코드 집합을 덮어씁니다.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>영역 및 레코드 집합 가져오기

`DnsManagementClient.Zones.Get` 및 `DnsManagementClient.RecordSets.Get` 메서드는 각각 개별 영역 및 레코드 집합을 검색합니다. RecordSets은 해당 형식, 이름 및 속해 있는 영역(및 리소스 그룹)으로 식별됩니다. 영역은 해당 이름 및 속해 있는 리소스 그룹으로 식별됩니다.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>기존 레코드 집합 업데이트

기존 DNS 레코드 집합을 업데이트하려면 먼저 레코드 집합을 검색한 후에 레코드 집합 내용을 업데이트하고 변경 내용을 제출합니다.  이 예제에서는 'If-Match' 매개 변수에 있는 검색된 레코드 집합에서 'Etag'를 지정합니다. 동시 작업이 동시에 레코드 집합을 수정하는 경우 호출은 실패합니다.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>영역 및 레코드 집합 나열

영역을 나열 하려면 지정 된 리소스 그룹의 모든 영역 또는 지정 된 Azure 구독의 모든 영역 (리소스 그룹 간)을 나열 하는 *Dnsmanagementclient* ... 메서드를 사용 합니다. 레코드 집합을 나열 하려면 지정 된 영역에 모든 레코드 집합을 나열 하거나 특정 형식의 레코드 집합만 표시 하는 데 사용할 수 있는 *Dnsmanagementclient. list ...* 메서드를 사용 합니다.

영역을 나열할 때 나오는 레코드 집합은 페이지를 매길 수 있습니다.  다음 예제에서는 결과 페이지를 반복하는 방법을 보여 줍니다. (인위적으로 작은 페이지 크기인 '2'는 페이지를 강제하는 데 사용됩니다. 실제로 이 매개 변수를 생략하고 기본 페이지 크기를 사용해야 합니다.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>다음 단계

다른 DNS 레코드 형식에 대한 예제를 비롯한 Azure DNS .NET SDK를 사용하는 방법의 추가 예제가 포함된 [Azure DNS .NET SDK 샘플 프로젝트](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)를 다운로드합니다.
