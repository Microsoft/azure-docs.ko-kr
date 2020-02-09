---
title: Azure Portal 기본 설정 지정 | Microsoft Docs
description: 기본 설정에 맞게 Azure Portal 기본 설정을 변경할 수 있습니다. 설정에는 비활성 세션 제한 시간, 기본 보기, 메뉴 모드, 대비, 테마, 알림, 언어 및 지역 형식이 포함 됩니다.
services: azure-portal
keywords: 설정, 시간 제한, 언어, 지역
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310682"
---
# <a name="set-your-azure-portal-preferences"></a>Azure Portal 기본 설정 지정

사용자의 기본 설정에 맞게 Azure Portal의 기본 설정을 변경할 수 있습니다. 아래에 나열 된 각 설정을 변경할 수 있습니다.

* [비활성 세션 제한 시간](#change-the-idle-duration-for-inactive-sign-out)
* [기본 보기](#choose-your-default-view)
* [포털 메뉴 모드](#choose-a-portal-menu-mode)
* [색 및 고대비 테마](#choose-a-theme)
* [팝업 알림](#enable-or-disable-pop-up-notifications)
* [언어 및 국가별 형식](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>일반 포털 설정 변경

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 전역 페이지 헤더에서 **설정을** 선택 합니다.

    ![설정이 강조 표시 된 전역 페이지 머리글 아이콘을 보여 주는 스크린샷](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>비활성 로그 아웃에 대한 유휴 기간 변경

비활성화 시간 제한 설정은 워크스테이션의 보안을 잊은 경우 리소스를 무단 액세스 로부터 보호 하는 데 도움이 됩니다. 잠시 유휴 상태 이면 Azure Portal 세션에서 자동으로 로그 아웃 됩니다.

**비활성화 된 경우 로그 아웃**아래에 있는 드롭다운을 선택 합니다. 유휴 상태일 때 Azure Portal 세션이 로그 아웃 되는 기간을 선택 합니다.

   ![비활성 시간 제한 설정이 강조 표시 된 포털 설정을 보여 주는 스크린샷](./media/set-preferences/inactive-signout-user.png)

변경 내용은 자동으로 저장 됩니다. 유휴 상태인 경우에는 설정한 기간 후에 Azure Portal 세션이 로그 아웃 됩니다.

이 설정은 디렉터리 수준에서 관리자가 최대 유휴 시간을 적용 하도록 설정할 수도 있습니다. 관리자가 디렉터리 수준 시간 제한 설정을 변경한 경우에도 사용자의 비활성 로그 아웃 기간을 설정할 수 있습니다. 디렉터리 수준에서 설정 된 것 보다 작은 시간 설정을 선택 합니다.

관리자가 비활성 시간 제한 정책을 사용 하도록 설정한 경우 **디렉터리 비활성 시간 제한 정책 재정의** 확인란을 선택 합니다. 정책 설정 보다 작은 시간 간격을 설정 합니다.

   ![디렉터리 비활성 시간 제한 정책 설정이 강조 표시 된 포털 설정을 보여 주는 스크린샷](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 관리자 인 경우 Azure Portal의 모든 사용자에 대해 비활성 시간 제한 설정을 적용 하려면 [Azure Portal 사용자에 대한 디렉터리 수준 비활성 시간 제한 설정](admin-timeout.md) 을 참조 하세요.
>

### <a name="choose-your-default-view"></a>기본 보기 선택 

Azure Portal에 로그인 할 때 기본적으로 열리는 페이지를 변경할 수 있습니다.

   ![기본 보기가 강조 표시 된 Azure Portal 설정을 보여 주는 스크린샷](./media/set-preferences/default-view.png)

기본 보기 설정은 로그인 할 때 표시 되는 Azure Portal 보기를 제어 합니다. Azure Home은 기본적으로 또는 대시보드 보기에서 열도록 선택할 수 있습니다.

* **Home** 은 사용자 지정할 수 없습니다.  인기 있는 Azure 서비스에 대한 바로 가기를 표시 하 고 가장 최근에 사용한 리소스를 나열 합니다. 또한 Microsoft Learn 및 Azure 로드맵과 같은 리소스에 대한 유용한 링크를 제공 합니다.
* 대시보드를 사용자 지정 하 여 자신만의 작업 영역을 만들 수 있습니다. 예를 들어, 프로젝트, 작업 또는 역할에 중점을 둘 수 있는 대시보드를 빌드할 수 있습니다. **대시보드**를 선택 하는 경우 기본 보기가 가장 최근에 사용한 대시보드로 이동 합니다.

### <a name="choose-a-portal-menu-mode"></a>포털 메뉴 모드 선택

포털 메뉴의 기본 모드는 페이지에서 포털 메뉴가 차지 하는 공간의 크기를 제어 합니다.

* 포털 메뉴는 **플라이 아웃** 모드에 있을 때 필요할 때까지 숨겨집니다. 메뉴 아이콘을 선택 하 여 메뉴를 열거나 닫습니다.
* 포털 메뉴에 대해 **도킹** 모드를 선택 하면 항상 표시 됩니다. 메뉴를 축소 하 여 더 많은 작업 공간을 제공할 수 있습니다. 

### <a name="choose-a-theme"></a>테마 선택

선택한 테마는 Azure Portal에 표시 되는 배경색과 글꼴 색에 영향을 줍니다. 4 가지 미리 설정 된 색 테마 중 하나를 선택할 수 있습니다. 각 미리 보기를 선택 하 여 가장 적합 한 테마를 찾습니다.

   ![테마가 강조 표시 된 Azure Portal 설정을 보여 주는 스크린샷](./media/set-preferences/theme.png)

고대비 테마 중 하나를 대신 선택할 수 있습니다. 고대비 설정을 사용 하면 시각 장애가 있는 사용자가 Azure Portal을 쉽게 읽고 다른 모든 테마 선택을 재정의할 수 있습니다. 자세한 내용은 [고대비 설정 또는 테마 변경](azure-portal-change-theme-high-contrast.md)을 참조 하세요.

### <a name="enable-or-disable-pop-up-notifications"></a>팝업 알림 사용 또는 사용 안 함

알림은 현재 세션과 관련 된 시스템 메시지입니다. 사용자는 현재 크레딧 잔액 같은 정보를 제공 하 고, 방금 만든 리소스를 사용할 수 있게 되 면, 예를 들어 마지막 작업을 확인할 수 있습니다. 팝업 알림이 설정 되 면 화면의 맨 위 모퉁이에 메시지가 잠깐 표시 됩니다. 

팝업 알림을 사용 하거나 사용 하지 않도록 설정 하려면 **팝업 알림 사용** 확인란을 선택 하거나 선택 취소 합니다.

   ![팝업 알림이 강조 표시 된 Azure Portal 설정을 보여 주는 스크린샷](./media/set-preferences/popup-notifications.png)

현재 세션 중에 수신 된 모든 알림을 읽으려면 전역 헤더에서 **알림** 을 선택 합니다.

   ![알림이 강조 표시 된 Azure Portal global 헤더를 보여 주는 스크린샷](./media/set-preferences/read-notifications.png)

이전 세션에서 알림을 읽으려면 활동 로그에서 이벤트를 찾으십시오. 자세히 알아보려면 [Azure 활동 로그 이벤트 보기 및 검색](/azure/azure-monitor/platform/activity-log-view)을 참조 하세요.

### <a name="settings-under-useful-links"></a>유용한 링크의 설정

Azure Portal 설정을 변경한 후 삭제 하려면 **기본 설정 복원**을 선택 합니다. 포털 설정에서 변경한 내용이 모두 손실 됩니다. 이 옵션은 대시보드 사용자 지정에는 영향을 주지 않습니다.

**모든 설정 내보내기** 또는 **모든 설정 및 개인 대시보드 삭제**에 대한 자세한 내용은 [사용자 설정 내보내기 또는 삭제](azure-portal-export-delete-settings.md)를 참조 하세요.

## <a name="change-language-and-regional-settings"></a>언어 및 국가별 설정 변경

Azure Portal의 텍스트가 표시 되는 방식을 제어 하는 두 가지 설정이 있습니다. **언어** 설정은 Azure Portal 텍스트에 대해 표시 되는 언어를 제어 합니다. **국가별 형식은** 날짜, 시간, 숫자 및 통화가 표시 되는 방식을 제어 합니다.

Azure Portal에 사용 되는 언어를 변경 하려면 드롭다운을 사용 하 여 사용 가능한 언어 목록에서 선택 합니다.

국가별 형식 선택은 선택한 언어에 대한 국가별 옵션만 표시 하도록 변경 됩니다. 자동 선택을 변경 하려면 드롭다운을 사용 하 여 원하는 국가별 형식을 선택 합니다.

예를 들어 사용자의 언어로 영어를 선택 하 고 미국을 지역 형식으로 선택 하는 경우 통화는 미국 달러 단위로 표시 됩니다. 영어를 언어로 선택 하 고 지역 형식으로 유럽을 선택 하는 경우 통화는 유로화로 표시 됩니다.

**적용** 을 선택 하 여 언어 및 국가별 형식 설정을 업데이트 합니다.

   ![언어 및 국가별 형식 설정을 보여 주는 스크린샷](./media/set-preferences/language.png)

>[!NOTE]
>이러한 언어 및 국가별 설정은 Azure Portal에만 영향을 줍니다. 새 탭 또는 창에서 열리는 설명서 링크는 브라우저의 언어 설정을 사용 하 여 표시할 언어를 결정 합니다.
>

## <a name="next-steps"></a>다음 단계

* [사용자 지정 대시보드 만들기 및 공유](azure-portal-dashboards.md)
* [Azure Portal 방법 비디오 시리즈](azure-portal-video-series.md)
