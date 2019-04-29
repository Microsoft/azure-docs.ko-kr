---
title: 마이크로 포커스 CICS BankDemo 마이크로 포커스 엔터프라이즈 개발자 4.0에서 Azure Virtual Machines에 대 한 설정
description: Azure Virtual Machines Micro Focus Enterprise Server 및 엔터프라이즈 개발자를 사용 하는 방법을 알아봅니다 (Vm)에서 마이크로 포커스 BankDemo 응용 프로그램을 실행 합니다.
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: be94cf0367f93f14249239fce5e09c8635a01136
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125477"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure에서 마이크로 포커스 엔터프라이즈 개발자 4.0에 대 한 마이크로 포커스 CICS BankDemo 설정

마이크로 포커스 Enterprise Server 4.0 및 Azure에서 엔터프라이즈 개발자 4.0 설정한 경우에 IBM z/OS 워크 로드의 배포를 테스트할 수 있습니다. 이 문서에는 CICS BankDemo, 엔터프라이즈 개발자와 함께 제공 되는 샘플 응용 프로그램을 설정 하는 방법을 보여 줍니다.

CICs 고객 정보 제어 시스템에서 다양 한 온라인 메인프레임 응용 프로그램에서 사용 하는 트랜잭션 플랫폼을 나타냅니다. BankDemo 응용 프로그램이 엔터프라이즈 서버 및 엔터프라이즈 개발자의 작동 방식 및 관리 하 고 녹색 화면 터미널을 사용 하 여 전체 실제 응용 프로그램을 배포 하는 방법을 학습 하는 데 유용 합니다.

## <a name="prerequisites"></a>필수 조건

- 사용 하 여 VM [엔터프라이즈 개발자](set-up-micro-focus-azure.md)합니다. 엔터프라이즈 개발자는 전체 Enterprise Server 인스턴스의에 개발 및 테스트 목적에 염두에 두어야 합니다. 이 데모에 사용 되는 Enterprise Server의 인스턴스입니다.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)합니다. 다운로드 하 고 Enterprise Developer VM에 설치 합니다. Enterprise Server CICS 영역의 관리에 대 한 데이터베이스가 필요 및 BankDemo 응용 프로그램에는 또한 BANKDEMO 라는 SQL Server 데이터베이스를 사용 합니다. 이 데모 데이터베이스 모두에 대 한 SQL Server Express를 사용 한다고 가정 합니다. 를 설치할 때 기본 설치를 선택 합니다.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS는 데이터베이스를 관리 하 고 T-SQL 스크립트를 실행에 사용 됩니다. 다운로드 하 고 Enterprise Developer VM에 설치 합니다.

