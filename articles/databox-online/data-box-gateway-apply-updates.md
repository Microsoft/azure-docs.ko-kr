---
title: Azure Data Box Gateway 시리즈 장치에 업데이트 설치 | Microsoft Docs
description: Azure Data Box Gateway 시리즈 장치에 대 한 Azure Portal 및 로컬 웹 UI를 사용 하 여 업데이트를 적용 하는 방법을 설명 합니다.
services: databox
author: twooley
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: twooley
ms.openlocfilehash: 3972aecf70a664980e8279152eb001915ae1bb53
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783878"
---
# <a name="update-your-azure-data-box-gateway"></a>Azure Data Box Gateway 업데이트

이 문서에서는 로컬 웹 UI 및 Azure Portal를 통해 Azure Data Box Gateway에 업데이트를 설치 하는 데 필요한 단계를 설명 합니다. Data Box Gateway 장치를 최신 상태로 유지 하기 위해 소프트웨어 업데이트나 핫픽스를 적용 합니다.

> [!IMPORTANT]
>
> - 업데이트 **1911** 은 장치의 **1.6.1049.786** 소프트웨어 버전에 해당 합니다. 이 업데이트에 대 한 자세한 내용은 [릴리스 정보](data-box-gateway-1911-release-notes.md)를 참조 하세요.
>
> - 업데이트 또는 핫픽스를 설치하면 디바이스가 다시 시작됩니다. Data Box Gateway 단일 노드 장치인 경우 진행 중인 모든 i/o가 중단 되 고 장치 소프트웨어 업데이트에 대해 최대 30 분의 가동 중지 시간이 발생 합니다.

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal를 통해 업데이트를 설치 하는 것이 좋습니다. 장치는 하루에 한 번 업데이트를 자동으로 검색 합니다. 업데이트를 사용할 수 있게 되 면 포털에 알림이 표시 됩니다. 그런 다음 업데이트를 다운로드 하 여 설치할 수 있습니다.

> [!NOTE]
> 업데이트 설치를 계속 하기 전에 장치가 정상 상태이 고 상태가 **온라인** 으로 표시 되는지 확인 합니다.

