---
title: 쿼리는 Azure Databricks notebook에서 가상 네트워크의 SQL Server Linux Docker 컨테이너
description: 이 문서에서는 가상 네트워크에 VNet 주입 라고도 Azure Databricks를 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770792"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>자습서: 쿼리는 Azure Databricks notebook에서 가상 네트워크의 SQL Server Linux Docker 컨테이너

이 자습서에서는 가상 네트워크의 SQL Server Linux Docker 컨테이너를 사용 하 여 Azure Databricks를 통합 하는 방법을 설명 합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크에 Azure Databricks 작업 영역을 배포 합니다.
> * 공용 네트워크에 Linux 가상 머신에 설치
> * Docker 설치
> * Linux docker 컨테이너에 Microsoft SQL Server 설치
> * JDBC를 사용 하 여 Databricks notebook에서 SQL Server 쿼리

## <a name="prerequisites"></a>필수 조건

* 만들기는 [가상 네트워크에서 Databricks 작업 영역](quickstart-create-databricks-workspace-vnet-injection.md)합니다.

* 설치할 [Windows에 대 한 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)합니다.

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)를 다운로드합니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

1. Azure portal에서 아이콘을 선택 **가상 머신**합니다. 그런 다음 선택 **+ 추가**합니다.

    ![새 Azure 가상 컴퓨터를 추가 합니다.](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 에 **기본 사항** 탭, Ubuntu Server 16.04 LTS를 선택 합니다. 에 하나의 VCPU와 2GB RAM B1ms VM 크기를 변경 합니다. SQL Server Linux Docker 컨테이너에 대 한 최소 요구 사항은 2GB입니다. 사용자 이름 및 암호 관리자를 선택 합니다.

    ![새 가상 컴퓨터 구성의 기본 사항 탭](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. **네트워킹** 탭으로 이동합니다. Azure Databricks 클러스터를 포함 하는 가상 네트워크와 공용 서브넷을 선택합니다. **검토 + 만들기** 선택한 다음 **만들기**를 선택하여 가상 머신을 배포 합니다.

    ![새 가상 컴퓨터 구성의 네트워킹 탭](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 배포가 완료되면 가상 컴퓨터로 이동합니다. **개요**에서 공용 IP 주소 및 가상 네트워크/서브넷을 확인합니다. **공용 IP 주소**를 선택합니다.

    ![가상 머신 개요](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. **할당**을 **정적**으로 변경하고 **DNS 이름 레이블**을 입력합니다. **저장**을 선택하고, 가상 머신을 다시 시작합니다.

    ![공용 IP 주소 구성](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. **설정**아래 **네트워킹** 탭을 선택합니다. 가상 머신과 연결된 Azure Databricks를 배포하는 동안 생성된 네트워크 보안 그룹인지 확인합니다. **인바운드 포트 규칙 추가**를 선택합니다.

7. SSH에 대한 포트 22를 여는 규칙을 추가합니다. 다음 설정을 사용합니다.
    
    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |source|IP 주소|IP 주소는 특정 원본 IP 주소에서 들어오는 트래픽이 이 규칙에 따라 허용 또는 거부됨을 지정 합니다.|
    |원본 IP 주소|<your public ip\>|입력된 공용 IP 주소입니다. [bing.com](https://www.bing.com/)을 방문하여 **"내 IP"** 를 검색하여 공용 IP 주소를 찾을 수 있습니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용 합니다.|
    |대상|IP 주소|IP 주소는 특정 원본 IP 주소에서 나가는 트래픽이 규칙에 따라 허용 또는 거부됨을 지정 합니다.|
    |대상 IP 주소|<your vm public ip\>|가상 머신의 공용 IP 주소를 입력합니다. 가상 머신의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|22|SSH에 대한 포트 22를 엽니다.|
    |우선 순위|290|규칙 우선 순위를 지정 합니다.|
    |이름|ssh-databricks-tutorial-vm|규칙 이름을 지정 합니다.|


    ![포트 22에 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port.png)

8. 다음 설정을 사용하여 SQL에 대한 1433 포트를 여는 규칙을 추가합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |source|IP 주소|IP 주소는 특정 원본 IP 주소에서 들어오는 트래픽이 이 규칙에 따라 허용 또는 거부됨을 지정 합니다.|
    |원본 IP 주소|10.179.0.0/16|가상 네트워크의 주소 범위를 입력 합니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용 합니다.|
    |대상|IP 주소|IP 주소는 특정 원본 IP 주소에서 나가는 트래픽이 규칙에 따라 허용 또는 거부됨을 지정 합니다.|
    |대상 IP 주소|<your vm public ip\>|가상 머신의 공용 IP 주소를 입력합니다. 가상 머신의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|1433|SQL Server에 대한 포트 1433을 엽니다.|
    |우선 순위|300|규칙 우선 순위를 지정 합니다.|
    |이름|sql-databricks-tutorial-vm|규칙 이름을 지정 합니다.|

    ![포트 1433에 대 한 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Docker 컨테이너에서 SQL Server 실행

1. [Ubuntu에 대 한 Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab) 또는 가상 머신에 SSH를 사용할 수 있는 다른 도구를 엽니다. Azure portal의 가상 머신으로 이동하여 연결을 위해 필요한 SSH 명령을 가져오기 위해 **연결** 을 선택합니다.

    ![가상 머신에 연결](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ubuntu 터미널에서 명령을 입력하고 가상 머신을 구성할 때 만든 관리자 암호를 입력합니다.

    ![Ubuntu에서 터미널 SSH 로그인](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 다음 명령을 사용하여 가상 머신에 Docker를 설치합니다.

    ```bash
    sudo apt-get install docker.io
    ```

    다음 명령을 사용하여 Docker 설치를 확인합니다.

    ```bash
    sudo docker --version
    ```

4. 이미지를 설치 합니다.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    이미지를 확인 합니다.

    ```bash
    sudo docker images
    ```

5. 이미지에서 컨테이너를 실행 합니다.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    컨테이너가 실행되고 있는지 확인합니다.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. SQL Server Management Studio를 열고 서버 이름 및 SQL 인증을 사용하여 서버에 연결합니다. 로그인 사용자 이름은 **SA**이며 암호는 Docker 명령에서 설정한 암호입니다. 예제 명령에서 암호는 `Password1234`입니다.

    ![SQL Server Management Studio를 사용 하 여 SQL Server에 연결](./media/vnet-injection-sql-server/ssms-login.png)

2. 2\. 성공적으로 연결한 후 **새 쿼리**를 선택하고 데이터베이스, 테이블을 만들고 테이블에 일부 레코드를 삽입하려면 다음 코드 조각을 입력합니다.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![SQL Server 데이터베이스를 만들려면 쿼리](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Azure Databricks에서 SQL Server 쿼리

1. Azure Databricks 작업 영역으로 이동한 다음 필수 구성 요소의 일부로 클러스터가 만들어졌는지 확인합니다. 그런 다음 **Notebook 만들기**를 선택합니다. 노트북에 이름을 입력하고 *Python* 언어와 사용자가 만든 클러스터를 선택합니다.

    ![새 Databricks notebook 설정](./media/vnet-injection-sql-server/create-notebook.png)

2. 다음 명령을 사용하여 SQL Server 가상 머신의 내부 IP 주소를 ping합니다. 이 ping은 성공해야 합니다. 그렇지 않은 경우 컨테이너가 실행 중인지 확인하고 네트워크 보안 그룹(NSG) 구성을 검토합니다.

    ```python
    %sh
    ping 10.179.64.4
    ```

    또한 검토하기 위해 nslookup 명령을 사용할 수 있습니다.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. SQL Server를 성공적으로 ping하고 나면 데이터베이스 및 테이블을 쿼리할 수 있습니다. 다음 python 코드를 실행합니다.

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, Azure Databricks 작업 영역 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 불필요한 요금 청구를 방지합니다. 나중에 Azure Databricks 작업 영역을 사용하려는 경우 클러스터를 중지하고 나중에 다시 시작할 수 있습니다. 이 Azure Databricks 작업 영역을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 모든 리소스를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음, 생성한 리소스 그룹의 이름을 클릭합니다.

2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제**를 다시 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Databricks를 사용하여 데이터를 추출, 변환 및 로드하는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](databricks-extract-load-sql-data-warehouse.md)
