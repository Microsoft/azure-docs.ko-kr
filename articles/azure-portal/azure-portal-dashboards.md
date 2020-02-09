---
title: Azure Portal에서 대시보드 만들기 및 공유
description: 이 문서에서는 Azure Portal에서 대시보드를 만들고, 사용자 지정 하 고, 게시 하 고, 공유 하는 방법을 설명 합니다.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901102"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기 및 공유

대시보드는 Azure Portal의 클라우드 리소스에 대해 집중적으로 구성 된 뷰입니다. 일상 작업을 신속 하 게 시작 하 고 리소스를 모니터링할 수 있는 작업 영역으로 대시보드를 사용 합니다. 예를 들어 프로젝트, 작업 또는 사용자 역할을 기반으로 사용자 지정 대시보드를 빌드합니다.

Azure Portal는 기본 대시보드를 시작 지점으로 제공 합니다. 기본 대시보드를 편집할 수 있습니다. 추가 대시보드를 만들고 사용자 지정 하 고 대시보드를 게시 및 공유 하 여 다른 사용자가 사용할 수 있도록 합니다. 이 문서에서는 새 대시보드를 만들고, 인터페이스를 사용자 지정 하 고, 대시보드를 게시 및 공유 하는 방법을 설명 합니다.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기

이 예제에서는 새 개인 대시보드를 만들고 이름을 할당 합니다. 시작하려면 다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 메뉴에서 **대시보드**를 선택 합니다. 기본 보기는 이미 대시보드로 설정 되어 있을 수 있습니다.

    ![대시보드 열기](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. **새 대시보드**를 선택 합니다.

    ![기본 대시보드의 스크린샷](./media/azure-portal-dashboards/create-new-dashboard.png)

    이 작업을 수행 하면 타일 **갤러리**가 열리며, 여기서 타일을 선택 하 고 타일을 정렬할 빈 그리드를 선택 합니다.

    ![타일 갤러리 및 빈 그리드 스크린샷](./media/azure-portal-dashboards/dashboard-name.png)

1. 대시보드 레이블에서 **내 대시보드** 텍스트를 선택 하 고 사용자 지정 대시보드를 쉽게 식별 하는 데 도움이 되는 이름을 입력 합니다.

1. 페이지 머리글에서 **사용자 지정 완료** 를 선택 하 여 편집 모드를 종료 합니다.

이제 대시보드 보기에 빈 대시보드가 표시 됩니다. 대시보드 이름 옆의 화살표를 선택 하 여 사용할 수 있는 대시보드를 표시 합니다. 이 목록에는 다른 사용자가 만들고 공유한 대시보드가 포함 되어 있을 수 있습니다.

## <a name="edit-a-dashboard"></a>대시보드 편집

이제 대시보드를 편집 하 여 Azure 리소스를 나타내는 타일을 추가, 크기 조정 및 정렬 해 보겠습니다.

### <a name="add-tiles"></a>타일 추가

대시보드에 타일을 추가 하려면 다음 단계를 수행 합니다.

1. ![편집 아이콘을 선택 하 여 페이지 머리글에서 **편집**](./media/azure-portal-dashboards/dashboard-edit-icon.png) 합니다.

    ![대시보드 강조 표시 편집의 스크린샷](./media/azure-portal-dashboards/dashboard-edit.png)

1. **타일 갤러리** 를 찾아보거나 검색 필드를 사용 하 여 원하는 타일을 찾습니다.

1. **추가** 를 선택 하 여 기본 크기 및 위치를 사용 하 여 타일을 대시보드에 추가 합니다. 또는 타일을 그리드로 끌어 원하는 위치에 놓습니다.

대부분의 리소스 페이지에는 명령 모음에 압정 아이콘이 있습니다. 아이콘을 선택 하면 원본 페이지를 나타내는 타일이 현재 활성 상태인 대시보드에 고정 됩니다. 이 메서드는 대시보드에 타일을 추가 하는 다른 방법입니다.

![고정 아이콘이 있는 페이지 명령 모음의 스크린샷](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 둘 이상의 조직에서 작업 하는 경우 **조직 id** 타일을 대시보드에 추가 하 여 리소스가 속한 조직을 명확 하 게 표시 합니다.
>
>

### <a name="resize-or-rearrange-tiles"></a>타일 크기 조정 또는 다시 정렬

타일의 크기를 변경 하거나 대시보드의 타일을 다시 정렬 하려면 다음 단계를 수행 합니다.

1. ![편집 아이콘을 선택 하 여 페이지 머리글에서 **편집**](./media/azure-portal-dashboards/dashboard-edit-icon.png) 합니다.

1. 타일의 오른쪽 위 모퉁이에 있는 상황에 맞는 메뉴를 선택 합니다. 그런 다음 타일 크기를 선택 합니다. 크기를 지 원하는 타일의 오른쪽 아래 모서리에는 타일을 원하는 크기로 끌 수 있는 "핸들"이 포함 됩니다.

    ![타일 크기 메뉴가 열린 대시보드의 스크린샷](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 타일을 선택 하 고 표의 새 위치로 끌어 대시보드를 정렬 합니다.

### <a name="additional-tile-configuration"></a>추가 타일 구성

일부 타일에는 원하는 정보를 표시 하는 추가 구성이 필요할 수 있습니다. 예를 들어 **메트릭 차트** 타일은 **Azure Monitor**의 메트릭을 표시 하도록 설정 되어야 합니다. 또한 타일 데이터를 사용자 지정 하 여 대시보드의 기본 시간 설정을 재정의할 수 있습니다.

설정 해야 하는 타일은 타일을 사용자 지정할 때까지 **타일 구성** 배너를 표시 합니다. 해당 배너를 선택한 다음 필요한 설정을 수행 합니다.

![구성이 필요한 타일의 스크린샷](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown 타일을 사용 하면 대시보드에 사용자 지정 정적 콘텐츠를 표시할 수 있습니다. 이는 기본 지침, 이미지, 하이퍼링크 집합 또는 연락처 정보 일 수도 있습니다. Markdown 타일을 사용 하는 방법에 대한 자세한 내용은 [Azure 대시보드의 markdown 타일을 사용 하 여 사용자 지정 콘텐츠 표시를](azure-portal-markdown-tile.md)참조 하세요.
>
>
### <a name="customize-tile-data"></a>타일 데이터 사용자 지정

대시보드의 데이터는 지난 24 시간 동안의 활동을 자동으로 표시 합니다. 이 타일에 대해 다른 시간 범위를 표시 하려면 다음 단계를 수행 합니다.

1. 상황에 맞는 메뉴 또는 타일의 왼쪽 위 모퉁이에서 필터](./media/azure-portal-dashboards/dashboard-filter.png) ![필터 아이콘에서 **타일 데이터 사용자 지정** 을 선택 합니다.

    ![타일 상황에 맞는 메뉴의 스크린샷](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. **타일 수준에서 대시보드 시간 설정을 재정의**하려면이 확인란을 선택 합니다.

    ![타일 시간 설정을 구성 하는 대화 상자 스크린샷](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 이 타일에 대해 표시할 시간 범위를 선택 합니다. 지난 30 분에서 지난 30 일 동안 선택 하거나 사용자 지정 범위를 정의할 수 있습니다.

1. 표시할 시간 단위를 선택 합니다. 1 분에서 1 개월까지 모든 위치를 표시할 수 있습니다.

1. **적용**을 선택합니다.

## <a name="delete-a-tile"></a>타일 삭제

대시보드에서 타일을 제거 하려면 다음 단계를 수행 합니다.

* 타일의 오른쪽 위 모퉁이에 있는 상황에 맞는 메뉴를 선택한 다음 **대시보드에서 제거**를 선택 합니다. 또는

* **편집 ![편집 아이콘을 선택** 하 여 사용자 지정 모드로 전환](./media/azure-portal-dashboards/dashboard-edit-icon.png) 합니다. 타일의 오른쪽 위 모서리를 마우스로 가리킨 다음 ![삭제 아이콘](./media/azure-portal-dashboards/dashboard-delete-icon.png) 삭제 아이콘을 선택 하 여 대시보드에서 타일을 제거 합니다.

   ![대시보드에서 타일을 제거 하는 방법을 보여 주는 스크린샷](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>대시보드 복제

새 대시보드의 템플릿으로 기존 대시보드를 사용 하려면 다음 단계를 수행 합니다.

1. 대시보드 보기에 복사 하려는 대시보드가 표시 되는지 확인 합니다.

1. 페이지 머리글에서 ![복제 아이콘](./media/azure-portal-dashboards/dashboard-clone.png) **복제**를 선택 합니다.

1. 대시보드 복사본 ( *대시보드 이름의* **복제본** )이 편집 모드에서 열립니다. 이 문서의 이전 단계를 사용 하 여 대시보드 이름을 바꾸고 사용자 지정 합니다.

## <a name="publish-and-share-a-dashboard"></a>대시보드 게시 및 공유

대시보드를 만들 때 기본적으로 비공개입니다. 즉, 사용자가 볼 수 있는 유일한 사용자입니다. 다른 사용자가 대시보드를 사용할 수 있도록 하려면 다른 사용자와 대시보드를 공유할 수 있습니다. 먼저 대시보드를 Azure 리소스로 게시 해야 합니다. 사용자 지정 대시보드를 게시 하 고 공유 하려면 다음 단계를 수행 합니다.

1. 페이지 머리글에서 공유 아이콘](./media/azure-portal-dashboards/dashboard-share-icon.png) **공유** ![선택 합니다. **공유 + access control** 이 열립니다.

1. 올바른 대시보드 이름이 표시 되는지 확인 합니다.

1. **구독 이름을**선택 합니다. 구독에 대한 액세스 권한이 있는 사용자는 공유 대시보드를 사용할 수 있습니다. 개별 타일로 표시 되는 리소스에 대한 액세스는 Azure 역할 기반 액세스 제어에 의해 결정 됩니다.

1. 선택한 구독에 대한 ' 대시보드 ' 리소스 그룹에이 대시보드를 게시 하려면이 확인란을 선택 합니다. 또는 확인란의 선택을 취소 하 고 대신 기존 리소스 그룹에 게시 하도록 선택 합니다.

1. 대시보드 리소스의 위치를 선택 합니다. 다른 리소스를 사용 하 여 대시보드를 찾는 것이 좋습니다. 기존 리소스 그룹에서 선택 하는 경우 대시보드가 해당 리소스 그룹에 자동으로 배치 됩니다.

1. **게시**를 선택합니다.

    ![대시보드 게시 대화 상자 스크린샷](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>공유 대시보드에 대한 액세스 제어 설정

대시보드가 게시 된 후 다음 단계에 따라 대시보드에 대한 액세스 권한이 있는 사용자를 관리 합니다.

1. **공유 + 액세스 제어** 창에서 **사용자 관리**를 선택 합니다.

    ![대시보드 공유 + access control 대화 상자의 스크린샷](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. **Access Control** 페이지가 열립니다. 사용자에 대한 액세스 수준을 검토 하거나 새 역할 할당을 추가할 수 있습니다. 여기에서 역할 할당을 추가 하면 대시보드에 대한 사용 권한을 부여 하 게 됩니다.

> [!NOTE]
> 타일은 조직 내 리소스의 대표적인 뷰입니다. 리소스에 대한 액세스는 역할 기반 액세스 제어 할당을 통해 관리 되며, 구독에서 리소스에 대한 사용 권한이 상속 됩니다. 대시보드에 대한 액세스 권한을 부여 하면 대시보드에 표시 되는 리소스에 대한 사용 권한이 자동으로 할당 되지 않습니다. 공유 대시보드 및 리소스에 대한 역할 기반 액세스 제어에 대한 사용 권한에 대한 자세한 내용은 [역할 기반 Access Control를 사용 하 여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)를 참조 하세요.

### <a name="open-a-shared-dashboard"></a>공유 대시보드 열기

공유 대시보드를 찾아서 열려면 다음 단계를 수행 합니다.

1. 대시보드 이름 옆의 화살표를 선택 합니다.

1. 표시 하려는 대시보드가 나열 되지 않은 경우 표시 된 대시보드 목록에서 선택 하거나 **모든 대시보드를 찾습니다** .

    ![대시보드 선택 메뉴 스크린샷](./media/azure-portal-dashboards/dashboard-browse.png)

1. **유형** 필드에서 **공유 대시보드**를 선택 합니다.

1. 하나 이상의 구독을 선택 합니다. 이름을 기준으로 대시보드를 필터링 하는 텍스트를 입력할 수도 있습니다.

1. 공유 대시보드 목록에서 대시보드를 선택 합니다.

## <a name="delete-a-dashboard"></a>대시보드 삭제

개인 또는 공유 대시보드를 영구적으로 삭제 하려면 다음 단계를 수행 합니다.

1. 대시보드 이름 옆의 목록에서 삭제 하려는 대시보드를 선택 합니다.

1. ![삭제 아이콘을 선택 하 여 페이지 머리글에서 **삭제**](./media/azure-portal-dashboards/dashboard-delete-icon.png) 합니다.

1. 개인 대시보드의 경우 확인 대화 상자에서 **확인** 을 선택 하 여 대시보드를 제거 합니다. 공유 대시보드의 경우 확인 대화 상자에서 확인란을 선택 하 여 게시 된 대시보드가 더 이상 다른 사용자에 게 표시 되지 않음을 확인 합니다. 그런 다음 **확인**을 선택합니다.

    ![삭제 확인 스크린샷](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>다음 단계

* [역할 기반 Access Control를 사용 하 여 Azure 대시보드 공유](azure-portal-dashboard-share-access.md)
* [프로그래밍 방식으로 Azure 대시보드 만들기](azure-portal-dashboards-create-programmatically.md)
