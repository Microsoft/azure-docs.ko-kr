---
title: Azure Portal 설정 내보내기 또는 삭제 | Microsoft Docs
description: 내보내기 하거나 사용자 설정, 개인 대시보드 및 Azure portal에서 사용자 지정 설정을 삭제 하는 방법에 대해 알아봅니다.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551670"
---
# <a name="export-or-delete-user-settings"></a>사용자 설정 내보내기 또는 삭제

사용자 지정 환경을 만들려면 Azure portal에서 설정 및 기능을 사용할 수 있습니다. 사용자 지정 설정에 대 한 정보는 Azure에 저장 됩니다. 내보내기 하거나 다음 사용자 데이터를 삭제할 수 있습니다.

* Azure portal에서 개인 대시보드
* 즐겨 찾는 구독 또는 디렉터리 및 마지막에 로그인 한 디렉터리와 같은 사용자 설정
* 테마 및 기타 사용자 지정 포털 설정

내보내기 및 삭제 하기 전에 설정을 검토 하는 것이 좋습니다. 대시보드를 다시 작성 하거나 사용자 지정 설정을 다시 실행 시간이 오래 걸릴 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>내보내기 또는 포털 설정 삭제

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 헤더에서 선택 **설정을**합니다.

    ![포털 설정 기어를 보여주는 스크린샷](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. **모든 설정 내보내기** 또는 **모든 설정 및 개인 대시보드 삭제**를 선택합니다.

    ![포털을 보여 주는 스크린샷 내보내고 설정 삭제](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      다음 표에서 이러한 작업을 설명 합니다.

      | 액션(Action) | 설명 |
      | --- | --- |
      | **모든 설정 내보내기** | 사용자 설정과 색 테마, 즐겨찾기 및 개인 대시보드와 같은 포함 된.json 파일을 만듭니다.|
      | **모든 설정 및 개인 대시보드 삭제** | 개인 대시보드 및 포털에 대 한 다른 사용자 지정 설정에 대 한 모든 링크를 삭제 합니다. |

> [!NOTE]
> 사용자 설정의 동적 특성 및 데이터 손상의 위험으로 인해.json 파일에서 설정을 가져올 수 없습니다.
>
>


## <a name="next-steps"></a>다음 단계

* [Azure 대시보드 만들기 및 공유](azure-portal-dashboard-share-access.md)
* [추가, 제거 및 정렬 즐겨찾기](azure-portal-add-remove-sort-favorites.md)
