---
title: Azure Linux Virtual Machine에서 Oracle Database 12c 데이터베이스 백업 및 복구 | Microsoft Docs
description: Azure 환경에서 Oracle Database 12c 데이터베이스를 백업하고 복구하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c41f13a6437f69121d3bbb387c96d8e13f2be0b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567084"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Azure Linux Virtual Machine에서 Oracle Database 12c 데이터베이스 백업 및 복구

Azure CLI를 사용하여 명령줄 프롬프트 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리할 수 있습니다. 이 문서에서는 Azure Marketplace 갤러리 이미지에서 Oracle Database 12c 데이터베이스를 배포하는 데 Azure CLI의 스크립트를 사용합니다.

시작하기 전에 Azure CLI가 설치되어 있는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-the-environment"></a>환경 준비

### <a name="step-1-prerequisites"></a>1단계: 필수 조건

*   백업 및 복구 프로세스를 수행하려면 먼저 Oracle Database 12c의 인스턴스가 설치되어 있는 Linux VM을 만들어야 합니다. VM을 만드는 데 사용하는 Marketplace 이미지는 *Oracle:Oracle-Database-Ee:12.1.0.2:latest*라고 합니다.

    Oracle 데이터베이스를 만드는 방법을 알아보려면 [Oracle 데이터베이스 빠른 시작](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)을 참조하세요.


### <a name="step-2-connect-to-the-vm"></a>2단계: VM에 연결

*   VM으로 SSH(Secure Shell) 세션을 만들려면 다음 명령을 사용합니다. IP 주소 및 호스트 이름 조합을 VM에 대한 `publicIpAddress` 값으로 바꿉니다.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3단계: 데이터베이스를 준비 합니다.

1.  이 단계에서는 *myVM*이라는 VM이 실행되는 Oracle 인스턴스(cdb1)가 있다고 가정합니다.

    *oracle* superuser 루트를 실행한 다음, 수신기를 초기화합니다.

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (선택 사항) 데이터베이스가 보관 로그 모드에 있는지 확인합니다.

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (선택 사항) 커밋을 테스트하려면 테이블을 만듭니다.

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  백업 파일 위치 및 크기를 확인하거나 변경합니다.

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Oracle RMAN(Recovery Manager)을 사용하여 데이터베이스를 백업합니다.

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4단계: Linux Vm의 응용 프로그램 일치 백업

애플리케이션 일치 백업은 Azure Backup의 새 기능입니다. VM 스냅숏(사전 스냅숏 및 사후 스냅숏) 이전 및 이후에 실행하는 스크립트를 만들고 선택할 수 있습니다.

1. JSON 파일을 다운로드합니다.

    https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig에서 VMSnapshotScriptPluginConfig.json을 다운로드합니다. 파일 콘텐츠는 다음과 유사하게 나타납니다.

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. VM에서 /etc/azure 폴더를 만듭니다.

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. JSON 파일을 복사합니다.

    VMSnapshotScriptPluginConfig.json을 /etc/azure 폴더에 복사합니다.

4. JSON 파일을 편집합니다.

    `PreScriptLocation` 및 `PostScriptlocation` 매개 변수를 포함하도록 VMSnapshotScriptPluginConfig.json 파일을 편집합니다. 예를 들면 다음과 같습니다.

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. 사전 스냅숏 및 사후 스냅숏 스크립트 파일을 만듭니다.

    다음은 “콜드 백업”에 대한 사전 스냅숏 및 사후 스냅숏 스크립트의 예(종료 및 다시 시작을 사용하는 오프라인 백업)입니다.

    /etc/azure/pre_script.sh의 경우:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/post_script.sh의 경우:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    다음은 “핫 백업”에 대한 사전 스냅숏 및 사후 스냅숏 스크립트의 예(온라인 백업)입니다.

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/post_script.sh의 경우:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/pre_script.sql의 경우 사용자 요구 사항에 따라 파일의 콘텐츠를 수정해야 합니다.

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /etc/azure/post_script.sql의 경우 사용자 요구 사항에 따라 파일의 콘텐츠를 수정해야 합니다.

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. 파일 사용 권한을 변경합니다.

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. 스크립트를 테스트합니다.

    스크립트를 테스트하려면 먼저 루트로 로그인합니다. 그런 다음, 오류가 없는지 확인합니다.

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

