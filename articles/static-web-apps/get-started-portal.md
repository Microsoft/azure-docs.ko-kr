---
title: '빠른 시작: Azure Portal을 사용하여 Azure Static Web Apps를 통해 첫 번째 정적 웹앱 빌드'
description: Azure Portal을 사용하여 Azure Static Web Apps 인스턴스를 빌드하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752862"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 첫 번째 정적 웹앱 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 웹 애플리케이션을 Azure Static 웹앱에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이제 리포지토리가 생성되었으므로 Azure Portal에서 정적 웹앱을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 선택
1. **Static Web Apps**를 검색합니다.
1. **Static Web Apps(미리 보기)** 선택
1. **만들기**를 선택합니다.

_기본 사항_ 탭에서 새 앱을 구성하고 GitHub 리포지토리에 연결하여 시작합니다.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="기본 사항 탭":::

1. _Azure 구독_ 선택
1. 새 _리소스 그룹_ 선택 또는 만들기
1. 앱 이름을 **my-first-static-web-app**으로 지정합니다.
      1. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.
1. 가장 가까운 _지역_을 선택합니다.
1. **무료** _SKU_를 선택합니다.
1. **GitHub로 로그인** 단추를 선택하고 GitHub로 인증합니다.

GitHub로 로그인한 후 리포지토리 정보를 입력합니다.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="리포지토리 세부 정보":::

1. 원하는 _조직_을 선택합니다.
1. _리포지토리_ 드롭다운에서 **my-first-web-static-app**을 선택합니다.
1. _분기_ 드롭다운에서 **마스터**를 선택합니다.
1. 페이지 맨 아래에서 **다음: 빌드 >** 단추를 클릭하여 빌드 구성을 편집합니다.

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="다음 빌드 단추":::

> [!NOTE]
> 리포지토리가 표시되지 않는 경우 GitHub에서 Azure Static Web Apps에 권한을 부여해야 할 수 있습니다. GitHub 리포지토리로 이동하여 **설정 > 애플리케이션 > 권한 부여된 OAuth 앱**으로 이동하고 **Azure Static Web Apps**를 선택한 다음 **권한 부여**를 선택합니다. 조직 리포지토리의 경우 사용 권한을 부여하려면 조직의 소유자여야 합니다.

1. _빌드_ 탭에서 선호하는 프런트 엔드 프레임워크와 관련된 구성 세부 정보를 추가합니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    - _앱 위치_ 상자에서 기본값을 선택 취소합니다.
    - _Api 위치_ 상자에서 기본값을 선택 취소합니다.
    - _앱 아티팩트 위치_ 상자에서 기본값을 선택 취소합니다.

    # <a name="angular"></a>[Angular](#tab/angular)

    - _앱 위치_ 상자에서 기본값을 선택 취소합니다.
    - _Api 위치_ 상자에서 기본값을 선택 취소합니다.
    - _앱 아티팩트 위치_ 상자에 **dist/angular-basic**을 입력합니다.

    # <a name="react"></a>[React](#tab/react)

    - _앱 위치_ 상자에서 기본값을 선택 취소합니다.
    - _Api 위치_ 상자에서 기본값을 선택 취소합니다.
    - _앱 아티팩트 위치_ 상자에 **빌드**를 입력합니다.

    # <a name="vue"></a>[Vue](#tab/vue)

    - _앱 위치_ 상자에서 기본값을 선택 취소합니다.
    - _Api 위치_ 상자에서 기본값을 선택 취소합니다.
    - _앱 아티팩트 위치_ 상자에 **dist**를 입력합니다.

    ---

1. **검토 + 만들기**를 선택합니다.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="만들기 단추 검토":::

    > [!NOTE]
    > 앱을 만든 후 이러한 값을 변경하려면 [워크플로 파일](github-actions-workflow.md)을 편집하면 됩니다.

1. **만들기**를 선택합니다.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="만들기 단추":::

1. **리소스로 이동**을 선택합니다.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="리소스로 이동 단추":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 다음 단계를 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 위쪽 검색 창에서 **my-first-web-static-app**을 검색합니다.
1. 앱 이름을 선택합니다.
1. **삭제** 단추를 선택합니다.
1. **예**를 선택하여 삭제 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
