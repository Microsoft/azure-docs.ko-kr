---
title: Azure Linux 가상 머신에서 Oracle Data Guard 구현 | Microsoft Docs
description: Azure 환경에서 Oracle Data Guard를 신속하게 가동하고 실행합니다.
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
ms.openlocfilehash: c98e59cd0e547381d6b173b3a4b91c3a3e27b3a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771722"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Azure Linux 가상 머신에서 Oracle Data Guard 구현 

Azure CLI는 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 문서에서는 Azure CLI를 사용하여 Azure Marketplace 이미지에서 Oracle Database 12c 데이터베이스를 배포하는 방법을 설명합니다. 그런 다음 이 문서는 Azure VM(가상 머신)에서 Data Guard를 설치하고 구성하는 방법을 단계별로 보여 줍니다.

시작하기 전에 Azure CLI가 설치되어 있는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-the-environment"></a>환경 준비
### <a name="assumptions"></a>가정

Oracle Data Guard를 설치하려면 동일한 가용성 집합에서 두 개의 Azure VM을 만들어야 합니다.

- 기본 VM(myVM1)에 실행 중인 Oracle 인스턴스가 있습니다.
- 대기 VM(myVM2)에는 Oracle 소프트웨어만 설치되어 있습니다.

VM을 만드는 데 사용하는 Marketplace 이미지는 Oracle:Oracle-Database-Ee:12.1.0.2:latest입니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/reference-index) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>가용성 집합 만들기

가용성 집합 만들기는 선택 사항이지만 만드는 것이 좋습니다. 자세한 내용은 [Azure 가용성 집합 지침](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)을 참조하세요.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 `myVM1` 및 `myVM2`라고 하는 VM 두 개를 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

myVM1(기본) 만들기:
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress` 값을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

myVM2(대기)를 만듭니다.
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

myVM2를 만든 후 `publicIpAddress` 값을 기록해 둡니다.

### <a name="open-the-tcp-port-for-connectivity"></a>연결에 대한 TCP 포트 열기

이 단계에서는 Oracle 데이터베이스에 대한 원격 액세스를 허용하는 외부 엔드포인트를 구성합니다.

myVM1에 대한 포트 열기:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

결과는 다음 응답과 유사하게 나타납니다.

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

myVM2에 대한 포트 열기:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>가상 컴퓨터에 연결

다음 명령을 사용하여 가상 머신과의 SSH 세션을 만듭니다. 해당 IP 주소를 가상 머신의 `publicIpAddress` 값으로 바꿉니다.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>myVM1(기본)에서 데이터베이스 만들기

Oracle 소프트웨어는 Marketplace 이미지에 이미 설치되어 있으므로 다음 단계에서 데이터베이스를 설치합니다. 

Oracle superuser로 전환합니다.

```bash
$ sudo su - oracle
```

데이터베이스를 만듭니다.

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
출력은 다음 응답과 유사하게 나타납니다.

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

ORACLE_SID 및 ORACLE_HOME 변수를 설정합니다.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

필요에 따라 /home/oracle/.bashrc 파일에 ORACLE_HOME 및 ORACLE_SID를 추가하여 후속 로그인을 위해 이러한 설정을 저장할 수 있습니다.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Data Guard 구성

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>myVM1(기본)에서 보관 로그 모드 사용

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
```
강제 로깅을 사용하도록 설정하고 하나 이상의 로그 파일이 있는지 확인합니다.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

대기 다시 실행 로그를 만듭니다.

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

복구가 훨씬 용이해지는 플래시백을 설정하고 STANDBY\_FILE\_MANAGEMENT를 자동으로 설정합니다. 그런 다음 SQL*Plus를 끝냅니다.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>myVM1(기본)에서 서비스 설정

$ORACLE_HOME\network\admin 폴더에서 tnsnames.ora 파일을 편집하거나 만듭니다.

다음 항목을 추가합니다.

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

$ORACLE_HOME\network\admin 폴더에서 listener.ora 파일을 편집하거나 만듭니다.

다음 항목을 추가합니다.

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Data Guard Broker를 사용하도록 설정합니다.
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```
수신기를 시작합니다.

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>myVM2(대기)에서 서비스 설정

myVM2에 대해 SSH를 설정합니다.

```bash 
$ ssh azureuser@<publicIpAddress>
```

Oracle로 로그인합니다.

```bash
$ sudo su - oracle
```

$ORACLE_HOME\network\admin 폴더에서 tnsnames.ora 파일을 편집하거나 만듭니다.

다음 항목을 추가합니다.

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

$ORACLE_HOME\network\admin 폴더에서 listener.ora 파일을 편집하거나 만듭니다.

다음 항목을 추가합니다.

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

수신기를 시작합니다.

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>myVM2(대기)로 데이터베이스 복원

다음 콘텐츠로 매개 변수 파일 /tmp/initcdb1_stby.ora를 만듭니다.
```bash
*.db_name='cdb1'
```

폴더를 만듭니다.

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

암호 파일을 만듭니다.

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
myVM2에서 데이터베이스를 시작합니다.

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

RMAN 도구를 사용하여 데이터베이스를 복원합니다.

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

RMAN에서 다음 명령을 실행합니다.
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

명령이 완료되면 다음과 비슷한 메시지가 표시됩니다. RMAN을 끝냅니다.
```bash
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17

RMAN> EXIT;
```

필요에 따라 /home/oracle/.bashrc 파일에 ORACLE_HOME 및 ORACLE_SID를 추가하여 후속 로그인을 위해 이러한 설정을 저장할 수 있습니다.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Data Guard Broker를 사용하도록 설정합니다.
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>myVM1(기본)에서 Data Guard Broker 구성

Data Guard Manager를 시작하고 SYS 및 암호를 사용하여 로그인합니다. OS 인증을 사용하지 마세요. 다음을 수행합니다.

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

구성을 검토합니다.
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Oracle Data Guard 설정이 완료되었습니다. 다음 섹션에서는 연결 및 전환을 테스트하는 방법을 보여 줍니다.

### <a name="connect-the-database-from-the-client-machine"></a>클라이언트 컴퓨터에서 데이터베이스 연결

클라이언트 컴퓨터에서 tnsnames.ora 파일을 업데이트하거나 만듭니다. 이 파일은 일반적으로 $ORACLE_HOME\network\admin에 있습니다.

해당 IP 주소를 myVM1 및 myVM2의 `publicIpAddress` 값으로 바꿉니다.

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

SQL*Plus를 시작합니다.

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-the-data-guard-configuration"></a>Data Guard 구성 테스트

### <a name="switch-over-the-database-on-myvm1-primary"></a>myVM1(기본)에서 데이터베이스 전환

기본에서 대기(cdb1에서 cdb1_stby)로 전환하려면 다음을 실행합니다.

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

이제 대기 데이터베이스에 연결할 수 있습니다.

SQL*Plus를 시작합니다.

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>myVM2(대기)에서 데이터베이스 전환

전환하려면 myVM2에서 다음을 실행합니다.
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

다시 한번, 주 데이터베이스에 연결할 수 있어야 합니다.

SQL*Plus를 시작합니다.

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Oracle Linux에서 Data Guard의 설치 및 구성을 완료했습니다.


## <a name="delete-the-virtual-machine"></a>가상 머신 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 가상 머신 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)
