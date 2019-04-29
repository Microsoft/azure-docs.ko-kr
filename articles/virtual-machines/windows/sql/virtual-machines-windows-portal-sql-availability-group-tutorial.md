---
title: SQL Server 가용성 그룹 - Azure Virtual Machines - 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual Machines에 SQL Server Always On 가용성 그룹을 만드는 방법을 보여 줍니다.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: d86538fca907f7181bf58ff236bba8de186641fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593792"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>자습서: 수동으로 Azure VM에서 Always On 가용성 그룹 구성

이 자습서에서는 Azure Virtual Machines에 SQL Server Always On 가용성 그룹을 만드는 방법을 보여 줍니다. 전체 자습서는 두 개의 SQL Server의 데이터베이스 복제본으로 가용성 그룹을 만듭니다.

**예상 시간**: 필수 조건이 충족된 경우 완료하는 데 30분이 소요됩니다.

다이어그램은 자습서에서 빌드할 작업을 나타냅니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>필수 조건

이 자습서는 사용자가 SQL Server Always On 가용성 그룹을 기본적으로 이해하고 있다고 가정합니다. 자세한 내용은 [Always On 가용성 그룹 개요(SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)를 참조하세요.

다음 표에 이 자습서를 시작하기 전에 완료해야 하는 필수 구성 요소가 나열되어 있습니다.

|  |요구 사항 |설명 |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | SQL Server 2개 | - Azure 가용성 집합에 <br/> - 단일 도메인에 <br/> - 장애 조치(Failover) 클러스터링 기능(설치됨) |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | 클러스터 감시를 위한 파일 공유 |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server 서비스 계정 | 도메인 계정 |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server 에이전트 서비스 계정 | 도메인 계정 |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|방화벽 포트 열기 | - SQL Server: 기본 인스턴스의 경우 **1433** <br/> - 데이터베이스 미러링 엔드포인트: **5022** 또는 사용 가능한 모든 포트 <br/> - 가용성 그룹 부하 분산 장치 IP 주소 상태 프로브: **59999** 또는 사용 가능한 모든 포트 <br/> - 클러스터 코어 부하 분산 장치 IP 주소 상태 프로브: **58888** 또는 사용 가능한 모든 포트 |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|장애 조치(Failover) 클러스터링 기능 추가 | 이 기능을 필요로 하는 SQL Server 2개 |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|설치 도메인 계정 | - 각 SQL Server의 로컬 관리자 <br/> - SQL Server의 각 인스턴스에 대해 SQL Server sysadmin 고정 서버 역할의 멤버  |


자습서를 시작하기 전에 [Azure Virtual Machines에 Always On 가용성 그룹을 만들기 위한 필수 조건을 완료](virtual-machines-windows-portal-sql-availability-group-prereq.md)해야 합니다. 이러한 필수 구성 요소를 이미 완료한 경우 [클러스터 만들기](#CreateCluster)로 이동할 수 있습니다.

  >[!NOTE]
  > 이 자습서에 나와 있는 단계 많은로 이제 자동화할 수 [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) 하 고 [Azure 빠른 시작 템플릿](virtual-machines-windows-sql-availability-group-quickstart-template.md)합니다.


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>클러스터 만들기

필수 구성 요소를 완료한 후 첫 번째 단계는 두 개의 SQL Sever와 미러링 모니터 서버를 포함하는 Windows Server 장애 조치(Failover) 클러스터를 만드는 것입니다.

1. SQL Server 및 미러링 모니터 서버에서 모두 관리자인 도메인 계정을 사용하여 첫 번째 SQL Server로 RDP합니다.

   >[!TIP]
   >[필수 구성 요소 문서](virtual-machines-windows-portal-sql-availability-group-prereq.md)에 따라 **CORP\Install**이라고 하는 계정을 만들었습니다. 이 계정을 사용합니다.

2. **서버 관리자** 대시보드에서 **도구**를 선택한 후 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.
3. 왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 마우스 오른쪽 단추로 클릭하고 **클러스터 만들기**를 클릭합니다.
   ![클러스터 만들기](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. 클러스터 만들기 마법사에서 아래 표에 나온 설정으로 페이지를 단계별로 진행하여 1노드 클러스터를 만듭니다.

   | 페이지 | 설정 |
   | --- | --- |
   | 시작하기 전에 |기본값 사용 |
   | 서버 선택 |첫 번째 SQL Server 이름을 **서버 이름 입력**에 입력하고 **추가**를 클릭합니다. |
   | 유효성 검사 경고 |**아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.** 를 선택합니다. |
   | 클러스터 관리를 위한 액세스 지점 |클러스터 이름(예: **SQLAGCluster1**)을 **클러스터 이름**에 입력합니다.|
   | 확인 |저장소 공간을 사용하지 않는 경우 기본값을 사용합니다. 이 표 다음의 참고 사항을 참조하세요. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Windows Server 장애 조치(Failover) 클러스터 IP 주소 설정

1. **장애 조치(Failover) 클러스터 관리자**에서 **클러스터 코어 리소스**로 아래로 스크롤하여 클러스터 세부 정보를 확장합니다. **이름** 및 **IP 주소** 리소스가 **실패** 상태에 모두 표시됩니다. 클러스터에 컴퓨터 자체와 동일한 IP 주소가 할당되어 주소가 중복되므로 IP 주소 리소스는 온라인 상태로 전환할 수 없습니다.

2. 오류가 발생한 **IP 주소** 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

   ![클러스터 속성](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. **고정 IP 주소**를 선택하고 가상 머신과 동일한 서브넷에서 사용 가능한 주소를 지정합니다.

4. **클러스터 코어 리소스** 섹션에서 클러스터 이름을 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 그런 다음 두 리소스가 모두 온라인 상태로 전환될 때까지 기다립니다. 클러스터 이름 리소스가 온라인 상태가 되면 새 AD 컴퓨터 계정으로 DC 서버를 업데이트합니다. 이 AD 계정을 사용하여 나중에 가용성 그룹 클러스터형 서비스를 실행합니다.

### <a name="addNode"></a>다른 SQL Server를 클러스터에 추가

다른 SQL Server를 클러스터에 추가합니다.

1. 브라우저 트리에서 클러스터를 마우스 오른쪽 단추로 클릭하고 **노드 추가**를 클릭합니다.

    ![클러스터에 노드 추가](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. **노드 추가 마법사**에서 **다음**을 클릭합니다. **서버 선택** 페이지에서 두 번째 SQL Server를 추가합니다. 서버 이름을 **서버 이름 입력**에 입력하고 **추가**를 클릭합니다. 완료하면 **다음**을 클릭합니다.

1. **유효성 검사 경고** 페이지에서 **아니요**(유효성 검사 테스트를 수행할 프로덕션 시나리오에서)를 클릭합니다. 그런 후 **다음**을 클릭합니다.

8. 저장소 공간을 사용 중인 경우 **확인** 페이지에서 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요.** 확인란을 선택 취소합니다.

   ![노드 확인 추가](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >만약 저장소 공간을 사용하면서 **클러스터에 사용할 수 있는 모든 저장소를 추가하세요.** 확인란을 선택 취소하지 않으면 Windows에서 클러스터링 프로세스 도중 가상 디스크를 분리합니다. 그 결과, 저장소 공간이 클러스터에서 제거되고 PowerShell을 사용하여 다시 연결할 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다. 저장소 공간은 여러 디스크를 저장소 풀로 그룹화합니다. 자세한 내용은 [저장소 공간](https://technet.microsoft.com/library/hh831739)을 참조하세요.

1. **다음**을 클릭합니다.

1. **Finish**를 클릭합니다.

   이제 장애 조치(Failover) 클러스터 관리자에 새 노드가 포함된 클러스터가 표시되고 **노드** 컨테이너에 목록으로 표시됩니다.

10. 원격 데스크톱 세션에서 로그아웃합니다.

### <a name="add-a-cluster-quorum-file-share"></a>클러스터 쿼럼 파일 공유 추가

이 예제에서 Windows 클러스터는 파일 공유를 사용하여 클러스터 쿼럼을 만듭니다. 이 자습서에서는 노드 및 파일 공유 과반수 쿼럼을 사용합니다. 자세한 내용은 [장애 조치(Failover) 클러스터의 쿼럼 구성 이해](https://technet.microsoft.com/library/cc731739.aspx)를 참조하세요.

1. 원격 데스크톱 세션으로 파일 공유 미러링 모니터 구성원 서버에 연결합니다.

1. **서버 관리자**에서 **도구**를 클릭합니다. **컴퓨터 관리**를 엽니다.

1. **공유 폴더**를 클릭합니다.

1. **공유**를 마우스 오른쪽 단추로 클릭하고 **새 공유...** 를 클릭합니다.

   ![새 공유](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   **공유 폴더 만들기 마법사**를 사용하여 공유를 만듭니다.

1. **폴더 경로**에서 **찾아보기**를 클릭한 후 공유 폴더에 대한 경로를 찾거나 만듭니다. **다음**을 클릭합니다.

1. **이름, 설명 및 설정**에서 공유 이름 및 경로를 확인합니다. **다음**을 클릭합니다.

1. **공유 폴더 사용 권한**에서 **사용 권한 사용자 지정**을 설정합니다. **사용자 지정...** 을 클릭합니다.

1. **사용 권한 사용자 지정**에서 **추가...** 를 클릭합니다.

1. 클러스터를 만드는 데 사용되는 계정에 모든 권한이 있는지 확인합니다.

   ![새 공유](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. **확인**을 클릭합니다.

1. **공유 폴더 사용 권한**에서 **마침**을 클릭합니다. **마침**을 다시 클릭합니다.  

1. 서버에서 로그아웃합니다.

### <a name="configure-cluster-quorum"></a>클러스터 쿼럼 구성

다음으로 클러스터 쿼럼을 설정합니다.

1. 원격 데스크톱을 사용하여 첫 번째 클러스터 노드에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 마우스 오른쪽 단추로 클릭하고, **기타 작업**을 가리킨 다음 **클러스터 쿼럼 설정 구성...** 을 클릭합니다.

   ![새 공유](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. **클러스터 쿼럼 구성 마법사**에서 **다음**을 클릭합니다.

1. **쿼럼 구성 옵션 선택**에서 **쿼럼 감시 선택**을 선택하고 **다음**을 클릭합니다.

1. **쿼럼 감시 선택**에서 **파일 공유 감시 구성**을 클릭합니다.

   >[!TIP]
   >Windows Server 2016은 클라우드 감시를 지원합니다. 이 유형의 감시를 선택한 경우 파일 공유 감시가 필요하지 않습니다. 자세한 내용은 [장애 조치(Failover) 클러스터에 대한 클라우드 감시 배포](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)를 참조하세요. 이 자습서에서는 이전 운영 체제에서 지원되는 파일 공유 감시를 사용합니다.

1. **파일 공유 감시 구성**에서 사용자가 만든 공유에 대한 경로를 입력합니다. **다음**을 클릭합니다.

1. **확인**에서 설정을 확인합니다. **다음**을 클릭합니다.

1. **Finish**를 클릭합니다.

클러스터 코어 리소스는 파일 공유 감시로 구성됩니다.

## <a name="enable-availability-groups"></a>가용성 그룹 사용

다음으로 **AlwaysOn 가용성 그룹** 기능을 사용하도록 설정합니다. 두 SQL Server에서 다음 단계를 수행합니다.

1. **시작** 화면에서 **SQL Server 구성 관리자**를 시작합니다.
2. 브라우저 트리에서 **SQL Server 서비스**를 클릭하고 **SQL Server (MSSQLSERVER)** 서비스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
3. **AlwaysOn 고가용성** 탭을 클릭한 후 다음과 같이 **AlwaysOn 가용성 그룹 사용**을 선택합니다.

    ![AlwaysOn 가용성 그룹 사용](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. **적용**을 클릭합니다. 팝업 대화 상자에서 **확인**을 클릭합니다.

5. SQL Server 서비스를 다시 시작합니다.

다른 SQL Server에서도 이 단계를 반복합니다.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>첫 번째 SQL Server에서 데이터베이스 만들기

1. Sysadmin 고정 서버 역할의 구성원인 도메인 계정을 사용하여 첫 번째 SQL Server에 RDP 파일을 시작합니다.
1. SQL Server Management Studio를 열고 첫 번째 SQL Server에 연결합니다.
7. **개체 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 데이터베이스**를 클릭합니다.
8. **데이터베이스 이름**에 **MyDB1**을 입력하고 **확인**을 클릭합니다.

### <a name="backupshare"></a> 백업 공유 만들기

1. 첫 번째 SQL Server의 **서버 관리자**에서 **도구**를 클릭합니다. **컴퓨터 관리**를 엽니다.

1. **공유 폴더**를 클릭합니다.

1. **공유**를 마우스 오른쪽 단추로 클릭하고 **새 공유...** 를 클릭합니다.

   ![새 공유](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   **공유 폴더 만들기 마법사**를 사용하여 공유를 만듭니다.

1. **폴더 경로**에서 **찾아보기**를 클릭한 후 데이터베이스 백업 공유 폴더에 대한 경로를 찾거나 만듭니다. **다음**을 클릭합니다.

1. **이름, 설명 및 설정**에서 공유 이름 및 경로를 확인합니다. **다음**을 클릭합니다.

1. **공유 폴더 사용 권한**에서 **사용 권한 사용자 지정**을 설정합니다. **사용자 지정...** 을 클릭합니다.

1. **사용 권한 사용자 지정**에서 **추가...** 를 클릭합니다.

1. 두 서버 모두에 대한 SQL Server 및 SQL Server 에이전트 서비스 계정에 모든 권한이 있는지 확인합니다.

   ![새 공유](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. **확인**을 클릭합니다.

1. **공유 폴더 사용 권한**에서 **마침**을 클릭합니다. **마침**을 다시 클릭합니다.  

### <a name="take-a-full-backup-of-the-database"></a>데이터베이스의 전체 백업 수행

새 데이터베이스를 백업하여 로그 체인을 초기화해야 합니다. 새 데이터베이스의 백업을 수행하지 않으면 가용성 그룹에 포함할 수 없습니다.

1. **개체 탐색기**에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업...** 을 가리킨 다음 **백업**을 클릭합니다.

1. **확인**을 클릭하여 기본 백업 위치로 전체 백업을 수행합니다.

## <a name="create-the-availability-group"></a>가용성 그룹 만들기
이제 다음과 같은 단계를 통해 가용성 그룹을 구성할 준비가 되었습니다.

* 첫 번째 SQL Server에서 데이터베이스를 만듭니다.
* 데이터베이스의 전체 백업 및 트랜잭션 로그 백업 수행
* **NORECOVERY** 옵션으로 전체 및 로그 백업을 두 번째 SQL Server로 복원
* 동기 커밋, 자동 장애 조치(failover) 및 읽을 수 있는 보조 복제본으로 가용성 그룹 만들기(**AG1**)

### <a name="create-the-availability-group"></a>가용성 그룹 만들기:

1. 첫 번째 SQL Server에 원격 데스크톱 세션을 엽니다. SSMS의 **개체 탐색기**에서 **AlwaysOn 고가용성**을 마우스 오른쪽 단추로 클릭하고 **새 가용성 그룹 마법사**를 클릭합니다.

    ![새 가용성 그룹 마법사 시작](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. **소개** 페이지에서 **다음**을 클릭합니다. **가용성 그룹 이름 지정** 페이지의 **가용성 그룹 이름**에 가용성 그룹에 사용할 이름(예: **AG1**)을 입력합니다. **다음**을 클릭합니다.

    ![새 AG 마법사, AG 이름 지정](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. **데이터베이스 선택** 페이지에서 데이터베이스를 선택하고 **다음**을 클릭합니다.

   >[!NOTE]
   >원하는 주 복제본에서 전체 백업을 하나 이상 생성했으므로, 이러한 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족합니다.

   ![새 AG 마법사, 데이터베이스 선택](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. **복제본 지정** 페이지에서 **복제본 추가**를 클릭합니다.

   ![새 AG 마법사, 복제본 지정](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. **서버에 연결** 대화 상자가 나타납니다. 두 번째 서버 이름을 **서버 이름**에 입력합니다. **Connect**를 클릭합니다.

   **복제본 지정** 페이지로 돌아가면 이제 **가용성 복제본**에 두 번째 서버가 표시됩니다. 아래와 같이 복제본을 구성합니다.

   ![새 AG 마법사, 복제본 지정(전체)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. **엔드포인트**를 클릭하여 이 가용성 그룹에 사용될 데이터베이스 미러링 엔드포인트를 확인합니다. [데이터베이스 미러링 엔드포인트에 대한 방화벽 규칙](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)을 설정할 때 사용한 것과 동일한 포트를 사용합니다.

    ![새 AG 마법사, 초기 데이터 동기화 선택](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. **초기 데이터 동기화 선택** 페이지에서 **전체**를 선택하고 공유 네트워크 위치를 지정합니다. 위치의 경우 [만든 백업 공유](#backupshare)를 사용합니다. 예제에서는 **\\\\\<First SQL Server\>\Backup\\\**입니다. **다음**을 클릭합니다.

   >[!NOTE]
   >전체 동기화는 SQL Server의 첫 번째 인스턴스에서 데이터베이스의 전체 백업을 수행하고 두 번째 인스턴스로 복원합니다. 대형 데이터베이스의 경우 전체 동기화는 시간이 오래 걸릴 수 있으므로 권장되지 않습니다. 수동으로 데이터베이스의 백업을 수행하고 `NO RECOVERY`를 통해 복원하여 이 시간을 줄일 수 있습니다. 가용성 그룹을 구성하기 전에 두 번째 SQL Server에서 이미 `NO RECOVERY`로 데이터베이스를 복원한 경우 **조인만**을 선택합니다. 가용성 그룹을 구성한 후 백업을 수행하려는 경우 **초기 데이터 동기화 건너뛰기**를 선택합니다.

    ![새 AG 마법사, 초기 데이터 동기화 선택](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. **유효성 검사** 페이지에서 **다음**을 클릭합니다. 이 페이지는 다음 이미지와 유사하게 나타납니다.

    ![새 AG 마법사, 유효성 검사](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >가용성 그룹 수신기가 구성되어 있지 않으므로 수신기 구성에 대한 경고가 표시됩니다. Azure Virtual Machines에서 Azure Load Balancer를 만든 후 수신기를 만들기 때문에 이 경고는 무시할 수 있습니다.

10. **요약** 페이지에서 **마침**을 클릭한 후 마법사에서 새 가용성 그룹을 구성하는 동안 기다립니다. **진행률** 페이지에서 **자세한 내용**을 클릭하여 자세한 진행 상태를 확인할 수 있습니다. 마법사가 완료되면 **결과** 페이지를 검토하여 가용성 그룹이 올바르게 만들어졌는지 확인합니다.

     ![새 AG 마법사, 결과](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. **닫기**를 클릭하여 마법사를 종료합니다.

### <a name="check-the-availability-group"></a>가용성 그룹 확인

1. **개체 탐색기**에서 **AlwaysOn 고가용성**을 확장하고 **가용성 그룹**을 확장합니다. 이 컨테이너에 새 가용성 그룹이 표시됩니다. 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시**를 클릭합니다.

   ![AG 대시보드 표시](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **AlwaysOn 대시보드**는 다음과 유사하게 표시되어야 합니다.

   ![AG 대시보드](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   복제본, 각 복제본의 장애 조치(failover) 모드 및 동기화 상태를 볼 수 있습니다.

2. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 클릭합니다. **역할**을 선택합니다. 사용하는 가용성 그룹 이름은 클러스터에서 역할입니다. 수신기를 구성하지 않았기 때문에 해당 가용성 그룹은 클라이언트 연결에 대한 IP 주소가 없습니다. Azure Load Balancer를 만든 후에 수신기를 구성하게 됩니다.

   ![장애 조치(Failover) 클러스터 관리자에서 AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > 장애 조치(Failover) 클러스터 관리자에서 가용성 그룹으로 장애 조치를 시도하지 마세요. 모든 장애 조치(Failover) 작업은 SSMS의 **AlwaysOn 대시보드** 에서 수행해야 합니다. 자세한 내용은 [가용성 그룹에서 장애 조치(Failover) 클러스터 관리자 사용에 대한 제한 사항](https://msdn.microsoft.com/library/ff929171.aspx)을 참조하세요.
    >

이 시점에서 SQL Server의 두 인스턴스에서 복제와 함께 가용성 그룹을 갖게 됩니다. 가용성 그룹을 인스턴스 간에 이동할 수 있습니다. 아직 수신기가 없으므로 가용성 그룹에 연결할 수는 없습니다. Azure Virtual Machines에서 수신기에는 분산 장치가 필요합니다. 다음 단계는 Azure에서 부하 분산 장치를 만드는 것입니다.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure Load Balancer 만들기

Azure Virtual Machines에서 SQL Server 가용성 그룹에는 부하 분산 장치가 필요합니다. 부하 분산 장치는 가용성 그룹 수신기 및 Windows Server 장애 조치(failover) 클러스터의 IP 주소를 보유합니다. 이 섹션에서는 Azure Portal에서 부하 분산 장치를 만드는 방법을 요약합니다.

Azure Load Balancer는 표준 Load Balancer 또는 기본 Load Balancer일 수 있습니다. 표준 Load Balancer는 기본 Load Balancer 보다 더 많은 기능을 제공합니다. 가용성 그룹의 경우 가용성 집합 대신 가용성 영역을 사용하는 경우 표준 Load Balancer가 필요합니다. 부하 분산 장치 유형 간의 차이점에 대한 자세한 내용은 [Load Balancer SKU 비교](../../../load-balancer/load-balancer-overview.md#skus)를 참조하세요.

1. Azure Portal에서 SQL Server가 있는 리소스 그룹으로 이동하여 **+추가**를 클릭합니다.
1. **부하 분산 장치**를 검색합니다. Microsoft에서 게시한 부하 분산 장치를 선택합니다.

   ![장애 조치(Failover) 클러스터 관리자에서 AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. **만들기**를 클릭합니다.
1. 부하 분산 장치에 대한 다음 매개 변수를 구성합니다.

   | 설정 | 필드 |
   | --- | --- |
   | **Name** |예를 들어 **sqlLB**와 같은 부하 분산 장치에 대한 텍스트 이름을 사용합니다. |
   | **형식** |내부 |
   | **가상 네트워크** |Azure Virtual Network의 이름을 사용합니다. |
   | **서브넷** |가상 컴퓨터가 있는 서브넷 이름을 사용합니다.  |
   | **IP 주소 할당** |공용 |
   | **IP 주소** |서브넷에서 사용 가능한 주소를 사용합니다. 가용성 그룹 수신기에 대해 이 주소를 사용합니다. 이것은 클러스터 IP 주소와 다릅니다.  |
   | **구독** |가상 머신과 동일한 구독을 사용합니다. |
   | **위치**: |가상 컴퓨터와 동일한 위치를 사용합니다. |

   Azure Portal 블레이드는 다음과 같습니다.

   ![부하 분산 장치 만들기](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. **만들기**를 클릭하여 부하 분산 장치를 만듭니다.

부하 분산 장치를 구성하려면 백 엔드 풀, 프로브를 만들고 부하 분산 규칙을 설정해야 합니다. Azure Portal에서 이러한 작업을 수행할 수 있습니다.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>가용성 그룹 수신기에 대한 백 엔드 풀 추가

1. Azure Portal에서 가용성 그룹으로 이동합니다. 새로 만든 부하 분산 장치를 보려면 보기를 새로 고쳐야 할 수도 있습니다.

   ![리소스 그룹의 부하 분산 장치 찾기](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. 부하 분산 장치를 클릭하고 **백엔드 풀**, **+추가**를 차례로 클릭합니다.

1. 백 엔드 풀의 이름을 입력합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성**에서 **가상 머신**을 확인하고, 가용성 그룹 복제본을 호스팅하는 두 개의 가상 머신을 선택합니다. 파일 공유 미러링 모니터 서버는 포함하지 마십시오.

   >[!NOTE]
   >가상 머신을 둘 다 지정하지 않은 경우 주 복제본에 대한 연결만 성공합니다.

1. **확인**을 클릭하여 백엔드 풀을 만듭니다.

### <a name="set-the-probe"></a>프로브 설정

1. 부하 분산 장치를 클릭하고 **상태 프로브**, **+추가**를 차례로 클릭합니다.

1. 다음과 같이 수신기 상태 프로브를 설정합니다.

   | 설정 | 설명 | 예
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointProbe |
   | **프로토콜** | TCP 선택 | TCP |
   | **포트** | 사용하지 않는 모든 포트 | 59999 |
   | **간격**  | 프로브 시도 간격(초) |5 |
   | **비정상 임계값** | 가상 머신이 비정상 상태로 간주되기 위한 연속된 프로브 실패 횟수  | 2 |

1. **확인**을 클릭하여 상태 프로브를 설정합니다.

### <a name="set-the-load-balancing-rules"></a>부하 분산 규칙 설정

1. 부하 분산 장치를 클릭하고 **부하 분산 규칙**, **+추가**를 차례로 클릭합니다.

1. 수신기 부하 분산 규칙을 다음과 같이 설정합니다.

   | 설정 | 설명 | 예
   | --- | --- |---
   | **Name** | Text | SQLAlwaysOnEndPointListener |
   | **프런트 엔드 IP 주소** | 주소 선택 |부하 분산 장치를 만들 때 생성된 주소를 사용합니다. |
   | **프로토콜** | TCP 선택 |TCP |
   | **포트** | 가용성 그룹 수신기용 포트 사용 | 1433 |
   | **백 엔드 포트** | 이 필드는 부동 IP가 직접 서버 반환에 대해 설정된 경우 사용하지 않습니다. | 1433 |
   | **프로브** |프로브에 대해 지정한 이름 | SQLAlwaysOnEndPointProbe |
   | **세션 지속성** | 드롭다운 목록 | **없음** |
   | **유휴 시간 제한** | TCP 연결을 열린 상태로 유지하는 시간(분) | 4 |
   | **부동 IP(Direct Server Return)** | |Enabled |

   > [!WARNING]
   > 직접 서버 반환은 만드는 동안 설정됩니다. 이는 변경할 수 없습니다.

1. **확인**을 클릭하여 수신기 부하 분산 규칙을 설정합니다.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>WSFC(Windows Server 장애 조치 클러스터)에 대한 클러스터 코어 IP 주소 추가

WSFC IP 주소는 부하 분산 장치에 배치되어야 합니다.

1. 포털의 동일한 Azure Load Balancer에서 **프런트 엔드 IP 구성**을 클릭하고 **+추가**를 클릭합니다. 클러스터 코어 리소스에서 WSFC에 대해 구성한 IP 주소를 사용합니다. IP 주소를 고정으로 설정합니다.

1. 부하 분산 장치에서 **상태 프로브**, **+추가**를 차례로 클릭합니다.

1. WSFC 클러스터 코어 IP 주소 상태 프로브를 다음과 같이 설정합니다.

   | 설정 | 설명 | 예
   | --- | --- |---
   | **Name** | Text | WSFCEndPointProbe |
   | **프로토콜** | TCP 선택 | TCP |
   | **포트** | 사용하지 않는 모든 포트 | 58888 |
   | **간격**  | 프로브 시도 간격(초) |5 |
   | **비정상 임계값** | 가상 머신이 비정상 상태로 간주되기 위한 연속된 프로브 실패 횟수  | 2 |

1. **확인**을 클릭하여 상태 프로브를 설정합니다.

1. 부하 분산 규칙을 설정합니다. **부하 분산 규칙**을 클릭하고 **+추가**를 클릭합니다.

1. 클러스터 코어 IP 주소 부하 분산 규칙을 다음과 같이 설정합니다.

   | 설정 | 설명 | 예
   | --- | --- |---
   | **Name** | Text | WSFCEndPoint |
   | **프런트 엔드 IP 주소** | 주소 선택 |WSFC IP 주소를 구성할 때 생성된 주소를 사용합니다. 수신기 IP 주소와는 다릅니다. |
   | **프로토콜** | TCP 선택 |TCP |
   | **포트** | 클러스터 IP 주소에 대한 포트를 사용합니다. 수신기 프로브 포트에 사용되지 않는 사용 가능한 포트입니다. | 58888 |
   | **백 엔드 포트** | 이 필드는 부동 IP가 직접 서버 반환에 대해 설정된 경우 사용하지 않습니다. | 58888 |
   | **프로브** |프로브에 대해 지정한 이름 | WSFCEndPointProbe |
   | **세션 지속성** | 드롭다운 목록 | **없음** |
   | **유휴 시간 제한** | TCP 연결을 열린 상태로 유지하는 시간(분) | 4 |
   | **부동 IP(Direct Server Return)** | |Enabled |

   > [!WARNING]
   > 직접 서버 반환은 만드는 동안 설정됩니다. 이는 변경할 수 없습니다.

1. **확인**을 클릭하여 부하 분산 규칙을 설정합니다.

## <a name="configure-listener"></a> 수신기 구성

다음에 수행할 작업은 장애 조치(failover) 클러스터에서 가용성 그룹 수신기를 구성하는 것입니다.

> [!NOTE]
> 이 자습서에서는 ILB IP 주소 하나로 단일 수신기를 만드는 방법을 보여 줍니다. 하나 이상의 IP 주소를 사용하여 하나 이상의 수신기를 만들려면 [가용성 그룹 수신기 및 부하 분산 장치 만들기 | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>수신기 포트 설정

SQL Server Management Studio에서 수신기 포트를 설정합니다.

1. SQL Server Management Studio를 시작하고 주 복제본에 연결합니다.

1. **AlwaysOn 고가용성** | **가용성 그룹** | **가용성 그룹 수신기**로 이동합니다.

1. 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 수신기 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

1. **포트** 상자에 가용성 그룹 수신기의 포트 번호를 지정합니다. 1433이 기본값이며 **확인**을 클릭합니다.

이제 Resource Manager 모드에서 실행 중인 Azure Virtual Machines의 SQL Server 가용성 그룹이 만들어졌습니다.

## <a name="test-connection-to-listener"></a>수신기에 대한 연결 테스트

연결을 테스트하려면

1. 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server로 RDP합니다. 클러스터의 다른 SQL Server를 사용할 수 있습니다.

1. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   수신기가 기본 포트(1433) 이외의 포트를 사용하는 경우 연결 문자열에서 포트를 지정합니다. 예를 들어 다음 sqlcmd 명령은 포트 1435에서 수신기에 연결합니다.

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다.

> [!TIP]
> 지정한 포트가 두 SQL Server의 방화벽에서 열려 있는지 확인합니다. 두 서버 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](https://technet.microsoft.com/library/cc753558.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [두 번째 가용성 그룹에 대한 부하 분산 장치에 IP 주소를 추가](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP)합니다.
