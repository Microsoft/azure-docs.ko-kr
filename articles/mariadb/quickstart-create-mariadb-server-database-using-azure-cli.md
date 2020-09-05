---
title: '빠른 시작: 서버 만들기 - Azure CLI - Azure Database for MariaDB'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 Azure Database for MariaDB 서버를 만드는 방법을 살펴봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7fe68e7b1a56c22e8c0d9638408982518105888e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185165"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Database for MariaDB 서버 만들기

Azure CLI를 사용하여 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리할 수 있습니다. 이 빠른 시작에서는 약 5분 안에 Azure CLI를 사용하여 Azure 리소스 그룹에서 Azure Database for MariaDB를 만드는 방법을 살펴봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하는 경우 이 빠른 시작에서는 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

구독이 여러 개인 경우 리소스 또는 구독 요금이 청구되는 구독을 선택합니다. 계정에 속한 특정 구독 ID를 선택하려면 [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다.

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버 만들기

[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) 명령을 사용하여 Azure Database for MariaDB 서버를 만듭니다. 서버는 여러 데이터베이스를 관리할 수 있습니다. 일반적으로 각 프로젝트 또는 각 사용자에 대해 별도의 데이터베이스가 사용됩니다.

설정 | 샘플 값 | Description
---|---|---
name | **mydemoserver** | Azure Database for MariaDB 서버를 식별하는 고유한 이름을 입력합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자여야 합니다.
resource-group | **myresourcegroup** | Azure 리소스 그룹의 이름을 입력합니다.
sku-name | **GP_Gen5_2** | SKU의 이름입니다. *가격 책정 계층*\_*계산 세대*\_*vCores* 규칙을 축약형으로 따릅니다. **sku-name** 매개 변수에 대한 자세한 내용은 이 표 다음에 나오는 섹션을 참조하세요.
backup-retention | **7** | 백업을 보존하는 기간입니다. 단위는 일입니다. 범위: 7 ~ 35 
geo-redundant-backup | **사용 안 함** | 이 서버에 지역 중복 백업을 사용할 것인지 여부를 결정합니다. 허용되는 값은 다음과 같습니다. **사용**, **사용 안 함**
위치 | **westus** | 서버에 대한 Azure 위치입니다.
ssl-enforcement | **Enabled** | 이 서버에 SSL을 사용할 것인지 여부를 결정합니다. 허용되는 값은 다음과 같습니다. **사용**, **사용 안 함**
storage-size | **51200** | 서버의 스토리지 용량입니다(단위는 메가바이트). 유효한 스토리지 크기는 5,120MB(최소)이고 1,024MB 단위로 증가합니다. 스토리지 크기 한도에 대한 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요. 
버전 | **10.2** | MariaDB 주 엔진 버전입니다.
admin-user | **myadmin** | 관리자 로그인에 대한 사용자 이름입니다. **admin-user** 매개 변수는 **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public**일 수 없습니다.
admin-password | *사용자 암호* | 관리자 사용자의 암호입니다. 암호는 8-128자여야 합니다. 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자 및 영숫자가 아닌 문자

sku-name 매개 변수 값은 아래 예에서와 같이 {가격 책정 계층}\_{컴퓨팅 세대}\_{vCores} 규칙을 따릅니다.
+ `--sku-name B_Gen5_1`은 기본, 5세대 및 1개 vCore에 매핑됩니다. 이 옵션은 사용 가능한 가장 작은 SKU입니다.
+ `--sku-name GP_Gen5_32`는 범용, 5세대 및 vCore 32개에 매핑됩니다.
+ `--sku-name MO_Gen5_2`는 메모리 최적화, 5세대 및 vCore 2개에 매핑됩니다.

지역 및 계층별로 유효한 값에 대한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

다음 예제는 미국 서부 지역에 **mydemoserver**라는 서버를 만듭니다. 이 서버는 리소스 그룹 **myresourcegroup**에 있으며 서버 관리자 로그인 **myadmin**을 갖고 있습니다. 이 서버는 범용 가격 책정 계층의 Gen 5 서버와 vCore 2개를 갖고 있습니다. 서버 이름은 DNS 이름에 매핑되며 Azure에서 글로벌하게 고유해야 합니다. `<server_admin_password>`를 자신의 서버 관리자 암호로 바꾸세요.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> 워크로드에 가벼운 컴퓨팅 및 I/O가 적합한 경우 기본 가격 책정 계층을 고려합니다. 기본 가격 책정 계층에서 만든 서버는 나중에 범용으로 또는 메모리 최적화되도록 확장할 수 없습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mariadb/)를 참조하세요.

