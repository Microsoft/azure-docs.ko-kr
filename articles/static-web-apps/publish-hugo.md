---
title: '자습서: Azure Static Web Apps에 Hugo 사이트 게시'
description: Azure Static Web Apps에 Hugo 애플리케이션을 배포하는 방법에 대해 알아봅니다.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: ff408f114784fa3f0b8fab49521b5ec7ec2be102
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797720"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>자습서: Azure Static Web Apps 미리 보기에 Hugo 사이트 게시

이 문서에서는 [Azure Static Web Apps](overview.md)에 [Hugo](https://gohugo.io/) 웹 애플리케이션을 만들고 배포하는 방법을 보여줍니다. 최종 결과는 앱이 빌드되고 게시되는 방식을 제어하는 관련 GitHub Actions를 포함하는 새로운 Azure Static Web App입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Hugo 앱 만들기
> - Azure Static Web Apps 설정
> - Azure에 Hugo 앱 배포

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- GitHub 계정. 계정이 없는 경우 [체험 계정을 만들](https://github.com/join) 수 있습니다.

## <a name="create-a-hugo-app"></a>Hugo 앱 만들기

Hugo CLI(명령줄 인터페이스)를 사용하여 Hugo 앱을 만듭니다.

1. OS에서 Hugo에 대한 [설치 가이드](https://gohugo.io/getting-started/installing/)를 따르세요.

1. 터미널 열기

1. Hugo CLI를 실행하여 새 앱을 만듭니다.

   ```bash
   hugo new site static-app
   ```

1. 새로 만든 앱으로 이동합니다.

   ```bash
   cd static-app
   ```

1. Git 리포지토리를 초기화합니다.

   ```bash
    git init
   ```

1. 다음으로, 테마를 git 하위 모듈로 설치한 다음, Hugo 구성 파일에서 테마를 지정하여 사이트에 테마를 추가합니다.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 변경 내용을 커밋합니다.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>GitHub에 애플리케이션 푸시

Azure Static Web Apps에 연결하려면 GitHub의 리포지토리가 필요합니다. 다음 단계에서는 사이트용 리포지토리를 만드는 방법을 보여줍니다.

1. **hugo-static-app**이라는 [https://github.com/new](https://github.com/new)에서 빈 GitHub 리포지토리(추가 정보를 만들지 않음)를 만듭니다.

1. 로컬 리포지토리에 GitHub 리포지토리를 원격으로 추가합니다. 다음 명령에서 `<YOUR_USER_NAME>` 자리 표시자 대신 GitHub 사용자 이름을 추가해야 합니다.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. 로컬 리포지토리를 GitHub로 푸시합니다.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>웹앱 배포

다음 단계에서는 새 정적 사이트 앱을 만들어 프로덕션 환경에 배포하는 방법을 보여줍니다.

### <a name="create-the-application"></a>애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기**를 클릭합니다.
1. **Static Web Apps**를 검색합니다.
1. **Static Web Apps(미리 보기)** 를 클릭합니다.
1. **만들기**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="포털에서 Azure Static Web Apps 리소스 만들기":::

1. **구독**의 경우 나열된 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다.

1. _리소스 그룹_에서 **새로 만들기**를 선택합니다. _새 리소스 그룹 이름_에서 **hugo-static-app**을 입력하고 **확인**을 선택합니다.

1. 그런 다음, **이름** 상자에 있는 앱 이름을 선택합니다. 유효한 문자에는 `a-z`, `A-Z`, `0-9` 및 `-`가 포함됩니다.

1. _지역_에 대해 가까운 사용 가능한 지역을 선택합니다.

1. _SKU_에 대해 **무료**를 선택합니다.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="세부 정보 입력":::

1. **GitHub로 로그인** 단추를 클릭합니다.

1. 리포지토리를 만든 **조직**을 선택합니다.

1. **hugo-static-app**을 _리포지토리_로 선택합니다.

1. _분기_에 대해 **마스터**를 선택합니다.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="완료된 GitHub 정보":::

### <a name="build"></a>빌드

그런 다음, 빌드 프로세스에서 앱을 빌드하는 데 사용하는 구성 설정을 추가합니다. 다음 설정은 GitHub Action 워크플로 파일을 구성합니다.

1. **다음: 빌드 >** 단추를 클릭하여 빌드 구성을 편집합니다.

1. _앱 위치_를 **/** 로 설정합니다.

1. _앱 아티팩트 위치_를 **공개**로 설정합니다.

   현재 API를 배포하지 않으므로 _API 위치_에 대한 값이 필요하지 않습니다.

### <a name="review-and-create"></a>검토 및 만들기

1. **검토 + 만들기** 단추를 클릭하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기**를 클릭하여 Azure Static Web Apps 만들기를 시작하고 배포를 위한 GitHub Action을 프로비저닝합니다.

1. GitHub Action이 완료될 때까지 기다립니다.

1. 새로 만든 Azure Static Web Apps 리소스의 Azure Portal _개요_ 창에서 _URL_ 링크를 클릭하여 배포된 애플리케이션을 엽니다.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="배포된 애플리케이션":::

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](custom-domain.md)
