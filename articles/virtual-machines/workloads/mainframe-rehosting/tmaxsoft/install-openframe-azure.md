---
title: Azure Virtual Machines에서 TmaxSoft OpenFrame 설치
description: Azure Virtual Machines (Vm)에서 TmaxSoft OpenFrame 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 다시 호스트 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 78a8b5e7a1c5512f81315519210bc7759dd15342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800869"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame 설치

Azure에서 OpenFrame 환경을 개발, 데모, 테스트 또는 프로덕션 워크 로드에 적합 한 설정 하는 방법에 알아봅니다. 이 자습서에서는 각 단계를 안내합니다.

OpenFrame는 메인프레임 에뮬레이션 환경을 Azure에 생성 되는 여러 구성 요소를 포함 합니다. 예를 들어, OpenFrame 온라인 서비스와 같은 IBM 고객 정보 제어 시스템 (CICS), 메인프레임 미들웨어 바꾸고 OpenFrame 일괄 처리를 해당 TJES 구성 요소를 사용 하 여 IBM 메인프레임의 작업 항목 하위 시스템 (랭은 여기서 서쪽)를 대체 합니다.

OpenFrame는 Oracle Database, Microsoft SQL Server, IBM Db2, MySQL 등, 모든 관계형 데이터베이스를 사용 하 여 작동 합니다. 이 기능 OpenFrame TmaxSoft Tibero 관계형 데이터베이스를 사용합니다. OpenFrame 및 Tibero Linux 운영 체제에서 실행 합니다. 기타 지원 되는 Linux 배포를 사용할 수 있지만이 자습서에서 CentOS 7.3를 설치 합니다. OpenFrame 응용 프로그램 서버와 Tibero 데이터베이스는 하나의 가상 머신 (VM)에 설치 됩니다.

이 자습서에서는 OpenFrame suite 구성 요소를 설치할을 안내합니다. 일부는 별도로 설치 해야 합니다.

Main OpenFrame 구성 요소:

- 설치 패키지가 필요합니다.
- Tibero 데이터베이스입니다.
- 개방형 데이터베이스 연결 (ODBC) Tibero 데이터베이스와 통신할 수 OpenFrame의 응용 프로그램에서 사용 됩니다.
- 전체 시스템을 관리 하는 미들웨어의 OpenFrame 기준입니다.
- OpenFrame 일괄 처리, 메인프레임의 일괄 처리 시스템을 대체 하는 솔루션입니다.
- TACF, 시스템 및 리소스에 대 한 사용자 액세스를 제어 하는 서비스 모듈입니다.
- ProSort, 일괄 처리 트랜잭션 위한 정렬 도구입니다.
- OFCOBOL, 컴파일러는 메인프레임 COBOL 프로그램을 해석 합니다.
- OFASM, 메인프레임의 어셈블러 프로그램을 해석 하는 컴파일러입니다.
- OpenFrame 서버 유형 C (OSC), 메인프레임의 미들웨어 및 IBM CICS를 대체 하는 솔루션입니다.
- Java Enterprise 사용자 솔루션 (JEUS), Java Enterprise Edition 6에 대 한 인증 되어 있는 웹 응용 프로그램 서버.
- OFGW, 3270 수신기를 제공 하는 OpenFrame 게이트웨이 구성 요소입니다.
- OFManager, 웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공 하는 솔루션입니다.

다른 필수 OpenFrame 구성 요소:

- OSI 메인프레임 미들웨어 및 IMS DC를 대체 하는 솔루션입니다.
- TJES, 메인프레임의 랭은 여기서 서쪽 환경을 제공 하는 솔루션입니다.
- OFTSAM, SAM (V) 파일은 개방형 시스템에서 사용할 수 있게 해 주는 솔루션입니다.
- OFHiDB, 메인프레임을 대체 하는 솔루션의 IMS DB입니다.
- OFPLI, 메인프레임을 해석 하는 컴파일러의 PL/I 프로그램입니다.
- PROTRIEVE, CA-Easytrieve 메인프레임 언어를 실행 하는 솔루션입니다.
- OFMiner, 솔루션을 메인프레임 자산을 분석 하 고 Azure로 마이그레이션합니다.

## <a name="architecture"></a>아키텍처

