---
title: Azure Portal 대시보드 만들기 및 공유 | Microsoft Docs
description: 이 문서에서는 Azure Portal에서 대시보드를 만들고 편집하는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552712"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Azure Portal에서 대시보드 만들기 및 공유
여러 개의 대시보드를 만들고 Azure 구독에 액세스할 수 있는 다른 사용자와 공유할 수 있습니다.  이 문서에서는 대시보드 만들기, 편집, 게시 및 액세스 관리의 기본 사항을 알아봅니다.

## <a name="create-a-dashboard"></a>대시보드 만들기
대시보드를 만들려면 현재 대시보드 이름 옆에 있는 **새 대시보드** 단추를 선택합니다.  

![대시보드 만들기](./media/azure-portal-dashboards/new-dashboard.png)

이 작업은 새 비어 있는 개인 대시보드를 만들고 대시보드의 이름을 지정하고 타일을 추가하거나 다시 정렬할 수 있는 사용자 지정 모드를 설정합니다.  이 모드에서는 축소 가능한 타일 갤러리가 왼쪽 탐색 메뉴보다 우선합니다.  타일 갤러리를 사용하면 다양한 방법으로 Azure 리소스에 대한 타일을 찾을 수 있습니다. [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups), 리소스 형식, [태그](../azure-resource-manager/resource-group-using-tags.md) 또는 이름별로 리소스의 이름을 검색하여 찾을 수 있습니다.  

![대시보드 사용자 지정](./media/azure-portal-dashboards/customize-dashboard.png)

타일을 원하는 대시보드 화면에 끌어서 놓아 추가합니다.

