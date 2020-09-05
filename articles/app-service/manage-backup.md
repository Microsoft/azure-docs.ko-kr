---
title: 앱 백업
description: Azure App Service에서 앱의 백업을 만드는 방법에 대해 알아봅니다. 수동 또는 예약된 백업을 실행합니다. 연결된 데이터베이스를 포함하여 백업을 사용자 지정합니다.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b160f0aa50c30f7856bc09b88b3eb305f32d6f98
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961927"
---
# <a name="back-up-your-app-in-azure"></a>Azure에서 앱 백업
[Azure App Service](overview.md)의 백업 및 복원 기능을 사용하여 수동으로 또는 일정에 따라 앱 백업을 쉽게 만들 수 있습니다. 백업이 무기한 보존되도록 구성할 수 있습니다. 기존 앱을 덮어쓰거나 다른 앱으로 복원하여 앱을 이전 상태의 스냅샷으로 복원할 수 있습니다.

앱을 백업에서 복원하는 방법에 대한 자세한 내용은 [Azure에서 앱 복원](web-sites-restore.md)을 참조하세요.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>백업 대상
App Service는 앱에서 사용하도록 구성한 Azure Storage 계정과 컨테이너에 다음 정보를 백업할 수 있습니다. 

* 앱 구성
* 파일 콘텐츠
* 앱에 연결된 데이터베이스

백업 기능과 함께 지원되는 데이터베이스 솔루션은 다음과 같습니다. 

