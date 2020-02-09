---
title: Azure Portal에서 삭제 된 대시보드 복구 | Microsoft Docs
description: Azure Portal에서 게시 된 대시보드를 삭제 하면 대시보드를 복구할 수 있습니다.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760201"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Azure Portal에서 삭제 된 대시보드 복구

Azure Portal에서 _게시_ 된 대시보드를 삭제 하는 경우 삭제 후 14 일 이내에 해당 대시보드를 복구할 수 있습니다. 대시보드가 게시 되지 않은 경우에는 복구할 수 없으므로 다시 빌드해야 합니다. 대시보드를 게시 하는 방법에 대한 자세한 내용은 [대시보드 게시](azure-portal-dashboard-share-access.md#publish-dashboard)를 참조 하세요. 게시 된 대시보드를 복구 하려면 다음 단계를 따르세요.

1. Azure Portal 메뉴에서 **리소스 그룹**을 선택한 다음 대시보드를 게시 한 리소스 그룹을 선택 합니다 (기본적으로 **대시보드**이름이 지정 됨).

1. **활동 로그**에서 **대시보드 삭제** 작업을 확장 합니다. **변경 기록** 탭을 선택한 다음 **\<삭제 된 리소스\>** 를 선택 합니다.

    ![변경 기록 탭의 스크린샷](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 왼쪽 창의 내용을 선택 하 고 복사한 다음 확장명이 _._ i n i 파일 확장명을 사용 하 여 텍스트 파일에 저장 합니다. 포털에서는 JSON 파일을 사용 하 여 대시보드를 다시 만듭니다.

    ![변경 기록 차이의 스크린샷](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Azure Portal 메뉴에서 **대시보드**를 선택한 다음 **업로드**를 선택 합니다.

    ![대시보드 업로드 스크린샷](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 저장 한 JSON 파일을 선택 합니다. 포털에서 삭제 된 대시보드와 동일한 이름 및 요소를 사용 하 여 대시보드를 다시 만듭니다.

1. **공유** 를 선택 하 여 대시보드를 게시 하 고 적절 한 액세스 제어를 다시 설정 합니다.

    ![대시보드 공유의 스크린샷](media/recover-shared-deleted-dashboard/dashboard-share.png)
