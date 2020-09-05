---
title: '자습서: 사용자 인터페이스 사용자 지정'
titleSuffix: Azure AD B2C
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C에서 애플리케이션의 UI(사용자 인터페이스)를 사용자 지정하는 방법을 알아봅니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9ca6d91be95bfb1a47f85b20f3775a57518ffcf
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922135"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C의 사용자 환경 인터페이스 사용자 지정

등록, 로그인 및 프로필 편집과 같은 보다 일반적인 사용자 환경을 위해 Azure AD B2C(Azure Active Directory B2C)에서 [사용자 흐름](user-flow-overview.md)을 사용할 수 있습니다. 이 자습서의 정보는 고유한 HTML 및 CSS 파일을 사용하여 이러한 환경의 [UI(사용자 인터페이스)를 사용자 지정](customize-ui-overview.md)하는 방법을 알아보는 데 도움이 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * UI 사용자 지정 파일 만들기
> * 파일을 사용하도록 사용자 흐름 업데이트
> * 사용자 지정된 UI 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).

## <a name="create-customization-files"></a>사용자 지정 파일 만들기

Azure Storage 계정 및 컨테이너를 만든 다음, 기본 HTML 및 CSS 파일을 컨테이너에 배치합니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

여러 가지 방법으로 파일을 저장할 수 있지만, 이 자습서에서는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 저장합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure 구독이 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 구독이 포함된 디렉터리를 선택합니다. 이 디렉터리는 Azure B2C 테넌트가 포함된 디렉터리와 다릅니다.
3. Azure Portal의 왼쪽 상단 모서리에서 모든 서비스를 선택하고 **스토리지 계정**을 검색하여 선택합니다.
4. **추가**를 선택합니다.
5. **리소스 그룹** 아래에서 **새로 만들기**를 선택하고 새 리소스 그룹의 이름을 입력한 다음, **확인**을 클릭합니다.
6. 스토리지 계정의 이름을 입력합니다. 선택하는 이름은 Azure 전체에서 고유해야 하고, 3~24자 사이여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
7. 스토리지 계정의 위치를 선택하거나 기본 위치를 적용합니다.
8. 다른 모든 기본값을 적용하고 **검토 + 만들기**를 선택한 다음, **만들기**를 클릭합니다.
9. 스토리지 계정을 만든 후 **리소스로 이동**을 선택합니다.

### <a name="create-a-container"></a>컨테이너 만들기

1. 스토리지 계정의 개요 페이지에서 **Blobs**를 선택합니다.
2. **컨테이너**를 선택하고, 컨테이너 이름을 입력하고, **Blob(BLOB 전용 익명 읽기 액세스)** 를 선택한 다음, **확인**을 클릭합니다.

### <a name="enable-cors"></a>CORS를 사용하도록 설정

 브라우저의 Azure AD B2C 코드는 현대적이고 표준적인 방법을 사용하여 사용자 흐름에서 지정하는 URL의 사용자 지정 콘텐츠를 로드합니다. CORS(원본 간 리소스 공유)를 사용하면 웹 페이지의 제한된 리소스를 다른 도메인에서 요청할 수 있습니다.

1. 메뉴에서 **CORS**를 선택합니다.
2. **허용된 원본**에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예들 들어 `https://fabrikam.b2clogin.com`입니다. 테넌트 이름을 입력할 때는 모두 소문자를 사용해야 합니다.
3. **허용된 메서드**에 대해 `GET`, `PUT` 및 `OPTIONS`를 선택합니다.
4. **허용된 헤더**에 별표(*)를 입력합니다.
5. **노출된 헤더**에 별표(*)를 입력합니다.
6. **최대 기간**에 200을 입력합니다.

    ![Azure Portal에 있는 Azure Blob 스토리지의 CORS 구성 페이지](./media/tutorial-customize-ui/enable-cors.png)

5. **저장**을 클릭합니다.

### <a name="create-the-customization-files"></a>사용자 지정 파일 만들기

등록 환경의 UI를 사용자 지정하려면 간단한 HTML 및 CSS 파일을 만드는 것으로 시작합니다. 원하는 방식으로 HTML을 구성할 수 있지만 식별자가 `api`인 **div** 요소가 있어야 합니다. 예들 들어 `<div id="api"></div>`입니다. Azure AD B2C는 페이지가 표시될 때 `api` 컨테이너에 요소를 삽입합니다.

1. 로컬 폴더에서 다음 파일을 만들고 `your-storage-account`를 스토리지 계정 이름으로 변경하고 `your-container`를 생성된 컨테이너의 이름으로 변경해야 합니다. 예들 들어 `https://store1.blob.core.windows.net/b2c/style.css`입니다.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    원하는 방식으로 페이지를 디자인할 수 있지만 **api** div 요소는 사용자가 만든 모든 HTML 사용자 지정 파일에 필요합니다.

3. 파일을 *custom-ui.html*로 저장합니다.
4. Azure AD B2C가 삽입하는 요소를 포함하여 등록 또는 로그인 페이지의 모든 요소를 센터에 배치하는 다음과 같은 간단한 CSS를 만듭니다.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. 파일을 *style.css*로 저장합니다.

### <a name="upload-the-customization-files"></a>사용자 지정 파일 업로드

이 자습서에서는 Azure AD B2C가 액세스할 수 있도록 스토리지 계정에서 만든 파일을 저장합니다.

1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **스토리지 계정**을 검색하여 선택합니다.
2. 생성한 스토리지 계정을 선택하고 **Blobs**를 선택한 다음, 생성한 컨테이너를 선택합니다.
3. **업로드**를 선택하고 *custom-ui.html* 파일을 선택한 다음, **업로드**를 클릭합니다.

    ![업로드 단추와 파일이 강조 표시된 포털의 Blob 페이지 업로드](./media/tutorial-customize-ui/upload-blob.png)

4. 자습서 뒷부분에서 사용하기 위해 업로드한 파일의 URL을 복사합니다.
5. *style.css* 파일에 대해 3 ~ 4단계를 반복합니다.

## <a name="update-the-user-flow"></a>사용자 흐름 업데이트

1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
2. **사용자 흐름**, *B2C_1_signupsignin1* 사용자 흐름을 차례로 선택합니다.
3. **페이지 레이아웃**을 선택한 다음, **통합 가입 또는 로그인 페이지** 아래에서 **사용자 지정 페이지 콘텐츠 사용**에 대해 **예**를 클릭합니다.
4. **사용자 지정 페이지 URI**에서 이전에 기록한 *custom-ui.html* 파일의 URI를 입력합니다.
5. 페이지 맨 위에서 **저장**을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트에서 **사용자 흐름**을 선택하고, *B2C_1_signupsignin1* 사용자 흐름을 선택합니다.
2. 페이지 맨 위에서 **사용자 흐름 실행**을 클릭합니다.
3. **사용자 흐름 실행** 단추를 클릭합니다.

    ![등록 또는 로그인 사용자 흐름에 대한 사용자 흐름 페이지 실행](./media/tutorial-customize-ui/run-user-flow.png)

    생성한 CSS 파일에 따라 요소가 중심에 있는 다음 예제와 유사한 페이지가 표시됩니다.

    ![사용자 지정 UI 요소를 사용하여 등록 또는 로그인 페이지를 표시하는 웹 브라우저](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * UI 사용자 지정 파일 만들기
> * 파일을 사용하도록 사용자 흐름 업데이트
> * 사용자 지정된 UI 테스트

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 UI 사용자 지정](customize-ui-overview.md)
