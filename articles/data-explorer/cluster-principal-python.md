---
title: Python을 사용 하 여 Azure 데이터 탐색기에 대한 클러스터 보안 주체 추가
description: 이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대한 클러스터 주체를 추가 하는 방법에 대해 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965139"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Python을 사용 하 여 Azure 데이터 탐색기에 대한 클러스터 보안 주체 추가

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager 템플릿](cluster-principal-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대한 클러스터 보안 주체를 추가 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터를 만듭니다](create-cluster-database-python.md).

## <a name="install-python-package"></a>Python 패키지 설치

Azure Data Explorer(Kusto)용 Python 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>클러스터 보안 주체 추가

다음 예제에서는 프로그래밍 방식으로 클러스터 주체를 추가 하는 방법을 보여 줍니다.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscription_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| client_secret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resource_group_name | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| principal_assignment_name | *clusterPrincipalAssignment1* | 클러스터 보안 주체 리소스의 이름입니다.|
| principal_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 사용자 전자 메일, 응용 프로그램 ID 또는 보안 그룹 이름일 수 있는 보안 주체 ID입니다.|
| 역할(role) | *AllDatabasesAdmin* | ' AllDatabasesAdmin' ' 또는 ' AllDatabasesViewer ' 일 수 있는 클러스터 보안 주체의 역할입니다.|
| tenant_id_for_principal | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 보안 주체의 테넌트 ID입니다.|
| principal_type | *앱* | ' 사용자 ', ' 앱 ' 또는 ' 그룹 ' 일 수 있는 보안 주체의 유형입니다.|

## <a name="next-steps"></a>다음 단계

* [데이터베이스 보안 주체 추가](database-principal-python.md)
