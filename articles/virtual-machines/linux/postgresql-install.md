---
title: Linux VM에서 PostgreSQL 설정 | Microsoft Docs
description: Azure Linux 가상 컴퓨터에 PostgreSQL을 설치하고 구성하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 76f1ddeebb173bf19b15753d12e4374e6365bf8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474230"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Azure에서 PostgreSQL 설치 및 구성
PostgreSQL은 Oracle 및 DB2와 유사한 고급 오픈 소스 데이터베이스입니다. 전체 ACID 규정 준수, 신뢰할 수 있는 트랜잭션 처리 및 다중 버전 동시성 제어와 같은 엔터프라이즈 기능이 포함됩니다. 또한 ANSI SQL 및 SQL/MED(Oracle, MySQL, MongoDB 등에 대한 외부 데이터 래퍼 포함)와 같은 표준을 지원합니다. 12개 이상의 프로시저 언어, GIN 및 GiST 인덱스, 공간 데이터 지원 및 JSON에 대한 여러 NoSQL 같은 기능 또는 키 값 기반 애플리케이션에 대한 지원을 통해 확장성을 높일 수 있습니다.

이 문서에서는 Linux를 실행하는 Azure 가상 머신에서 PostgreSQL을 설치 및 구성하는 방법을 알아봅니다.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>PostgreSQL 설치
> [!NOTE]
> 이 자습서를 완료하려면 Linux를 실행하는 Azure 가상 컴퓨터가 이미 있어야 합니다. 계속하기 전에 Linux VM을 생성하고 설정하려면 [Azure Linux VM 자습서](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
> 
> 

이 경우 PostgreSQL 포트로 포트 1999를 사용하세요.  

PuTTY를 통해 생성한 Linux VM에 연결합니다. Azure Linux VM을 처음 사용하는 경우 [Azure에서 Linux와 함께 SSH를 사용하는 방법](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하여 Linux VM에 연결하기 위해 PuTTY를 사용하는 방법을 배웁니다.

1. 다음 명령을 실행하여 루트(관리자)로 전환합니다.
   
        # sudo su -
2. 일부 배포의 경우 PostgreSQL을 설치하기 전에 설치해야 하는 종속성이 있습니다. 이 목록에서 배포를 확인하고 적절한 명령을 실행합니다.
   
   * Red Hat 기반 Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian 기반 Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. 루트 디렉터리에 PostgreSQL를 다운로드한 다음 패키지의 압축을 풉니다.
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    위 내용은 한 예입니다. [/pub/source/의 인덱스](https://ftp.postgresql.org/pub/source/)에서 더 자세한 다운로드 주소를 찾을 수 있습니다.
4. 빌드를 시작하려면 다음 명령을 실행합니다.
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. 설명서(HTML 및 man 페이지) 및 추가 모듈(contrib)을 비롯하여 빌드할 수 있는 모든 항목을 빌드하려는 경우에는 다음 명령을 실행합니다.
   
        # gmake install-world
   
    다음과 같은 확인 메시지가 표시됩니다.
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>PostgreSQL 구성
1. (선택 사항) 바로 가기 링크를 만들어 버전 번호를 포함하지 않도록 PostgreSQL 참조를 짧게 줄입니다.
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. 데이터베이스에 대한 디렉터리를 만듭니다.
   
        # mkdir -p /opt/pgsql_data
3. 루트가 아닌 사용자를 만들고 해당 사용자의 프로필을 수정합니다. 그런 다음 이 새 사용자(이 예제에서는 *postgres* 임)로 전환합니다.
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > 보안상의 이유로, PostgreSQL은 루트가 아닌 사용자를 사용하여 데이터베이스를 초기화, 시작 또는 종료합니다.
   > 
   > 
4. 아래 명령을 입력하여 *bash_profile* 파일을 편집합니다. *bash_profile* 파일 끝에 다음 줄이 추가됩니다.
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. *bash_profile* 파일을 실행합니다.
   
        $ source .bash_profile
6. 다음 명령을 사용하여 설치의 유효성을 검사합니다.
   
        $ which psql
   
    설치에 성공하면 다음과 같은 응답이 표시됩니다.
   
        /opt/pgsql/bin/psql
7. 또한 PostgreSQL 버전을 확인할 수 있습니다.
   
        $ psql -V

8. 데이터베이스를 초기화합니다.
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    다음과 같은 출력이 표시됩니다.

![Image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL 설정
<!--    [postgres@ test ~]$ exit -->

다음 명령을 실행합니다.

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

/Etc/init.d/postgresql 파일에서 두 변수를 수정합니다. 접두사가 PostgreSQL의 설치 경로인 **/opt/pgsql**로 설정됩니다. PGDATA가 PostgreSQL의 데이터 저장소 경로인 **/opt/pgsql_data**로 설정됩니다.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Image](./media/postgresql-install/no2.png)

파일을 변경하여 실행 가능하도록 설정합니다.

    # chmod +x /etc/init.d/postgresql

PostgreSQL을 시작합니다.

    # /etc/init.d/postgresql start

PostgreSQL의 엔드포인트가 켜져 있는지 확인합니다.

    # netstat -tunlp|grep 1999

다음 출력이 표시됩니다.

![Image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Postgres 데이터베이스에 연결
다시 한 번 postgres 사용자로 전환합니다.

    # su - postgres

Postgres 데이터베이스를 만듭니다.

    $ createdb events

방금 만든 이벤트 데이터베이스에 연결합니다.

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Postgres 테이블 만들기 및 삭제
데이터베이스에 연결했으므로 해당 데이터베이스에 테이블을 만들 수 있습니다.

예를 들어 다음 명령을 사용하여 새로운 예제 Postgres 테이블을 만듭니다.

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

이제 다음 열 이름 및 제한 사항을 사용하는 4열 테이블을 설정했습니다.

1. "name" 열은 20자 이하가 되도록 VARCHAR 명령에 의해 제한됩니다.
2. "food" 열은 각자 가져오는 음식 항목을 나타냅니다. VARCHAR이 이 텍스트를 30자 이하로 제한합니다.
3. "confirmed" 열은 초대받은 사람이 집들이에 RSVP를 설정했는지 여부를 기록합니다. 허용되는 값은 "Y"와 "N"입니다.
4. "date" 열에는 이벤트에 대해 등록한 날짜가 표시됩니다. Postgres에서는 날짜가 yyyy-mm-dd로 작성되도록 요구합니다.

테이블이 생성된 경우 다음이 표시됩니다.

![Image](./media/postgresql-install/no4.png)

다음 명령을 사용하여 테이블 구조를 확인할 수 있습니다.

![Image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>테이블에 데이터 추가
먼저, 행에 정보를 삽입합니다.

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

다음 출력이 표시됩니다.

![Image](./media/postgresql-install/no6.png)

테이블에 몇 명의 더 많은 사람을 추가할 수 있습니다. 다음은 몇 가지 옵션입니다. 또는 직접 만들 수도 있습니다.

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>테이블 표시
테이블을 표시하려면 다음 명령을 사용합니다.

    select * from potluck;

출력은 다음과 같습니다.

![Image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>테이블의 데이터 삭제
테이블의 데이터를 삭제하려면 다음 명령을 사용합니다.

    delete from potluck where name=’John’;

"John" 행에 있는 모든 정보를 삭제합니다. 출력은 다음과 같습니다.

![Image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>테이블의 데이터 업데이트
테이블의 데이터를 업데이트하려면 다음 명령을 사용합니다. 이 경우에는 Sandy가 참석하겠다고 확인했으므로 회신을 "N"에서 "Y"로 RSVP를 변경합니다.

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>PostgreSQL에 대한 자세한 정보 얻기
Azure Linux VM에서 PostgreSQL의 설치를 완료하면 Azure에서 사용하여 즐길 수 있습니다. PostgreSQL에 대해 자세히 알아보려면 [PostgreSQL 웹 사이트](https://www.postgresql.org/)를 방문하세요.