- [SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL 인앱](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> 각 백업은 증분 업데이트가 아니라 앱의 완전한 오프라인 복사본입니다.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>요구 사항 및 제한 사항
* 백업 및 복원 기능을 사용하려면 App Service 계획이 **표준**, **프리미엄** 또는 **격리** 계층에 있어야 합니다. 더 높은 계층을 사용하도록 App Service 계획을 확장하는 방법에 대한 자세한 내용은 [Azure에서 앱 확장](manage-scale-up.md)을 참조하세요. **프리미엄** 및 **격리** 계층에서는 **표준** 계층보다 자주 매일 백업이 가능합니다.
* 백업하려는 앱과 동일한 구독에 Azure Storage 계정 및 컨테이너가 필요합니다. Azure Storage 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.
* 최대 10GB의 앱 및 데이터베이스 콘텐츠를 백업할 수 있습니다. 백업 크기가 이 제한을 초과하면 오류가 발생합니다.
* TLS 지원 Azure Database for MySQL의 백업은 지원되지 않습니다. 백업이 구성된 경우, 실패한 백업이 표시됩니다.
* TLS 지원 Azure Database for PostgreSQL의 백업은 지원되지 않습니다. 백업이 구성된 경우, 실패한 백업이 표시됩니다.
* 인앱 MySQL 데이터베이스는 구성 없이도 자동으로 백업됩니다. 연결 문자열 추가 등의 인앱 MySQL 데이터베이스에 대한 설정을 수동으로 수행하는 경우 백업이 제대로 작동하지 않을 수 있습니다.
* 방화벽 지원 스토리지 계정을 백업 대상으로 사용하는 기능은 지원되지 않습니다. 백업이 구성된 경우, 실패한 백업이 표시됩니다.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>수동 백업 만들기
1. [Azure Portal](https://portal.azure.com)에서 앱의 페이지로 이동하여 **백업**을 선택합니다. **백업** 페이지가 표시됩니다.

    ![Backup 페이지](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > 다음 메시지가 표시되면 백업을 계속 진행하기 전에 해당 App Service 계획을 클릭하여 업그레이드해야 합니다.
    > 자세한 내용은 [Azure에서 앱 확장](manage-scale-up.md)을 참조하세요.
    > ![스토리지 계정 선택](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. **백업** 페이지에서 **백업이 구성되어 있지 않습니다. 앱에 대한 백업을 구성하려면 여기를 클릭하세요.**

    ![구성 클릭](./media/manage-backup/configure-start.png)

3. **백업 구성** 페이지에서 **스토리지 구성되지 않음**을 클릭하여 스토리지 계정을 구성합니다.

    ![스토리지 계정 선택](./media/manage-backup/configure-storage.png)

4. **Storage 계정** 및 **컨테이너**를 선택하여 백업 대상을 선택합니다. 스토리지 계정은 백업할 앱이 있는 동일한 구독에 속해야 합니다. 필요한 경우 각 페이지에서 새 스토리지 계정이 나 새 컨테이너를 만들 수 있습니다. 완료되면 **선택**을 클릭합니다.

5. 남아 있는 **백업 구성** 페이지에서 **백업 데이터베이스**를 구성한 다음 백업에 포함할 데이터베이스 (SQL Database 또는 MySQL)를 선택 하 고 **확인**을 클릭 합니다.

    ![스토리지 계정 선택](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > 이 목록에 표시될 데이터베이스의 경우 연결 문자열은 앱의 **애플리케이션 설정** 페이지에서 **연결 문자열** 섹션에 있어야 합니다. 
    >
    > 인앱 MySQL 데이터베이스는 구성 없이도 자동으로 백업됩니다. 연결 문자열 추가 등의 인앱 MySQL 데이터베이스에 대한 설정을 수동으로 수행하는 경우 백업이 제대로 작동하지 않을 수 있습니다.
    > 
    > 

6. **백업 구성** 페이지에서 **저장**을 클릭합니다.
7. **백업** 페이지에서 **백업**을 클릭합니다.

    ![BackUpNow 단추](./media/manage-backup/manual-backup.png)

    백업 프로세스를 수행하는 동안 진행 메시지가 표시됩니다.

스토리지 계정과 컨테이너가 구성되면 언제든지 수동 백업을 시작할 수 있습니다.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>자동화된 백업 구성
1. **백업 구성** 페이지에서 **예약된 백업**을 **켜기**로 설정합니다. 

    ![자동 백업 사용](./media/manage-backup/scheduled-backup.png)

2. 원하는 대로 백업 일정을 구성하고 **확인**을 선택합니다.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>부분 Backup 구성
앱의 모든 것을 백업하고 싶지 않을 때도 있습니다. 다음은 몇 가지 예입니다.

* 오래된 블로그 게시물이나 이미지처럼 변하지 않는 정적 콘텐츠가 포함된 앱의 [주별 백업을 설정](#configure-automated-backups) 합니다.
* 앱의 콘텐츠가 10GB 이상이며, 이는 한 번에 백업할 수 있는 최대 용량입니다.
* 로그 파일은 백업하지 않아도 됩니다.

부분 백업을 사용하면 백업할 파일을 정확히 선택할 수 있습니다.

> [!NOTE]
> 백업에서 개별 데이터베이스는 4GB까지 가능하지만 백업의 총 최대 크기는 10GB입니다.

### <a name="exclude-files-from-your-backup"></a>백업에서 파일 제외
한 번 백업되었고 변경하지 않을 로그 파일과 정적 이미지가 포함된 앱이 있다고 가정해 보겠습니다. 이러한 경우 해당 폴더와 파일을 이후의 백업에서 저장하지 않도록 제외할 수 있습니다. 백업에서 파일과 폴더를 제외하려면 앱의 `D:\home\site\wwwroot` 폴더에 `_backup.filter` 파일을 만듭니다. 이 파일에서 제외할 파일과 폴더의 목록을 지정합니다. 

`https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동하여 파일에 액세스할 수 있습니다. 메시지가 표시되면 Azure 계정에 로그인합니다.

백업에서 제외하려는 폴더를 식별합니다. 예를 들어 강조 표시된 폴더와 파일을 필터링하려고 합니다.

![Images 폴더](./media/manage-backup/kudu-images.png)

`_backup.filter`라는 파일을 만들고 앞에 나온 목록을 파일에 저장하지만 `D:\home`을 제거합니다. 줄당 하나의 디렉터리 또는 파일을 나열하세요. 파일의 내용은 다음과 같아야 합니다.

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

[ftp](deploy-ftp.md) 또는 다른 방법을 사용하여 해당 사이트의 `D:\home\site\wwwroot\` 디렉터리에 `_backup.filter` 파일을 업로드합니다. 필요한 경우 Kudu `DebugConsole`을 사용하여 파일을 직접 만들고 여기에 내용을 삽입할 수도 있습니다.

이제 평소와 같이 [수동](#create-a-manual-backup) 또는 [자동](#configure-automated-backups)으로 백업을 실행합니다. 이제 `_backup.filter`에 지정된 파일과 폴더가 이후에 예약되거나 수동으로 시작되는 백업에서 제외됩니다. 

> [!NOTE]
> [정기 백업을 복원](web-sites-restore.md)할 때와 동일한 방법으로 사이트의 부분 백업을 복원합니다. 복원 프로세스는 올바르게 수행됩니다.
> 
> 전체 백업이 복원되면 해당 사이트의 모든 콘텐츠가 백업에 있는 항목들로 대체됩니다. 파일이 사이트에 있지만 백업에 없는 경우 해당 파일은 삭제됩니다. 하지만 부분 백업이 복원되면 블랙 리스트 디렉터리에 위치한 모든 콘텐츠 또는 블랙 리스트에 포함된 모든 파일이 그대로 유지됩니다.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>백업 저장 방법
앱에 대해 하나 이상의 백업을 만들면 스토리지 계정의 **컨테이너** 페이지와 앱에 해당 백업이 표시됩니다. 스토리지 계정에서 각 백업은 백업 데이터가 포함된 `.zip` 파일과 해당 `.zip` 파일 콘텐츠의 매니페스트가 포함된 `.xml` 파일로 구성되어 있습니다. 실제로 앱 복원을 수행하지 않고 백업에 액세스하고자 한다면 이들 파일의 압축을 풀고 찾아볼 수 있습니다.

앱에 대한 데이터베이스 백업이 .zip 파일의 루트에 저장됩니다. SQL Database의 경우 BACPAC 파일 (파일 확장명 없음) 이며 가져올 수 있습니다. BACPAC 내보내기를 기반으로 Azure SQL Database에서 데이터베이스를 만들려면 [bacpac 파일을 가져와 Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/database-import.md)를 참조 하세요.

> [!WARNING]
> **websitebackups** 컨테이너에 있는 파일을 변경하면 백업이 잘못되어 복원할 수 없게 됩니다.
> 
> 

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)을 사용하여 스크립트를 통해 백업 관리를 자동화할 수 있습니다.

샘플을 보려면 다음을 참조하세요.

- [Azure CLI 샘플](samples-cli.md)
- [Azure PowerShell 샘플](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>다음 단계
앱을 백업에서 복원하는 방법에 대한 자세한 내용은 [Azure에서 앱 복원](web-sites-restore.md)을 참조하세요.