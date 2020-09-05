---
title: '빠른 시작: 서버 만들기 - Azure Portal - Azure Database for PostgreSQL - 단일 서버'
description: 이 빠른 시작 가이드에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL 서버를 만들고 관리합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9b3e1916301d6346defd6ebeab2f3784882f2d5f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798859"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Database for PostgreSQL 서버 만들기

PostgreSQL용 Azure Database는 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 약 5분만에 Azure Database for PostgreSQL 서버를 만드는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
웹 브라우저를 열고 [포털](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

Azure Database for PostgreSQL 서버는 구성된 [컴퓨팅 및 스토리지 리소스](./concepts-pricing-tiers.md) 세트로 만들어집니다. 서버는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) 내에 만들어집니다.

Azure Database for PostgreSQL 서버를 만들려면 다음을 수행합니다.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.

2. **데이터베이스** > **PostgreSQL용 Azure Database**를 차례로 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![메뉴에 "Azure Database for PostgreSQL"을 보여주는 스크린샷.](./media/quickstart-create-database-portal/1-create-database.png)

3. **단일 서버** 배포 옵션을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Database for PostgreSQL에 대한 단일 서버 배포 옵션을 선택하는 스크린샷.](./media/quickstart-create-database-portal/select-deployment-option.png)

4. 다음 정보로 **기본 사항** 양식을 입력합니다.

   > [!div class="mx-imgBorder"]
   > ![단일 서버를 만들기 위한 기본 탭을 보여주는 스크린샷.](./media/quickstart-create-database-portal/create-basics.png)

   설정|제안 값|Description
   ---|---|---
   Subscription|구독 이름|서버에 사용할 Azure 구독입니다. 구독이 여러 개인 경우 해당 리소스에 대해 요금이 청구되는 적절한 구독을 선택합니다.
   Resource group|*myresourcegroup*| 새 리소스 그룹 이름 또는 구독의 기존 이름입니다.
   서버 이름 |*mydemoserver*|PostgreSQL 서버용 Azure Database를 식별하는 고유한 이름입니다. 사용자가 제공한 서버 이름에 *postgres.database.azure.com* 도메인 이름이 추가됩니다. 서버는 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자를 포함해야 합니다.
   데이터 원본 | **없음** | 새 서버를 처음부터 만들려면 **없음**을 선택합니다. (기존 Azure Database for PostgreSQL 서버의 지역 백업에서 서버를 만든 경우 **백업**을 선택합니다.)
   관리자 사용자 이름 |*myadmin*| 서버에 연결할 경우 사용할 사용자 고유의 로그인 계정입니다. 관리자 로그인 이름은 **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** 또는 **public**이 될 수 없습니다. **pg_** 로 시작할 수 없습니다.
   암호 |사용자 암호| 서버 관리자 계정의 새 암호입니다. 다음 세 가지 범주에서 8~128자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(예: !, $, #, %).
   위치|사용자와 가장 가까운 지역| 사용자에게 가장 가까운 위치입니다.
   버전|최신 주 버전| 다른 특정 요구 사항이 없는 한 최신 PostgreSQL 주 버전입니다.
   컴퓨팅 + 스토리지 | **범용**, **5세대**, **vCore 2개**, **5GB**, **7일**, **지역 중복** | 새 서버에 대한 컴퓨팅, 스토리지 및 백업 구성입니다. **서버 구성**을 선택합니다. 그런 다음, 적절한 가격 책정 계층을 선택합니다. 자세한 내용은 [가격 책정 정보](https://azure.microsoft.com/pricing/details/postgresql/server/)를 참조하세요. 지역 중복 스토리지에서 서버 백업을 사용하도록 설정하려면 **백업 중복 옵션**에서 **지역 중복**을 선택합니다. **확인**을 선택합니다.

   > [!NOTE]
   > 워크로드에 가벼운 컴퓨팅 및 I/O가 적합한 경우 기본 가격 책정 계층을 고려합니다. 기본 가격 책정 계층에서 만든 서버는 나중에 범용으로 또는 메모리 최적화되도록 확장할 수 없습니다. 
   
5. **검토 + 만들기**를 선택하여 선택 사항을 검토합니다. **만들기**를 선택하여 서버를 프로비전합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

6. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림** 아이콘(벨)을 선택합니다. 배포가 완료되면 **리소스로 이동**을 선택하여 서버의 **개요** 페이지를 엽니다.

빈 데이터베이스 **postgres**가 생성됩니다. 관리되는 서비스 프로세스를 사용자 작업과 구분하는 데 사용되는 **azure_maintenance** 데이터베이스도 찾을 수 있습니다. **azure_maintenance** 데이터베이스에 액세스할 수 없습니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성
기본적으로 사용자가 만든 서버는 공개적으로 액세스할 수 없습니다. IP 주소에 대한 사용 권한을 부여해야 합니다. Azure Portal의 서버 리소스로 이동하여 서버 리소스의 왼쪽 메뉴에서 **연결 보안**을 선택합니다. 리소스를 찾는 방법을 모를 경우 [리소스 열기](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)를 참조하세요.

> [!div class="mx-imgBorder"]
> ![연결 보안에 대한 방화벽 규칙을 보여주는 스크린샷.](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
**현재 클라이언트 IP 주소 추가**를 선택한 다음, **저장**을 선택합니다. 추가 IP를 추가하거나 해당 IP 주소에서 서버에 연결할 IP 범위를 제공할 수 있습니다. 자세한 내용은 [Azure Database for PostgreSQL의 방화벽 규칙](./concepts-firewall-rules.md)을 참조하세요.
   
> [!NOTE]
> 연결 문제를 방지하려면 네트워크에서 포트 5432를 통한 아웃바운드 트래픽을 허용하는지 확인합니다. Azure Database for PostgreSQL은 해당 포트를 사용합니다.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>psql을 사용하여 Azure Database for PostgreSQL 서버에 연결

인기 있는 PostgreSQL 클라이언트인 [psql](http://postgresguide.com/utilities/psql.html) 또는 [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)을 사용할 수 있습니다. 이 빠른 시작에서는 Azure Portal 내의 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)에서 psql을 사용하여 연결합니다.

1. 서버의 **개요** 섹션에서 새로 만든 서버에 대한 서버 이름, 서버 관리자 로그인 이름, 암호 및 구독 ID를 기록해 둡니다.

2. 왼쪽 위에 있는 아이콘을 선택하여 포털에서 Azure Cloud Shell을 엽니다.

   > [!NOTE]
   > Cloud Shell을 처음 여는 경우 리소스 그룹과 스토리지 계정을 생성하라는 메시지가 표시됩니다. 이는 일회성 단계이며 모든 세션에서 자동으로 연결됩니다. 

   > [!div class="mx-imgBorder"]
   > ![서버 정보 및 Azure Cloud Shell을 열기 위한 아이콘을 보여주는 스크린샷.](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Azure Cloud Shell 터미널에서 다음 명령을 실행합니다. 값을 실제 서버 이름 및 관리 사용자 로그인 이름으로 바꿉니다. `<admin-username>@<servername>` 형식의 관리자와 함께 빈 데이터베이스 **postgres**를 사용합니다.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Cloud Shell 터미널의 환경은 다음과 같습니다.
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. 동일한 Azure Cloud Shell 터미널에서 **게스트**라는 데이터베이스를 만듭니다.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. 연결을 새로 만든 **게스트** 데이터베이스로 전환합니다.

   ```bash
   \c guest
   ```
6. `\q`를 입력한 다음, Enter 키를 선택하여 psql을 닫습니다. 

## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹에서 Azure Database for PostgreSQL 서버를 성공적으로 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 경우에는 리소스 그룹 또는 PostgreSQL 서버를 삭제하여 리소스를 삭제할 수 있습니다. 

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal에서 **리소스 그룹**을 검색하고 선택합니다. 
2. 리소스 그룹 목록에서 리소스 그룹의 이름을 선택합니다.
3. 리소스 그룹의 **개요** 페이지에서 **리소스 그룹 삭제**를 선택합니다.
4. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제**를 선택합니다.

서버를 삭제하려면 서버의 **개요** 페이지에서 **삭제** 단추를 선택합니다.

> [!div class="mx-imgBorder"]
> ![서버 삭제 단추를 보여주는 스크린샷.](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
