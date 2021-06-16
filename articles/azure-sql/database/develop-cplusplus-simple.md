---
title: C 및 C++를 사용하여 SQL Database에 연결
description: 이 빠른 시작에 포함된 샘플 코드를 사용하여 C++으로 최신 애플리케이션을 개발하고 Azure SQL Database로 클라우드에서 강력한 관계형 데이터베이스를 통해 지원할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 12/12/2018
ms.openlocfilehash: c874ccdf2f94bf07aab51d8510065d9e259daa44
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110694538"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>C 및 C++를 사용하여 SQL Database에 연결
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 게시물의 목적은 Azure SQL Database에 연결하려고 시도하는 C 및 C++ 개발자를 위한 것입니다. 가장 관심 있는 부분을 캡처하는 섹션으로 이동할 수 있도록 섹션이 세분화됩니다.

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ 자습서의 필수 구성 요소

다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 아직 구독하지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* [Visual Studio](https://www.visualstudio.com/downloads/) 이 샘플을 빌드하고 실행하려면 C++ 언어 구성 요소를 설치해야 합니다.
* [Visual Studio Linux 개발](/cpp/linux/). Linux에서 개발하는 경우에 Visual Studio Linux 확장도 설치해야 합니다.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>가상 머신에서 Azure SQL Database 및 SQL Server

Azure SQL Database는 Microsoft SQL Server를 기반으로 빌드되며, 성능이 뛰어나고 스케일링 가능한 고가용성 서비스를 제공하도록 설계되었습니다. Azure SQL을 사용하는 것은 온-프레미스에서 실행되는 전용 데이터베이스를 사용하는 것보다 많은 이점이 있습니다. Azure SQL에서는 데이터베이스를 설치, 설정, 유지 또는 관리할 필요가 없이 데이터베이스의 콘텐츠와 구조만 관리하면 됩니다. 내결함성과 중복성처럼 데이터 베이스에 대해 일반적으로 걱정하는 것이 모두 기본 제공됩니다.

Azure에는 현재 SQL Server 워크로드를 호스트하기 위한 두 가지 옵션, 즉 Database as a Service인 Azure SQL Database와 VM(Virtual Machines)의 SQL Server가 있습니다. Azure SQL Database가 새로운 클라우드 기반 애플리케이션을 위해 클라우드 서비스가 제공하는 비용 절감과 성능 최적화를 활용하는 최선의 방법이라는 점을 제외하고 이 두 옵션 간에 차이점을 찾을 수 없습니다. 클라우드로 온-프레미스 애플리케이션을 마이그레이션 또는 확장하려는 경우 Azure 가상 머신에서 SQL server가 더 적합할 수 있습니다. 이 문서에서 작업을 더 간단하게 유지하기 위해, Azure SQL Database를 만들어 보겠습니다.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>데이터 액세스 기술: ODBC 및 OLE DB

Azure SQL Database에 연결하는 것은 다르지 않고 데이터베이스에 연결하는 방법에는 ODBC(Open Database connectivity) 및 OLE DB(개체 연결 및 포함 데이터베이스)의 두 가지가 있습니다. 최근 몇 년간 Microsoft는 [기본 관계형 데이터 액세스에 대해 ODBC](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access)에 맞추어 왔습니다. ODBC은 비교적 간단하고 OLE DB보다 훨씬 빠릅니다. 한 가지 주의할 점은 ODBC는 이전 C 스타일 API를 사용한다는 것입니다.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>1단계: Azure SQL Database 만들기

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](single-database-create-quickstart.md) 를 참조하세요.  또는 [짧은 2분 비디오](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/)를 보고 Azure Portal을 사용하여 Azure SQL Database를 만듭니다.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>2단계: 연결 문자열 가져오기

Azure SQL Database를 프로비저닝한 후 연결 정보를 확인하고 방화벽 액세스에 대한 클라이언트 IP를 추가하려면 다음 단계를 수행해야 합니다.

