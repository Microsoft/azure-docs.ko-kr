---
title: Azure Databricks를 사용 하 여 SQL Server Linux Docker 컨테이너 쿼리
description: 이 문서에서는 VNet 주입이 라고도 하는 가상 네트워크에 Azure Databricks를 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747658"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>자습서: Azure Databricks 노트북에서 가상 네트워크의 SQL Server Linux Docker 컨테이너 쿼리

이 자습서에서는 가상 네트워크에서 SQL Server Linux Docker 컨테이너와 Azure Databricks를 통합 하는 방법을 배웁니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크에 Azure Databricks 작업 영역 배포
> * 공용 네트워크에 Linux 가상 머신 설치
> * Docker 설치
> * Docker 컨테이너 Microsoft SQL Server on Linux 설치
> * Databricks 노트북에서 JDBC를 사용 하 여 SQL Server 쿼리

## <a name="prerequisites"></a>필수 조건

* [가상 네트워크에서 Databricks 작업 영역](quickstart-create-databricks-workspace-vnet-injection.md)을 만듭니다.

* [Windows 용 Ubuntu를](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)설치 합니다.

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)를 다운로드합니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

1. Azure Portal에서 **Virtual Machines**아이콘을 선택 합니다. 그런 다음 **+ 추가**를 선택 합니다.

    ![새 Azure 가상 컴퓨터 추가](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. **기본 사항** 탭에서 Ubuntu Server 18.04 Lts를 선택 하 고 VM 크기를 B2s로 변경 합니다. 관리자 사용자 이름 및 암호를 선택 합니다.

    ![새 가상 컴퓨터 구성의 기본 사항 탭](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. **네트워킹** 탭으로 이동 합니다. Azure Databricks 클러스터를 포함 하는 가상 네트워크 및 공용 서브넷을 선택 합니다. **검토 + 만들기**, **만들기** 를 차례로 선택 하 여 가상 컴퓨터를 배포 합니다.

    ![새 가상 컴퓨터 구성의 네트워킹 탭](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 배포가 완료 되 면 가상 머신으로 이동 합니다. **개요**의 공용 IP 주소와 가상 네트워크/서브넷을 확인 합니다. **공용 IP 주소** 를 선택 합니다.

    ![가상 머신 개요](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. **할당** 을 **정적** 으로 변경 하 고 **DNS 이름 레이블을**입력 합니다. **저장**을 선택 하 고 가상 컴퓨터를 다시 시작 합니다.

    ![공용 IP 주소 구성](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. **설정**아래에서 **네트워킹** 탭을 선택 합니다. Azure Databricks 배포 중에 생성 된 네트워크 보안 그룹은 가상 머신과 연결 됩니다. **인바운드 포트 규칙 추가**를 선택 합니다.

7. SSH에 대한 포트 22를 여는 규칙을 추가 합니다. 다음 설정을 사용합니다.
    
    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |원본|IP 주소|IP 주소는이 규칙에 의해 특정 원본 IP 주소에서 들어오는 트래픽을 허용 하거나 거부 하도록 지정 합니다.|
    |원본 IP 주소|공용 ip를 <\>|공용 IP 주소를 입력 합니다. [Bing.com](https://www.bing.com/) 를 방문 하 고 **"내 IP"** 를 검색 하 여 공용 IP 주소를 찾을 수 있습니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용 합니다.|
    |대상|IP 주소|IP 주소는이 규칙에 의해 특정 원본 IP 주소에 대한 나가는 트래픽을 허용 하거나 거부 하도록 지정 합니다.|
    |대상 IP 주소|vm 공용 ip를 <\>|가상 머신의 공용 IP 주소를 입력 합니다. 가상 컴퓨터의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|22|SSH에 대한 포트 22를 엽니다.|
    |우선 순위|290|규칙에 우선 순위를 지정 합니다.|
    |Name|databricks-vm|규칙에 이름을 지정 합니다.|


    ![포트 22에 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port.png)

8. 다음 설정을 사용 하 여 SQL 용 1433 포트를 여는 규칙을 추가 합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |원본|모두|원본 특정 원본 IP 주소에서 들어오는 트래픽이이 규칙에 의해 허용 또는 거부 되도록 지정 합니다.|
    |원본 포트 범위|*|모든 포트에서 트래픽을 허용 합니다.|
    |대상|IP 주소|IP 주소는이 규칙에 의해 특정 원본 IP 주소에 대한 나가는 트래픽을 허용 하거나 거부 하도록 지정 합니다.|
    |대상 IP 주소|vm 공용 ip를 <\>|가상 머신의 공용 IP 주소를 입력 합니다. 가상 컴퓨터의 **개요** 페이지에서 찾을 수 있습니다.|
    |대상 포트 범위|1433|SQL Server에 대해 포트 22를 엽니다.|
    |우선 순위|300|규칙에 우선 순위를 지정 합니다.|
    |Name|databricks-vm|규칙에 이름을 지정 합니다.|

    ![포트 1433에 대한 인바운드 보안 규칙 추가](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Docker 컨테이너에서 SQL Server 실행

1. [Windows 용 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)또는 가상 머신에 대한 SSH를 허용 하는 기타 도구를 엽니다. Azure Portal에서 가상 머신으로 이동 하 고 **연결** 을 선택 하 여 연결 해야 하는 SSH 명령을 가져옵니다.

    ![가상 머신에 연결](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Ubuntu 터미널에 명령을 입력 하 고 가상 머신을 구성할 때 만든 관리자 암호를 입력 합니다.

    ![Ubuntu 터미널 SSH 로그인](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 다음 명령을 사용 하 여 가상 컴퓨터에 Docker를 설치 합니다.

    ```bash
    sudo apt-get install docker.io
    ```

    다음 명령을 사용 하 여 Docker 설치를 확인 합니다.

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

    컨테이너가 실행 중인지 확인 합니다.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. SQL Server Management Studio를 열고 서버 이름과 SQL 인증을 사용 하 여 서버에 연결 합니다. 로그인 사용자 이름은 **SA** 이며 암호는 Docker 명령에 설정 된 암호입니다. 예제 명령의 암호는 `Password1234`입니다.

    ![SQL Server Management Studio를 사용 하 여 SQL Server에 연결](./media/vnet-injection-sql-server/ssms-login.png)

2. 성공적으로 연결 되 면 **새 쿼리** 를 선택 하 고 다음 코드 조각을 입력 하 여 데이터베이스 및 테이블을 만들고 테이블에 일부 레코드를 삽입 합니다.

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

    ![SQL Server 데이터베이스를 만드는 쿼리](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Azure Databricks에서 SQL Server 쿼리

1. Azure Databricks 작업 영역으로 이동 하 고 필수 구성 요소의 일부로 클러스터를 만들었는지 확인 합니다. 그런 다음, **노트북 만들기**를 선택 합니다. 노트북 이름을 지정 하 고, *Python* 을 언어로 선택 하 고, 만든 클러스터를 선택 합니다.

    ![새 Databricks 노트북 설정](./media/vnet-injection-sql-server/create-notebook.png)

2. 다음 명령을 사용 하 여 SQL Server 가상 컴퓨터의 내부 IP 주소를 ping 합니다. 이 ping은 성공적으로 수행 됩니다. 그렇지 않으면 컨테이너가 실행 중인지 확인 하 고 NSG (네트워크 보안 그룹) 구성을 검토 합니다.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Nslookup 명령을 사용 하 여을 검토할 수도 있습니다.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. SQL Server 성공적으로 ping 하면 데이터베이스 및 테이블을 쿼리할 수 있습니다. 다음 python 코드를 실행 합니다.

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

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음, 만든 리소스 그룹의 이름을 클릭합니다.

2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제**를 다시 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Databricks를 사용 하 여 데이터를 추출, 변환 및 로드 하는 방법을 알아보려면 다음 문서로 이동 합니다.
> [!div class="nextstepaction"]
> [자습서: Azure Databricks을 사용 하 여 데이터 추출, 변환 및 로드](databricks-extract-load-sql-data-warehouse.md)