자세한 내용은 [Linux VM에 대한 애플리케이션 일치 백업](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)을 참조하세요.


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5단계: VM 백업에 사용 하 여 Azure Recovery Services 자격 증명 모음

1.  Azure Portal에서 **Recovery Services 자격 증명 모음**을 검색합니다.

    ![Recovery Services 자격 증명 모음 페이지](./media/oracle-backup-recovery/recovery_service_01.png)

2.  **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음을 추가하려면 **추가**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 추가 페이지](./media/oracle-backup-recovery/recovery_service_02.png)

3.  계속하려면 **myVault**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 세부 정보 페이지](./media/oracle-backup-recovery/recovery_service_03.png)

4.  **myVault** 블레이드에서 **Backup**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 백업 페이지](./media/oracle-backup-recovery/recovery_service_04.png)

5.  **Backup 목표** 블레이드에서 **Azure** 및 **가상 머신**의 기본 값을 사용합니다. **확인**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 세부 정보 페이지](./media/oracle-backup-recovery/recovery_service_05.png)

6.  **Backup 정책**의 경우 **DefaultPolicy**를 사용하거나 **정책 새로 만들기**를 선택합니다. **확인**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 백업 정책 세부 정보 페이지](./media/oracle-backup-recovery/recovery_service_06.png)

7.  **가상 머신 선택** 블레이드에서 **myVM1** 확인란을 선택한 다음 **확인**을 클릭합니다. **백업 사용** 단추를 클릭합니다.

    ![백업할 Recovery Services 자격 증명 모음 항목 세부 정보 페이지](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > **백업 사용**을 클릭한 후, 백업 프로세스는 예정된 시간이 만료될 때까지 시작되지 않습니다. 즉시 백업을 시작하려면 다음 단계를 완료합니다.

8.  **myVault-Backup 항목** 블레이드의 **Backup 항목 수**에서 백업 항목 수를 선택합니다.

    ![Recovery Services 자격 증명 모음 myVault 세부 정보 페이지](./media/oracle-backup-recovery/recovery_service_08.png)

9.  **Backup 항목(Azure Virtual Machine)** 블레이드에서 페이지의 오른쪽에 있는 줄임표(**...**) 단추를 클릭한 다음 **지금 Backup**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 지금 Backup 명령](./media/oracle-backup-recovery/recovery_service_09.png)

10. **Backup** 단추를 클릭합니다. 백업 프로세스가 완료될 때까지 기다립니다. 그런 다음 [6 단계: 데이터베이스 파일 제거](#step-6-remove-the-database-files)합니다.

    백업 작업의 상태를 보려면 **작업**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 작업 페이지](./media/oracle-backup-recovery/recovery_service_10.png)

    백업 작업의 상태가 다음 이미지에 표시됩니다.

    ![상태가 표시된 Recovery Services 자격 증명 모음 작업 페이지](./media/oracle-backup-recovery/recovery_service_11.png)

11. 애플리케이션 일치 백업의 경우 로그 파일에서 오류를 해결 합니다. 로그 파일은 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0에 있습니다.

### <a name="step-6-remove-the-database-files"></a>6단계: 데이터베이스 파일 제거 
이 문서의 뒷부분에서는 복구 프로세스를 테스트하는 방법을 배웁니다. 복구 프로세스를 테스트하려면 먼저 데이터베이스 파일을 제거해야 합니다.

