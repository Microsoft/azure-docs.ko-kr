---
title: Azure(큰 인스턴스) 형식 II SKU에서 SAP HANA의 운영 체제 백업 및 복원| Microsoft Docs
description: Azure(큰 인스턴스) 형식 II SKU에서 SAP HANA의 운영 체제 백업 및 복원 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708603"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>형식 II SKU에 대한 OS 백업 및 복원

이 문서에서는 HANA 큰 인스턴스의 **형식 II SKU**에 대한 운영 체제 파일 수준 백업 및 복원을 수행하는 단계를 설명합니다. 

>[!NOTE]
>OS 백업 스크립트는 서버에 미리 설치되어 있는 ReaR 소프트웨어를 사용합니다.  

기본적으로 Microsoft 서비스 관리 팀에서 프로비전을 완료한 후에 서버는 운영 체제의 파일 시스템 수준 백업을 수행하는 두 번의 백업 일정으로 구성됩니다. 다음 명령을 사용하여 백업 작업 일정을 확인할 수 있습니다.
```
#crontab –l
```
다음 명령을 사용하여 언제든지 백업 일정을 변경할 수 있습니다.
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>수동 백업을 수행하는 방법

**cron 작업**을 사용하여 운영 체제 파일 시스템 백업이 이미 예약되었습니다. 그러나 운영 체제 파일 수준 백업을 수동으로도 수행할 수 있습니다. 수동 백업을 수행하려면 다음 명령을 실행합니다.

```
#rear -v mkbackup
```
다음 화면에서는 샘플 수동 백업을 보여줍니다.

![방법](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>백업을 복원하는 방법

백업에서 전체 백업 또는 개별 파일을 복원할 수 있습니다. 복원하려면 다음 명령을 실행합니다.

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
복원한 후에 파일은 현재 작업 디렉터리에 복구됩니다.

다음 명령에서는 파일(*/etc/fstabfrom*) 및 백업 파일(*backup.tar.gz*)의 복원을 보여줍니다.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>백업에서 복원된 후에 원하는 위치에 파일을 복사해야 합니다.

다음 스크린샷은 전체 백업 복원 합니다.

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>ReaR 도구를 설치하고 구성을 변경하는 방법 

ReaR(Relax-and-Recover) 패키지는 HANA 큰 인스턴스의 **형식 II SKU**에 **미리 설치되어** 있고 추가 작업이 필요하지 않습니다. 운영 체제 백업을 위해 ReaR를 사용하여 직접 시작할 수 있습니다.
그러나 고유한 패키지를 설치해야 하는 경우에 ReaR 도구를 설치하고 구성하는 나열된 단계를 따르면 됩니다.

**ReaR** 백업 패키지를 설치하려면 다음 명령을 사용합니다.

**SLES** 운영 체제의 경우 다음 명령을 사용합니다.
```
#zypper install <rear rpm package>
```
**RHEL** 운영 체제의 경우 다음 명령을 사용합니다. 
```
#yum install rear -y
```
ReaR 도구를 구성하려면 *file /etc/rear/local.conf*에서 **OUTPUT_URL** 및 **BACKUP_URL** 매개 변수를 업데이트해야 합니다.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

다음 스크린샷은 전체 백업 복원 합니다. ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
