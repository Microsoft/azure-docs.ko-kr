---
title: '빠른 시작: Azure CLI를 사용하여 Synapse 작업 영역 만들기'
description: 이 가이드의 단계에 따라 Azure CLI를 사용하여 Azure Synapse 작업 영역을 만듭니다.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7f0bf7d409c5e47abaaa5b59271f55952d8ccff4
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871591"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Synapse 작업 영역 만들기

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경입니다. 브라우저에서 Azure Cloud Shell과 함께 사용할 수 있습니다. macOS, Linux 또는 Windows에 설치하여 명령줄에서 실행할 수도 있습니다.

이 빠른 시작에서는 Azure CLI를 사용하여 Synapse 작업 영역을 만드는 방법을 알아봅니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>필수 구성 요소

- 간단하고 유연한 명령줄 JSON 프로세서인 [jq](https://stedolan.github.io/jq/download/)를 다운로드하여 설치합니다.
- [Azure Data Lake Storage Gen2 스토리지 계정](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > 선택한 ADLS Gen2 계정을 Azure Synapse 작업 영역에서 읽고 쓸 수 있어야 합니다. 또한 기본 스토리지 계정으로 연결하는 모든 스토리지 계정의 경우 [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account) 페이지에 설명된 대로 스토리지 계정을 만들 때 **계층 구조 네임스페이스**를 사용하도록 설정해야 합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>로컬로 Azure CLI 설치

Azure CLI를 로컬로 설치하고 사용하도록 선택한 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure CLI를 로컬로 실행하는 경우 로그인하여 인증해야 합니다. Azure Cloud Shell을 사용하는 경우 이 단계가 필요하지 않습니다. Azure CLI에 로그인하려면 `az login`을 실행하고 브라우저 창에서 인증합니다.

```azurecli
az login
```

Azure CLI의 인증에 대한 자세한 내용은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Azure CLI용 Azure Synapse 확장 설치

```azurecli
az extension add --name synapse
```

> [!WARNING]
> Azure CLI용 Azure Synapse 확장은 미리 보기로 제공됩니다.

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Synapse 작업 영역 만들기

1. Azure Synapse 작업 영역에 대한 리소스를 만드는 데 필요한 환경 변수를 정의합니다.

    | 환경 변수 이름 | 설명 |
    |---|---|---|
    |StorageAccountName| 기존 ADLS Gen2 스토리지 계정에 대한 이름입니다.|
    |StorageAccountResourceGroup| 기존 ADLS Gen2 스토리지 계정 리소스 그룹의 이름입니다. |
    |FileShareName| 기존 스토리지 파일 시스템의 이름입니다.|
    |SynapseResourceGroup| Azure Synapse 리소스 그룹의 새 이름을 선택합니다. |
    |지역| [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/#overview) 중 하나를 선택합니다. |
    |SynapseWorkspaceName| 새 Azure Synapse 작업 영역의 고유한 이름을 선택합니다. |
    |SqlUser| 새 사용자 이름에 대한 값을 선택합니다.|
    |SqlPassword| 보안 암호를 선택합니다.|
    |||

2. Azure Synapse 작업 영역의 컨테이너로 리소스 그룹을 만듭니다.
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. ADLS Gen 2 Storage 계정 키를 검색합니다.
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. ADLS Gen 2 Storage Endpoint URL을 검색합니다.
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (선택 사항) 항상 ADLS Gen2 Storage 계정 키와 엔드포인트 항목을 확인할 수 있습니다.
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Azure Synapse 작업 영역을 만듭니다.
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Azure Synapse 작업 영역에 대한 웹 및 개발 URL을 가져옵니다.
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. 머신에서 Azure Synapse 작업 영역에 액세스할 수 있도록 허용하는 방화벽 규칙을 만듭니다.

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. 환경 변수 `WorkspaceWeb`에 저장된 Azure Synapse 작업 영역 웹 URL 주소를 열어 작업 영역에 액세스합니다.

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Azure Synapse 작업 영역 웹](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>리소스 정리

Azure Synapse 작업 영역을 삭제하려면 다음 단계를 수행합니다.
> [!WARNING]
> Azure Synapse 작업 영역을 삭제하면 포함된 SQL 풀 및 작업 영역 메타데이터 데이터베이스에 저장된 분석 엔진과 데이터가 제거됩니다. 더 이상 SQL 또는 Apache Spark 엔드포인트에 연결할 수 없게 됩니다. 모든 코드 아티팩트(쿼리, Notebook, 작업 정의 및 파이프라인)가 삭제됩니다.
>
> 작업 영역을 삭제해도 작업 영역에 연결된 Data Lake Store Gen2의 데이터에는 영향을 주지 **않습니다**.

Azure Synapse 작업 영역을 삭제하려면 다음 명령을 완료합니다.

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>다음 단계

다음으로 [SQL 풀 만들기](quickstart-create-sql-pool-studio.md) 또는 [Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-studio.md)를 수행하여 데이터를 분석하고 살펴볼 수 있습니다.
