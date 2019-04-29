---
title: StorSimple 5000-7000 시리즈에서 8000 시리즈 디바이스로 데이터 마이그레이션| Microsoft Docs
description: 마이그레이션 기능의 개요와 필수 구성 요소를 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631681"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>StorSimple 5000-7000 시리즈에서 8000 시리즈 디바이스로 데이터 마이그레이션

> [!IMPORTANT]
> - 2019년 7월 31일부로, StorSimple 5000/7000 시리즈는 지원 종료(EOS) 상태에 도달합니다. StorSimple 5000/7000 시리즈 고객은 이 문서에 설명된 대안 제품 중 하나로 마이그레이션하는 것이 좋습니다.
> - 마이그레이션은 현재 지원형 작업입니다. StorSimple 5000-7000 시리즈 디바이스에서 8000 시리즈 디바이스로 데이터를 마이그레이션하려면 Microsoft 지원에 마이그레이션을 예약해야 합니다. 그러면 Microsoft 지원에서 구독을 마이그레이션할 수 있도록 설정합니다. 자세한 내용은 [지원 티켓 열기](storsimple-8000-contact-microsoft-support.md)를 참조하세요.
> - 서비스 요청을 제출한 후 마이그레이션 계획을 실행하고 마이그레이션이 실제로 시작될 때까지 몇 주가 걸릴 수 있습니다.
> - Microsoft 지원에 문의하기 전에 이 문서에 언급된 [마이그레이션 필수 구성 요소](#migration-prerequisites)를 끝까지 검토하시기 바랍니다.

## <a name="overview"></a>개요

이 문서에서는 StorSimple 5000-7000 시리즈 고객이 데이터를 StorSimple 8000 시리즈 실제 디바이스 또는 8010/8020 클라우드 어플라이언스로 마이그레이션할 수 있는 마이그레이션 기능을 소개합니다. 또한 5000-7000 시리즈 레거시 디바이스에서 8000 시리즈 실제 디바이스 또는 클라우드 어플라이언스로 데이터를 마이그레이션하는 데 필요한 단계별 연습 다운로드 링크를 제공합니다.

이 문서는 온-프레미스 8000 시리즈 디바이스와 StorSimple Cloud Appliance에 모두 적용됩니다.


## <a name="migration-feature-versus-host-side-migration"></a>마이그레이션 기능과 호스트 측 마이그레이션 비교

마이그레이션 기능을 사용하거나 호스트 측 마이그레이션을 수행하여 데이터를 이동할 수 있습니다. 이 섹션에서는 장단점을 포함하여 각 방법의 세부 사항을 설명합니다. 이 정보를 사용하여 데이터 마이그레이션에 사용할 방법을 알아보세요.

마이그레이션 기능은 7000/5000 시리즈에서 8000 시리즈로의 DR(재해 복구) 프로세스를 시뮬레이션합니다. 이 기능을 사용하면 Azure에서 5000/7000 시리즈 형식의 데이터를 8000 시리즈 형식으로 마이그레이션할 수 있습니다. 마이그레이션 프로세스는 StorSimple 마이그레이션 도구를 사용하여 시작됩니다. 이 도구는 8000 시리즈 디바이스에서 다운로드 및 백업 메타데이터 변환을 시작한 후 최신 백업을 사용하여 디바이스의 볼륨을 노출합니다.

|      | 장점                                                                                                                                     |단점                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | 마이그레이션 프로세스는 5000/7000 시리즈에서 수행된 백업 기록을 유지합니다.                                               | 사용자가 데이터에 액세스하려고 시도하면 이 마이그레이션이 Azure에서 데이터를 다운로드하므로 데이터 다운로드 비용이 발생합니다.                                     |
| 2.   | 호스트 쪽에 데이터가 마이그레이션되지 않습니다.                                                                                                     | 이 프로세스는 백업을 시작하는 시점과 마지막 백업이 8000 시리즈에 표시되는 시점 사이에 가동 중지 시간이 필요합니다(마이그레이션 도구를 사용하여 예측 가능). |
| 3.   | 이 프로세스는 모든 정책, 대역폭 템플릿, 암호화 및 기타 설정을 8000 시리즈 디바이스에 그대로 유지합니다.                      | 사용자 액세스는 사용자가 이전에 액세스한 데이터만 가져오고 전체 데이터 세트를 리하이드레이션하지 않습니다.                                                  |
| 4.   | 이 프로세스에는 프로덕션에 영향을 주지 않고 Azure의 모든 이전 백업을 비동기적으로 백업하기 위한 추가 시간이 필요합니다. | 마이그레이션은 클라우드 구성 수준에서만 수행할 수 있습니다.  클라우드 구성의 개별 볼륨은 개별적으로 마이그레이션할 수 없습니다.                       |

호스트 측 마이그레이션은 8000 시리즈를 독립적으로 설정하고 5000/7000 시리즈 디바이스에서 8000 시리즈 디바이스로 데이터를 복사할 수 있습니다. 한 저장 디바이스에서 다른 저장 디바이스로 데이터를 마이그레이션하는 것과 동일합니다. Diskboss, robocopy 등의 다양한 도구가 데이터 복사에 사용됩니다.

|      | 장점                                                                                                                      |단점                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | 마이그레이션을 볼륨별로 단계적으로 접근할 수 있습니다.                                               | 이전 백업(5000/7000 시리즈에서 수행된)은 8000 시리즈 디바이스에서 사용할 수 없습니다.                                                                                                       |
| 2.   | 데이터를 Azure의 저장소 계정 하나로 통합할 수 있습니다.                                                       | 8000 시리즈에서 클라우드에 처음으로 백업할 때 8000 시리즈의 모든 데이터를 Azure에 백업해야 하기 때문에 시간이 오래 걸립니다.                                                                     |
| 3.   | 마이그레이션이 성공하면 모든 데이터가 로컬 어플라이언스에 있습니다. 데이터에 액세스할 때 대기 시간이 없습니다. | 5000/7000 디바이스에서 데이터가 삭제될 때까지 Azure 저장소 사용량이 증가합니다.                                                                                                        |
| 4.   |                                                                                                                           | 7000/5000 시리즈 디바이스에 대량의 데이터가 있는 경우 마이그레이션하는 동안 이 데이터를 azure에서 다운로드해야 하며, Azure에서 데이터를 다운로드하는 것과 관련된 비용과 대기 시간이 발생합니다. |

이 문서는 5000/7000 시리즈 디바이스에서 8000 시리즈 디바이스로 마이그레이션하는 기능만 집중적으로 다룹니다. 호스트 측 마이그레이션에 대한 자세한 내용은 [다른 저장 디바이스에서 마이그레이션](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf)을 참조하세요.

## <a name="migration-prerequisites"></a>마이그레이션 필수 구성 요소

레거시 5000 또는 7000 시리즈 디바이스와 8000 시리즈 StorSimple 디바이스의 마이그레이션 필수 구성 요소는 다음과 같습니다.

> [!IMPORTANT]
> Microsoft 지원에 서비스 요청을 제출하기 전에 마이그레이션 필수 구성 요소를 검토하고 완료하세요.

### <a name="for-the-50007000-series-device-source"></a>5000/7000 시리즈 디바이스(소스)의 경우

마이그레이션을 시작하기 전에 다음 사항을 확인합니다.

* 5000 또는 7000 시리즈 소스 디바이스를 갖고 있습니다. 디바이스가 정상 작동 중일 수도 있고 다운되었을 수도 있습니다.

    > [!IMPORTANT]
    > 마이그레이션 프로세스에서 이 디바이스에 순차적으로 액세스하는 것이 좋습니다. 디바이스 문제가 있는 경우 순차적 액세스가 문제 해결에 도움이 될 수 있습니다.

* 5000 또는 7000 시리즈 소스 디바이스에서는 소프트웨어 버전 v2.1.1.518 이상을 실행 중입니다. 이보다 낮은 버전은 지원되지 않습니다.
* 5000 또는 7000 시리즈에서 실행 중인 버전을 확인하려면 웹 UI의 오른쪽 위 모서리를 살펴봅니다. 그러면 디바이스에서 실행 중인 소프트웨어 버전이 표시될 것입니다. 마이그레이션을 수행하려면 5000 또는 7000 시리즈에서 v2.1.1.518 버전을 실행해야 합니다.

    ![레거시 디바이스의 소프트웨어 버전 확인](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * 정상 작동하는 디바이스에서 v2.1.1.518 이상 버전을 실행하고 있지 않는 경우 시스템을 최소 필수 버전으로 업그레이드하세요. 자세한 지침은 [v2.1.1.518 버전으로 시스템 업그레이드](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)를 참조하세요.
    * v2.1.1.518을 실행 중인 경우 웹 UI로 이동하여 레지스트리 복원 실패에 대한 알림이 있는지 확인합니다. 레지스트리 복원이 실패했으면 레지스트리 복원을 실행합니다. 자세한 내용은 [레지스트리 복원 실행](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry)을 참조하세요.
    * v2.1.1.518을 실행하지 않는 다운된 디바이스가 있으면 v2.1.1.518을 실행하는 대체 디바이스로 장애 조치(failover)를 수행합니다. 자세한 지침은 5000/7000 시리즈 StorSimple 디바이스의 DR을 참조하세요.
    * 클라우드 스냅숏을 만들어서 디바이스 데이터를 백업합니다.
    * 소스 디바이스에서 실행 중인 다른 활성 백업 작업이 있는지 확인합니다. 여기에는 StorSimple 데이터 보호 콘솔 호스트의 작업이 포함됩니다. 현재 작업이 완료될 때까지 기다립니다.


### <a name="for-the-8000-series-physical-device-target"></a>8000 시리즈 실제 디바이스(대상)의 경우

마이그레이션을 시작하기 전에 다음 사항을 확인합니다.

* 대상 8000 시리즈 디바이스가 등록되었고 실행 중입니다. 자세한 내용은 [StorSimple Manager 서비스를 사용하여 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.
* 8000 시리즈 디바이스에 최신 StorSimple 8000 시리즈 업데이트 5가 설치되어 있고 6.3.9600.17845 이상 최신 버전을 실행 중입니다. 디바이스에 최신 업데이트가 설치되어 있지 않은 경우 최신 업데이트를 설치해야만 마이그레이션을 진행할 수 있습니다. 자세한 내용은 [8000 시리즈 디바이스에 최신 업데이트 설치](storsimple-8000-install-update-5.md)를 참조하세요.
* Azure 구독에서 마이그레이션이 설정되었습니다. 구독이 설정되지 않으면 Microsoft 지원에 문의하여 구독에서 마이그레이션을 설정합니다.

### <a name="for-the-80108020-cloud-appliance-target"></a>8010/8020 클라우드 어플라이언스(대상)의 경우

마이그레이션을 시작하기 전에 다음 사항을 확인합니다.

* 대상 클라우드 어플라이언스가 등록되었고 실행 중입니다. 자세한 내용을 보려면 [StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)를 참조하세요.
* 클라우드 어플라이언스에서 최신 StorSimple 8000 시리즈 업데이트 5 소프트웨어 버전 6.3.9600.17845를 실행 중입니다. 클라우드 어플라이언스에서 업데이트 5를 실행하고 있지 않은 경우 새로운 업데이트 5 클라우드 어플라이언스를 만든 후 마이그레이션을 진행합니다. 자세한 내용은 [8010/8020 클라우드 어플라이언스 만들기](storsimple-8000-cloud-appliance-u2.md)를 참조하세요.

### <a name="for-the-computer-running-storsimple-migration-tool"></a>StorSimple 마이그레이션 도구를 실행하는 컴퓨터의 경우

StorSimple 마이그레이션 도구는 StorSimple 5000-7000 시리즈 디바이스에서 8000 시리즈 디바이스로 데이터를 마이그레이션할 수 있는 UI 기반 도구입니다. StorSimple 마이그레이션 도구를 설치하려면 다음 요구 사항을 충족하는 컴퓨터를 사용합니다.

컴퓨터가 인터넷에 연결되어 있습니다.

* 다음 운영 체제를 실행하고 있습니다.
    * Windows 10.
    * StorSimple 마이그레이션 도구를 설치하려면 Windows Server 2012 R2(또는 이상).
* .NET 4.5.2가 설치되어 있습니다.
* 도구를 설치하고 사용하는 데 필요한 최소 여유 공간 5GB가 있습니다.

> [!TIP]
> StorSimple 디바이스가 Windows Server 호스트에 연결되면 Windows Server 호스트 컴퓨터에 마이그레이션 도구를 설치할 수 있습니다.

#### <a name="to-install-storsimple-migration-tool"></a>StorSimple 마이그레이션 도구를 설치하려면

다음 단계를 수행하여 컴퓨터에 StorSimple 마이그레이션 도구를 설치합니다.

1. _StorSimple8000SeriesMigrationTool_ 폴더를 Windows 컴퓨터에 복사합니다. 소프트웨어가 복사되는 드라이브에 충분한 공간이 있는지 확인합니다.

    폴더에서 도구 구성 파일 _StorSimple8000SeriesMigrationTool.exe.config_를 엽니다. 다음은 파일의 코드 조각입니다.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. 키에 해당하는 값을 편집하고 다음 항목으로 바꿉니다.

    * `UserName` - Azure Portal에 로그인하는 사용자 이름.
    * `SubscriptionName and SubscriptionId` –  Azure 구독의 이름 및 ID. StorSimple 디바이스 관리자 서비스 시작 페이지의 **일반** 아래에서 **속성**을 클릭합니다. 서비스와 연결된 구독 이름 및 구독 ID를 복사합니다.
    * `ResourceName` - Azure Portal에서 StorSimple 디바이스 관리자 서비스의 이름. 서비스 속성 아래에도 표시됩니다.
    * `ResourceGroup` – Azure Portal의 StorSimple 디바이스 관리자 서비스와 연결된 리소스 그룹의 이름. 서비스 속성 아래에도 표시됩니다.
    ![대상 디바이스의 서비스 속성 확인](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure Portal의 Azure Active Directory 테넌트 ID. 관리자 권한으로 Microsoft Azure에 로그인합니다. Microsoft Azure Portal에서 **Azure Active Directory**를 클릭합니다. **관리** 아래에서 **속성**을 클릭합니다. **디렉터리 ID** 상자에 테넌트 ID가 표시됩니다.
    ![Azure Active Directory의 테넌트 ID 확인](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  구성 파일의 변경 내용을 저장합니다.
4.  _StorSimple8000SeriesMigrationTool.exe_ 명령을 실행하여 도구를 시작합니다. 자격 증명을 입력하라는 메시지가 표시되면 Azure Portal의 구독과 연결된 자격 증명을 입력합니다. 
5.  StorSimple 마이그레이션 도구 UI가 표시됩니다.
  

## <a name="next-steps"></a>다음 단계
[StorSimple 5000-7000 시리즈 디바이스에서 8000 시리즈 디바이스로 데이터를 마이그레이션](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)하는 방법에 대한 단계별 지침을 다운로드합니다.
