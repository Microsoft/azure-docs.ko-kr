---
title: Azure CLI를 사용하여 Azure Database for MariaDB 방화벽 규칙 만들기 및 관리
description: 이 문서에서는 Azure CLI 명령줄을 사용하여 Azure Database for MariaDB 방화벽 규칙을 만들고 관리하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038623"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MariaDB 방화벽 규칙 만들기 및 관리
서버 수준 방화벽 규칙은 특정 IP 주소 또는 IP 주소에서 MariaDB 서버에 대 한 Azure Database에 대 한 액세스 관리를 사용할 수 있습니다. 편리한 Azure CLI 명령을 사용하면 서버를 관리하는 방화벽 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. MariaDB 방화벽에 대 한 Azure Database의 개요를 보려면 [MariaDB 서버 방화벽 규칙에 대 한 Azure Database](./concepts-firewall-rules.md)합니다.

Virtual Network (VNet) 규칙은 서버에 대 한 액세스를 보호 하려면 데도 사용할 수 있습니다. 에 대해 자세히 알아보세요 [만들기 및 관리 가상 네트워크 서비스 끝점 및 Azure CLI를 사용 하 여 규칙](howto-manage-vnet-cli.md)합니다.

## <a name="prerequisites"></a>필수 조건
* [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Azure Database for MariaDB 서버 및 데이터베이스](quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>방화벽 규칙 명령:
**az mariadb server firewall-rule** 명령은 Azure CLI에서 방화벽 규칙을 만들고, 삭제, 나열, 표시 및 업데이트하는 데 사용됩니다.

명령:
- **create**: Azure MariaDB 서버 방화벽 규칙을 만듭니다.
- **delete**: Azure MariaDB 서버 방화벽 규칙을 삭제합니다.
- **list**: Azure MariaDB 서버 방화벽 규칙을 나열합니다.
- **show**: Azure MariaDB 서버 방화벽 규칙의 세부 정보를 표시합니다.
- **update**: Azure MariaDB 서버 방화벽 규칙을 업데이트합니다.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Azure에 로그인 하 고 MariaDB 서버용 Azure Database 목록
**az login** 명령을 사용하여 Azure 계정으로 Azure CLI를 안전하게 연결합니다.

1. 명령줄에서 다음 명령을 실행합니다.
   ```azurecli
   az login
   ```
   이 명령은 다음 단계에서 사용할 코드를 출력합니다.

2. 웹 브라우저를 사용하여 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 페이지를 연 다음, 제공된 코드를 입력하세요.

3. 프롬프트에서 Azure 자격 증명을 사용 하 여 로그인 합니다.

4. 로그인 권한이 부여되면 구독 목록은 콘솔에 인쇄됩니다. 원하는 구독의 ID를 복사하여 사용할 현재 구독을 설정합니다. [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. 이름이 확실치 않은 경우 리소스 그룹 및 구독에 대한 Azure Database for MariaDB 서버를 나열합니다. [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list) 명령을 사용합니다.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   나열되는 이름 특성은 작업할 MariaDB 서버를 지정하는 데 필요합니다. 필요한 경우 해당 서버에 대한 세부 정보를 확인하고 이름 특성을 사용하여 올바른지 확인합니다. [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) 명령을 사용합니다.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에서 방화벽 규칙 나열 
서버 이름 및 리소스 그룹 이름을 사용하여 기존 서버 방화벽 규칙을 서버에 나열합니다. [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) 명령을 사용합니다.  서버 이름 특성은 **--name** 스위치가 아닌 **--server** 스위치에서 지정됩니다. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
출력에 기본적으로 JSON 형식으로 규칙(있는 경우)이 나열됩니다. **--output table** 스위치를 사용하여 결과를 좀 더 읽기 쉬운 테이블 형식으로 출력할 수 있습니다.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에서 방화벽 규칙 만들기
Azure MariaDB 서버 이름 및 리소스 그룹 이름을 사용하여 서버에 새로운 방화벽 규칙을 만듭니다. [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) 명령을 사용합니다. 규칙에 사용할 이름, 규칙에 대한 시작 IP 및 끝 IP(IP 주소 범위에 액세스 권한 제공)를 제공합니다.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

단일 IP 주소의 액세스를 허용하려면 이 예제에서와 같이 시작 IP 및 끝 IP와 동일한 IP 주소를 제공합니다.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Azure IP 주소의 애플리케이션에서 Azure Database for MariaDB 서버에 연결할 수 있게 하려면 다음 예제와 같이 0.0.0.0 IP 주소를 시작 IP와 종료 IP로 제공합니다.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

성공하면 각각의 create 명령 출력은 만든 방화벽 규칙의 세부 정보를 JSON 형식(기본값)으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에서 방화벽 규칙 업데이트 
Azure MariaDB 서버 이름 및 리소스 그룹 이름을 사용하여 서버에서 기존 방화벽 규칙을 업데이트합니다. [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) 명령을 사용합니다. 기존 방화벽 규칙의 이름과 업데이트할 시작 IP 및 끝 IP 특성을 입력합니다.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
성공하면 명령 출력은 업데이트한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

> [!NOTE]
> 방화벽 규칙이 존재하지 않는 경우 업데이트 명령에 의해 생성됩니다.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에서 방화벽 규칙 세부 정보 표시
Azure MariaDB 서버 이름 및 리소스 그룹 이름을 사용하여 서버에서 기존 방화벽 규칙 세부 정보를 표시합니다. [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) 명령을 사용합니다. 기존 방화벽 규칙의 이름을 입력합니다.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
성공하면 명령 출력은 지정한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에서 방화벽 규칙 삭제
Azure MariaDB 서버 이름 및 리소스 그룹 이름을 사용하여 서버에서 기존 방화벽 규칙을 제거합니다. [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) 명령을 사용합니다. 기존 방화벽 규칙의 이름을 제공합니다.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
성공하면 출력은 없습니다. 실패하면 오류 메시지 텍스트가 표시됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MariaDB 서버 방화벽 규칙](./concepts-firewall-rules.md)에 대해 자세히 알아봅니다.
- [Azure Portal을 사용하여 Azure Database for MariaDB 방화벽 규칙을 만들고 관리합니다](./howto-manage-firewall-portal.md).
- 서버에 대 한 액세스 보안을 강화할 [만들기 및 관리 가상 네트워크 서비스 끝점 및 Azure CLI를 사용 하 여 규칙](howto-manage-vnet-cli.md)합니다.
