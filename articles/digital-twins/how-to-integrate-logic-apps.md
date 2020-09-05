---
title: Logic Apps와 통합
titleSuffix: Azure Digital Twins
description: 사용자 지정 커넥터를 사용 하 여 Azure Digital Twins에 Logic Apps를 연결 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 2fc2db54217756ba0f4f7d643b1bc12ad2668209
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848771"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>사용자 지정 커넥터를 사용 하 여 Logic Apps와 통합

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 는 앱 및 서비스에서 워크플로를 자동화 하는 데 도움이 되는 클라우드 서비스입니다. Azure Digital Twins Api에 Logic Apps를 연결 하 여 Azure 디지털 쌍 및 해당 데이터를 중심으로 이러한 자동화 된 흐름을 만들 수 있습니다.

Azure Digital Twins는 현재 Logic Apps에 대해 인증 된 (미리 작성 된) 커넥터를가지고 있지 않습니다. 대신, Azure Digital Twins와 Logic Apps를 사용 하는 현재 프로세스는 Logic Apps 작업 하도록 수정 된 [사용자 지정 Azure Digital Twins Swagger 파일](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json) 을 사용 하 여 [**사용자 지정 Logic Apps 커넥터**](../logic-apps/custom-connector-overview.md)를 만드는 것입니다.

