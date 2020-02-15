---
title: 종동체 데이터베이스 기능을 사용 하 여 Azure 데이터 탐색기에 데이터베이스 연결
description: Azure 데이터 탐색기에서 종동체 데이터베이스 기능을 사용 하 여 데이터베이스를 연결 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 447e8a67cedbb8f78e4db9602f603fefd382693c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162942"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>종동체 데이터베이스를 사용 하 여 Azure 데이터 탐색기에 데이터베이스 연결

**종동체 데이터베이스** 기능을 사용 하면 다른 클러스터에 있는 데이터베이스를 Azure 데이터 탐색기 클러스터에 연결할 수 있습니다. **종동체 데이터베이스** 는 *읽기* 전용 모드로 연결 되어 데이터를 확인 하 고 **리더 데이터베이스로**수집 된 데이터에 대 한 쿼리를 실행할 수 있게 합니다. 종동체 데이터베이스는 리더 데이터베이스의 변경 내용을 동기화 합니다. 동기화로 인해 데이터 가용성에 몇 초에서 몇 분의 데이터 지연 시간이 있습니다. 지연 시간 길이는 리더 데이터베이스 메타 데이터의 전체 크기에 따라 달라 집니다. 리더 및 종동체 데이터베이스는 동일한 저장소 계정을 사용 하 여 데이터를 가져옵니다. 저장소는 리더 데이터베이스가 소유 합니다. 종동체 데이터베이스는 데이터를 수집 하지 않고도 데이터를 볼 수 있습니다. 연결 된 데이터베이스는 읽기 전용 데이터베이스 이므로 [캐싱 정책](#configure-caching-policy), [보안 주체](#manage-principals)및 [사용 권한을](#manage-permissions)제외 하 고 데이터베이스의 데이터, 테이블 및 정책을 수정할 수 없습니다. 연결 된 데이터베이스는 삭제할 수 없습니다. 리더가 나 종동체에 의해 분리 되어야 하며, 그런 후에만 삭제할 수 있습니다. 

종동체 기능을 사용 하 여 다른 클러스터에 데이터베이스를 연결 하는 것은 조직과 팀 간에 데이터를 공유 하는 인프라로 사용 됩니다. 이 기능은 계산 리소스를 분리 하 여 비프로덕션 사용 사례에서 프로덕션 환경을 보호 하는 데 유용 합니다. 종동체를 사용 하 여 데이터에 대 한 쿼리를 실행 하는 파티에 Azure 데이터 탐색기 클러스터의 비용을 연결할 수도 있습니다.

## <a name="which-databases-are-followed"></a>어떤 데이터베이스를 팔 로우 합니까?

* 클러스터는 하나의 데이터베이스, 여러 데이터베이스 또는 리더 클러스터의 모든 데이터베이스를 따를 수 있습니다. 
* 단일 클러스터는 여러 개의 리더 클러스터에서 데이터베이스를 따라갈 수 있습니다. 
* 클러스터에는 종동체 데이터베이스와 리더 데이터베이스가 모두 포함 될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
1. 리더 및 종동체에 대 한 [클러스터 및 DB를 만듭니다](/azure/data-explorer/create-cluster-database-portal) .
1. [수집](/azure/data-explorer/ingest-sample-data) [개요](/azure/data-explorer/ingest-data-overview)에 설명 된 다양 한 방법 중 하나를 사용 하 여 데이터를 리더 데이터베이스로 수집 합니다.

## <a name="attach-a-database"></a>데이터베이스 연결

데이터베이스를 연결 하는 데 사용할 수 있는 여러 가지 방법이 있습니다. 이 문서에서는 또는 Azure Resource Manager 템플릿을 사용 하 여 데이터베이스 C# 를 연결 하는 방법을 설명 합니다. 데이터베이스를 연결 하려면 리더 클러스터 및 종동체 클러스터에 대 한 권한이 있어야 합니다. 사용 권한에 대 한 자세한 내용은 [권한 관리](#manage-permissions)를 참조 하세요.

### <a name="attach-a-database-using-c"></a>을 사용 하 여 데이터베이스 연결C#

#### <a name="needed-nugets"></a>필요한 Nuget

* [Microsoft. ..kusto를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치 합니다.
* [인증을 위해 Microsoft Azure 인증을](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)설치 합니다.

#### <a name="code-example"></a>코드 예

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Python을 사용 하 여 데이터베이스 연결

#### <a name="needed-modules"></a>필요한 모듈

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>코드 예

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 데이터베이스 연결

이 섹션에서는 [Azure Resource Manager 템플릿을](../azure-resource-manager/management/overview.md)사용 하 여 기존 배포한에 데이터베이스를 연결 하는 방법에 대해 알아봅니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('attachedDatabaseConfigurationsName')]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>템플릿 배포 

[Azure Portal](https://portal.azure.com) 또는 powershell을 사용 하 여 Azure Resource Manager 템플릿을 배포할 수 있습니다.

   ![템플릿 배포](media/follower/template-deployment.png)


|**설정**  |**설명**  |
|---------|---------|
|종동체 클러스터 이름     |  종동체 클러스터의 이름입니다. 템플릿이 배포 되는 위치입니다.  |
|연결 된 데이터베이스 구성 이름    |    연결 된 데이터베이스 구성 개체의 이름입니다. 이름은 클러스터 수준에서 고유한 임의의 문자열일 수 있습니다.     |
|데이터베이스 이름     |      따를 데이터베이스의 이름입니다. 모든 리더의 데이터베이스를 팔 로우 하려면 ' * '를 사용 합니다.   |
|리더 클러스터 리소스 ID    |   리더 클러스터의 리소스 ID입니다.      |
|기본 보안 주체 수정 종류    |   기본 보안 주체 수정 종류입니다. `Union`, `Replace` 또는 `None`수 있습니다. 기본 보안 주체 수정 종류에 대 한 자세한 내용은 [principal 수정 kind 제어 명령](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)을 참조 하세요.      |
|위치   |   모든 리소스의 위치입니다. 리더와 종동체는 동일한 위치에 있어야 합니다.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>데이터베이스가 성공적으로 연결 되었는지 확인 합니다.

데이터베이스가 성공적으로 연결 되었는지 확인 하려면 [Azure Portal](https://portal.azure.com)에서 연결 된 데이터베이스를 찾습니다. 

1. 종동체 클러스터로 이동 하 고 **데이터베이스** 를 선택 합니다.
1. 데이터베이스 목록에서 새 읽기 전용 데이터베이스를 검색 합니다.

    ![읽기 전용 종동체 데이터베이스](media/follower/read-only-follower-database.png)

또는 다음과 같습니다.

1. 리더 클러스터로 이동 하 고 **데이터베이스** 를 선택 합니다.
2. 관련 데이터베이스가 **다른 사용자와 공유** 로 표시 되었는지 확인 > **예**

    ![연결 된 데이터베이스 읽기 및 쓰기](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>을 사용 하 여 종동체 데이터베이스 분리C# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>연결 된 종동체 데이터베이스를 종동체 클러스터에서 분리

종동체 클러스터는 다음과 같이 연결 된 데이터베이스를 분리할 수 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>연결 된 종동체 데이터베이스를 리더 클러스터에서 분리 합니다.

리더 클러스터는 연결 된 모든 데이터베이스를 다음과 같이 분리할 수 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Python을 사용 하 여 종동체 데이터베이스 분리

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>연결 된 종동체 데이터베이스를 종동체 클러스터에서 분리

종동체 클러스터는 다음과 같이 연결 된 데이터베이스를 분리할 수 있습니다.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>연결 된 종동체 데이터베이스를 리더 클러스터에서 분리 합니다.

리더 클러스터는 연결 된 모든 데이터베이스를 다음과 같이 분리할 수 있습니다.

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>보안 주체, 권한 및 캐싱 정책 관리

### <a name="manage-principals"></a>보안 주체 관리

데이터베이스를 연결할 때 **"기본 보안 주체 수정 종류"** 를 지정 합니다. 기본적으로는 [인증 된 보안 주체의](/azure/kusto/management/access-control/index#authorization) 리더 데이터베이스 컬렉션을 유지 합니다.

|**종류** |**설명**  |
|---------|---------|
|**Union**     |   연결 된 데이터베이스 보안 주체에는 항상 원래 데이터베이스 보안 주체와 종동체 데이터베이스에 추가 된 새 보안 주체가 모두 포함 됩니다.      |
|**바꾸기**   |    원본 데이터베이스의 보안 주체를 상속 하지 않습니다. 연결 된 데이터베이스에 대 한 새 보안 주체를 만들어야 합니다.     |
|**없음**   |   연결 된 데이터베이스 보안 주체에는 추가 보안 주체가 없는 원래 데이터베이스의 보안 주체만 포함 됩니다.      |

제어 명령을 사용 하 여 권한이 부여 된 보안 주체를 구성 하는 방법에 대 한 자세한 내용은 [종동체 클러스터를 관리 하기 위한 제어 명령](/azure/kusto/management/cluster-follower)을 참조 하세요.

### <a name="manage-permissions"></a>사용 권한 관리

읽기 전용 데이터베이스 권한을 관리 하는 것은 모든 데이터베이스 유형의 경우와 동일 합니다. [Azure Portal에서 관리 권한을](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)참조 하세요.

### <a name="configure-caching-policy"></a>캐싱 정책 구성

종동체 데이터베이스 관리자는 호스팅 클러스터에서 연결 된 데이터베이스의 [캐싱 정책이](/azure/kusto/management/cache-policy) 나 해당 테이블을 수정할 수 있습니다. 기본적으로 데이터베이스 및 테이블 수준 캐싱 정책의 리더 데이터베이스 컬렉션을 유지 합니다. 예를 들어 월간 보고를 실행 하기 위해 리더 데이터베이스에 30 일 캐싱 정책을 설정 하 고, 문제 해결을 위해 최신 데이터만 쿼리하려면 종동체 데이터베이스에서 3 일 캐싱 정책을 사용할 수 있습니다. 제어 명령을 사용 하 여 종동체 데이터베이스 또는 테이블에 대 한 캐싱 정책을 구성 하는 방법에 대 한 자세한 내용은 [종동체 클러스터를 관리 하기 위한 제어 명령](/azure/kusto/management/cluster-follower)을 참조 하세요.

## <a name="limitations"></a>제한 사항

* 종동체와 리더 클러스터는 동일한 지역에 있어야 합니다.
* 진행 중인 데이터베이스에서는 [스트리밍](/azure/data-explorer/ingest-data-streaming) 수집을 사용할 수 없습니다.
* [고객 관리 키](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) 를 사용 하는 데이터 암호화는 리더가 나 종동체 클러스터에서 지원 되지 않습니다. 
* 분리 하기 전에 다른 클러스터에 연결 된 데이터베이스는 삭제할 수 없습니다.
* 다른 클러스터에 연결 된 데이터베이스를 분리 하기 전에는 해당 클러스터를 삭제할 수 없습니다.
* 연결 된 종동체 또는 리더 데이터베이스가 있는 클러스터는 중지할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

* 종동체 클러스터 구성에 대 한 자세한 내용은 [종동체 클러스터를 관리 하는 명령 제어](/azure/kusto/management/cluster-follower)를 참조 하세요.
