---
title: Azure HDInsight Go SDK
description: Azure HDInsight Go SDK 참조
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: tyfox
ms.custom: seodec18
ms.openlocfilehash: 2e5b7816fda89e25dcb0de26f526e5187e0640b9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098561"
---
# <a name="hdinsight-go-management-sdk-preview"></a>HDInsight Go 관리 SDK 미리 보기

## <a name="overview"></a>개요
HDInsight Go SDK는 HDInsight 클러스터 관리를 위한 클래스 및 함수를 제공합니다. 여기에는 HDInsight 클러스터의 속성 만들기, 삭제, 업데이트, 나열, 크기 조정, 스크립트 작업 실행, 모니터링, 가져오기 작업을 포함합니다.

> [!NOTE]  
>이 SDK에 대한 GoDoc 참조 자료도 [여기](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
* [Go](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK 설치

GOPATH 위치에서 `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight` 실행

## <a name="authentication"></a>Authentication

Azure 구독을 사용해서 SDK를 먼저 인증해야 합니다.  아래 예제에 따라 서비스 주체를 만들고 이를 인증에 사용합니다. 완료되면 관리 작업 수행을 위해 사용할 수 있는 여러 함수(아래 섹션 참조)가 포함된 `ClustersClient` 인스턴스가 준비됩니다.

> [!NOTE]  
> 아래 설명된 예제 외에도 사용자 요구에 더 적합할 수 있는 다른 인증 방법이 있습니다. 모든 함수가 다음에 설명되어 있습니다. [Azure SDK for Go에서의 인증 함수](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization).

### <a name="authentication-example-using-a-service-principal"></a>서비스 주체를 사용한 인증 예제

먼저, [Azure Cloud Shell](https://shell.azure.com/bash)에 로그인합니다. 서비스 주체를 만들려는 구독을 현재 사용하고 있는지 확인합니다. 

```azurecli-interactive
az account show
```

구독 정보는 JSON으로 표시됩니다.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

올바른 구독으로 로그인되지 않은 경우 다음을 실행하여 올바른 구독을 선택합니다. 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> 경우 등록 하지 않은 이미 HDInsight 리소스 공급자를 다른 함수로 (같은 Azure portal 통해 HDInsight 클러스터를 만들어)를 인증할 수 전에 되 면이 작업을 수행 해야 합니다. 이 작업은 [Azure Cloud Shell](https://shell.azure.com/bash)에서 다음 명령을 실행하여 수행할 수 있습니다.
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

그런 다음 서비스 주체 이름을 선택하고 다음 명령을 사용해서 만듭니다.

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

서비스 주체 정보는 JSON으로 표시됩니다.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
아래 코드 조각을 복사하고 서비스 주체를 만들기 위해 명령을 실행한 후 반환된 JSON 문자열을 `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` 및 `SUBSCRIPTION_ID`에 채웁니다.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>클러스터 관리

> [!NOTE]  
> 이 섹션에서는 이미 인증이 수행되었고 `ClusterClient` 인스턴스가 생성되었으며, `client`라는 변수로 저장되었다고 가정합니다. `ClusterClient` 인증 및 가져오기 지침은 위에 표시된 인증 섹션에서 찾을 수 있습니다.

### <a name="create-a-cluster"></a>클러스터 만들기

`client.Create()`을(를) 호출하여 새 클러스터를 만들 수 있습니다. 

#### <a name="example"></a>예

이 예제에서는 2개의 헤드 노드 및 1개의 작업자 노드를 사용하여 [Apache Spark](https://spark.apache.org/) 클러스터를 만드는 방법을 보여줍니다.

> [!NOTE]  
> 먼저 아래 설명된 대로 리소스 그룹 및 저장소 계정을 만들어야 합니다. 이미 만든 경우에는 이 단계를 건너뛸 수 있습니다.

##### <a name="creating-a-resource-group"></a>리소스 그룹 만들기

다음을 실행하여 [Azure Cloud Shell](https://shell.azure.com/bash)을 사용해서 리소스 그룹을 만들 수 있습니다.
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>저장소 계정 만들기

다음을 실행하여 [Azure Cloud Shell](https://shell.azure.com/bash)을 사용해서 저장소 계정을 만들 수 있습니다.
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
이제 다음 명령을 사용해서 저장소 계정에 대한 키를 가져옵니다(클러스터를 만들기 위해 필요).
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
아래의 Go 코드 조각은 2개의 헤드 노드 및 1개의 작업자 노드를 사용해서 Spark 클러스터를 만듭니다. 주석 설명에 따라 빈 변수를 채우고 특정 요구에 따라 다른 매개변수를 변경합니다.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>클러스터 세부 정보 가져오기

지정된 클러스터에 대한 속성을 가져오려면:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>예

`get`을(를) 사용하여 클러스터 만들기가 성공했는지 확인할 수 있습니다.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

출력은 다음과 같습니다.

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>클러스터 나열

#### <a name="list-clusters-under-the-subscription"></a>구독 아래에 클러스터 나열
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>리소스 그룹별로 클러스터 나열
```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()` 및 `ListByResourceGroup()` 모두 `ClusterListResultPage` 구조체를 반환합니다. 다음 페이지를 가져오려면 `Next()`을(를) 호출할 수 있습니다. 이 작업은 아래 예제에 표시된 것처럼 `ClusterListResultPage.NotDone()`에서 `false`를 반환할 때까지 반복할 수 있습니다.

#### <a name="example"></a>예
다음 예제는 현재 구독에 대해 모든 클러스터 속성을 출력합니다.

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>클러스터 삭제

클러스터를 삭제하려면:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>클러스터 태그 업데이트

지정된 클러스터의 태그를 다음과 같이 업데이트할 수 있습니다.

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>예

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>클러스터 크기 조정

새 크기를 지정하여 작업자 노드의 지정된 클러스터 번호를 크기 조정할 수 있습니다.

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>클러스터 모니터링

또한 HDInsight 관리 SDK를 사용하여 OMS(Operations Management Suite)를 통해 클러스터에서 모니터링을 관리할 수 있습니다.

관리 작업에 사용할 `ClusterClient`를 만든 방법과 마찬가지로, 모니터링 작업에 사용할 `ExtensionClient`를 만들어야 합니다. 위의 인증 섹션을 완료하면 다음과 같이 `ExtensionClient`를 만들 수 있습니다.

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 아래의 모니터링 예제에서는 이미 위처럼 `extClient`라고 하는 `ExtensionClient`를 초기화하고 `Authorizer`를 설정한 것으로 가정합니다.

### <a name="enable-oms-monitoring"></a>OMS 모니터링 사용

> [!NOTE]  
> OMS 모니터링을 사용하려면 기존 Log Analytics 작업 영역이 있어야 합니다. 아직 작업 영역을 만들지 않은 경우 다음에서 작업 영역을 만드는 방법을 알아볼 수 있습니다. [Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

클러스터에서 OMS 모니터링을 사용하려면:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>OMS 모니터링의 상태 보기

클러스터에서 OMS 상태를 가져오려면:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS 모니터링 사용 안 함

클러스터에서 OMS를 사용하지 않으려면:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>스크립트 작업

HDInsight는 클러스터 사용자 지정을 위해 사용자 지정 스크립트를 호출하는 스크립트 작업이라고 부르는 구성 함수를 제공합니다.

> [!NOTE]  
> 스크립트 작업을 사용하는 방법에 대한 자세한 내용은 다음에서 찾을 수 있습니다. [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

### <a name="execute-script-actions"></a>스크립트 작업 실행

지정된 클러스터에서 다음과 같이 스크립트 작업을 실행할 수 있습니다.

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

'스크립트 작업 삭제' 및 '지속형 스크립트 작업 나열' 작업의 경우 관리 작업에 사용할 `ClusterClient`를 만든 방법과 마찬가지로 `ScriptActionsClient`를 만들어야 합니다. 위의 인증 섹션을 완료하면 다음과 같이 `ScriptActionsClient`를 만들 수 있습니다.

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 아래의 스크립트 작업 예제에서는 이미 위처럼 `scriptActionsClient`라고 하는 `ScriptActionsClient`를 초기화하고 `Authorizer`를 설정한 것으로 가정합니다.

### <a name="delete-script-action"></a>스크립트 작업 삭제

지정된 클러스터에서 지정된 지속형 스크립트 작업을 삭제하려면:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>지속형 스크립트 작업 나열

> [!NOTE]  
> 두 `ListByCluster()` 모두 `ScriptActionsListPage` 구조체를 반환합니다. 다음 페이지를 가져오려면 `Next()`을(를) 호출할 수 있습니다. 이 작업은 아래 예제에 표시된 것처럼 `ClusterListResultPage.NotDone()`에서 `false`를 반환할 때까지 반복할 수 있습니다.

지정된 클러스터에 대해 모든 지속형 스크립트 작업을 나열하려면:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>예

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>모든 스크립트 실행 기록 나열

이 작업의 경우 관리 작업에 사용할 `ClusterClient`를 만든 방법과 마찬가지로, `ScriptExecutionHistoryClient`를 만들어야 합니다. 위의 인증 섹션을 완료하면 다음과 같이 `ScriptActionsClient`를 만들 수 있습니다.

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 아래의 예제에서는 이미 위처럼 `scriptExecutionHistoryClient`라고 하는 `ScriptExecutionHistoryClient`를 초기화하고 `Authorizer`를 설정한 것으로 가정합니다.

지정된 클러스터에 대해 모든 스크립트 실행 기록을 나열하려면:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>예

이 예제는 모든 과거 스크립트 실행에 대한 모든 세부 정보를 출력합니다.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>다음 단계

* [GoDoc 참조 자료](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)를 탐색합니다. GoDocs는 SDK의 모든 함수에 대한 참조 설명서를 제공합니다.
