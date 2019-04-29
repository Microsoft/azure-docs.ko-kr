---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633420"
---
> [!NOTE]
> SharePoint RBS용 StorSimple 어댑터 구성을 변경하면, Domain Admins 그룹에 속하는 사용자 계정으로 로그온해야 합니다. 또한 중앙 관리와 같은 호스트에서 실행 되는 브라우저에서 구성 페이지에 액세스해야 합니다.
> 
> 

#### <a name="to-configure-rbs"></a>RBS를 구성하려면
1. SharePoint 중앙 관리 페이지를 열고 **시스템 설정**으로 이동합니다. 
2. **Azure StorSimple** 섹션에서 **StorSimple 어댑터 구성**을 클릭합니다.
   
    ![StorSimple 어댑터 구성](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. **StorSimple 어댑터 구성** 페이지에서 다음을 수행합니다.
   
   1. **경로 편집 사용** 확인란이 선택되었는지 확인합니다.
   2. 텍스트 상자에 BLOB 저장소의 UNC(범용 명명 규칙) 경로를 입력합니다.
      
      > [!NOTE]
      > BLOB 저장소 볼륨은 StorSimple 디바이스에 구성된 iSCSI 볼륨에서 호스팅해야 합니다.

   3. 원격 저장소에 대해 구성하려는 각 콘텐츠 데이터베이스 아래에 있는 **사용** 단추를 클릭합니다.
      
      > [!NOTE]
      > BLOB 저장소가 공유되고 모든 WFE(웹 프런트 엔드) 서버에서 연결 가능해야 하며, SharePoint 서버 팜에 대해 구성된 사용자 계정에 공유 액세스 권한이 있어야 합니다.
      
      ![RBS 공급자를 사용하도록 설정](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      RBS를 사용하지 않도록 설정하거나 사용하도록 설정하면 다음 메시지가 나타납니다.
      
      ![StorSimple 어댑터 사용 또는 사용 안함 구성](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. **업데이트** 단추를 클릭하여 구성을 적용합니다. **업데이트** 단추를 클릭하면 RBS 구성 상태가 모든 WFE 서버에서 업데이트되며, 전체 팜에서 RBS가 사용됩니다. 다음과 같은 메시지가 나타납니다.
      
      ![어댑터 구성 메시지](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > 매우 많은(200개 초과) 데이터베이스가 있는 SharePoint 팜에 RBS를 구성하는 경우 SharePoint 중앙 관리 페이지가 시간을 초과할 수 있습니다. 이런 경우 페이지를 새로 고칩니다. 구성 프로세스에는 영향을 주지 않습니다.

4. 구성 확인:
   
   1. SharePoint 중앙 관리 웹 사이트에 로그인한 다음, **StorSimple 어댑터 구성** 페이지로 이동합니다.
   2. 구성 세부 정보를 확인하고 입력한 설정과 일치하는지 확인합니다. 
5. RBS가 올바르게 작동하는지 확인합니다.
   
   1. SharePoint에 문서를 업로드합니다. 
   2. 구성한 UNC 경로를 찾습니다. RBS 디렉터리 구조가 만들어지고 업로드된 개체가 포함되어있는지 확인합니다.
6. (선택 사항) SharePoint에 포함된 Microsoft RBS `Migrate()` PowerShell cmdlet을 사용하여 기존 BLOB 콘텐츠를 StorSimple 디바이스로 마이그레이션할 수 있습니다. 자세한 내용은 [SharePoint 2013에서 RBS 내부 또는 외부로 콘텐츠 마이그레이션][6] 또는 [RBS(SharePoint Foundation 2010) 내부 또는 외부로 콘텐츠 마이그레이션][7]을 참조하세요.
7. (선택 사항) 테스트 설치에서 다음과 같이 BLOB이 콘텐츠 데이터베이스 외부로 이동되었는지를 확인할 수 있습니다. 
   
   1. SQL Management Studio를 시작합니다.
   2. 다음과 같이 ListBlobsInDB_2010.sql 또는 ListBlobsInDB_2013.sql 쿼리를 실행합니다.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      RBS가 올바르게 구성된 경우, 업로드되고 RBS로 성공적으로 표면화된 모든 개체에 대한 SizeOfContentInDB 열에 NULL 값이 나타나야 합니다.
8. (선택 사항)RBS를 구성하고 모든 BLOB 콘텐츠를 StorSimple 디바이스로 이동한 후, 디바이스에 콘텐츠 데이터베이스를 이동할 수 있습니다. 콘텐츠 데이터베이스를 이동하도록 선택한 경우, 기본 볼륨으로 디바이스에서 콘텐츠 데이터베이스 저장소를 구성하는 것이 좋습니다. 그런 다음, 설정된 SQL Server 모범 사례를 사용하여 콘텐츠 데이터베이스를 StorSimple 디바이스로 마이그레이션합니다. 
   
   > [!NOTE]
   > 콘텐츠 데이터베이스를 디바이스로 이동하면 StorSimple 8000 시리즈 디바이스만 지원합니다(5000 또는 7000 시리즈를 지원하지 않음).
   
   StorSimple 디바이스에 대한 별도 볼륨에 BLOB 및 콘텐츠 데이터베이스를 저장하는 경우, 동일한 볼륨 컨테이너에 구성하는 것이 좋습니다. 이렇게 하면 같이 백업됩니다.
   
   > [!WARNING]
   > RBS를 사용하도록 설정하지 않은 경우, 콘텐츠 데이터베이스를 StorSimple 디바이스로 이동하지 않는 것이 좋습니다. 테스트되지 않은 구성입니다.
   
9. 다음 단계 [가비지 수집 구성](#configure-garbage-collection)으로 이동합니다.

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