이 문서에서는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 Azure Digital twins 인스턴스에 Logic Apps를 연결 하는 데 사용할 수 있는 **사용자 지정 커넥터를 만듭니다** . 그런 다음이 연결을 사용 하는 **논리 앱을 만듭니다** . 예를 들어 타이머에서 트리거되는 이벤트는 Azure Digital twins 인스턴스의 쌍을 자동으로 업데이트 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 **[체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 을 만듭니다.

이 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

이 문서에서 Logic Apps 하기 위해 Azure Digital Twins 인스턴스를 연결 하려면 **Azure Digital Twins 인스턴스가** 이미 설정 되어 있어야 합니다. 

지금 새 인스턴스를 설정 해야 하는 경우에는 자동화 된 배포 스크립트 샘플을 실행 하는 것이 가장 간단한 방법입니다. [*방법: 인스턴스 및 인증 (스크립팅된) 설정*](how-to-set-up-instance-scripted.md) 의 지침에 따라 새 인스턴스와 필요한 Azure AD 앱 등록을 설정 합니다. 지침에는 각 단계를 성공적으로 완료했으며 새 인스턴스를 사용할 준비가 되었는지 확인하는 단계도 포함되어 있습니다.

이 자습서에서는 인스턴스를 설정할 때 다음 값이 필요합니다. 이러한 값을 다시 수집해야 하는 경우 설정 문서의 해당 섹션에 대한 아래 링크를 사용하여 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다.
* Azure Digital Twins 인스턴스 **_호스트 이름_**([포털에서 찾기](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD 앱 등록 **_애플리케이션(클라이언트) ID_**([포털에서 찾기](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD 앱 등록 **_디렉터리(테넌트) ID_**([포털에서 찾기](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>앱 등록 클라이언트 암호 가져오기

또한 Azure AD 앱 등록에 대 한 **_클라이언트 암호_** 를 만들어야 합니다. 이렇게 하려면 Azure Portal에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 찾을 수 있습니다. 목록에서 등록을 선택 하 여 세부 정보를 엽니다. 

등록 메뉴에서 *인증서 및 비밀* 을 적중 하 고 *+ 새 클라이언트 암호*를 선택 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD 앱 등록의 포털 뷰입니다. 리소스 메뉴에서 ' 인증서 및 암호 '를 강조 표시 하 고 ' 새 클라이언트 암호 ' 주위의 페이지에서 강조 표시 합니다.":::

설명 및 만료에 사용할 값을 입력 하 고 *추가*를 누릅니다.
*인증서 및 암호* 페이지의 클라이언트 암호 목록에 비밀이 추가 됩니다. 나중에 사용 하기 위해 값을 기록해 둡니다 (복사 아이콘을 사용 하 여 클립보드로 복사할 수도 있음).

### <a name="add-a-digital-twin"></a>디지털 쌍 추가

이 문서에서는 Logic Apps를 사용 하 여 Azure Digital Twins 인스턴스의 쌍을 업데이트 합니다. 계속 하려면 인스턴스에 하나 이상의 쌍을 추가 해야 합니다. 

[DigitalTwins api](how-to-use-apis-sdks.md), [.net (c #) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)또는 [Azure Digital 쌍 CLI](how-to-use-cli.md)를 사용 하 여 쌍를 추가할 수 있습니다. 이러한 방법을 사용 하 여 쌍를 만드는 방법에 대 한 자세한 단계 [*는 방법: 디지털 쌍 관리*](how-to-manage-twin.md)를 참조 하세요.

사용자가 만든 인스턴스에 쌍의 쌍 **_ID_** 가 필요 합니다.

## <a name="create-custom-logic-apps-connector"></a>사용자 지정 Logic Apps 커넥터 만들기

이 단계에서는 Azure Digital Twins Api에 대 한 [사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md) 를 만듭니다. 이 작업을 수행한 후에는 다음 섹션에서 논리 앱을 만들 때 Azure Digital Twins에 연결할 수 있습니다.

Azure Portal에서 [Logic Apps 사용자 지정 커넥터](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) 페이지로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 검색할 수 있습니다. 적중 *+ 추가*

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure Portal의 ' Logic Apps 사용자 지정 커넥터 ' 페이지입니다. ' 추가 ' 단추를 강조 표시 합니다.":::

다음에 나오는 *Logic Apps 사용자 지정 커넥터 만들기* 페이지에서 구독 및 리소스 그룹을 선택 하 고 새 커넥터에 대 한 이름 및 배포 위치를 선택 합니다. *검토 + 만들기*를 누릅니다. 그러면 아래에서 *만들기* 를 눌러 리소스를 만들 수 있는 *검토 + 만들기* 탭으로 이동 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure Portal에서 ' Logic Apps 사용자 지정 커넥터 만들기 ' 페이지의 ' 검토 + 만들기 ' 탭 ' 만들기 ' 단추를 강조 표시 합니다.":::

커넥터에 대 한 배포 페이지로 이동 합니다. 배포가 완료 되 면 *리소스로 이동* 단추를 눌러 포털에서 커넥터의 세부 정보를 확인 합니다.

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins 용 커넥터 구성

다음으로, Azure Digital Twins에 연결 하기 위해 만든 커넥터를 구성 합니다.

먼저 Logic Apps 작업 하도록 수정 된 사용자 지정 Azure Digital Twins Swagger를 다운로드 합니다. [이 링크](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json)에서 *digitaltwins.js을* 다운로드 합니다.

그런 다음 Azure Portal의 커넥터 개요 페이지에서 *편집*을 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="이전 단계에서 만든 커넥터에 대 한 ' 개요 ' 페이지입니다. ' 편집 ' 단추를 강조 표시 합니다.":::

뒤에 나오는 *Logic Apps 사용자 지정 커넥터 편집* 페이지에서 다음 정보를 구성 합니다.
* **사용자 지정 커넥터**
    - API 끝점: REST (기본값 유지)
    - 가져오기 모드: OpenAPI 파일 (기본값 유지)
    - 파일: 앞에서 다운로드 한 사용자 지정 Swagger 파일입니다. *가져오기*를 눌러 컴퓨터에서 파일을 찾은 다음 *열기*를 누릅니다.
* **일반 정보**
    - 아이콘, 아이콘 배경색, 설명: 원하는 모든 값을 입력 합니다.
    - 구성표: HTTPS (기본값 유지)
    - Host: Azure Digital Twins 인스턴스의 *호스트 이름* 입니다.
    - 기준 URL:/(기본값 유지)

그런 다음 창 맨 아래에 있는 *보안* 단추를 눌러 다음 구성 단계로 이동 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="' Logic Apps 사용자 지정 커넥터 편집 ' 페이지 아래쪽의 스크린샷 보안을 계속 하려면 단추 주변 강조 표시":::

보안 단계에서 다음 정보를 *편집* 하 고 구성 합니다.
* **인증 유형**: OAuth 2.0
* **OAuth 2.0**:
    - Id 공급자: Azure Active Directory
    - 클라이언트 ID: Azure AD 앱 등록에 대 한 *응용 프로그램 (클라이언트) id*
    - 클라이언트 암호: Azure AD 앱 등록을 위한 [*필수 구성 요소*](#prerequisites) 에서 만든 *클라이언트 암호* 입니다.
    - 로그인 URL: https://login.windows.net (기본값 유지)
    - 테 넌 트 ID: Azure AD 앱 등록에 대 한 *디렉터리 (테 넌 트) id*
    - 리소스 URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 범위: 디렉터리. AccessAsUser. 모두
    - 리디렉션 URL: (지금은 기본값 유지)

리디렉션 URL 필드에는 *리디렉션 url을 생성 하는 사용자 지정 커넥터 저장*이 표시 됩니다. 이 작업을 수행 하려면 창 위쪽에 있는 *업데이트 커넥터* 를 방문 하 여 커넥터 설정을 확인 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="' Logic Apps 사용자 지정 커넥터 편집 ' 페이지 위쪽의 스크린샷 ' 커넥터 업데이트 ' 단추를 강조 표시 합니다.":::

<!-- Success message? didn't see one -->

리디렉션 URL 필드로 돌아가서 생성 된 값을 복사 합니다. 이 버전 번호는 다음 단계에서 사용합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="' Logic Apps 사용자 지정 커넥터 편집 ' 페이지의 리디렉션 URL 필드에는 이제 값 ' '이 (가) https://logic-apis-westus2.consent.azure-apim.net/redirect 있습니다. 값을 복사 하는 단추가 강조 표시 됩니다.":::

커넥터를 만드는 데 필요한 모든 정보입니다 (정의 단계에 대 한 이전 보안을 계속할 필요 없음). *편집 Logic Apps 사용자 지정 커넥터* 창을 닫을 수 있습니다.

>[!NOTE]
>처음에 *편집*을 누르고 있는 커넥터의 개요 페이지로 돌아가 *편집* 을 다시 방문 하면 구성 선택 항목을 입력 하는 전체 프로세스가 다시 시작 됩니다. 마지막으로 수행한 시간에서 값을 채우지 않으므로 변경 된 값을 사용 하 여 업데이트 된 구성을 저장 하려는 경우에는 다른 모든 값을 다시 입력 하 여 기본값으로 덮어쓰는 것을 방지 해야 합니다.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD 앱에서 커넥터 권한 부여

다음으로, 마지막 단계에서 복사한 사용자 지정 커넥터의 *리디렉션 URL* 값을 사용 하 여 Azure AD 앱 등록에서 커넥터 권한을 부여 합니다.

Azure Portal에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동 하 여 목록에서 등록을 선택 합니다. 

등록 메뉴의 *인증* 에서 URI를 추가 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure Portal의 앱 등록에 대 한 인증 페이지입니다. 메뉴의 ' 인증 '이 강조 표시 되 고 페이지에서 ' URI 추가 ' 단추가 강조 표시 됩니다."::: 

사용자 지정 커넥터의 *리디렉션 URL* 을 새 필드에 입력 하 고 *저장* 아이콘을 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure Portal의 앱 등록에 대 한 인증 페이지입니다. 새 리디렉션 URL이 강조 표시 되 고 페이지의 ' 저장 ' 단추가 강조 표시 됩니다.":::

이제 Azure Digital Twins Api에 액세스할 수 있는 사용자 지정 커넥터 설정이 완료 되었습니다. 

## <a name="create-logic-app"></a>논리 앱 만들기

다음으로, 새 커넥터를 사용 하 여 Azure Digital Twins 업데이트를 자동화 하는 논리 앱을 만듭니다.

Azure Portal의 [Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) 페이지로 이동 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 찾을 수 있습니다. *논리 앱 만들기*를 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure Portal의 ' Logic Apps ' 페이지입니다. ' 논리 앱 만들기 ' 단추를 강조 표시 합니다.":::

다음 *논리 앱* 페이지에서 구독 및 리소스 그룹을 선택 하 고 새 논리 앱에 대 한 이름 및 배포 위치를 선택 합니다. *검토 + 만들기*를 누릅니다. 그러면 아래에서 *만들기* 를 눌러 리소스를 만들 수 있는 *검토 + 만들기* 탭으로 이동 합니다.

논리 앱에 대 한 배포 페이지로 이동 합니다. 배포가 완료 되 면 *리소스로 이동* 단추를 클릭 하 여 워크플로의 논리를 채울 *Logic Apps 디자이너로*이동 합니다.

### <a name="design-workflow"></a>디자인 워크플로

*Logic Apps 디자이너*의 *공통 트리거로 시작*에서 _**되풀이**_ 를 선택 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure Portal의 ' Logic Apps Designer ' 페이지입니다. ' 되풀이 ' 일반 트리거를 강조 표시 합니다.":::

다음에 나오는 *Logic Apps 디자이너* 페이지에서 **되풀이** 빈도를 *Second*로 변경 하 여 이벤트가 3 초 마다 트리거됩니다. 이렇게 하면 나중에 대기 하지 않고도 결과를 쉽게 확인할 수 있습니다.

적중 *+ 새 단계*

이렇게 하면 *작업 선택* 상자가 열립니다. *사용자 지정* 탭으로 전환 합니다. 맨 위에 있는 상자에 사용자 지정 커넥터가 표시 됩니다.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure Portal의 Logic Apps 디자이너에서 흐름 만들기 ' 작업 선택 ' 상자에 ' 사용자 지정 ' 탭이 선택 되어 있습니다. 이전에 나온 사용자의 사용자 지정 커넥터가 상자에 표시 되 고이를 중심으로 강조 표시 됩니다.":::

해당 커넥터에 포함 된 Api 목록을 표시 하려면이를 선택 합니다. 검색 표시줄을 사용 하거나 목록을 스크롤하여 **DigitalTwins_Add**를 선택 합니다. 이 API는이 문서에서 사용 되는 API 이지만 Logic Apps 연결에 대 한 유효한 선택으로 다른 API를 선택할 수도 있습니다.

커넥터에 연결 하려면 Azure 자격 증명을 사용 하 여 로그인 하 라는 메시지가 표시 될 수 있습니다. *요청 된 사용 권한* 대화 상자가 표시 되 면 메시지에 따라 앱에 대 한 동의를 부여 하 고 수락 합니다.

새 *DigitalTwinsAdd* 상자에서 다음과 같이 필드를 채웁니다.
* id: 논리 앱을 업데이트 하려는 인스턴스에서 디지털 쌍의 쌍 *ID* 를 채웁니다.
* 항목-1:이 필드는 선택한 API 요청에 필요한 본문을 입력 합니다. *DigitalTwinsUpdate*의 경우이 본문은 JSON 패치 코드 형식입니다. 쌍을 업데이트 하는 JSON 패치를 구조화 하는 방법에 대 한 자세한 내용은 *방법: 디지털 쌍 관리*의 디지털 쌍 [업데이트](how-to-manage-twin.md#update-a-digital-twin) 섹션을 참조 하십시오.
* api-version: 현재 공개 미리 보기에서이 값은 *2020-05-31-미리 보기* 입니다.

Logic Apps 디자이너에서 *저장* 을 누릅니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="논리 앱 커넥터에서 완료 된 앱 보기입니다. DigitalTwinsAdd 상자는 샘플 JSON 패치 본문을 포함 하 여 위에서 설명한 값으로 채워집니다. 창에 대 한 ' 저장 ' 단추가 강조 표시 됩니다.":::

## <a name="query-twin-to-see-the-update"></a>업데이트를 확인 하는 쌍 쿼리

이제 논리 앱을 만들었으므로, Logic Apps 디자이너에서 정의한 쌍 업데이트 이벤트가 3 초 마다 되풀이에서 발생 해야 합니다. 즉, 3 초 후에 쌍을 쿼리하고 새 패치 된 값이 반영 된 것을 확인할 수 있습니다.

사용자 지정 메서드 (예: [사용자 지정 클라이언트 앱](tutorial-command-line-app.md), [Azure 디지털 쌍 탐색기 샘플 앱](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [sdk 및 api](how-to-use-apis-sdks.md)또는 [CLI](how-to-use-cli.md))를 통해 쌍을 쿼리할 수 있습니다. 

Azure Digital Twins 인스턴스를 쿼리 하는 방법에 대 한 자세한 내용은 [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자가 제공한 패치를 사용 하 여 Azure Digital Twins 인스턴스의 쌍을 정기적으로 업데이트 하는 논리 앱을 만들었습니다. 사용자 지정 커넥터에서 다른 Api를 선택 하 여 인스턴스에 대 한 다양 한 작업에 대 한 Logic Apps를 만들 수 있습니다.

사용 가능한 Api 작업 및 요구 사항에 대 한 자세한 내용을 보려면 [*방법: Azure Digital Twins api 및 Sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요.