---
title: Azure VM에서 Oracle 데이터베이스 만들기 | Microsoft Docs
description: Azure 환경에서 Oracle Database 12c 데이터베이스를 신속하게 가동하고 실행합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/28/2020
ms.author: rogardle
ms.openlocfilehash: fb4403747a3681abd6023cdb9b5e62fd50af12c3
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179643"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Azure VM에서 Oracle 데이터베이스 만들기

이 가이드에서는 Oracle 12c 데이터베이스를 만들기 위해 Azure CLI를 사용하여 [Oracle 마켓플레이스 갤러리 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)에서 Azure 가상 머신을 배포하는 방법에 대해 자세히 설명합니다. 서버가 배포된 후 Oracle 데이터베이스를 구성하려면 SSH를 통해 연결합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

VM(가상 머신)을 만들려면 [az vm create](/cli/azure/vm) 명령을 사용합니다. 

다음 예제는 `myVM`라는 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`에 대한 값을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>VM에 연결

VM으로 SSH 세션을 만들려면 다음 명령을 사용합니다. 해당 IP 주소를 VM의 `publicIpAddress` 값으로 바꿉니다.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>데이터베이스 만들기

Oracle 소프트웨어는 이미 Marketplace 이미지에 설치되어 있습니다. 다음과 같이 샘플 데이터베이스를 만듭니다. 

1.  *oracle* 사용자로 전환한 다음, Oracle 수신기를 시작합니다.

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    다음과 유사하게 출력됩니다.

    ```output
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
2. Oracle 데이터 파일용 데이터 디렉터리 만들기

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  데이터베이스를 만듭니다.

    ```bash
    dbca -silent \
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
           -datafileDestination "/u01/app/oracle/oradata/"
           -ignorePreReqs
    ```

    데이터베이스를 만드는 데 몇 분이 걸립니다.

    다음과 유사한 출력이 표시됩니다.

    ```output
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

4. Oracle 변수를 설정합니다.

    연결하기 전에 두 환경 변수를 설정해야 합니다. *ORACLE_HOME* 및 *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    ORACLE_HOME 및 ORACLE_SID 변수를 .bashrc 파일에 추가할 수도 있습니다. 그러면 나중의 로그인을 위한 환경 변수가 저장됩니다. 선택한 편집기를 사용하여 다음 문이 `~/.bashrc`에 추가되었는지 확인합니다.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express 연결

데이터베이스를 탐색할 수 있는 GUI 관리 도구의 경우 Oracle EM Express를 설정합니다. Oracle EM Express에 연결하려면 먼저 Oracle에서 포트를 구성해야 합니다. 

1. sqlplus를 사용하여 데이터베이스에 연결합니다.

    ```bash
    sqlplus / as sysdba
    ```

2. 연결되면 EM Express에 대해 5502 포트를 설정합니다.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. PDB1 컨테이너를 아직 열지 않았으면 이 컨테이너를 열어 먼저 상태를 확인합니다.

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    다음과 유사하게 출력됩니다.

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. `PDB1`의 OPEN_MODE가 READ WRITE가 아닌 경우 다음 명령을 실행하여 PDB1을 엽니다.

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

`quit`를 입력하여 sqlplus 세션을 종료하고 `exit`를 입력하여 oracle 사용자를 로그아웃해야 합니다.

## <a name="automate-database-startup-and-shutdown"></a>데이터베이스 시작 및 종료 자동화

Oracle 데이터베이스는 기본적으로 VM을 다시 시작할 때 자동으로 시작되지 않습니다. Oracle 데이터베이스가 자동으로 시작되도록 설정하려면 먼저 루트로 로그인합니다. 그런 다음 일부 시스템 파일을 만들고 업데이트합니다.

1. 루트로 로그인합니다.

    ```bash
    sudo su -
    ```

2.  원하는 편집기를 사용하여 파일 `/etc/oratab`를 편집하고 기본값 `N`을 `Y`로 변경합니다.

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  `/etc/init.d/dbora`라는 파일을 만들고 다음 콘텐츠를 붙여 넣습니다.

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  *chmod*를 사용하여 다음과 같이 파일에 대한 권한을 변경합니다.

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  다음과 같이 시작 및 종료에 대한 기호 링크를 만듭니다.

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  변경 내용을 테스트하려면 VM을 다시 시작합니다.

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>연결을 위한 포트 열기

마지막 작업은 일부 외부 엔드포인트를 구성하는 것입니다. VM을 보호하는 Azure 네트워크 보안 그룹을 설정하려면 먼저 VM에서 SSH 세션을 종료합니다(이전 단계에서 다시 부팅 할 때 SSH에서 제외되어 있어야 함). 

1.  Oracle 데이터베이스에 원격으로 액세스하는 데 사용하는 엔드포인트를 열려면 다음과 같이 [az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Oracle EM Express에 원격으로 액세스하는 데 사용하는 엔드포인트를 열려면 다음과 같이 [az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. 필요한 경우 다음과 같이 [az network public-ip show](/cli/azure/network/public-ip)를 사용하여 VM의 공용 IP 주소를 다시 가져옵니다.

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  브라우저에서 EM Express를 연결합니다. 브라우저가 EM Express와 호환되는지 확인합니다(Flash 설치 필요). 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

**SYS** 계정을 사용하여 로그인하고 **as sysdba** 확인란을 선택합니다. 설치 중에 설정한 **OraPasswd1** 암호를 사용합니다. 

![Oracle OEM Express 로그인 페이지의 스크린샷](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 첫 번째 Oracle 데이터베이스 탐색이 끝나고 VM이 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[Azure의 다른 Oracle 솔루션](./oracle-overview.md)에 대해 알아봅니다. 

[Oracle Automated Storage Management 설치 및 구성](configure-oracle-asm.md) 자습서를 사용해 봅니다.