- [Visual Studio 2017](https://azure.microsoft.com/downloads/) 최신 서비스 팩을 사용 하 여 또는 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), 무료로 다운로드할 수 있습니다.

- 데스크톱 또는 다른 rumba 3270 에뮬레이터.

## <a name="configure-the-windows-environment"></a>Windows 환경 구성

VM에서 엔터프라이즈 개발자 4.0을 설치한 후 함께 제공 되는 Enterprise Server의 인스턴스를 구성 해야 합니다. 이렇게 하려면 다음과 같은 몇 가지 추가 Windows 기능을 설치 해야 합니다.

1. RDP를 사용 하 여 만든 Enterprise Server 4.0 VM에 로그온 합니다.

2. 클릭 합니다 **검색** 옆에 아이콘을 **시작** 단추 **Windows 기능**합니다. 서버 관리자 추가 역할 및 기능 마법사가 열립니다.

3. 선택 **웹 서버 (IIS) 역할**, 그런 다음 다음을 확인 합니다.

    - 웹 관리 도구
    - IIS 6 관리 호환성 (사용 가능한 모든 기능을 선택 합니다.)
    - IIS 관리 콘솔
    - IIS 관리 스크립트 및 도구
    - IIS 관리 서비스

4. 선택 **World Wide Web 서비스**, 다음을 확인 합니다.

     응용 프로그램 개발 기능:
    - .NET 확장성
    - ASP.NET
    - 일반 HTTP 기능: 사용 가능한 모든 기능을 추가 합니다.
    - 상태 및 진단: 사용 가능한 모든 기능을 추가 합니다.
    - 보안:
        - 기본 인증
        - Windows 인증

5. 선택 **Windows Process Activation Service** 및 모든 자식입니다.

6. 에 대 한 **기능**, 확인 **Microsoft.NET framework 3.5.1**, 다음을 확인 합니다.

    - Windows Communication Foundation HTTP 활성화
    - Windows Communication Foundation 비 HTTP 활성화

7. 에 대 한 **기능**, 확인 **Microsoft.NET framework 4.6**, 다음을 확인 합니다.

   - 명명 된 파이프 활성화
   - TCP 활성화
   - TCP 포트 공유

     ![역할 및 기능 추가 마법사: 역할 서비스](media/01-demo-roles.png)

8. 모든 옵션을 선택한 경우 클릭 **다음** 를 설치 합니다.

9. Windows 기능을 한 후로 이동 **Control Panel \> 시스템 및 보안 \> 관리 도구**를 선택한 **Services**. 아래로 스크롤하여 다음 서비스가 실행 되 고으로 설정 되었는지 확인 하십시오 **자동**:

    - **NetTcpPortSharing**
    - **Net.Pipe Listener Adapter**
    - **Net.tcp Listener Adapter**

10. IIS와 WAS 지원 구성 메뉴에서 찾습니다 **마이크로 포커스 엔터프라이즈 개발자 명령 프롬프트 (64 비트)** 으로 실행 하 고 **관리자**합니다.

11. 형식 **wassetup – i** 누릅니다 **Enter**합니다.

12. 스크립트가 실행 된 후 창을 닫을 수 있습니다.

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server에 대 한 로컬 시스템 계정 구성

일부 Enterprise Server 프로세스는 SQL Server에 로그온 하 고 데이터베이스 및 기타 개체를 만들 수 있으려면 해야 합니다. 이러한 프로세스 해당 계정에 sysadmin 권한을 부여 해야 하므로 로컬 시스템 계정을 사용 합니다.

1. 시작 합니다 **SSMS** 클릭 **연결** Windows 인증을 사용 하 여 로컬 SQLEXPRESS 서버에 연결 합니다. 사용할 수 있어야 합니다 **서버 이름** 목록입니다.

2. 왼쪽의 확장을 **보안** 선택한 폴더 **로그인**합니다.

3. 선택 **NT AUTHORITY\\SYSTEM** 선택한 **속성**합니다.

4. 선택 **서버 역할** 확인 하 고 **sysadmin**합니다.

     ![SSMS 개체 탐색기 창: 로그인 속성](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo 데이터베이스 및 모든 해당 개체 만들기

1. 오픈 **Windows 탐색기** 이동할 **c:\\사용자가\\공용\\문서\\Micro Focus\\엔터프라이즈 개발자\\ 샘플\\메인프레임\\CICS\\DotNet\\BankDemo\\SQL**합니다.

2. 내용을 복사 합니다 **BankDemoCreateAll.SQL** 클립보드 파일입니다.

3. 오픈 **SSMS**합니다. 오른쪽 클릭 **Server** 선택한 **새 쿼리**합니다.

4. 클립보드의 내용을 붙여 합니다 **새 쿼리** 상자입니다.

5. 클릭 하 여 SQL 실행 **Execute** 에서 합니다 **명령** 쿼리 위에 탭 합니다.

쿼리는 오류 없이 실행 해야 합니다. 완료 되 면 BankDemo 응용 프로그램에 대 한 샘플 데이터베이스를 해야 합니다.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>데이터베이스 테이블 및 개체 생성 되었는지 확인

1. 마우스 오른쪽 단추로 클릭 합니다 **BANKDEMO** 선택한 데이터베이스 **새로 고침**합니다.

2. 확장 된 **데이터베이스** 선택한 **테이블**합니다. 다음과 유사한 출력이 표시 됩니다.

     ![개체 탐색기에서 BANKDEMO 테이블 확장](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>엔터프라이즈 개발자의 응용 프로그램 빌드

1. Visual Studio를 열고 로그온 합니다.

2. 아래는 **파일** 메뉴 옵션을 선택 **프로젝트/솔루션 열기**, 이동할 **c:\\사용자가\\공용\\문서\\마이크로 포커스\\Enterprise Developer\\샘플\\메인프레임\\CICS\\DotNet\\BankDemo**를 선택 하 고는 **sln**파일입니다.

3. 개체를 조사 하는 데 시간이 소요 됩니다. COBOL 프로그램 CopyBooks (CPY)와 JCL CBL 확장을 사용 하 여 솔루션 탐색기에 표시 됩니다.

4. 마우스 오른쪽 단추로 클릭 합니다 **BankDemo2** 프로젝트를 마우스 **시작 프로젝트로 설정**합니다.

    > [!NOTE]
    > BankDemo 프로젝트는이 데모에 대 한 사용 되지 않는 HCOSS (호스트 호환성 옵션에 대 한 SQL Server)를 사용 합니다.

5. **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 합니다 **BankDemo2** 프로젝트를 마우스 **빌드**합니다.

    > [!NOTE]
    > 솔루션 수준 빌드 HCOSS 구성 되지 않아이 오류가 발생 합니다.

6. 프로젝트를 빌드할 때 검사 합니다 **출력** 창입니다. 아래 이미지와 같이 표시 됩니다.

     ![출력 창 성공적인 빌드를 표시 합니다.](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>영역 데이터베이스로 BankDemo 응용 프로그램 배포

1. (64 비트) Enterprise 개발자 명령 프롬프트를 관리자 권한으로 엽니다.

2. 로 이동 합니다 **공용 %\\문서\\Micro Focus\\Enterprise Developer\\샘플\\메인프레임\\CICS\\DotNet\\ BankDemo**합니다.

3. 명령 프롬프트에서 실행할 **bankdemodbdeploy** 예를 들어, 배포 데이터베이스에 대 한 매개 변수를 포함 합니다.

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 슬래시 (/) 하지 백슬래시를 사용 하도록 (\\). 이 스크립트를 잠시 실행 됩니다.

![관리: 엔터프라이즈 개발자 명령 프롬프트 창](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>.NET에 대 한 엔터프라이즈 관리자에서 BankDemo 영역 만들기

1. 엽니다는 **.NET 관리를 위한 엔터프라이즈 서버** UI입니다.

2. Windows에서 MMC 스냅인을 시작 하려면 **시작** 메뉴 선택 **마이크로 포커스 엔터프라이즈 개발자 \> Configuration \> .NET 관리자에 대 한 Enterprise Server**합니다. (Windows Server에 대 한 선택 **마이크로 포커스 Enterprise Developer \> .NET 관리자에 대 한 Enterprise Server**).

3. 확장 된 **지역** 컨테이너의 왼쪽된 창에서 마우스 오른쪽 단추로 **CICS**합니다.

4. 선택 **정의 지역** 라는 새 CICS 영역을 만들려면 **BANKDEMO**, (local) 데이터베이스에 호스트 된 합니다.

5. 데이터베이스 서버 인스턴스를 입력, 클릭 **다음**, 영역 이름을 입력 합니다 **BANKDEMO**합니다.

     ![영역 대화 상자를 정의 합니다.](media/07-demo-cics.png)

6. 지역 간 데이터베이스에 대 한 지역 정의 파일을 선택 하려면 찾습니다 **지역\_bankdemo\_db.config** 에서 **c:\\사용자가\\공용\\ 문서\\Micro Focus\\Enterprise Developer\\샘플\\메인프레임\\CICS\\DotNet\\BankDemo**합니다.

     ![지역-지역 이름을 정의 합니다. BANKDEMO](media/08-demo-cics.png)

7. **Finish**를 클릭합니다.

## <a name="create-xa-resource-definitions"></a>XA 리소스 정의 만들기

1. 왼쪽된 창에서를 **.NET 관리를 위한 엔터프라이즈 서버** UI 확장 **System**를 차례로 **XA 리소스 정의**. 이 설정은 지역 엔터프라이즈 서버 및 응용 프로그램 데이터베이스와 상호 운용 되는 방식을 정의 합니다.

2. 마우스 오른쪽 단추로 클릭 **XA 리소스 정의가** 선택한 **서버 인스턴스 추가**합니다.

3. 드롭다운 목록 상자에서 선택 **데이터베이스 서비스 인스턴스**합니다. 로컬 컴퓨터 SQLEXPRESS 됩니다.

4. 아래에서 인스턴스를 선택 합니다 **XA 리소스 정의 (machinename\\sqlexpress)** 컨테이너를 클릭 **추가**합니다.

5. 선택 **데이터베이스 XA 리소스 정의** 차례로 **BANKDEMO** 에 대 한 합니다 **이름** 및 **지역**합니다.

     ![새 데이터베이스 XA 리소스 정의 화면](media/09-demo-xa.png)

6. 줄임표를 클릭 (**...** ) 연결 문자열 마법사를 표시 합니다. 에 대 한 **서버 이름**, 형식 **(local)\\SQLEXPRESS**합니다. 에 대 한 **로그온**를 선택 **Windows 인증**합니다. 데이터베이스 이름에 대 한 입력 **BANKDEMO**

     ![연결 문자열 화면 편집](media/10-demo-string.png)

7. 연결을 테스트합니다.

## <a name="start-the-bankdemo-region"></a>BANKDEMO 지역 시작

> [!NOTE]
> 첫 번째 단계는 중요 합니다. 방금 만든 XA 리소스 정의 사용 하는 영역을 설정 해야 합니다.

1. 이동할를 **BANDEMO CICS 지역** 아래를 **지역 컨테이너**를 선택한 후 **지역 시작 파일 편집** 에서 **작업** 창입니다. SQL 속성까지 아래로 스크롤하여 입력 **bankdemo** 에 대 한 합니다 **XA 리소스 이름** , 줄임표를 사용 하 여 선택 합니다.

2. 클릭 합니다 **저장** 변경 내용을 저장 하는 아이콘입니다.

3. 마우스 오른쪽 단추로 클릭 **BANKDEMO CICS 지역** 에 **콘솔** 창과 선택 **Start/Stop 지역**합니다.

4. 맨 아래에는 **시작/중지 지역** 의 가운데 창에 나타나는 입력란 **시작**합니다. 몇 초 후 영역을 시작합니다.

     ![SQL 시작/중지 상자](/media/11-demo-sql.png)

     ![CICS 지역 BANKDEMO-시작된 화면](media/12-demo-cics.png)

## <a name="create-a-listener"></a>수신기 만들기

TN3270 BankDemo 응용 프로그램에 액세스 하는 세션에 대 한 수신기를 만들려고 합니다.

1. 왼쪽된 창에서 확장 **구성 편집기** 선택한 **수신기**합니다.

2. 클릭 합니다 **열려 있는 파일** 아이콘을 선택 합니다 **seelistener.exe.config** 파일입니다. 이 파일을 편집할 및 엔터프라이즈 서버를 시작할 때마다 로드 됩니다.

3. 두 개의 지역 (ESDEMO 및 JCLDEMO) 이전에 정의 확인 합니다.

4. BANKDEMO에 대 한 새 영역을 만들려면 마우스 오른쪽 단추로 클릭 **지역**, 선택한 **추가 지역**합니다.

5. 선택 **BANKDEMO 지역**합니다.

6. TN3270 채널을 마우스 오른쪽 단추로 클릭 하 여 추가 **BANKDEMO 지역** 를 선택 하 고 **채널 추가**합니다.

7. 에 대 한 **이름을**를 입력 **TN3270**합니다. 에 대 한 **포트**를 입력 **9024**합니다. (을 ESDEMO 응용 프로그램이 다른 포트를 사용 해야 하므로 포트 9230을 사용 하는 note).

8. 파일을 저장 하려면 클릭 합니다 **저장** 아이콘 선택 하거나 **파일** \> **저장**합니다.

9. 수신기를 시작 하려면 클릭 합니다 **수신기 시작** 아이콘 선택 하거나 **옵션** \> **수신기 시작**.

     ![수신기 구성 편집기 창](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Rumba BankDemo 응용 프로그램에 액세스를 구성 합니다.

마지막 작업 해야 할은 Rumba, 3270 에뮬레이터를 사용 하 여 3270 세션을 구성 합니다. 이 단계를 사용 하면 방금 만든 수신기를 통해 BankDemo 응용 프로그램에 액세스할 수 있습니다.

1. Windows에서 **시작** 메뉴 Rumba Desktop을 시작 합니다.

2. 아래는 **연결** 메뉴 항목을 선택 **TN3270**합니다.

3. 클릭 **삽입** 유형과 **127.0.0.1** IP 주소 및 **9024** 사용자 정의 포트에 대 한 합니다.

4. 대화 상자의 맨 아래에서 클릭 **Connect**합니다. CICS 검은 화면에 표시 됩니다.

5. 형식 **은행** BankDemo 응용 프로그램에 대 한 초기 3270 화면을 표시 합니다.

6. 사용자 ID를 입력 **B0001** 고 아무 것도 암호를 입력 합니다. 첫 번째 화면 BANK20 열립니다.

![메인프레임 표시 시작 화면](media/14-demo.png)
![메인프레임 표시-Rumba-하위 시스템 데모 화면](media/15-demo.png)

축하합니다! 이제에서 실행 하는 CICS 응용 프로그램을 Azure Micro Focus Enterprise Server를 사용 하 여 합니다.

## <a name="next-steps"></a>다음 단계

- [Enterprise Server를 Azure에서 Docker 컨테이너에서 실행](run-enterprise-server-container.md)
- [메인프레임 마이그레이션-포털](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Azure로 마이그레이션 메인프레임 익히기](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