다음 그림에는이 자습서에서는 설치 OpenFrame 7.0 아키텍처 구성 요소에 간략하게 설명:

![OpenFrame 구성 요소](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 시스템 요구 사항

다음 표에서 Azure에서의 설치에 대 한 요구 사항을 나열합니다.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>요구 사항</th><th>설명</th></tr>
</thead>
<tbody>
<tr><td>Azure에서 지원 되는 Linux 배포판
</td>
<td>
X86 Linux 2.6 (32 비트, 64 비트)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>하드웨어
</td>
<td>코어: 2 (최소)<br/>
메모리: 4GB (최소)<br/>
스왑 공간: 1GB (최소)<br/>
하드 디스크: 100GB (최소)<br/>
</td>
</tr>
<tr><td>Windows 사용자에 대 한 선택적 소프트웨어
</td>
<td>PuTTY: VM 기능을 구성 하려면이 가이드에 사용<br/>
WinSCP: 사용할 수 있는 인기 있는 SFTP 클라이언트 및 FTP 클라이언트<br/>
Windows에 대 한 eclipse: TmaxSoft에서 지 원하는 개발 플랫폼<br/>
(Microsoft Visual Studio는 현재 지원 되지 않음)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>필수 조건

필요한 모든 소프트웨어를 조합 하 고 모든 수동 프로세스를 완료 하는 데 며칠이 지출 한도를 계획 합니다.

시작 하기 전에 다음을 수행 합니다.

- TmaxSoft에서 OpenFrame 설치 미디어를 가져옵니다. 기존 TmaxSoft 고객 인 경우 사용이 허가 된 복사본이 TmaxSoft 담당자에 게 문의 합니다. 그렇지 않으면에서 평가판을 요청 [TmaxSoft](http://www.tmaxsoft.com/contact/)합니다.

- 전자 메일을 전송 하 여 OpenFrame 설명서 요청 <support@tmaxsoft.com>합니다.

- 아직 없는 경우 Azure 구독을 가져옵니다. 만들 수도 있습니다는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 시작 하기 전에 합니다.

- 선택 사항입니다. 조직에서 허용 된 사용자에 게 Azure VM에 대 한 액세스를 제한 하는 jumpbox 또는 사이트 간 VPN 터널을 설정 합니다. 이 단계가 필요 하지 않습니다. 하지만 가장 좋습니다.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>OpenFrame 및 Tibero Azure에서 VM으로 설정

다양 한 배포 패턴을 사용 하 여 OpenFrame 환경을 설정할 수 있지만 다음 절차에 OpenFrame 응용 프로그램 서버 및 Tibero 데이터베이스 하나의 VM에 배포 하는 방법을 보여 줍니다. 규모가 큰 환경 및 많은 워크 로드에 대 한 성능 향상을 위해 자체 VM에 개별적으로 데이터베이스를 배포 하는 것이 좋습니다.

**VM을 만들려면**

1. Azure portal로 이동 <http://portal.azure.com> 계정에 로그인 합니다.

2. **가상 머신**을 클릭합니다.

    ![Azure portal의 리소스 목록](media/vm-01.png)

3. **추가**를 클릭합니다.

    ![Azure portal의 옵션을 추가 합니다.](media/vm-02.png)

4. 오른쪽 **운영 체제**, 클릭 **자세한**합니다.

     ![Azure portal에서 더 많은 옵션](media/vm-03.png)

5. 클릭 **CentOS 기반 7.3** 정확 하 게이 연습을 수행 하거나 다른 지원 Linux 배포판을 선택할 수 있습니다.

     ![Azure portal에서 운영 체제 옵션](media/vm-04.png)

6. 에 **기본 사항** 설정을 입력 **이름**를 **사용자 이름**를 **인증 유형**를 **구독** (종 량 제는 지불의 AWS 스타일) 및 **리소스 그룹** (기존 TmaxSoft 그룹 만들기 또는).

7. 완료 되 면 (에 대 한 공개/개인 키 쌍을 포함 하 여 **인증 유형**)를 클릭 **제출**합니다.

> [!NOTE]
> 에 대 한 SSH 공개 키를 사용 하는 경우 **인증 유형**, 공개/개인 키 쌍을 생성 하려면 다음 섹션의 단계를 참조 하세요. 그런 다음 여기에 나오는 단계를 다시 시작 합니다.

### <a name="generate-a-publicprivate-key-pair"></a>공개/개인 키 쌍 생성

Windows 운영 체제를 사용 하는 경우 PuTTYgen 공개/개인 키 쌍을 생성 해야 합니다.

공개 키를 자유롭게 공유할 수 있습니다 하지만 개인 키는 전적으로 비밀로 유지 되어야 하 고 다른 당사자와 공유 해서는 안 됩니다. 키를 생성 한 후 붙여 넣어야 합니다 **SSH 공개 키** 구성에-실제로 Linux VM에 업로드 합니다. 저장 되어 내부 권한이\_내에서 키를 \~사용자 계정의 홈 디렉터리의 /.ssh 디렉터리입니다. Linux VM을 인식 하 고 연결 된 제공 하면 연결의 유효성을 검사 하는 일을 할 이면 **SSH 개인 키** SSH 클라이언트 (이 경우 PuTTY)에 있습니다.

새로 제공 하는 경우 개인 VM에 액세스 합니다. 

- 새 개별 PuTTYgen을 사용 하 여 자신의 공개/개인 키를 생성 합니다.
- 개인 자체 개인 키를 별도로 저장 하 고 VM의 관리자에 게 공개 키 정보를 보냅니다.
- 관리자에 공개 키의 내용을 붙여 합니다 \~/.ssh/authorized\_키 파일입니다.
- 새 개인 PuTTY를 통해 연결합니다.

**공개/개인 키 쌍을 생성 하려면**

1.  PuTTYgen 다운로드 <https://www.putty.org/> 모든 기본 설정을 사용 하 여 설치 합니다.

2.  PuTTYgen을 열려면 c:에서 PuTTY 설치 디렉터리를 찾습니다\\Program Files\\PuTTY 합니다.

    ![PuTTY 인터페이스](media/puttygen-01.png)

3.  **생성**을 클릭합니다.

    ![PuTTY 키 생성기 대화 상자](media/puttygen-02.png)

4.  생성 후 공개 키와 개인 키를 저장 합니다. 공개 키의 내용을 붙여를 **SSH 공개 키** 섹션을 **가상 머신 만들기 \> 기본 사항** 창 (이전 섹션의 6, 7 단계에서 표시 됨).

    ![PuTTY 키 생성기 대화 상자](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM 기능 구성

1. Azure portal에서에 **크기 선택** 블레이드에서 하려는 Linux 컴퓨터 하드웨어 설정을 선택 합니다. 합니다 *최소* Tibero와 OpenFrame 설치에 대 한 요구 사항은 2 개 Cpu 및 4GB RAM이 예제에서는 설치와 같이:

    ![기본 사항-가상 머신 만들기](media/create-vm-01.png)

2. 클릭 **3 설정** 및 기본 설정을 사용 하 여 선택적 기능을 구성 합니다.
3. 결제 세부 정보를 검토 합니다.

    ![가상 머신-구매 만들기](media/create-vm-02.png)

4. 선택 항목을 제출 합니다. Azure VM을 배포 하기 시작 합니다. 이 프로세스는 일반적으로 몇 분 정도 걸립니다.

5. VM을 배포할 때 구성 하는 동안 선택 된 모든 설정을 보여 주는 해당 대시보드가 표시 됩니다. 기록해는 **공용 IP 주소**합니다.

    ![Azure 대시보드에 tmax](media/create-vm-03.png)

6. PuTTY를 엽니다.

7. 에 대 한 **호스트 이름**사용자 이름을 입력 하 고 공용 IP 주소로 복사 합니다. 예를 들어 **사용자 이름\@publicip**합니다.

    ![PuTTY 구성 대화 상자](media/putty-01.png)

8. 에 **범주** 상자를 클릭 합니다 **연결 \> SSH \> Auth**합니다. 경로를 제공 하 **개인 키** 파일입니다.

    ![PuTTY 구성 대화 상자](media/putty-02.png)

9. 클릭 **열고** PuTTY 창을 시작 합니다. 성공한 경우에 Azure에서 실행 중인 새 CentOS VM에 연결 됩니다.

10. 루트 사용자로 로그온 할 입력 **sudo bash**합니다.

    ![명령 창의 루트 사용자 로그온](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>환경 및 패키지 설정

VM이 만들어진를 로그온에 몇 가지 설치 단계를 수행 하 고 필요한 사전 설치 패키지를 설치 해야 합니다.

1. 이름을 매핑합니다 **ofdemo** vi 호스트 파일을 편집 하 여 로컬 IP 주소 (`vi /etc/hosts`). IP도 192.168.96.148 ofdemo,이 변경 하기 전에:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     다음은 변경 된 후입니다.

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. 그룹 및 사용자를 만듭니다.

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 사용자 oframe7에 대 한 암호를 변경 합니다.

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. /Etc/sysctl.conf에서 커널 매개 변수를 업데이트 합니다.

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 다시 부팅 하지 않고 동적으로 커널 매개 변수를 새로 고칩니다.

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 필요한 패키지를 가져옵니다. 서버가 인터넷에 연결 되어 있는지 확인 하 고 다음 패키지를 다운로드 한 다음 설치 합니다.

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurse

          > [!NOTE]
          > Ncurse 패키지를 설치한 후 다음 기호화 된 링크를 만듭니다.
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Java RPM 설치의 경우 다음을 수행 합니다.

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Tibero 데이터베이스 설치

Tibero는 Azure 대 OpenFrame 환경의 몇 가지 주요 기능을 제공합니다.

- Tibero 다양 한 시스템 함수에 대 한 OpenFrame 내부 데이터 저장소로 사용 됩니다.
- 파일 VSAM KSDS, RRDS, ESDS, 등 데이터 저장소에 대 한 Tibero 데이터베이스를 내부적으로 사용 합니다.
- Tibero TACF 데이터 리포지토리에 저장 됩니다.
- OpenFrame 카탈로그 정보 Tibero에 저장 됩니다.
- Tibero 데이터베이스 응용 프로그램 데이터 저장을 IBM Db2에 대 한 대체를 사용할 수 있습니다.

**Tibero를 설치 하려면**

1. Tibero 이진 설치 관리자 파일이 있는지 확인 하 고 버전 번호를 검토 합니다.
2. Tibero 소프트웨어 Tibero 사용자 계정 (oframe)에 복사 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. .Bash 엽니다\_vi에서 프로필 (`vi .bash_profile`)에서 다음을 붙여넣습니다.

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. 실행 하려면 명령 프롬프트, bash 프로필:

    ```
    source .bash_profile
    ```

5. 팁 파일 (Tibero는 구성 파일)를 생성 한 다음 vi에서 엽니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. 수정할 \$TB\_HOME/client/config/tbdsn.tbr 127.0.0.1 대신 put 및 표시 된 것 처럼 oflocalhost:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 데이터베이스를 만듭니다. 다음과 같은 출력이 표시 됩니다.

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Tibero 재활용을 먼저 종료를 사용 하는 `tbdown` 명령입니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 이제 Tibero를 사용 하 여 부팅 `tbboot`합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 테이블 스페이스를 만들려면 SYS를 사용 하 여 데이터베이스에 액세스할 사용자 (sys/tmax) 다음 TACF 기본 볼륨에 필요한 테이블 스페이스를 만듭니다.

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 이제 다음 SQL 명령을 입력 합니다.

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero 부팅 하 고 Tibero 프로세스가 실행 되 고 있는지 확인 합니다.

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

출력

![Tibero 출력](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC 설치

OpenFrame에서 응용 프로그램은 unixODBC 오픈 소스 프로젝트에서 제공 하는 ODBC API를 사용 하 여 Tibero 데이터베이스와 통신 합니다.

ODBC 설치:

1. UnixODBC 2.3.4.tar.gz 설치 관리자 파일이 있는지 확인 하거나 사용 하 여는 `wget unixODBC-2.3.4.tar.gz` 명령입니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 이진 파일을 압축을 풉니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. UnixODBC 2.3.4 디렉터리로 이동한 검사 컴퓨터 정보를 사용 하 여 메이크파일을 생성 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     기본적으로 unixODBC 되어 /usr /local에 따라서 `--prefix` 위치를 변경 하려면 값을 전달 합니다. 마찬가지로, 구성 파일은 /etc에 기본적으로 설치 되므로 `--sysconfdir` 원하는 위치 값을 전달 합니다.

4. 메이크파일을 실행 합니다. `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 실행 파일을 컴파일한 후 프로그램 디렉터리에 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Vi를 사용 하 여 bash 프로필 편집 (`vi ~/.bash_profile`) 다음을 추가 합니다.

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC를 적용 합니다. 그에 따라 다음 파일을 편집 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. 기호화 된 링크를 만들고 Tibero 데이터베이스 연결의 유효성을 검사 합니다.

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

다음 출력이 표시 됩니다.

![SQL에 연결 된 ODBC 출력 표시](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame 자료를 설치 합니다.

기본 응용 프로그램 서버 OpenFrame 서버 프로세스를 처리 하는 트랜잭션을 포함 하 여 Azure에서 시스템을 관리 하는 데 사용 하는 개별 서비스 하기 전에 설치 됩니다.

**OpenFrame 자료를 설치 하려면**

1. Tibero 설치에 성공 했는지를 확인 하는 다음 OpenFrame\_Base7\_0\_Linux\_x86\_64 bin installer 파일 및 base.properties 구성 파일을 제공 합니다.

2. 다음 Tibero 관련 정보를 사용 하 여 bash 프로필 업데이트:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Bash 프로필을 실행 합니다.`[oframe7@ofdemo ~]$ . .bash_profile`
4. Tibero 프로세스가 실행 되 고 있는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![기본](media/base-01.png)

     > [!IMPORTANT]
     > 설치 하기 전에 Tibero를 시작 해야 합니다.

5. 라이선스를 생성할 [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) OSC OpenFrame 자료, Batch, TACF 말해 해당 폴더에서 라이선스 및:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame 자료 이진 및 base.properties 파일을 다운로드 합니다.

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Base.properties 파일을 사용 하 여 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    완료 되 면 설치 완료 메시지 diplayed입니다.

8. 사용 하 여 OpenFrame 기본 디렉터리 구조를 확인 합니다 `ls -ltr` 명령입니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. OpenFrame 자료를 시작 합니다.

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 명령 출력](media/base-02.png)

10. Si tmadmin 명령을 사용 하 여 준비 프로세스 상태를 확인 합니다. RDY에 표시 되는 **상태** 각 프로세스에 대 한 열:

     ![tmadmin 명령 출력](media/base-03.png)

11. OpenFrame 자료를 종료 합니다.

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame 일괄 처리를 설치 합니다.

OpenFrame Batch 메인프레임 일괄 처리 환경을 시뮬레이션 하는 몇 가지 구성 요소로 이루어져 및 Azure에서 일괄 처리 작업을 실행 하는 데 사용 됩니다.

**일괄 처리를 설치 하려면**

1. 기본 설치에 성공 했는지를 확인 합니다 OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64 bin installer 파일 및 batch.properties 구성 파일은.

2. 명령 프롬프트에서 입력 `vi batch.properties` vi를 사용 하 여 batch.properties 파일을 편집 합니다.

3. 매개 변수를 다음과 같이 수정 합니다.

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. 명령 프롬프트에서 입력 일괄 처리 설치 관리자를 실행 합니다.

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 설치가 완료 되 면 입력 하 여 설치 된 OpenFrame 도구 모음 시작 `tmboot` 명령 프롬프트에서.

    ![tmboot 출력](media/tmboot-01.png)

6. 형식 `tmadmin` OpenFrame 프로세스를 확인 하려면 명령 프롬프트에서.

    ![Tmax 관리 화면](media/tmadmin-01.png)

7. 다음 명령을 실행 합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. 사용 하 여는 `tmdown` 명령을 시작 하 고 일괄 처리를 종료 합니다.

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF 설치

TACF 관리자는 시스템 및 RACF 보안을 통해 리소스에 대 한 사용자 액세스를 제어 하는 OpenFrame 서비스 모듈입니다.

**TACF를 설치 하려면**

1. 확인 된 OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64 bin installer 파일 및 tacf.properties 구성 파일은입니다.
2. 일괄 처리 설치에 성공 했는지 후 vi를 사용 하 여 tacf.properties 파일을 엽니다 (`vi tacf.properties`).
3. TACF 매개 변수를 수정 합니다.

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. TACF 설치 관리자를 완료 한 후 TACF 환경 변수를 적용 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     source \~/.bash\_profile
     ```

5. TACF 설치 관리자를 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     출력은 다음과 같습니다.

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. 명령 프롬프트에서 입력 `tmboot` OpenFrame를 다시 시작 합니다. 출력은 다음과 같습니다.

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. 프로세스 상태를 사용 하 여 준비가 되었는지 확인 `tmadmin` 에 `si` 명령입니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     에 **상태** 열 RDY 나타납니다.

    ![상태 열에 RDY](media/tmboot-02.png)

8. 다음 명령을 실행 합니다.

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. 서버 사용 하 여 종료를 `tmdown` 명령입니다. 출력은 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>ProSort 설치

ProSort는 데이터 정렬에 대 한 일괄 처리 트랜잭션에서 사용 하는 유틸리티입니다.

**ProSort를 설치 하려면**

1. 성공적으로 일괄 처리 설치 되었는지 확인 하 고 있는지를 확인 합니다 **bin을 prosort prosort\_linux64 2123 opt.tar.gz 2sp3** 설치 관리자 파일이 있습니다.

2. 속성 파일을 사용 하 여 설치 관리자를 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. 홈 위치로 prosort 디렉터리를 이동 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 라이선스 하위 디렉터리를 만들고에 라이선스 파일을 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Vi에서 bash.profile 엽니다 (`vi .bash_profile`) 하 고 다음과 같이 업데이트 합니다.

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. 명령 프롬프트, bash 프로필을 실행 하려면 다음을 입력 합니다. `. .bash_profile`

7. 구성 파일을 만듭니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 기호화 된 링크를 만듭니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 실행 하 여 ProSort 설치를 확인 합니다 `prosort -h` 명령입니다. 예를 들면 다음과 같습니다.

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>OFCOBOL 설치

OFCOBOL은 OpenFrame 컴파일러는 메인프레임 COBOL 프로그램을 해석 하는 경우 

**OFCOBOL를 설치 하려면**

1. 일괄 처리/온라인 설치에 성공 했는지, 있는지 확인 한 후 확인을 OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64 bin 설치 관리자 파일이 합니다.

2. 명령 프롬프트에서 OFCOBOL 설치 관리자를 실행 하려면 다음을 입력 합니다.

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 라이선스 계약 내용을 잘 읽고 계속 하려면 Enter 키를 누릅니다.

4. 라이선스에 동의 합니다. 설치가 완료 되 면 다음 메시지가 나타납니다.

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Vi에서 bash 프로필을 엽니다 (`vi .bash_profile`)는 OFCOBOL 변수를 사용 하 여 업데이트 되었는지 확인 합니다.
6. Bash 프로필을 실행 합니다. 명령 프롬프트에 다음을 입력합니다.

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL 라이선스 설치 폴더로 복사 합니다. 예를 들면 다음과 같습니다.
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame tjclrun.conf 구성 파일을 이동한 vi에서 엽니다. 예를 들면 다음과 같습니다.
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   다음은 변경 전의 SYSLIB 섹션:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   변경 후 SYSLIB 섹션을 다음과 같습니다.
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. 검토를 OpenFrame\_COBOL\_InstallLog.log vi 파일 및 오류가 없는지 확인 합니다. 예를 들면 다음과 같습니다.
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. 사용 된 `ofcob --version` 명령 및 설치를 확인 하려면 버전 번호를 검토 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. OpenFrame를 사용 하 여 다시 부팅 합니다 `tmdown/tmboot` 명령입니다.

## <a name="install-ofasm"></a>OFASM 설치

OFASM은 OpenFrame 컴파일러 메인프레임의 어셈블러 프로그램을 해석 하는 경우

**OFASM를 설치 하려면**

1. 일괄 처리/온라인 설치에 성공 했는지, 있는지 확인 한 후 확인을 OpenFrame\_ASM3\_0\_Linux\_x86\_64 bin 설치 관리자 파일이 합니다.

2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 라이선스 계약 내용을 잘 읽고 계속 하려면 Enter 키를 누릅니다.
4. 라이선스에 동의 합니다.
5. Bash 프로필 OFASM 변수를 사용 하 여 업데이트 되었는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Vi에서 OpenFrame tjclrun.conf 구성 파일을 열고 다음과 같이 편집 합니다.

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     다음은 [SYSLIB] 섹션 *하기 전에* 변경:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     다음은 [SYSLIB] 섹션 *후* 변경:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. 엽니다는 OpenFrame\_ASM\_InstallLog.log vi 파일 및 오류가 없는지 확인 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 다음 명령 중 하나를 실행 하 여 OpenFrame를 다시 부팅 합니다.

     ```
     tmdown / tmboot
     ```

     —또는—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC 설치

OSC는 고속 OLTP 트랜잭션과 다른 관리 기능을 지 원하는 IBM CICS 비슷합니다 OpenFrame 환경입니다.

**OSC를 설치 하려면**

1. 기본 설치에 성공 했는지를 확인 합니다 OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64 bin 설치 관리자 파일과 osc.properties 구성 파일은 존재 합니다.
2. Osc.properties 파일에서 다음 매개 변수를 편집 합니다.
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 표시 된 것 처럼 속성 파일을 사용 하 여 설치 관리자를 실행 합니다.

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     완료 되 면 "설치 완료" 메시지가 표시 됩니다.

4. OSC 변수를 사용 하 여 bash 프로필 업데이트 되었는지 확인 합니다.
5. 검토를 OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log 파일입니다. 다음과 같이 표시됩니다.

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Vi를 사용 하 여 ofsys.seq 구성 파일을 엽니다. 예를 들면 다음과 같습니다.

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. 에 \#자료 및 \#섹션에서는 일괄 처리, 표시 된 것 처럼 매개 변수를 편집 합니다.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. 라이선스 파일을 복사 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. 시작 하 고 OSC 종료를 입력 하 여 CICS 지역 공유 메모리를 초기화할 `osctdlinit OSCOIVP1` 명령 프롬프트에서.

10. 실행 `oscboot` OSC 부팅을 합니다. 출력은 다음과 같습니다.

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. 프로세스 상태가 준비 인지를 확인 하려면 사용 된 `tmadmin` si에서 명령을 합니다. 모든 프로세스에서 RDY 표시 해야 합니다 **상태** 열입니다.

    ![RDY 표시 하는 프로세스](media/tmadmin-02.png)

12. OSC 사용 하 여 종료를 `oscdown` 명령입니다.

## <a name="install-jeus"></a>JEUS 설치

(Java Enterprise 사용자 솔루션) JEUS OpenFrame 웹 응용 프로그램 서버의 프레젠테이션 계층을 제공합니다.

JEUS를 설치 하기 전에 라이브러리 및 JEUS를 설치 하는 데 필요한 명령줄 도구를 제공 하는 Apache Ant 패키지를 설치 합니다.

**Apache Ant를 설치 하려면**

1. Ant 이진으로 다운로드할은 `wget` 명령입니다. 예를 들면 다음과 같습니다.

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 사용 된 `tar` 유틸리티를 이진 파일을 추출 하 고 적절 한 위치로 이동 합니다. 예를 들면 다음과 같습니다.

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 효율성을 높이기 위해 기호화 된 링크를 만듭니다.

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Vi에서 bash 프로필을 엽니다 (`vi .bash_profile`) 하 고 다음 변수를 사용 하 여 업데이트 합니다.

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  수정 된 환경 변수를 적용 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS를 설치 하려면**

1. 설치 관리자를 사용 하 여 확장 된 `tar` 유틸리티입니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. 만들기는 **jeus** 폴더 (`mkdir jeus7`) 이진 파일의 압축을 풉니다.
3. 변경 된 **설치** 디렉터리 (또는 사용자 고유의 환경에 대 한 JEUS 매개 변수 사용). 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 실행 `ant clean-all` 빌드를 수행 하기 전에 합니다. 출력은 다음과 같습니다.

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  도메인-구성-template.properties 파일의 백업을 만듭니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Vi에서 도메인-구성-template.properties 파일을 엽니다.

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. `jeus.password=jeusadmin nodename=Tmaxsoft`를 `jeus.password=tmax1234 nodename=ofdemo`로 변경

8. 실행 된 `ant install` JEUS 명령입니다.
9.  업데이트 된.bash\_같이 JEUS 변수를 사용 하 여 프로필 파일:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Bash 프로필을 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *옵션*. 쉽게 종료 및 부팅 JEUS 구성 요소에 대 한 별칭을 만듭니다.

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 설치를 확인 하려면 표시 된 대로 도메인 관리 서버 시작:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 웹 로그온 구문을 사용 하 여 확인 합니다.

     ```
     http://<IP>:<port>/webadmin/login
     ```

     예를 들어 <http://192.168.92.133:9736/webadmin/login.> 로그온 화면이 표시 됩니다.
    
     ![JEUS 172.16.0.0 WebAdmin 로그온 화면](media/jeus-01.png)

     > [!NOTE]
     > 포트 보안을 사용 하 여 문제를 발생 하는 경우 포트 9736 엽니다 방화벽을 사용 하지 않도록 설정 하거나 (`systemctl stop firewall`).

14. Server1에 대 한 호스트 이름을 변경 하려면 **잠금 및 편집**, 클릭 **server1**합니다. 서버 창에서 호스트를 다음과 같이 변경 합니다.

    1.  변경 **Nodename** 하 **ofdemo**합니다.
    2.  클릭 **확인** 창의 오른쪽에 있습니다.
    3.  클릭 **변경 내용 적용** 왼쪽 창의 및 설명, 입력 *호스트 이름 변경*합니다.

    ![JEUS 172.16.0.0 WebAdmin 화면](media/jeus-02.png)

15. 확인 화면에 성공적으로 구성 되었는지 확인 합니다.

    ![jeus_domain 서버 화면](media/jeus-03.png)

16. 다음 명령을 사용 하는 관리 되는 서버 프로세스 "server1"를 시작 합니다.

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW 설치

OFGW는 3270 터미널 에뮬레이터와 OSI 기준 간의 통신을 지원 하 고 터미널 에뮬레이터와 OSI 세션을 관리 하는 OpenFrame 게이트웨이입니다.

**OFGW를 설치 하려면**

1. JEUS 성공적으로 설치 되었는지 확인 한 다음 확인을 OFGW7\_0\_1\_Generic.bin 설치 관리자 파일이 있습니다.
2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 해당 프롬프트의 다음 위치를 사용 합니다.
     -   JEUS 홈 디렉터리
     -   JEUS 도메인 이름
     -   JEUS 서버 이름
     -   Tibero 드라이버
     -   Ofdemo Tmax 노드 ID

4. 나머지 기본값을 한 다음 enter 키를 눌러 설치 관리자를 종료 합니다.

5. OFGW에 대 한 URL이 예상 대로 작동 하는지 확인 합니다.

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     다음 화면이 나타납니다.

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager 설치

OFManager는 OpenFrame에 대 한 웹 환경에서 작업 및 관리 기능을 제공합니다.

**OFManager를 설치 하려면**

1. 확인 된 OFManager7\_Generic.bin 설치 관리자 파일이 합니다.
2. 설치 관리자를 실행 합니다. 예를 들면 다음과 같습니다.

     ```
     OFManager7_Generic.bin
     ```

3.  Enter 키를 눌러 계속 하 고 사용권 계약에 동의 합니다.
4.  설치 폴더를 선택 합니다.
5.  기본값을 그대로 적용합니다.
6.  데이터베이스로 Tibero를 선택 합니다.
7.  설치 관리자를 종료 하려면 Enter 키를 누릅니다.
8.  OFManager에 대 한 URL이 예상 대로 작동 하는지 확인 합니다.

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

시작 화면에 표시 됩니다.

![Tmax OpenFrame 관리자 로그온 화면](media/ofmanager-01.png)

OpenFrame 구성 요소의 설치를 완료 됩니다.

## <a name="next-steps"></a>다음 단계

메인프레임 마이그레이션을 고려 하는 경우 확장 파트너 에코 시스템은 하는 데 사용할 수 있습니다. 파트너 솔루션 선택과 관련한 상세 지침은 [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx)를 참조하세요.

-   [Azure 시작](https://docs.microsoft.com/azure/)
-   [Host Integration Server(HIS) 설명서](https://docs.microsoft.com/host-integration-server/)
-   [Azure 가상 데이터 센터 리프트 앤 시프트 가이드](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
