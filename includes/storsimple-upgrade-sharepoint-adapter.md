---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564211"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>SharePoint 2010에서 SharePoint 2013으로 업그레이드한 다음 SharePoint용 StorSomple 어댑터 설치
> [!IMPORTANT]
> 업그레이드가 완료되고 RBS 기능을 다시 사용하도록 설정할 때까지 이전에 RBS를 통해 외부 스토리지에 이동된 모든 파일은 사용할 수 없습니다. 사용자 영향을 제한하려면 계획된 유지 관리 기간 동안 업그레이드 또는 다시 설치를 수행합니다.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>SharePoint 2010에서 SharePoint 2013으로 업그레이드한 다음 어댑터를 설치하려면
1. SharePoint 2010 팜에서 구체화된 BLOB 및 RBS를 사용할 수 있는 콘텐츠 데이터베이스에 대한 BLOB 저장소 경로를 기록합니다. 
2. 새 SharePoint 2013 팜을 설치하고 구성합니다. 
3. SharePoint 2010 팜에서 새 SharePoint 2013 팜으로 데이터베이스, 애플리케이션 및 사이트 모음을 이동합니다. 지침을 보려면 [SharePoint 2013으로의 업그레이드 프로세스 개요](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process)로 이동합니다.
4. 새 팜에 SharePoint용 StorSimple 어댑터를 설치합니다. 절차를 보려면 [SharePoint용 StorSimple 어댑터 설치](#install-the-storsimple-adapter-for-sharepoint) 로 이동합니다.
5. 1단계에서 기록한 정보를 사용하여 콘텐츠 데이터베이스의 동일한 집합에 대해 RBS를 사용하도록 설정하고 SharePoint 2010 설치에 사용된 동일한 BLOB 저장소 경로를 제공합니다. 절차를 보려면 [RBS 구성](#configure-rbs) 으로 이동합니다. 이 단계를 완료한 후에 이전에 구체화된 파일을 새 팜에서 액세스할 수 있어야 합니다. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터 업그레이드
> [!IMPORTANT]
> 다음과 같은 이유로 계획된 유지 관리 기간 동안 이 업그레이드가 발생하도록 예약해야 합니다.
> 
> * 어댑터를 다시 설치할 때까지 이전에 구체화된 콘텐츠를 사용할 수 없습니다.
> * 하지만, SharePoint 용 StorSimple 어댑터의 이전 버전을 제거한 후 새 버전을 설치 하기 전에 사이트에 업로드된 모든 콘텐츠는 콘텐츠 데이터베이스에 저장됩니다. 새 어댑터를 설치한 후 해당 콘텐츠를 StorSimple 디바이스로 이동해야 합니다. SharePoint에 포함된 Microsoft `RBS Migrate()` PowerShell cmdlet을 사용하여 콘텐츠를 마이그레이션할 수 있습니다. 자세한 내용은 [RBS에서 콘텐츠 마이그레이션](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14))을 참조하세요. 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터를 업그레이드하려면
1. SharePoint 용 StorSimple 어댑터의 이전 버전을 제거합니다.
   
   > [!NOTE]
   > RBS는 콘텐츠 데이터베이스에서 자동으로 해제됩니다. 그러나 기존 BLOB은 StorSimple 디바이스에서 유지됩니다. RBS를 사용하지 않도록 설정하고 BLOB을 콘텐츠 데이터베이스로 다시 마이그레이션되지 않기 때문에 해당 BLOB에 대한 모든 요청이 실패합니다. 
   > 
   > 
2. SharePoint용 새 StorSimple 어댑터 설치 새 어댑터는 이전에 사용하도록 설정하거나 RBS에 대해 사용하지 않도록 설정하는 콘텐츠 데이터베이스를 자동으로 인식하여 이전 설정을 사용합니다.