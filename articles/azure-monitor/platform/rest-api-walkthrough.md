---
title: Azure Monitoring REST API 연습
description: 요청을 인증하고 Azure Monitor REST API를 사용하여 사용 가능한 메트릭 정의 및 메트릭 값을 검색하는 방법입니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 03/19/2018
ms.openlocfilehash: 6dff1fe974ad4ffa993e4df03b4903d7e46e1990
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162262"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API 연습

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 [Microsoft Azure Monitor REST API 참조](https://docs.microsoft.com/rest/api/monitor/)를 사용할 수 있도록 인증을 수행하는 방법을 살펴봅니다.

Azure Monitor API를 통해 사용 가능한 기본 메트릭 정의, 세분성 및 메트릭 값을 프로그래밍 방식으로 검색할 수 있습니다. Azure SQL Database, Azure Cosmos DB 또는 Azure Data Lake와 같은 별도의 데이터 저장소에 데이터를 저장할 수 있습니다. 여기에서 필요에 따라 추가적인 분석을 수행할 수 있습니다.

다양한 메트릭 데이터 요소 작업 외에도 Monitor API를 사용하여 경고 규칙을 나열하고, 활동 로그를 확인하는 등 다양한 작업을 수행할 수 있습니다. 사용 가능한 작업의 전체 목록은 [Microsoft Azure Monitor REST API 참조](https://docs.microsoft.com/rest/api/monitor/)에서 확인하세요.

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor 요청 인증

첫 번째 단계는 요청을 인증하는 것입니다.

Azure Monitor API에 대해 실행되는 모든 작업은 Azure Resource Manager 인증 모델을 사용합니다. 따라서 모든 요청은 Azure AD(Azure Active Directory)로 인증되어야 합니다. 클라이언트 애플리케이션을 인증하는 한 가지 방법은 Azure AD 서비스 주체를 만들고 인증(JWT) 토큰을 검색하는 것입니다. 다음 예제 스크립트에서는 PowerShell을 통해 Azure AD 서비스 주체를 만드는 것을 보여 줍니다. 상세한 연습은 [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)를 참조하세요. [Azure Portal을 통해 서비스 주체를 만들 수도](../../active-directory/develop/howto-create-service-principal-portal.md)있습니다.

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Azure Monitor API를 쿼리하려면 클라이언트 애플리케이션이 이전에 만든 서비스 주체를 인증에 사용해야 합니다. 다음 예제 PowerShell 스크립트에서는 JWT 인증 토큰을 가져오기 위해 ADAL([Active Directory 인증 라이브러리](../../active-directory/azuread-dev/active-directory-authentication-libraries.md))을 사용하는 방법을 보여줍니다. JWT 토큰은 Azure Monitor REST API에 대한 요청에서 HTTP 권한 부여 매개 변수의 일부로 전달됩니다.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

인증한 후에 Azure Monitor REST API에 대해 쿼리를 실행할 수 있습니다. 다음과 같이 두 가지 유용한 쿼리 가지가 있습니다.

1. 리소스에 대한 메트릭 정의 나열
2. 메트릭 값 검색

> [!NOTE]
> Azure REST API을 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 [azure REST API 참조](https://docs.microsoft.com/rest/api/azure/)를 참조 하세요.
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>메트릭 정의 검색(다차원 API)

[Azure Monitor 메트릭 정의 REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)를 사용하여 서비스에 사용 가능한 메트릭 목록에 액세스합니다.

**메서드**: GET

**요청 URI**: https:\/\/Management.azure.com/subscriptions/ *{subscriptionId}* /Stgg/ *{ResourceGroupName}* /providers/ *{resourceprovidernamespace}* / *{resourceType* }/ *{context.resourcename}* /providers/microsoft.insights/metricdefinitions? api-version? api-version = *{apiVersion}*

예를 들어 Azure Storage 계정의 메트릭 정의를 검색하기 위해 요청이 다음과 같이 표시됩니다.

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> 다차원 Azure Monitor 메트릭 REST API를 사용하여 메트릭 정의를 검색하려면 API 버전으로 "2018-01-01"을 사용합니다.
>
>

결과 JSON 응답 본문은 다음 예제와 유사합니다(두 번째 메트릭에 차원이 있음)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>차원 값 검색(다차원 API)

사용 가능한 메트릭 정의를 알고 있는 경우 차원이 포함된 메트릭이 있을 수 있습니다. 메트릭을 쿼리하기 전에 차원에 있는 값의 범위를 검색하려고 합니다. 메트릭을 쿼리하는 동안 이러한 차원 값에 따라 메트릭을 필터링하거나 분할하도록 선택할 수 있습니다.  [Azure Monitor 메트릭 REST API](https://docs.microsoft.com/rest/api/monitor/metrics)를 사용하여 이 작업을 수행합니다.

필터링 요청에 메트릭 이름 '값'('localizedValue' 아님)을 사용합니다. 필터를 지정하지 않은 경우 기본 메트릭이 반환됩니다. 이 API의 사용은 하나의 차원에 하나의 와일드카드 필터만을 허용합니다.

> [!NOTE]
> Azure Monitor REST API를 사용하여 차원 값을 검색하려면 API 버전으로 "2018-01-01"을 사용합니다.
>
>

**메서드**: GET

**요청 URI**: https\://management.azure.com/subscriptions/ *{subscription-id}* /stgg// *{* /providers/ *}/providers/microsoft.insights/metrics {* resource-name}/{*resource-* *name*}? metricnames = *{metric}* & Timespan = *{starttime/endtime}* & $filter = *{filter}* & resultType = 메타 데이터 & api-version = *{ apiVersion}*

예를 들어 지정된 시간 범위 동안 GeoType dimension = 'Primary'인 'API Name dimension'에 대해 내보내진 차원의 값 목록을 검색하기 위해 요청은 다음과 같을 수 있습니다.

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

결과 JSON 응답 본문은 다음 예제와 유사합니다.

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>메트릭 값 검색(다차원 API)

사용 가능한 메트릭 정의 및 가능한 차원 값을 알고 나면 관련 메트릭 값을 검색할 수 있습니다.  [Azure Monitor 메트릭 REST API](https://docs.microsoft.com/rest/api/monitor/metrics)를 사용하여 이 작업을 수행합니다.

필터링 요청에 메트릭 이름 '값'('localizedValue' 아님)을 사용합니다. 차원 필터가 지정되는 경우 롤업되고 집계된 메트릭을 반환합니다. 메트릭 쿼리가 여러 시계열을 반환하는 경우 'Top' 및 'OrderBy' 쿼리 매개 변수를 사용하여 시계열의 제한된 정렬 목록을 반환할 수 있습니다.

> [!NOTE]
> Azure Monitor REST API를 사용하여 다차원 메트릭 값을 검색하려면 API 버전으로 "2018-01-01"을 사용합니다.
>
>

**메서드**: GET

**Request URI**: https://management.azure.com/subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/ *{resource-provider-namespace}* / *{resource-type}* / *{resource-name}* /providers/microsoft.insights/metrics?metricnames= *{metric}* &timespan= *{starttime/endtime}* &$filter= *{filter}* &interval= *{timeGrain}* &aggregation= *{aggreation}* &api-version= *{apiVersion}*

예를 들어 GeotType이 'Primary'였던 5분 범위 동안 'Transactions' 수에 의한 내림차순 값에서 상위 3개의 API를 검색하기 위해 요청은 다음과 같습니다.

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

결과 JSON 응답 본문은 다음 예제와 유사합니다.

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>메트릭 정의 검색

[Azure Monitor 메트릭 정의 REST API](https://msdn.microsoft.com/library/mt743621.aspx)를 사용하여 서비스에 사용 가능한 메트릭 목록에 액세스합니다.

**메서드**: GET

**요청 URI**: https:\/\/Management.azure.com/subscriptions/ *{subscriptionId}* /Stgg/ *{ResourceGroupName}* /providers/ *{resourceprovidernamespace}* / *{resourceType* }/ *{context.resourcename}* /providers/microsoft.insights/metricdefinitions? api-version? api-version = *{apiVersion}*

예를 들어 Azure Logic App의 메트릭 정의를 검색하기 위해 요청이 다음과 같이 표시됩니다.

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Azure Monitor REST API를 사용하여 메트릭 정의를 검색하려면 API 버전으로 "2016-03-01"을 사용합니다.
>
>

결과 JSON 응답 본문은 다음 예제와 유사합니다.

```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

자세한 내용은 [Azure Monitor REST API에서 리소스에 대한 메트릭 정의 나열](https://msdn.microsoft.com/library/azure/mt743621.aspx) 설명서를 참조하세요.

## <a name="retrieve-metric-values"></a>메트릭 값 검색

사용 가능한 메트릭 정의를 알고나면 관련 메트릭 값을 검색할 수 있습니다. 모든 필터링 요청(예: ‘CpuTime’ 및 ‘Requests’ 메트릭 데이터 포인트 요청)에서 메트릭 이름 ‘value’(‘localizedValue’ 아님)를 사용합니다. 필터를 지정하지 않은 경우 기본 메트릭이 반환됩니다.

> [!NOTE]
> Azure Monitor REST API를 사용하여 메트릭 값을 검색하려면 API 버전으로 "2016-09-01"을 사용합니다.
>
>

**메서드**: GET

**Request URI**: https://management.azure.com/subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/ *{resource-provider-namespace}* / *{resource-type}* / *{resource-name}* /providers/microsoft.insights/metrics?$filter= *{filter}* &api-version= *{apiVersion}*

예를 들어, 주어진 범위와 시간 조직 1시간에 대해 RunsSucceeded 메트릭 데이터를 검색하려면 요청은 다음과 같습니다.

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

결과 JSON 응답 본문은 다음 예제와 유사합니다.

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

여러 데이터 또는 집계 포인트를 검색하려면 다음 예제에서처럼 메트릭 정의 이름과 집계 형식을 필터에 추가합니다.

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

결과 JSON 응답 본문은 다음 예제와 유사합니다.

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient 사용

추가 접근 방식은 Windows 컴퓨터에서 [ARMClient](https://github.com/projectkudu/armclient)를 사용하는 것입니다. ARMClient는 자동으로 Azure AD 인증을 처리합니다(결과 JWT 토큰 포함). 다음 단계에서는 ARMClient를 사용하여 메트릭 데이터를 검색하는 방법을 개괄적으로 보여줍니다.

1. [Chocolatey](https://chocolatey.org/) 및 [ARMClient](https://github.com/projectkudu/armclient)를 설치합니다.
2. 터미널 창에서 *armclient.exe login*을 입력합니다. 그러면 Azure에 로그인하라는 메시지가 표시됩니다.
3. *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*을 입력합니다.
4. *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*을 입력합니다.

예를 들어 특정 Logic App에 대한 메트릭 정의를 검색하려면 다음 명령을 실행합니다.

```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>리소스 ID 검색

REST API를 사용하면 사용 가능한 메트릭 정의, 세분성, 관련 값을 이해하는 데 크게 도움이 됩니다. 이런 정보는 [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)를 사용할 때 유용합니다.

앞의 코드에서 사용하는 리소스 ID는 원하는 Azure 리소스의 전체 경로입니다. 예를 들어 Azure Web App에 대해 쿼리하려면 리소스 ID는 다음과 같습니다.

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

다음 목록은 다양한 Azure 리소스에 대한 리소스 ID 형식의 몇 가지 예입니다.

* **IoT Hub** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Devices/IotHubs/ *{iot-hub-name}*
* **탄력적인 SQL 풀** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Sql/servers/ *{pool-db}* /elasticpools/ *{sql-pool-name}*
* **SQL Database(v12)** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Sql/servers/ *{server-name}* /databases/ *{database-name}*
* **Service Bus** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.ServiceBus/ *{namespace}* / *{servicebus-name}*
* **가상 머신 확장 집합** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Compute/virtualMachineScaleSets/ *{vm-name}*
* **VM** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.Compute/virtualMachines/ *{vm-name}*
* **Event Hubs** - /subscriptions/ *{subscription-id}* /resourceGroups/ *{resource-group-name}* /providers/Microsoft.EventHub/namespaces/ *{eventhub-namespace}*

Azure Resource Explorer 등, 다른 방법으로 리소스 ID를 검색하고 Azure 포털과 PowerShell 또는 Azure CLI를 통해 원하는 리소스를 확인할 수 있습니다.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

원하는 리소스에 대한 리소스 ID를 찾을 때는 [Azure Resource Explorer](https://resources.azure.com) 도구를 사용하는 것도 유용한 방법입니다. 원하는 리소스로 이동한 다음 스크린샷에서처럼 표시된 ID를 살펴봅니다.

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure portal

Azure 포털에서 리소스 ID를 가져올 수도 있습니다. 이렇게 하려면 원하는 리소스로 이동한 다음 속성을 선택합니다. 리소스 ID는 다음 스크린샷에 표시된 것처럼 속성 섹션에 표시됩니다.

![Alt "Azure 포털의 속성 블레이드에 표시된 리소스 ID”](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell cmdlet을 사용해서 리소스 ID를 검색할 수도 있습니다. 예를 들어, Azure Logic App의 리소스 ID를 가져오려면 다음 예제에서처럼 Get-AzureLogicApp cmdlet을 실행합니다.

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

결과는 다음 예제와 유사합니다.

```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 Azure Storage 계정에 대 한 리소스 ID를 검색 하려면 다음 예제와 같이 `az storage account show` 명령을 실행 합니다.

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

결과는 다음 예제와 유사합니다.

```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps는 Azure CLI를 통해 사용할 수 있습니다. 따라서 Azure Storage 계정이 앞의 예제에서 표시됩니다.
>
>

## <a name="retrieve-activity-log-data"></a>활동 로그 데이터 검색

또한 Azure Monitor REST API를 사용하여 메트릭 정의 및 관련 값 외에도 Azure 리소스와 관련한 다른 흥미로운 정보를 검색할 수 있습니다. 예를 들어, [활동 로그](https://msdn.microsoft.com/library/azure/dn931934.aspx) 데이터를 쿼리할 수 있습니다. 다음 예제에서는 Azure Monitor REST API를 사용하여 Azure 구독에 대해 특정 날짜 범위의 활동 로그 데이터를 쿼리합니다.

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>다음 단계

* [모니터링 개요](../../azure-monitor/overview.md)를 검토합니다.
* [Azure Monitor에서 지원되는 메트릭](metrics-supported.md)을 확인합니다.
* [Microsoft Azure Monitor REST API 참조](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 검토합니다.
* [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)를 검토합니다.
