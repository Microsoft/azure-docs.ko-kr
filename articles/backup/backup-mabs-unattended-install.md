---
title: Azure Backup Server V2의 자동 설치
description: PowerShell 스크립트를 사용하여 Azure Backup Server V2를 자동으로 설치할 수 있습니다. 이런 종류의 설치를 무인 설치라고도 합니다.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74172237"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Azure Backup Server의 무인 설치 실행

Azure Backup Server의 무인 설치를 실행하는 방법을 알아봅니다.

Azure Backup Server V1을 설치할 경우에는 다음 단계가 적용되지 않습니다.

## <a name="install-backup-server"></a>Backup Server 설치

1. Azure Backup Server V2 이상을 호스트하는 서버에서 텍스트 파일을 만듭니다. (메모장이나 다른 텍스트 편집기에서 파일을 만들 수 있습니다.) 파일을 MABSSetup.ini로 저장합니다.

2. MABSSetup.ini 파일에 다음 코드를 붙여넣습니다. 대괄호(\< \>) 내부의 텍스트를 사용자 환경의 값으로 바꿉니다. 다음 텍스트는 예제입니다.

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. 파일을 저장합니다. 그다음에 설치 서버의 관리자 권한 명령 프롬프트에서 이 명령을 입력합니다.

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

설치에 다음 플래그를 사용할 수 있습니다.</br>
**/f**: .ini 파일 경로</br>
**/l**: 로그 경로</br>
**/i**: 설치 경로</br>
**/x**: 제거 경로</br>

## <a name="next-steps"></a>다음 단계

Backup Server를 설치한 후 서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.

- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
- [Backup Server에 Modern Backup Storage 추가](backup-mabs-add-storage.md)