1. 장치에 대 한 업데이트를 사용할 수 있는 경우 알림이 표시 됩니다. 알림을 선택 하거나 맨 위 명령 모음에서 **장치 업데이트**를 선택 합니다. 이렇게 하면 장치 소프트웨어 업데이트를 적용할 수 있습니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. **장치 업데이트** 블레이드에서 릴리스 정보의 새 기능과 관련 된 사용 조건을 검토 했는지 확인 합니다.

    업데이트를 다운로드 하 여 **설치** 하거나 업데이트를 **다운로드** 하도록 선택할 수 있습니다. 그런 다음 나중에 이러한 업데이트를 설치 하도록 선택할 수 있습니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    업데이트를 다운로드 하 여 설치 하려면 다운로드를 완료 한 후 업데이트가 자동으로 설치 되도록 하는 옵션을 선택 합니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 업데이트 다운로드가 시작 됩니다. 다운로드가 진행 중 이라고 표시 되는 알림이 표시 됩니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    알림 배너가 Azure Portal에도 표시 됩니다. 다운로드 진행률을 나타냅니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    이 알림을 선택 하거나 **장치 업데이트** 를 선택 하 여 업데이트의 자세한 상태를 확인할 수 있습니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. 다운로드가 완료 되 면 알림 배너가 업데이트 되어 완료 되었음을 표시 합니다. 업데이트를 다운로드 하 여 설치 하도록 선택한 경우 설치가 자동으로 시작 됩니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    업데이트만 다운로드 하도록 선택한 경우에는 알림을 선택 하 여 **장치 업데이트** 블레이드를 엽니다. **설치**를 선택합니다.
  
    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. 설치가 진행 중 이라고 표시 되는 알림이 표시 됩니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    또한 포털에는 설치가 진행 중임을 나타내는 정보 경고도 표시 됩니다. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. 이는 1 개 노드 장치 이므로 업데이트가 설치 된 후 장치가 다시 시작 됩니다. 다시 시작 하는 동안 중요 한 경고는 장치 하트 비트가 손실 되었음을 표시 합니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    경고를 선택 하 여 해당 하는 장치 이벤트를 확인 합니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 업데이트를 설치한 후 장치 상태가 **온라인** 으로 업데이트 됩니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    위쪽 명령 모음에서 **장치 업데이트**를 선택 합니다. 업데이트가 성공적으로 설치 되었으며 장치 소프트웨어 버전이이를 반영 하는지 확인 합니다.

    ![업데이트 후 소프트웨어 버전](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>로컬 웹 UI 사용

로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

- 업데이트 또는 핫픽스 다운로드
- 업데이트 또는 핫픽스 설치

이러한 각 단계는 다음 섹션에서 자세히 설명합니다.

### <a name="download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 다운로드

업데이트를 다운로드 하려면 다음 단계를 수행 합니다. Microsoft에서 제공 하는 위치 또는 Microsoft 업데이트 카탈로그에서 업데이트를 다운로드할 수 있습니다.

Microsoft 업데이트 카탈로그에서 업데이트를 다운로드 하려면 다음 단계를 수행 합니다.

1. 브라우저를 시작 하 고로 이동 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 합니다.

   ![카탈로그 검색](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드 하려는 업데이트에 대 한 기술 자료 (KB) 번호 (KB)를 입력 합니다. 예를 들어 **Azure Data Box Gateway**를 입력 한 다음 **검색**을 클릭 합니다.

   업데이트 목록은 **Azure Data Box Gateway 1911**으로 표시 됩니다.

   ![카탈로그 검색](./media/data-box-gateway-apply-updates/download-update-2.png)

3. **다운로드**를 선택합니다. 장치 소프트웨어 업데이트에 해당 하는 *SoftwareUpdatePackage.exe* 이라고 하는 다운로드할 파일이 하나 있습니다. 로컬 시스템의 폴더에 파일을 다운로드 합니다. 디바이스에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

   ![카탈로그 검색](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에 다음을 확인합니다.

- 업데이트 또는 핫픽스를 호스트에 로컬로 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.
- 로컬 웹 UI의 **개요** 페이지에 표시 된 대로 장치 상태가 정상입니다.

   ![디바이스 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

이 절차를 완료 하는 데 약 20 분이 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

1. 로컬 웹 UI에서 **유지 관리**  >  **소프트웨어 업데이트**로 이동 합니다. 실행 중인 소프트웨어 버전을 기록해 둡니다.

   ![디바이스 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 업데이트 파일의 경로를 제공 합니다. 네트워크 공유에 배치 된 경우 업데이트 설치 파일로 이동할 수도 있습니다. *SoftwareUpdatePackage.exe* 접미사가 포함 된 소프트웨어 업데이트 파일을 선택 합니다.

   ![디바이스 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. **적용**을 선택합니다.

   ![디바이스 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 확인 메시지가 표시 되 면 **예** 를 선택 하 여 계속 합니다. 장치가 단일 노드 장치인 경우 업데이트가 적용 된 후 장치가 다시 시작 되 고 가동 중지 시간이 발생 합니다.
   ![장치 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 업데이트가 시작됩니다. 디바이스가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.

6. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 장치 소프트웨어가 업데이트 되었는지 확인 하려면 로컬 웹 UI에서 **유지 관리**  >  **소프트웨어 업데이트**로 이동 합니다. 이 예제에서 표시 된 소프트웨어 버전은 **1.6.1049.786**입니다.

   ![디바이스 업데이트](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>다음 단계

[Azure Data Box Gateway 관리](data-box-gateway-manage-users.md)에 대해 자세히 알아보세요.
