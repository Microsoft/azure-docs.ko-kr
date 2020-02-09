---
title: VNet 엔드포인트 관리-Azure CLI-Azure Database for MariaDB
description: 이 문서에서는 Azure CLI 명령줄을 사용하여 Azure Database for MariaDB VNet 서비스 엔드포인트 및 규칙을 만들고 관리하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f15c8230c2dad558e9a125ebe6874a7429f3488
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965898"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MariaDB VNet 서비스 엔드포인트 만들기 및 관리

VNet(Virtual Network) 서비스 엔드포인트 및 규칙이 Virtual Network의 프라이빗 주소 공간을 Azure Database for MariaDB 서버로 확장합니다. 편리한 Azure CLI(명령줄 인터페이스) 명령을 사용하면 서버를 관리하는 VNet 서비스 엔드포인트 및 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. 제한을 포함하여 Azure Database for MariaDB VNet 서비스 엔드포인트에 대한 개요는 [Azure Database for MariaDB 서버 VNet 서비스 엔드포인트](concepts-data-access-security-vnet.md)를 참조하세요. VNet 서비스 엔드포인트는 Azure Database for MariaDB에 대해 지원되는 모든 지역에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure CLI](/cli/azure/install-azure-cli)를 설치하거나 브라우저에서 Azure Cloud Shell을 사용합니다.
- [Azure Database for MariaDB 서버 및 데이터베이스](quickstart-create-mariadb-server-database-using-azure-cli.md)

> [!NOTE]
> VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

## <a name="configure-vnet-service-endpoints"></a>VNet 서비스 엔드포인트 구성
가상 네트워크를 구성하는 데 [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) 명령이 사용됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

CLI를 로컬로 실행 중인 경우 [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 명령을 사용하여 사용자 계정에 로그인해야 합니다. 해당 구독 이름에 대한 명령 출력에서 **id** 속성을 참고합니다.
```azurecli-interactive
az login
```

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 구독에 대한 **az login** 출력의 **id** 속성을 구독 ID 자리 표시자로 바꿉니다.

- 계정에는 가상 네트워크 및 서비스 엔드포인트를 만드는 데 필요한 사용 권한이 있어야 합니다.

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다.

VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/"에 대한 사용 권한을 갖고 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.

[기본 제공 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 및 [사용자 지정 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)에 특정 권한 할당에 대해 자세히 알아보세요.

VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. VNet 및 Azure 서비스 리소스가 서로 다른 구독에 있는 경우 리소스가 동일한 AD(Active Directory) 테넌트에 있어야 합니다. 두 구독 모두에 **Microsoft .sql** 리소스 공급자가 등록 되어 있는지 확인 합니다. 자세한 내용은 [resource manager-등록][resource-manager-portal] 을 참조 하세요.

> [!IMPORTANT]
> 서비스 엔드포인트를 구성하기 전에 서비스 엔드포인트 및 고려 사항에 대한 이 문서를 읽어보는 것이 매우 좋습니다. **Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. VNet 서비스 엔드포인트는 SQL Database라는 Azure 서비스를 나타내는 서비스 형식 이름 **Microsoft.Sql**을 사용합니다. 이 서비스 태그는 Azure SQL Database, Azure Database for MariaDB, PostgreSQL 및 MySQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB 및 Azure Database for MySQL 서버를 포함하는 모든 Azure Database 서비스의 서비스 엔드포인트 트래픽을 서브넷에서 구성합니다.

### <a name="sample-script"></a>샘플 스크립트

이 샘플 스크립트는 Azure Database for MariaDB 서버, VNet, VNet 서비스 엔드포인트를 만들고 VNet 규칙으로 서브넷에 대해 서버를 보호하는 데 사용됩니다. 이 샘플 스크립트에서 관리자 사용자 이름 및 암호를 변경합니다. `az account set --subscription` 명령에 사용된 구독 ID를 자신의 구독 ID로 바꿉니다.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md