1.  테이블스페이스 및 백업 파일을 제거합니다.

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (선택 사항)Oracle 인스턴스를 종료합니다.

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Recovery Services 자격 증명 모음에서 삭제된 파일 복원
삭제된 파일을 복원하려면 다음 단계를 완료합니다.

1. Azure Portal에서 *myVault* Recovery Services 자격 증명 모음 항목을 검색합니다. **개요** 블레이드의 **Backup 항목**에서 항목의 수를 선택합니다.

    ![Recovery Services 자격 증명 모음 myVault 백업 항목](./media/oracle-backup-recovery/recovery_service_12.png)

2. **백업 항목 수**에서 항목 수를 선택합니다.

    ![Recovery Services 자격 증명 모음 Azure Virtual Machine 백업 항목 수](./media/oracle-backup-recovery/recovery_service_13.png)

3. **myvm1** 블레이드에서 **파일 복구(미리 보기)** 를 클릭합니다.

    ![Recovery Services 자격 증명 모음 파일 복구 페이지의 스크린샷](./media/oracle-backup-recovery/recovery_service_14.png)

4. **파일 복구(미리 보기)** 창에서 **스크립트 다운로드**를 클릭합니다. 그런 다음 다운로드(.sh) 파일을 클라이언트 컴퓨터의 폴더에 저장합니다.

    ![다운로드한 스크립트 파일 저장 옵션](./media/oracle-backup-recovery/recovery_service_15.png)

5. .sh 파일을 VM에 복사합니다.

    다음 예제에서는 파일을 VM으로 이동하는 안전한 복사(scp) 명령을 사용하는 방법을 보여 줍니다. 콘텐츠를 클립보드로 복사한 다음 VM에서 설정한 새 파일로 붙여넣을 수 있습니다.

    > [!IMPORTANT]
    > 다음 예제에서는 IP 주소와 폴더 값을 업데이트했는지 확인합니다. 값은 파일이 저장된 폴더를 매핑해야 합니다.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. 루트에서 소유하고 있으므로 파일을 변경합니다.

    다음 예제에서는 루트에서 소유하고 있으므로 파일을 변경합니다. 그런 다음, 사용 권한을 변경합니다.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    다음 예제에서는 이전 스크립트를 실행한 후 표시되는 내용이 나옵니다. 계속할 것인지 묻는 메시지가 표시되면 **Y**를 입력합니다.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. 탑재된 볼륨에 대한 액세스가 확인되었습니다.

    종료하려면 **q**를 입력한 다음, 탑재된 볼륨을 검색합니다. 명령 프롬프트에서 추가된 볼륨의 목록을 만들려면 **df -k**를 입력합니다.

    ![df -k 명령](./media/oracle-backup-recovery/recovery_service_16.png)