## <a name="configure-a-firewall-rule"></a>방화벽 규칙 구성

[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) 명령을 사용하여 Azure Database for MariaDB 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 mysql 명령줄 도구 또는 MySQL Workbench 같은 외부 애플리케이션에서 Azure Database for MariaDB 서비스 방화벽을 통해 서버에 연결할 수 있습니다.

다음 예제는 특정 IP 주소 192.168.0.1에서 연결하는 것을 허용하는 `AllowMyIP` 방화벽 규칙을 만듭니다. 연결하는 위치에 해당하는 IP 주소 또는 IP 주소 범위로 바꾸세요.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MariaDB에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열어야만 서버에 연결할 수 있습니다.

## <a name="configure-ssl-settings"></a>SSL 설정 구성

기본적으로 서버와 클라이언트 애플리케이션 간에 SSL 연결이 적용됩니다. 이 기본 설정은 인터넷을 통해 데이터 스트림을 암호화하여 "이동 중"인 데이터를 보호합니다. 이 빠른 시작에서는 서버에 SSL 연결을 사용하지 않겠습니다. SSL 비활성화는 프로덕션 서버에는 권장되지 않습니다. 자세한 내용은 [Azure Database for MariaDB에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.

다음 예제에서는 Azure Database for MariaDB 서버에 적용되는 SSL을 사용하지 않습니다.

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다. 연결 정보를 가져오려면 다음 명령을 실행합니다.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin** 값을 기록해 둡니다.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>mysql 명령줄 도구를 사용하여 서버에 연결

mysql 명령줄 도구를 사용하여 서버에 연결합니다. 명령줄 도구를 [다운로드](https://dev.mysql.com/downloads/)하여 컴퓨터에 설치할 수 있습니다. 이 문서의 코드 샘플에서 **사용해보기** 단추를 선택하여 명령줄 도구에 액세스할 수도 있습니다. 명령줄 도구에 액세스하는 또 다른 방법은 Azure Portal의 오른쪽 위 도구 모음에서 **>_** 단추를 선택하여 **Azure Cloud Shell**을 여는 것입니다.

mysql 명령줄 도구를 사용하여 서버에 연결하려면:

1. 서버에 연결합니다.

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. `mysql>` 프롬프트에서 서버 상태를 봅니다.

   ```sql
   status
   ```

   다음 텍스트와 비슷한 내용이 표시됩니다.

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> 다른 명령은 [MySQL 5.7 참조 설명서 - 4.5.1장](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)을 참조하세요.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>MySQL Workbench를 사용하여 서버에 연결

1. 클라이언트 컴퓨터에서 MySQL Workbench를 엽니다. 설치되어 있지 않은 경우 애플리케이션을 [다운로드](https://dev.mysql.com/downloads/workbench/)하여 설치합니다.

2. **새 연결 설정** 대화 상자의 **매개 변수** 탭에서 다음 정보를 입력합니다.

   ![새 연결 설정](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | 설정 | 제안 값 | Description |
   |---|---|---|
   | 연결 이름 | **데모 연결** | 이 연결의 레이블 입력(아무 연결 이름이나 가능) |
   | 연결 방법 | **표준(TCP/IP)** | TCP/IP 프로토콜을 사용하여 Azure Database for MariaDB에 연결 |
   | Hostname | **mydemoserver.mariadb.database.azure.com** | 앞에서 기록한 서버 이름. |
   | 포트 | **3306** | Azure Database for MariaDB의 기본 포트. |
   | 사용자 이름 | **myadmin\@mydemoserver** | 앞에서 기록한 서버 관리자 로그인. |
   | 암호 | *사용자 암호* | 이전에 설정한 관리자 계정 암호를 사용합니다. |

3. 모든 매개 변수가 올바르게 구성되었는지 테스트하려면 **연결 테스트**를 선택합니다.

4. 연결을 선택하여 서버에 연결합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 사용한 리소스가 다른 빠른 시작 또는 자습서에 필요 없는 경우 다음 명령을 수행하여 삭제할 수 있습니다. 

```azurecli-interactive
az group delete --name myresourcegroup
```

이 빠른 시작에서 만든 서버만 삭제하려면 [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)을 실행합니다.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI로 MariaDB 데이터베이스 디자인](./tutorial-design-database-cli.md)
