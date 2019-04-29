---
title: Azure Monitor에서 Linux 애플리케이션 성능 수집 | Microsoft Docs
description: 이 문서에서는 MySQL 및 Apache HTTP 서버에 대한 성능 카운터를 수집하도록 Linux용 Log Analytics 에이전트를 구성하는 세부 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628917"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Azure Monitor에서 Linux 애플리케이션에 대한 성능 카운터 수집 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
이 문서에서는 특정 애플리케이션에 대한 성능 카운터를 Azure Monitor로 수집하도록 [Linux용 Log Analytics 에이전트](https://github.com/Microsoft/OMS-Agent-for-Linux)를 구성하는 방법에 대한 세부 정보를 제공합니다.  이 문서에 포함된 애플리케이션은 다음과 같습니다.  

- [MySQL](#mysql)
- [Apache HTTP 서버](#apache-http-server)

## <a name="mysql"></a>MySQL
Log Analytics 에이전트가 설치된 경우 컴퓨터에서 MySQL 서버나 MariaDB 서버가 감지되면, MySQL 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 통계를 공개하기 위해 로컬 MySQL/MariaDB 서버에 연결합니다. 공급자가 MySQL 서버에 액세스할 수 있도록 MySQL 사용자 자격 증명을 구성해야 합니다.

### <a name="configure-mysql-credentials"></a>MySQL 자격 증명 구성
MySQL OMI 공급자가 MySQL 인스턴스의 성능 및 상태 정보를 쿼리하려면, 미리 정의된 MySQL 사용자와 설치되어 있는 MySQL 클라이언트 라이브러리가 필요합니다.  이러한 자격 증명은 Linux 에이전트에 저장된 인증 파일에 저장됩니다.  인증 파일은 MySQL 인스턴스가 수신 대기할 바인딩 주소와 포트를 결정하고 메트릭 수집에 사용할 자격 증명을 지정합니다.  

Linux용 Log Analytics 에이전트를 설치하는 동안 MySQL OMI 공급자는 MySQL my.cnf 구성 파일(기본 위치)에서 바인딩 주소와 포트를 검색하고, MySQL OMI 인증 파일을 부분적으로 설정합니다.

MySQL 인증 파일은 `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`에 저장됩니다.


### <a name="authentication-file-format"></a>인증 파일 형식
다음은 MySQL OMI 인증 파일의 형식입니다.

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

인증 파일의 항목은 다음 테이블에 설명되어 있습니다.

| 자산 | 설명 |
|:--|:--|
| 포트 | MySQL 인스턴스가 수신 대기 중인 현재 포트를 나타냅니다. 포트 0은 뒤에 나오는 속성이 기본 인스턴스에 사용된다는 것을 지정합니다. |
| 바인딩 주소| 현재 MySQL 바인딩-주소입니다. |
| username| MySQL 서버 인스턴스를 모니터링하는 데 사용되는 MySQL 사용자입니다. |
| Base64로 인코딩된 암호| Base64로 인코딩된 MySQL 모니터링 사용자의 암호입니다. |
| AutoUpdate| MySQL OMI 공급자가 업그레이드되면 my.cnf 파일에서 변경 내용을 검색하고 MySQL OMI 인증 파일을 덮어쓸지 여부를 지정합니다. |

### <a name="default-instance"></a>기본 인스턴스
MySQL OMI 인증 파일은 하나의 Linux 호스트에서 여러 MySQL 인스턴스를 쉽게 관리할 수 있도록 기본 인스턴스 및 포트 번호를 정의할 수 있습니다.  기본 인스턴스는 포트 0으로 인스턴스에 의해 표시됩니다. 모든 추가 인스턴스는 서로 다른 값을 지정하지 않는 한 기본 인스턴스의 설정 속성을 상속합니다. 예를 들어, '3308' 포드에서 수신 대기 중인 MySQL 인스턴스가 추가되면, 3308 포트에서 수신 대기 중인 인스턴스를 시도하고 모니터링하기 위해, 기본 인스턴스의 바인딩 주소, 사용자 이름, Base64로 인코딩된 암호가 사용됩니다. 3308의 인스턴스가 다른 주소로 바인딩되고 동일한 MySQL 사용자 이름과 암호 쌍이 사용되면, 바인딩 주소만 필요하고 다른 속성은 상속됩니다.

다음 테이블에는 예제 인스턴스 설정이 있습니다. 

| 설명 | 파일 |
|:--|:--|
| 기본 인스턴스 및 포트 3308의 인스턴스입니다. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| 기본 인스턴스 및 포트 3308의 인스턴스, 다른 사용자 이름 및 암호입니다. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI 인증 파일 프로그램
MySQL OMI 공급자의 설치에 포함된 것은 MySQL OMI 인증 파일 편집에 사용할 수 있는 MySQL OMI 인증 파일 프로그램입니다. 다음 위치에서 인증 파일 프로그램을 찾을 수 있습니다.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> 자격 증명 파일은 omsagent 계정이 읽을 수 있어야 합니다. omsgent로 mycimprovauth 명령을 실행하는 것이 좋습니다.

다음 테이블에서 mycimprovauth 사용에 대한 구문의 세부 정보를 제공합니다.

| 작업(Operation) | 예 | 설명
|:--|:--|:--|
| autoupdate *false or true* | mycimprovauth autoupdate false | 다시 시작 또는 업데이트 시 인증 파일이 자동으로 업데이트될지 여부를 설정합니다. |
| default *bind-address username password* | mycimprovauth default 127.0.0.1 root pwd | MySQL OMI 인증 파일에서 기본 인스턴스를 설정합니다.<br>암호 필드는 일반 텍스트로 입력되어야 하며 MySQL OMI 인증 파일의 암호는 Base 64로 인코딩됩니다. |
| delete *default or port_num* | mycimprovauth 3308 | 기본값 또는 포트 번호로 지정된 인스턴스를 삭제합니다. |
| help | mycimprov help | 사용할 명령 목록을 인쇄합니다. |
| print | mycimprov print | 읽기 쉬운 MySQL OMI 인증 파일을 인쇄합니다. |
| update port_num *bind-address username password* | mycimprov update 3307 127.0.0.1 root pwd | 지정된 인스턴스를 업데이트하거나 존재하지 않는 경우 인스턴스를 추가합니다. |

다음 예제 명령은 localhost에서 MySQL 서버의 기본 사용자 계정을 정의합니다.  암호 필드는 일반 텍스트로 입력되어야 하며 MySQL OMI 인증 파일의 암호는 Base 64로 인코딩됩니다.

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL 성능 카운터에 필요한 데이터베이스 권한
MySQL 사용자는 MySQL 서버 성능 데이터를 수집하기 위해 다음 쿼리에 대한 액세스가 필요합니다. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


또한 MySQL 사용자는 다음 기본 테이블에 대한 SELECT 액세스가 필요합니다.

- information_schema
- mysql. 

이러한 권한은 다음과 같은 권한 부여 명령을 실행하여 부여될 수 있습니다.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> MySQL 모니터링 사용자에게 권한을 부여하려면, 권한을 부여하는 사용자에게 'GRANT option' 권한은 물론 부여되는 권한에 대한 권한이 있어야 합니다.

### <a name="define-performance-counters"></a>성능 카운터 정의

Azure Monitor에 데이터를 보내도록 Linux용 Log Analytics 에이전트를 구성하고 나면 수집할 성능 카운터를 구성해야 합니다.  다음 표의 카운터와 함께 [Azure Monitor의 Windows 및 Linux 성능 데이터 원본](data-sources-performance-counters.md)에 제공되는 절차를 사용합니다.

| 개체 이름 | 카운터 이름 |
|:--|:--|
| MySQL 데이터베이스 | 디스크 공간(바이트) |
| MySQL 데이터베이스 | 테이블 |
| MySQL Server | 중단된 연결 Pct |
| MySQL Server | 연결 사용 Pct |
| MySQL Server | 디스크 공간 사용(바이트) |
| MySQL Server | 전체 테이블 검색 Pct |
| MySQL Server | InnoDB 버퍼 풀 적중 Pct |
| MySQL Server | InnoDB 버퍼 풀 사용 Pct |
| MySQL Server | InnoDB 버퍼 풀 사용 Pct |
| MySQL Server | 키 캐시 적중 Pct |
| MySQL Server | 키 캐시 사용 Pct |
| MySQL Server | 키 캐시 쓰기 Pct |
| MySQL Server | 쿼리 캐시 적중 Pct |
| MySQL Server | 쿼리 캐시 잘라내기 Pct |
| MySQL Server | 쿼리 캐시 사용 Pct |
| MySQL Server | 테이블 캐시 적중 Pct |
| MySQL Server | 테이블 캐시 사용 Pct |
| MySQL Server | 테이블 잠금 경합 Pct |

## <a name="apache-http-server"></a>Apache HTTP 서버 
omsagent 번들이 설치된 경우 컴퓨터에서 Apache HTTP 서버가 감지되면, Apache HTTP 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 데이터에 액세스하기 위해 Apache HTTP 서버에 로드되어야 하는 Apache 모듈에 의존합니다. 모듈은 다음 명령을 사용하여 로드될 수 있습니다.
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache 모니터링 모듈을 언로드하려면, 다음 명령을 실행합니다.
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>성능 카운터 정의

Azure Monitor에 데이터를 보내도록 Linux용 Log Analytics 에이전트를 구성하고 나면 수집할 성능 카운터를 구성해야 합니다.  다음 표의 카운터와 함께 [Azure Monitor의 Windows 및 Linux 성능 데이터 원본](data-sources-performance-counters.md)에 제공되는 절차를 사용합니다.

| 개체 이름 | 카운터 이름 |
|:--|:--|
| Apache HTTP 서버 | 근무 중인 작업자 |
| Apache HTTP 서버 | 유휴 작업자 |
| Apache HTTP 서버 | Pct 근무 중인 작업자 |
| Apache HTTP 서버 | 총 Pct CPU |
| Apache 가상 호스트 | 분당 오류 - 클라이언트 |
| Apache 가상 호스트 | 분당 오류 - 서버 |
| Apache 가상 호스트 | 요청당 KB |
| Apache 가상 호스트 | 초당 요청 KB |
| Apache 가상 호스트 | 초당 요청 |



## <a name="next-steps"></a>다음 단계
* Linux 에이전트에서 [성능 카운터를 수집합니다](data-sources-performance-counters.md).
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다. 