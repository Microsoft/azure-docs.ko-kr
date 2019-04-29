---
title: 앱 복원 - Azure App Service
description: 백업에서 앱을 복원하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832514"
---
# <a name="restore-an-app-in-azure"></a>Azure에서 앱 복원
이 문서에서는 이전에 백업한 [Azure App Service](../app-service/overview.md)에서 앱을 복원하는 방법을 보여 줍니다([Azure에서 앱 백업](manage-backup.md) 참조). 요청 시 연결된 데이터베이스와 함께 앱을 이전 상태로 복원하거나, 원래 앱의 백업 중 하나를 기반으로 하여 새 앱을 만들 수 있습니다. Azure App Service는 백업 및 복원을 위해 다음과 같은 데이터베이스를 지원합니다.
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL 인앱](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

백업에서 복원하는 방식은 **표준** 및 **프리미엄** 계층에서 실행되는 앱에 사용할 수 있습니다. 앱 확장에 대한 자세한 내용은 [Azure에서 앱 확장](web-sites-scale.md)을 참조하세요. **프리미엄** 계층을 사용하면 **표준** 계층보다 더 많은 매일 백업을 수행할 수 있습니다.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>기존 백업에서 앱 복원
1. Azure Portal에서 앱의 **설정** 페이지에서 **백업**을 클릭하여 **백업** 페이지를 표시합니다. 그런 다음 **복원**을 클릭합니다.
   
    ![지금 복원 선택][ChooseRestoreNow]
2. **복원** 페이지에서 먼저 백업 원본을 선택합니다.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    **앱 백업** 옵션에는 현재 앱의 모든 기존 백업을 표시하며 백업 중 하나를 쉽게 선택할 수 있습니다.
    **스토리지** 옵션을 사용하면 구독의 기존 Azure Storage 계정 및 컨테이너에서 백업 ZIP 파일을 선택할 수 있습니다.
    다른 앱의 백업을 복원하려는 경우 **저장소** 옵션을 사용합니다.
3. 그런 다음 **복원 대상**에서 앱 복원 대상을 지정합니다.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > **덮어쓰기**를 선택하면 현재 앱의 기존 데이터를 모두 지우고 덮어씁니다. **확인**을 클릭하기 전에 수행하려는 작업이 정확히 맞는지 확인하세요.
   > 
   > 
   
   > [!WARNING]
   > 데이터를 복구하는 동안 App Service가 데이터베이스에 데이터를 쓰는 경우 기본 키 및 데이터 손실 위반 같은 증상이 발생할 수 있습니다. 먼저 App Service를 중지한 후 데이터베이스를 복원하는 것이 좋습니다.
   > 
   > 
   
    **기존 앱** 을 선택하여 같은 리소스 그룹에 있는 다른 앱으로 앱 백업을 복원할 수 있습니다. 이 옵션을 사용하려면 리소스 그룹에 미러링 데이터베이스가 앱 백업에 정의된 것으로 구성되어 있는 다른 앱이 이미 만들어져 있어야 합니다. **새** 앱을 만들어 콘텐츠를 복원할 수도 있습니다.

4. **확인**을 클릭합니다.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>저장소 계정에서 백업 다운로드 또는 삭제
1. Azure Portal의 기본 **찾아보기** 페이지에서 **Storage 계정**을 선택합니다. 기존 저장소 계정 목록이 표시됩니다.
2. 다운로드 또는 삭제하려는 백업을 포함하는 저장소 계정을 선택합니다. 저장소 계정에 대한 페이지가 표시됩니다.
3. 저장소 계정 페이지에서 원하는 컨테이너를 선택합니다.
   
    ![컨테이너 보기][ViewContainers]
4. 다운로드 또는 삭제하려는 백업 파일을 선택합니다.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 수행하려는 작업에 따라 **다운로드** 또는 **삭제**를 클릭합니다.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>복원 작업 모니터링
앱 복원 작업의 성공 또는 실패에 대한 자세한 내용을 보려면 Azure Portal의 **활동 로그** 페이지로 이동합니다.  
 

아래로 스크롤하여 원하는 복원 작업을 찾은 후 클릭하여 선택합니다.

세부 정보 페이지에서 복원 작업과 관련하여 사용 가능한 정보를 표시합니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/overview)을 사용하여 스크립트를 통해 백업 관리를 자동화할 수 있습니다.

샘플을 보려면 다음을 참조하세요.

- [Azure CLI 샘플](samples-cli.md)
- [Azure PowerShell 샘플](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