[Azure Portal](https://portal.azure.com/)에서, 데이터베이스에 대한 개요 섹션의 일부로 나열된 **데이터베이스 연결 문자열 표시** 를 사용하여 Azure SQL Database ODBC 연결 문자열로 이동합니다.

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

**ODBC(Node.js 포함) [SQL 인증]** 문자열의 내용을 복사합니다. 이 문자열은 C++ ODBC 명령줄 인터프리터에서 연결하는 데 사용합니다. 이 문자열은 드라이버, 서버 및 다른 데이터베이스 연결 매개 변수 등의 세부 정보를 제공합니다.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>3단계: 방화벽에 IP 추가

서버의 방화벽 섹션으로 이동하고 [관련 단계에 따라 방화벽에 클라이언트 IP](firewall-configure.md)를 추가하여 다음과 같이 연결을 설정합니다.

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

이 시점에서 Azure SQL Database를 구성하고 C++ 코드에서 연결할 준비가 되었습니다.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>4단계: Windows C/C++ 애플리케이션에서 연결

Visual Studio로 만든 [이 샘플을 사용하는 Windows에서 ODBC를 사용하는 Azure SQL Database](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)에 쉽게 연결할 수 있습니다. 샘플에서는 Azure SQL Database에 연결하는 데 사용할 수 있는 ODBC 명령줄 인터프리터를 구현합니다. 이 샘플에는 명령줄 인수로서 데이터베이스 원본 이름(DSN) 파일 또는 Azure Portal에서 이전에 복사한 세부 정보 표시 연결 문자열을 사용합니다. 이 프로젝트에 대한 속성 페이지를 표시하고 다음과 같이 명령 인수로서 연결 문자열을 붙여 넣습니다.

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

해당 데이터베이스에 그 데이터베이스 연결 문자열로 올바른 인증을 해야 합니다.

빌드하려면 애플리케이션을 시작합니다. 성공적인 연결의 유효성을 검사하는 창이 다음과 같이 표시됩니다. 데이터베이스 연결의 유효성을 검사하려면 **테이블 만들기** 와 같은 몇 가지 기본 SQL 명령을 실행할 수도 있습니다.

![SQL 명령](./media/develop-cplusplus-simple/sqlcommands.png)

또는 명령 인수가 제공되지 않는 경우 시작되는 마법사를 사용 하여 DSN 파일을 만들 수 있습니다. 이 옵션을 함께 시도하는 것이 좋습니다. 자동화 및 인증 설정 보호를 위해 DSN 파일을 사용할 수 있습니다.

![파일 DSN 만들기](./media/develop-cplusplus-simple/datasource.png)

축하합니다! 이제 Windows에서 C++ 및 ODBC를 사용하여 Azure SQL에 성공적으로 연결했습니다. Linux 플랫폼에도 동일한 작업을 수행하려면 다음을 읽어주세요.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>5 단계: Linux C/C++ 애플리케이션에서 연결

아직 새 소식을 듣지 못했다면 Visual Studio에서 이제 C++ Linux 애플리케이션도 개발할 수 있습니다. [Linux 개발용 Visual C++](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) 블로그에서 이 새 시나리오에 대해 참고할 수 있습니다. Linux용으로 빌드하려면 Linux distro가 실행되고 있는 원격 컴퓨터가 필요합니다. 원격 컴퓨터가 없다면 [Linux Azure 가상 머신](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 신속하게 하나를 설정할 수 있습니다.

이 자습서에서는 Ubuntu 16.04 Linux 배포판이 설치되어 있다고 가정합니다. 여기 나온 단계는 Ubuntu 15.10, Red Hat 6 및 Red Hat 7에도 적용 해야 합니다.

다음 단계에서는 배포를 위해 SQL 및 ODBC에 필요한 라이브러리를 설치합니다.

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Visual Studio를 실행합니다. [도구] -> [옵션] -> [크로스 플랫폼] -> [연결 관리자]에서 Linux 상자에 연결을 추가합니다.

![도구 옵션](./media/develop-cplusplus-simple/tools.png)

SSH 통해 연결이 설정된 후에 빈 프로젝트(Linux) 템플릿을 만듭니다.

![새 프로젝트 템플릿](./media/develop-cplusplus-simple/template.png)

그런 다음 [새 C 소스 파일을 추가하고 다음 내용으로 교체](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)할 수 있습니다. ODBC Api SQLAllocHandle, SQLSetConnectAttr 및 SQLDriverConnect를 사용하여 데이터베이스에 연결을 만들고 초기화 할 수 있습니다.
Windows ODBC 샘플과 마찬가지로 이전에 Azure Portal에서 복사한 데이터베이스 연결 문자열 매개 변수로 SQLDriverConnect 호출을 교체해야 합니다.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

컴파일하기 전에 마지막으로 라이브러리 종속성으로 **odbc** 를 추가합니다.

![입력된 라이브러리로 ODBC 추가](./media/develop-cplusplus-simple/lib.png)

애플리케이션을 시작하려면 **디버그** 메뉴에서 Linux 콘솔을 표시합니다.

![Linux 콘솔](./media/develop-cplusplus-simple/linuxconsole.png)

연결에 성공하면 이제 Linux 콘솔에 인쇄된 현재 데이터베이스 이름이 보입니다.

![Linux 콘솔 창 출력](./media/develop-cplusplus-simple/linuxconsolewindow.png)

축하합니다! 이 자습서를 성공적으로 완료했습니다. 이제 Windows 및 Linux 플랫폼의 C++에서 Azure SQL Database에 연결할 수 있습니다.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a> 전체 C++ 자습서 솔루션 가져오기

GitHub에서 이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 찾을 수 있습니다.

* [ODBC C++ Windows 샘플](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Azure SQL에 연결하려면 Windows C++ ODBC 샘플 다운로드
* [ODBC C++ Windows 샘플](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Azure SQL에 연결하려면 Linux C++ ODBC 샘플 다운로드

## <a name="next-steps"></a>다음 단계

* [SQL Database 개발 개요](develop-overview.md)
* [ODBC API 참조](/sql/odbc/reference/syntax/odbc-api-reference/)에 대한 자세한 정보

## <a name="additional-resources"></a>추가 리소스

* [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)
* 모든 [SQL Database의 기능](https://azure.microsoft.com/services/sql-database/)