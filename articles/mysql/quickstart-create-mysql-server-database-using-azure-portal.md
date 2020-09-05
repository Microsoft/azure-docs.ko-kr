---
title: '빠른 시작: 서버 만들기 - Azure Portal - Azure Database for MySQL'
description: 이 문서에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버 샘플을 만드는 과정을 안내합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: 840e7769e8a242314b10562684c942a2468ea5ad
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705149"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Database for MySQL 서버 만들기

Azure Database for MySQL은 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리되는 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버를 만드는 방법을 보여 줍니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
웹 브라우저를 연 다음 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
정의된 [컴퓨팅 및 스토리지 리소스](./concepts-compute-unit-and-storage.md) 세트를 사용하여 Azure Database for MySQL 서버를 만듭니다. 서버는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) 내에 만듭니다.

다음 단계에 따라 Azure Database for MySQL 서버를 만듭니다.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.

2. **데이터베이스** > **Azure Database for MySQL**을 차례로 선택합니다. 검색 상자에서 **MySQL**을 입력하여 해당 서비스를 찾을 수도 있습니다.

  
>[!div class="mx-imgBorder"]
> ![Azure Database for MySQL 옵션](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 새 서버 세부 정보 양식을 다음 정보로 작성합니다.
    
>[!div class="mx-imgBorder"]
> ![서버 양식 만들기](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)

**설정** | **제안 값** | **필드 설명** 
---|---|---
Subscription | 사용자의 구독 | 서버에 사용할 Azure 구독을 선택합니다. 구독이 여러 개인 경우, 리소스에 대해 요금이 청구되는 구독을 선택합니다.
Resource group | *myresourcegroup* | 새 또는 기존 리소스 그룹 이름을 제공합니다. 리소스 그룹은 단일 프로젝트에 속한 종속성을 구성하는 데 사용할 수 있습니다.
서버 이름 | 고유 서버 이름 | Azure Database for MySQL 서버를 식별하는 고유한 이름을 입력합니다. 예를 들면 'mysqldbserver'입니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
데이터 원본 |*없음* | 새 서버를 처음부터 만들려면 *없음*을 선택합니다. (기존 Azure Database for MySQL 서버의 지역 백업에서 서버를 만든 경우 *백업*을 선택합니다).
서버 관리자 로그인 | myadmin | 서버 관리자의 사용자 이름을 입력합니다. 관리자 사용자 이름으로 **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public**을 사용할 수 없습니다.
암호 | *사용자 선택* | 서버 관리자 계정의 새 암호를 제공합니다. 암호는 대문자 또는 소문자, 숫자 및 영숫자가 아닌 문자(!, $, #, % 등)를 조합하여 8~128자 길이여야 합니다.
암호 확인 | *사용자 선택*| 관리자 계정 암호를 확인합니다.
위치 | *사용자와 가장 가까운 지역*| 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다.
버전 | *최신 주 버전*| 최신 주 버전입니다(다른 버전이 필요한 특정 요구 사항이 없는 경우).
컴퓨팅 + 스토리지 | **범용**, **5세대**, **vCore 2개**, **5GB**, **7일**, **지역 중복** |새 서버에 대한 컴퓨팅, 스토리지 및 백업 구성입니다. **서버 구성**을 선택합니다. 다음으로 적절한 가격 책정 계층을 선택합니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mysql/)를 참조하세요. 지역 중복 스토리지에서 서버 백업을 사용하도록 설정하려면 **백업 중복 옵션**에서 **지역 중복**을 선택합니다. **확인**을 선택합니다.

   > [!NOTE]
   > 워크로드에 가벼운 컴퓨팅 및 I/O가 적합한 경우 기본 가격 책정 계층을 고려합니다. 기본 가격 책정 계층에서 만든 서버는 나중에 범용으로 또는 메모리 최적화되도록 확장할 수 없습니다. 

4. **검토 + 만들기**를 선택하여 서버를 프로비저닝합니다. 프로비전에는 최대 20분이 걸릴 수 있습니다.
   
5. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림**(벨 아이콘)을 클릭합니다.
   
기본적으로 서버 아래에 **information_schema**, **mysql**, **performance_schema** 및 **sys** 데이터베이스가 만들어집니다.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성
기본적으로 만들어진 서버는 방화벽으로 보호되며 공개적으로 액세스할 수 없습니다. IP에 대한 액세스 권한을 부여하려면 Azure Portal의 서버 리소스로 이동하여 서버 리소스의 왼쪽 메뉴에서 **연결 보안**을 선택합니다. 리소스를 찾는 방법을 모를 경우 [리소스를 여는 방법](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)을 참조하세요.

>[!div class="mx-imgBorder"]
> ![연결 보안 - 방화벽 규칙](./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png)
   
이제 **현재 클라이언트 IP 주소 추가**를 선택한 다음, **저장**을 선택합니다. 추가 IP를 추가하거나 IP 범위를 제공하여 해당 IP에서 서버에 연결할 수 있습니다. 자세한 내용은 [Azure Database for MySQL 서버에서 방화벽 규칙을 관리하는 방법](./concepts-firewall-rules.md)을 참조하세요.

> [!NOTE]
> 네트워크에서 연결 문제를 방지하기 위해 Azure Database for MySQL에서 사용하는 포트 3306을 통한 아웃바운드 트래픽을 허용하는지 확인합니다.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>mysql 명령줄 클라이언트를 사용하여 Azure Database for MySQL 서버에 연결
[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 선택하여 로컬 환경에서 서버에 연결할 수 있습니다. 이 빠른 시작에서는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)에서 **mysql.exe**를 실행하여 서버에 연결합니다.

1. 왼쪽 위에 있는 강조 표시된 아이콘을 클릭하여 포털에서 Azure Cloud Shell을 시작합니다. 아래 이미지와 같이 **개요** 섹션에서 새로 만든 서버에 대한 서버 이름, 서버 관리자 로그인 이름, 암호 및 구독 ID를 기록해 둡니다.

    >[!NOTE]
    >클라우드 셸을 처음 시작하는 경우 리소스 그룹, 스토리지 계정을 생성하라는 메시지가 표시됩니다. 이는 일회성 단계이며 모든 세션에서 자동으로 연결됩니다. 

   >[!div class="mx-imgBorder"]
   > ![포털 전체 보기 Cloud Shell](./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png)
2. Azure Cloud Shell 터미널에서 이 명령을 실행합니다. 값을 실제 서버 이름 및 관리 사용자 로그인 이름으로 바꿉니다. 관리자 사용자 이름에는 아래와 같이 Azure Database for MySQL에 대해 '@\<servername>'이 필요합니다.  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Cloud Shell 터미널에서의 환경은 다음과 같습니다.
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. 동일한 Azure Cloud Shell 터미널에서 데이터베이스 **게스트**를 만듭니다. 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. 데이터베이스 **게스트**로 변경
  ```
  mysql> USE guest;
  Database changed 
  ```
5. ```quit```를 입력한 다음, Enter 키를 선택하여 mysql을 종료합니다.   

## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹에서 Azure Database for MySQL 서버를 성공적으로 만들었습니다.  이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 MySQL 서버만 삭제하여 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하려면 다음 단계를 수행합니다.
1. Azure Portal에서 **리소스 그룹**을 검색하고 선택합니다. 
2. 리소스 그룹 목록에서 리소스 그룹의 이름을 선택합니다.
3. 리소스 그룹의 개요 페이지에서 **리소스 그룹 삭제**를 선택합니다.
4. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제**를 선택합니다.

서버를 삭제하려면 아래와 같이 서버의 **개요** 페이지에서 **삭제** 단추를 클릭하면 됩니다.
> [!div class="mx-imgBorder"]
> ![리소스 삭제](media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
>[MySQL을 사용하여 Windows에서 PHP 앱 빌드](../app-service/app-service-web-tutorial-php-mysql.md)
>[MySQL을 사용하여 Linux에서 PHP 앱 빌드](../app-service/containers/tutorial-php-mysql-app.md)
>[MySQL을 사용하여 Java 기반 Spring 앱 빌드](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