8. 다음 스크립트를 사용하여 누락된 파일을 폴더에 다시 복사합니다.

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. 다음 스크립트에서 RMAN을 사용하여 데이터베이스를 복구합니다.

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. 디스크를 분리합니다.

    Azure Portal의 **파일 복구(미리 보기)** 블레이드에서 **디스크 분리**를 클릭합니다.

    ![디스크 분리 명령](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>전체 VM 복원

Recovery Services 자격 증명 모음에서 삭제된 파일을 복원하는 대신 전체 VM을 복원할 수 있습니다.

### <a name="step-1-delete-myvm"></a>1단계: MyVM 삭제

*   Azure Portal에서 **myVM1**로 이동한 다음, **삭제**를 선택합니다.

    ![자격 증명 모음 삭제 명령](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2단계: VM 복구

1.  **Recovery Services 자격 증명 모음**으로 이동한 다음, **myVault**를 선택합니다.

    ![myVault 항목](./media/oracle-backup-recovery/recover_vm_02.png)

2.  **개요** 블레이드의 **Backup 항목**에서 항목의 수를 선택합니다.

    ![myVault 백업 항목](./media/oracle-backup-recovery/recover_vm_03.png)

3.  **Backup 항목(Azure Virtual Machine)** 블레이드에서 **myvm1**을 선택합니다.

    ![VM 복구 페이지](./media/oracle-backup-recovery/recover_vm_04.png)

4.  **myvm1** 블레이드의 줄임표(**...**) 단추를 클릭한 다음 **VM 복원**을 클릭합니다.

    ![VM 복원 명령](./media/oracle-backup-recovery/recover_vm_05.png)

5.  **복원 지점 선택** 블레이드에서 복원하려는 항목을 선택한 다음, **확인**을 클릭합니다.

    ![복원 지점 선택](./media/oracle-backup-recovery/recover_vm_06.png)

    애플리케이션 일치 백업을 사용하는 경우 파란색 세로 막대가 나타납니다.

6.  **복원 구성** 블레이드에서 가상 머신 이름을 선택하고 리소스 그룹을 선택한 다음, **확인**을 클릭합니다.

    ![복원 구성 값](./media/oracle-backup-recovery/recover_vm_07.png)

7.  VM을 복원하려면 **복원** 단추를 클릭합니다.

8.  복원 프로세스의 상태를 보려면 **작업**을 클릭한 다음, **백업 작업**을 클릭합니다.

    ![Backup 작업 상태 명령](./media/oracle-backup-recovery/recover_vm_08.png)

    다음 그림에서는 복원 프로세스의 상태를 보여 줍니다.

    ![복원 프로세스의 상태](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3단계: 공용 IP 주소를 설정 합니다.
VM을 복원한 후에 공용 IP 주소를 설정합니다.

1.  검색 상자에서 **공용 IP 주소**를 입력합니다.

    ![공용 IP 주소의 목록](./media/oracle-backup-recovery/create_ip_00.png)

2.  **공용 IP 주소** 블레이드에서 **추가**를 클릭합니다. **공용 IP 주소 만들기** 블레이드에서 **이름**에 공용 IP 이름을 선택합니다. **리소스 그룹**의 경우 **기존 항목 사용**을 선택합니다. 그런 다음에 **만들기**를 클릭합니다.

    ![IP 주소 만들기](./media/oracle-backup-recovery/create_ip_01.png)

3.  VM에 대한 네트워크 인터페이스에 공용 IP 주소를 연결하려면 검색하여 **myVMip**를 선택합니다. 그런 다음, **연결**을 클릭합니다.

    ![IP 주소 연결](./media/oracle-backup-recovery/create_ip_02.png)

4.  **리소스 종류**의 경우 **네트워크 인터페이스**를 선택합니다. myVM 인스턴스에서 사용되는 네트워크 인터페이스를 선택한 다음 **확인**을 클릭합니다.

    ![리소스 종류 및 NIC 값 선택](./media/oracle-backup-recovery/create_ip_03.png)

5.  검색하여 포털에서 이식된 myVM 인스턴스를 엽니다. VM과 연결된 IP 주소가 myVM **개요** 블레이드에 나타납니다.

    ![IP 주소 값](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4단계: VM에 연결

*   VM에 연결하려면 다음 스크립트를 사용합니다.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5단계: 데이터베이스에 액세스할 수 있는지 여부를 테스트 합니다.
*   액세스 가능 여부를 테스트하려면 다음 스크립트를 사용합니다.

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > 경우 데이터베이스 **시동** 명령은 오류를 생성, 데이터베이스를 복구 하려면 참조 [6 단계: RMAN을 사용 하 여 데이터베이스를 복구 하려면](#step-6-optional-use-rman-to-recover-the-database)합니다.

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>6단계: (선택 사항) RMAN을 사용 하 여 데이터베이스를 복구 하려면
*   데이터베이스를 복구하려면 다음 스크립트를 사용합니다.

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

이제 Azure Linux VM에서 Oracle 12c 데이터베이스의 백업 및 복구가 완료되었습니다.

## <a name="delete-the-vm"></a>VM 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)



