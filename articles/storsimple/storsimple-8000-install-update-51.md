---
title: StorSimple 8000 시리즈 장치에 업데이트 5.1 설치 | Microsoft Docs
description: Storsimple 8000 시리즈 장치에 StorSimple 8000 시리즈 업데이트 5.1을 설치 하는 방법을 설명 합니다.
services: storsimple
documentationcenter: NA
author: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: twooley
ms.openlocfilehash: f9cc5181d6cc29ee4b3c2373dbbc91d6290fbe6e
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782773"
---
# <a name="install-update-51-on-your-storsimple-device"></a>StorSimple 장치에 업데이트 5.1 설치

## <a name="overview"></a>개요

이 자습서에서는 Azure Portal를 통해 이전 소프트웨어 버전을 실행 하는 StorSimple 장치에 업데이트 5.1을 설치 하는 방법을 설명 합니다. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

업데이트 5.1에는 중단 없는 보안 업데이트가 포함 되어 있습니다. 비 중단 또는 정기 업데이트는 Azure Portal를 통해 적용할 수 있습니다. <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * 업데이트 5.1은 필수 업데이트 이며 즉시 설치 해야 합니다. 자세한 내용은 [업데이트 5.1 릴리스 정보](storsimple-update51-release-notes.md)를 참조 하세요.
> * 설치하기 전에 일련의 수동 및 자동 전 검사를 수행하며 하드웨어 상태와 네트워크 연결 측면에서 디바이스 상태를 확인합니다. 이러한 사전 검사는 Azure Portal에서 업데이트를 적용 하는 경우에만 수행 됩니다.
> * 핫픽스 방법을 사용 하 여를 설치 하려는 경우 [Microsoft 지원](mailto:support@microsoft.com)에 문의 하세요.

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Azure Portal를 통해 업데이트 5.1 설치

장치를 업데이트 [5.1](storsimple-update51-release-notes.md)로 업데이트 하려면 다음 단계를 수행 합니다.

> [!NOTE]
> Microsoft는 디바이스로부터 추가적인 진단 정보를 확보할 수 있습니다. 따라서 운영 팀에서 문제가 있는 디바이스를 확인하는 경우, 디바이스로부터 정보를 수집하고 문제를 진단할 준비가 더욱 잘 갖추어져 있습니다.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure 포털에서 업데이트를 설치하려면

1. StorSimple 서비스 페이지에서 디바이스를 선택합니다.

    ![디바이스 선택](./media/storsimple-8000-install-update-51/update1.png)

2. **장치 설정**  >  **장치 업데이트**로 이동 합니다.

    ![디바이스 업데이트 클릭](./media/storsimple-8000-install-update-51/update2.png)

3. 새 업데이트를 사용할 수 있는 경우 알림이 나타납니다. 또는 **디바이스 업데이트** 블레이드에서 **업데이트 검색**을 클릭합니다. 사용 가능한 업데이트를 검색하는 작업이 만들어집니다. 작업이 성공적으로 완료되면 알림이 표시됩니다.

    ![디바이스 업데이트 클릭](./media/storsimple-8000-install-update-51/update3.png)

4. 디바이스에 업데이트를 적용하기 전에 릴리스 정보를 검토하는 것이 좋습니다. 업데이트를 적용하려면 **업데이트 설치**를 클릭합니다. **Confirm regular updates(정기 업데이트 확인)** 블레이드에서 업데이트를 적용하기 전에 완료할 필수 구성 요소를 검토합니다. 디바이스를 업데이트할 준비가 되었음을 나타내는 확인란을 선택한 후 **설치**를 클릭합니다.

    ![디바이스 업데이트 클릭](./media/storsimple-8000-install-update-51/update4.png)

5. 일련의 필수 조건 검사가 시작됩니다. 이들 검사는 다음과 같습니다.
   
   * **컨트롤러 상태 검사** 디바이스 컨트롤러가 정상이고 온라인에 있는지 모두 확인합니다.
   * **하드웨어 구성 요소 상태 검사** StorSimple 디바이스에서 모든 하드웨어 구성 요소가 정상인지 확인합니다.
   * **데이터 0 검사** 데이터 0이 디바이스에서 사용할 수 있는지 확인합니다. 이 인터페이스를 사용하지 않는 경우 다음을 사용하도록 설정하고 다시 시도해야 합니다.

     모든 검사가 성공적으로 완료된 경우에만 업데이트가 다운로드되어 설치됩니다. 검사가 진행 중인 경우 알림이 표시됩니다. 사전 검사가 실패하면 실패한 이유가 표시됩니다. 이러한 문제를 해결한 다음 작업을 다시 시도합니다. 혼자서 이러한 문제를 해결할 수 없는 경우 Microsoft 지원에 문의해야 할 수 있습니다.

7. 사전 검사를 성공적으로 완료한 후 업데이트 작업이 생성됩니다. 업데이트 작업이 성공적으로 만들어지면 알림이 표시됩니다.
   
    ![업데이트 작업 만들기](./media/storsimple-8000-install-update-51/update6.png)
   
    그런 다음 업데이트가 디바이스에 적용됩니다.

9. 업데이트를 완료하는 데 몇 시간이 걸립니다. 업데이트 작업을 선택하고 **세부 정보** 를 클릭하여 언제든지 작업의 세부 정보를 봅니다.

    ![업데이트 작업 만들기](./media/storsimple-8000-install-update-51/update8.png)

     **디바이스 설정 &gt; 작업**에서 업데이트 작업의 진행률을 모니터링할 수도 있습니다. **작업** 블레이드에서 업데이트 진행률을 볼 수 있습니다.

     ![업데이트 작업 만들기](./media/storsimple-8000-install-update-51/update7.png)

10. 작업이 완료되면 **디바이스 설정 &gt; 디바이스 업데이트**로 이동합니다. 이제 소프트웨어 버전이 업데이트됩니다.


장치가 **StorSimple 8000 시리즈 업데이트 5.1 (6.3.9600.17885)** 를 실행 중인지 확인 합니다. **마지막 업데이트 날짜**를 수정해야 합니다.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>다음 단계

[업데이트 5.1 릴리스](storsimple-update51-release-notes.md)에 대해 자세히 알아보세요.