특정 리소스에 연결되지 않은 타일에 대한 **일반** 이라는 새 범주가 있습니다.  이 예제에서는 Markdown 타일을 고정합니다.  이 타일을 사용하여 대시보드에 사용자 지정 콘텐츠를 추가합니다.  타일은 일반 텍스트인 [Markdown 구문](https://daringfireball.net/projects/markdown/syntax)및 제한된 일련의 HTML을 지원합니다.  (안전을 위해 `<script>` 태그를 삽입하거나 포털을 방해할 수 있는 CSS의 특정 스타일 요소를 사용하는 등의 작업을 수행할 수 없습니다.) 

![Markdown 추가](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>대시보드 편집
대시보드를 만든 후에 타일 갤러리 또는 블레이드의 타일 표현에서 타일을 고정할 수 있습니다. 리소스 그룹의 표현을 고정해 보겠습니다. 해당 항목을 검색할 때 또는 리소스 그룹 블레이드에서 고정할 수 있습니다. 두 가지 방법은 리소스 그룹의 타일 표시를 고정합니다.

![대시보드에 고정](./media/azure-portal-dashboards/pin-to-dashboard.png)

항목을 고정한 후에 대시보드에 나타납니다.

![대시보드 보기](./media/azure-portal-dashboards/view-dashboard.png)

Markdown 타일 및 대시보드에 고정된 리소스 그룹이 있으므로 타일의 크기를 조정하고 적합한 레이아웃으로 다시 정렬할 수 있습니다.

"..."에 마우스를 가져가고 선택하거나 타일을 마우스 오른쪽 단추로 클릭하여 해당 타일에 대한 상황에 맞는 모든 명령을 볼 수 있습니다. 기본적으로 두 가지 항목이 있습니다.

1. **대시보드에서 고정 해제** – 대시보드에서 타일을 제거합니다.
2. **사용자 지정** – 사용자 지정 모드로 실행됩니다.

![타일 사용자 지정](./media/azure-portal-dashboards/customize-tile.png)

사용자 지정을 선택하여 타일의 크기를 조정하고 순서를 변경합니다. 타일 크기를 조정하려면 다음 이미지에 나와 있는 것처럼 상황에 맞는 메뉴에서 새 크기를 선택합니다.

![타일 크기 조정](./media/azure-portal-dashboards/resize-tile.png)

또는 타일이 모든 크기를 지원하는 경우 하단 오른쪽 모서리를 원하는 크기로 끌어올 수 있습니다.

![타일 크기 조정](./media/azure-portal-dashboards/resize-corner.png)

타일의 크기를 조정한 후에 대시보드를 봅니다.

![타일 보기](./media/azure-portal-dashboards/view-tile.png)

대시보드의 사용자 지정을 완료한 후에 사용자 지정 모드를 종료하려면 **사용자 지정 완료**를 선택하거나 상황에 맞는 메뉴에서 **사용자 지정 완료**를 마우스 오른쪽 단추로 클릭하고 선택합니다.

## <a name="publish-a-dashboard-and-manage-access-control"></a>대시보드 게시 및 액세스 제어 관리
대시보드를 만들 때 기본적으로 비공개입니다. 즉, 볼 수 있는 유일한 사람은 사용자입니다.  다른 사람이 볼 수 있도록 하려면 다른 대시보드 명령 옆에 나타나는 **공유** 단추를 사용합니다.

![대시보드 공유](./media/azure-portal-dashboards/share-dashboard.png)

게시하려는 대시보드에 대한 구독 및 리소스 그룹을 선택하라는 메시지가 표시됩니다. 에코시스템에 대시보드를 매끄럽게 통합하기 위해 공유 대시보드를 Azure 리소스로 구현했습니다(따라서 전자 메일 주소를 입력하여 공유할 수 없습니다).  포털에서 대부분 타일에 표시되는 정보에 대한 액세스는 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)로 제어됩니다. 액세스 제어 관점에서 공유 대시보드는 가상 머신 또는 저장소 계정과 차이가 없습니다.  

Azure 구독을 보유하고 구독의 **소유자**, **참여자** 또는 **읽기 권한자** 역할에 할당된 팀의 구성원이 있다고 가정해 보겠습니다.  소유자 또는 참여자인 사용자는 해당 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다.  읽기 권한자인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 또는 삭제할 수 없습니다.  읽기 권한자 액세스를 보유한 사용자는 공유 대시보드에 대한 로컬 편집을 만들 수 있지만 해당 변경 내용을 서버로 다시 게시할 수 없습니다.  그러나 사용하기 위해 대시보드의 개인 복사본을 만들 수 있습니다.  늘 그렇듯이 대시보드의 개별 타일은 해당하는 리소스에 따라 고유한 액세스 제어 규칙을 적용합니다.  

편의를 위해 포털의 게시 환경에서는 **대시보드**라는 리소스 그룹에 대시보드를 배치한 위치의 패턴을 안내합니다.  

![대시보드 게시](./media/azure-portal-dashboards/publish-dashboard.png)

또한 특정 리소스 그룹에 대시보드를 게시할 수 있습니다.  해당 대시보드에 대한 액세스 제어는 리소스 그룹에 대한 액세스 제어와 일치합니다.  해당 리소스 그룹에서 리소스를 관리할 수 있는 사용자는 대시보드에도 액세스 권한이 있습니다.

![리소스 그룹에 대시보드 게시](./media/azure-portal-dashboards/publish-to-resource-group.png)

대시보드를 게시한 후에 **공유 + 액세스** 제어 창이 새로 고쳐지고 대시보드에 대한 사용자 액세스를 관리하는 링크를 포함하여 게시된 대시보드에 대한 정보를 보여 줍니다.  이 링크는 Azure 리소스에 대한 액세스를 관리하는 데 사용되는 표준 역할 기반 Access Control 블레이드를 시작합니다.  언제든지 **공유**를 선택하여 이 보기로 돌아올 수 있습니다.

![액세스 제어 관리](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>다음 단계
* 리소스를 관리 하려면 참조 [Azure portal을 사용 하 여 Azure 관리 리소스](../azure-resource-manager/manage-resources-portal.md)합니다.
* 리소스를 배포하려면 [Resource Manager 템플릿 및 Azure Portal을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md)를 참조하세요.

