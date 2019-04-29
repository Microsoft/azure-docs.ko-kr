---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0b5d9deacdd4266da30f17c95b6e575a652d2f76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633390"
---
이 절차에서는 다음을 수행합니다.

1. [유지 관리자 실행 파일을 실행할 준비를 합니다](#to-prepare-to-run-the-maintainer) .
2. [분리된 BLOB의 즉시 삭제를 위해 콘텐츠 데이터베이스 및 휴지통을 준비합니다](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Maintainer.exe를 실행합니다](#to-run-the-maintainer).
4. [콘텐츠 데이터베이스 및 휴지통 설정을 되돌립니다](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>유지 관리자 실행을 준비하려면
1. 웹 프런트 엔드 서버에서 SharePoint 2013 관리 셸을 관리자 권한으로 엽니다.
2. *부팅 드라이브*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\. 폴더로 이동합니다.
3. **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config**의 이름을 **web.config**로 바꿉니다.
4. `aspnet_regiis -pdf connectionStrings` 를 사용하여 web.config 파일의 암호를 해독합니다.
5. 암호 해독된 web.config 파일의 `connectionStrings` 노드에 SQL Server 인스턴스 및 콘텐츠 데이터베이스 이름에 대한 연결 문자열을 추가합니다. 다음 예제를 참조하세요.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. `aspnet_regiis –pef connectionStrings` 를 사용하여 web.config 파일을 다시 암호화합니다. 
7. web.config의 이름을 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config로 바꿉니다. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>분리된 BLOB을 즉시 삭제하기 위한 콘텐츠 데이터베이스 및 휴지통을 준비하려면
1. SQL Server의 SQL Management Studio에서 대상 콘텐츠 데이터베이스에 대한 다음 업데이트 쿼리를 실행합니다. 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. 웹 프런트 엔드 서버의 **중앙 관리**에서 원하는 콘텐츠 데이터베이스에 대한 **웹 애플리케이션의 일반 설정**을 편집하여 휴지통을 일시적으로 사용하지 않도록 설정합니다. 또한 이 작업은 관련 사이트 모음에 대한 휴지통을 비웁니다. 이 작업을 수행하려면 **중앙 관리** -> **애플리케이션 관리** -> **웹 애플리케이션(웹 애플리케이션 관리)** -> **SharePoint - 80** -> **일반 애플리케이션 설정**을 클릭합니다. **휴지통 상태**를 **OFF**로 설정합니다.
   
    ![웹 애플리케이션의 일반 설정](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>유지 관리자를 실행하려면
* 다음과 같이 웹 프런트 엔드 서버의 SharePoint 2013 관리 셸에서 유지 관리자를 실행합니다.
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > 지금은 StorSimple에 대해 `GarbageCollection` 작업만 지원됩니다. Microsoft.Data.SqlRemoteBlobs.Maintainer.exe에 대한 실행 매개 변수는 대/소문자를 구분합니다. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>콘텐츠 데이터베이스 및 휴지통 설정을 되돌리려면
1. SQL Server의 SQL Management Studio에서 대상 콘텐츠 데이터베이스에 대한 다음 업데이트 쿼리를 실행합니다.
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. 웹 프런트 엔드 서버의 **중앙 관리**에서 원하는 콘텐츠 데이터베이스에 대한 **웹 애플리케이션의 일반 설정**을 편집하여 휴지통을 다시 사용할 수 있도록 설정합니다. 이 작업을 수행하려면 **중앙 관리** -> **애플리케이션 관리** -> **웹 애플리케이션(웹 애플리케이션 관리)** -> **SharePoint - 80** -> **일반 애플리케이션 설정**을 클릭합니다. 휴지통 상태를 **ON**으로 설정합니다.

