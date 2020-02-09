---
title: 하이브리드 모드 사용에 대한 알려진 문제/마이그레이션 제한 사항
description: 하이브리드 모드에서 Azure Database Migration Service 사용에 대한 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/19/2019
ms.openlocfilehash: 60d1fc46ada70dc67c161f048a0206e7081ba591
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483116"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>하이브리드 모드 사용에 대한 알려진 문제/마이그레이션 제한 사항

하이브리드 모드에서 Azure Database Migration Service를 사용 하는 것과 관련 된 알려진 문제 및 제한 사항에 대해서는 다음 섹션에서 설명 합니다.

## <a name="installer-fails-to-authenticate"></a>설치 관리자가 인증에 실패 함

AdApp에 인증서를 업로드 한 후 Azure를 사용 하 여 인증할 수 있을 때까지 몇 분까지 지연 됩니다. 설치 관리자는 몇 시간 후에 다시 시도 하려고 하지만, 다시 시도 하는 것 보다는 전파 지연 시간이 길어질 수 있으며 **FailedToGetAccessTokenException** 메시지가 표시 됩니다. 인증서가 올바른 AdApp에 업로드 되 고 올바른 AppId가 dmsSettings. json에 제공 된 경우 설치 명령을 다시 실행 해 봅니다.

## <a name="service-offline-after-successful-installation"></a>설치가 완료 된 후 "오프 라인" 서비스

설치 프로세스가 성공적으로 완료 된 후 서비스가 오프 라인으로 표시 되 면 다음 단계를 사용 하십시오.

1. Azure Portal의 Azure Database Migration Service 인스턴스에서 **하이브리드** 설정 탭으로 이동한 다음 등록 된 작업자의 그리드를 확인 하 여 worker가 등록 되었는지 확인 합니다.

    이 작업자의 상태는 **온라인**상태 여야 하지만 문제가 있는 경우 **오프 라인** 으로 표시 될 수 있습니다.

2. 작업자 컴퓨터에서 다음 PowerShell 명령을 실행 하 여 서비스의 상태를 확인 합니다.

    ```
    Get-Service Scenario*
    ```

    이 명령은 작업자를 실행 하는 Windows 서비스의 상태를 제공 합니다. 단일 결과만 있으면 됩니다. 작업 자가 중지 된 경우 다음 PowerShell 명령을 사용 하 여 다시 시작할 수 있습니다.

    ```
    Start-Service Scenario*
    ```

    Windows 서비스 UI에서 서비스를 확인할 수도 있습니다.

3. Windows 서비스가 실행 중과 중지 된 사이에서 순환 되는 경우 작업자는 시작 하는 데 문제가 발생 했습니다. 하이브리드 작업자 로그 Azure Database Migration Service 확인 하 여 문제를 확인 합니다.

    - 설치 프로세스 로그는 설치 관리자 실행 파일이 실행 된 폴더 내의 "logs" 폴더에 저장 됩니다.
    - Azure Database Migration Service hybrid worker **로그는 작업자** 가 설치 되어 있는 폴더에 저장 됩니다. 하이브리드 작업자 로그 파일의 기본 위치는 **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**입니다.

## <a name="using-your-own-signed-certificate"></a>자체 서명 된 인증서 사용

작업 GenerateCert 생성 된 인증서는 자체 서명 된 인증서로, 내부 보안 정책에 따라 허용 되지 않을 수 있습니다. 이 인증서를 사용 하는 대신 사용자 고유의 인증서를 제공 하 고, 사용자 고유의 인증서를 제공 하 고, 사용자의 지문을 제공 합니다. 이 인증서는 AdApp에 업로드 하 고 Azure Database Migration Service hybrid worker를 설치 하는 컴퓨터에 설치 해야 합니다. 그런 다음 개인 키를 사용 하 여이 인증서를 로컬 컴퓨터 인증서 저장소에 설치 합니다.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>낮은 권한 계정으로 작업자 서비스 실행

기본적으로 Azure Database Migration Service hybrid worker 서비스는 로컬 시스템 계정으로 실행 됩니다. 사용 하는 계정에 네트워크 사용 권한이 있으면이 서비스에 사용 되는 계정을 변경할 수 있습니다. ' 실행 계정 ' 서비스를 변경 하려면 다음 프로세스를 사용 합니다.

1. Windows 서비스를 사용 하거나 PowerShell에서 Stop-Service 명령을 사용 하 여 서비스를 중지 합니다.

2. 다른 로그온 계정을 사용 하도록 서비스를 업데이트 합니다.

3. 로컬 컴퓨터 인증서에 대한 certmgr.exe에서 **Dms 하이브리드 앱 키** 및 **Dms 시나리오 엔진 키 쌍** 인증서에 대한 새 계정에 개인 키 권한을 부여 합니다.

    a. Certmgr.exe를 열어 다음 키를 확인 합니다.

    - DMS 하이브리드 앱 키
    - DMS Hybrid Worker 설정 키
    - DMS 시나리오 엔진 키 쌍

    b. **DMS 하이브리드 앱 키** 항목을 마우스 오른쪽 단추로 클릭 하 고 **모든 작업**을 가리킨 다음 **개인 키 관리**를 선택 합니다.

    다. **보안** 탭에서 **추가**를 선택한 다음 계정 이름을 입력 합니다.

    d. 동일한 단계를 사용 하 여 새 계정에 대한 개인 키 권한을 **DMS 시나리오 엔진 키 쌍** 인증서에 부여 합니다.

## <a name="unregistering-the-worker-manually"></a>수동으로 작업자 등록 취소

작업자 컴퓨터에 더 이상 액세스할 수 없는 경우 다음 단계를 수행 하 여 worker 등록을 취소 하 고 Azure Database Migration Service 인스턴스를 다시 사용할 수 있습니다.

1. Azure Portal에서 Azure Database Migration Service 인스턴스를 가져온 다음 **하이브리드** 설정 페이지로 이동 합니다.

   상태가 **오프 라인**으로 표시 되 고 목록에 작업자 항목이 표시 됩니다.

2. 작업자 항목 목록의 맨 오른쪽에 있는 줄임표를 선택 하 고 **등록 취소**를 선택 합니다.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>특정 마이그레이션 시나리오에 대한 문제 해결

다음 섹션에서는 Azure Database Migration Service 하이브리드 모드를 사용 하 여 온라인 마이그레이션을 수행 하는 것과 관련 된 시나리오 관련 문제에 대해 설명 합니다.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스로 온라인 마이그레이션

**높은 CPU 사용량**

**문제**: 관리 되는 인스턴스로의 온라인 마이그레이션에 대해 너무 많은 백업이 있거나 백업이 너무 큰 경우 hybrid worker를 실행 SQL Database 하는 컴퓨터에 높은 CPU 사용량이 발생 합니다.

**완화**:이 문제를 완화 하려면 압축 된 백업을 사용 하 고, 여러 공유를 사용 하도록 마이그레이션을 분할 하거나, 하이브리드 작업자를 실행 하는 컴퓨터를 강화 합니다.
