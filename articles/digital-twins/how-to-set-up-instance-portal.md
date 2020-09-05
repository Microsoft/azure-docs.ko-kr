---
title: 인스턴스 및 인증 설정(포털)
titleSuffix: Azure Digital Twins
description: Azure Portal를 사용 하 여 Azure Digital Twins 서비스 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 300a11c36b11230d2bd75534f152a0a5955743ed
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181795"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins 인스턴스 및 인증 설정 (포털)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 문서의 버전은 Azure Portal를 사용 하 여 이러한 단계를 하나씩 수동으로 진행 합니다. Azure Portal은 명령줄 도구의 대안을 제공하는 웹 기반의 통합 콘솔입니다.
* CLI를 사용 하 여 이러한 단계를 수동으로 진행 하려면이 문서의 CLI 버전 [*(방법: 인스턴스 및 인증 설정 (cli))*](how-to-set-up-instance-cli.md)을 참조 하세요.
* 배포 스크립트 샘플을 사용 하 여 자동화 된 설치를 실행 하려면이 문서의 스크립팅된 버전 [*(방법: 인스턴스 및 인증 설정 (스크립팅된))*](how-to-set-up-instance-scripted.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 [Azure Portal](https://ms.portal.azure.com/)를 사용 하 여 **Azure Digital twins의 새 인스턴스를 만듭니다** . 포털로 이동 하 여 자격 증명으로 로그인 합니다.

포털에서 Azure 서비스 홈 페이지 메뉴에서 _리소스 만들기_ 를 선택 하 여 시작 합니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

검색 상자에서 *Azure Digital Twins* 를 검색 하 고 결과에서 **Azure digital Twins (미리 보기)** 서비스를 선택 합니다. _만들기_ 단추를 선택 하 여 서비스의 새 인스턴스를 만듭니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Azure Digital Twins 서비스 페이지에서 ' 만들기 ' 선택":::

다음 *리소스 만들기* 페이지에서 아래 지정 된 값을 입력 합니다.
* **구독**: 사용 중인 Azure 구독
  - **리소스 그룹**: 인스턴스를 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 *새로* 만들기 링크를 선택 하 고 새 리소스 그룹의 이름을 입력 하 여 여기에서 하나를 만들 수 있습니다.
* **Location**: 배포를 위한 Azure Digital twins 지원 지역입니다. 지역별 지원에 대 한 자세한 내용은 [*지역별 사용 가능한 azure 제품 (Azure Digital Twins)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조 하세요.
* **리소스 이름**: Azure Digital twins 인스턴스의 이름입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 즉, 사용자가 선택한 이름을 이미 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="설명 된 값을 입력 하 여 Azure Digital Twins 리소스 만들기":::

완료 되 면 _검토 + 만들기_를 선택 합니다. 그러면 입력 한 인스턴스 세부 정보를 검토 하 고 _만들기_를 누르면 요약 페이지로 이동 합니다. 

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요 한 값 수집

*만든*후에는 포털 아이콘 표시줄을 따라 Azure 알림에서 인스턴스의 배포 상태를 볼 수 있습니다. 배포에 성공 하면 알림이 표시 되 고, _리소스로 이동_ 단추를 선택 하 여 생성 된 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="성공적인 배포를 표시 하 고 ' 리소스로 이동 ' 단추를 강조 표시 하는 Azure 알림 보기":::

또는 배포에 실패 하는 경우 알림이 표시 됩니다. 오류 메시지에서 조언을 관찰 하 고 인스턴스 만들기를 다시 시도 합니다.

>[!TIP]
>인스턴스를 만든 후에는 Azure Portal 검색 표시줄에서 인스턴스 이름을 검색 하 여 언제 든 지 페이지로 돌아갈 수 있습니다.

인스턴스의 *개요* 페이지에서 *이름*, *리소스 그룹*및 *호스트 이름을*확인 합니다. 이러한 값은 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있는 모든 중요 한 값입니다. 다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우 이러한 값을 공유 해야 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="인스턴스의 개요 페이지에서 중요 한 값 강조 표시":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 그런 다음 적절 한 Azure 사용자에 게 관리 권한을 부여 합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

먼저 Azure Portal에서 Azure Digital Twins 인스턴스의 페이지를 엽니다. 인스턴스의 메뉴에서 *Access control (IAM)* 을 선택 합니다. *역할 할당 추가*에서 *추가* 단추를 선택 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="' Access control (IAM) ' 페이지에서 역할 할당을 추가 하려면 선택 합니다.":::

다음 *역할 할당 추가* 페이지에서 값을 입력 합니다 (Azure 구독에서 [충분 한 권한이](#prerequisites-permission-requirements) 있는 사용자가 완료 해야 함).
* **역할**: 드롭다운 메뉴에서 *Azure Digital twins 소유자 (미리 보기)* 를 선택 합니다.
* **액세스 권한 할당**: 드롭다운 메뉴에서 *Azure AD 사용자, 그룹 또는 서비스 사용자를* 선택 합니다.
* **선택**: 할당할 사용자의 이름 또는 전자 메일 주소를 검색 합니다. 결과를 선택 하면 사용자가 *선택한 구성원* 섹션에 표시 됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="' 역할 할당 추가 ' 대화 상자에 나열 된 필드를 채웁니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

세부 정보 입력을 마치면 *저장* 단추를 누릅니다.

### <a name="verify-success"></a>성공 확인

*액세스 제어 (IAM) > 역할 할당*에 설정 된 역할 할당을 볼 수 있습니다. 사용자는 *Azure 디지털 쌍 소유자 (미리 보기)* 의 역할을 사용 하 여 목록에 표시 되어야 합니다. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스에 대 한 역할 할당 보기":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되 고 관리 권한이 할당 됩니다. 다음으로 클라이언트 앱에 액세스할 수 있는 권한을 설정 합니다.

## <a name="set-up-access-permissions-for-client-applications"></a>클라이언트 응용 프로그램에 대 한 액세스 권한 설정

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Azure Portal에서 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 로 이동 하 여 시작 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 찾을 수 있습니다. 서비스 메뉴에서 *앱 등록* 를 선택 하 고 *+ 새로 만들기*를 선택 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

뒤에 나오는 *응용 프로그램 등록* 페이지에서 요청 된 값을 입력 합니다.
* **이름**: 등록과 연결할 Azure AD 응용 프로그램 표시 이름입니다.
* **지원 되는 계정 유형**: *이 조직 디렉터리 에서만 계정 선택 (기본 디렉터리만-단일 테 넌 트)*
* **리디렉션 URI**: azure ad 응용 프로그램에 대 한 *azure ad 응용 프로그램 회신 URL* 입니다. 에 대 한 *공용 클라이언트/네이티브 (모바일 & 데스크톱)* URI를 추가 `http://localhost` 합니다.

작업이 완료 되 면 *등록* 단추를 누릅니다.

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="설명 된 값이 채워진 ' 응용 프로그램 등록 ' 페이지의 보기":::

등록이 완료 되 면 포털에서 세부 정보 페이지로 리디렉션됩니다.

### <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API 권한 제공

다음으로, 기본 사용 권한으로 만든 앱 등록을 Azure Digital Twins Api에 구성 합니다.

앱 등록에 대 한 포털 페이지의 메뉴에서 *API 권한* 을 선택 합니다. 다음 사용 권한 페이지에서 *+ 권한 추가* 단추를 누릅니다.

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="' API 권한 ' 메뉴 옵션 및 ' + 권한 추가 ' 단추를 강조 표시 하 여 Azure Portal에서 앱 등록 보기":::

다음에 나오는 *api 권한 요청* 페이지에서 *내 조직* 에서 tab 키를 사용 하 고 *azure digital 쌍*를 검색 하는 api로 전환 합니다. Azure Digital Twins Api에 대 한 권한 할당을 진행 하려면 검색 결과에서 *Azure Digital Twins* 를 선택 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Azure Digital Twins를 표시 하는 ' API 권한 요청 ' 페이지 검색 결과 보기":::

>[!NOTE]
> 서비스의 이전 공개 미리 보기 (7 월 2020 이전)에서 기존 Azure Digital Twins 인스턴스가 아직 구독에 있는 경우 *Azure 스마트 공간 서비스* 를 대신 찾아야 합니다. 이는 동일한 Api 집합의 이전 이름 이며이 단계를 벗어나 사용자 환경을 변경 하지 않습니다.

다음으로 이러한 Api에 부여할 사용 권한을 선택 합니다. **읽기 (1)** 권한을 확장 하 고 *읽기. 쓰기* 확인란을 선택 하 여이 앱 등록 판독기 및 작성기 권한을 부여 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="' API 권한 요청 ' 페이지가 표시 됩니다. Azure Digital Twins Api에 대 한 ' 읽기. 쓰기 ' 권한을 선택 합니다.":::

완료 되 면 *권한 추가* 를 누릅니다.

### <a name="verify-success"></a>성공 확인

*API 사용 권한* 페이지로 돌아가서 읽기/쓰기 권한을 반영 하는 Azure Digital twins의 항목이 이제 있는지 확인 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Azure AD 앱 등록에 대 한 API 권한의 포털 보기 (Azure Digital Twins의 ' 읽기/쓰기 액세스 ' 표시)":::

API 사용 권한을 추가할 때 Azure Digital Twins 정보를 사용 하 여 자동으로 업데이트 된 앱 등록의 *manifest.js*에서 Azure Digital twins에 대 한 연결을 확인할 수도 있습니다.

이렇게 하려면 메뉴에서 *매니페스트* 를 선택 하 여 앱 등록의 매니페스트 코드를 확인 합니다. 코드 창의 아래쪽으로 스크롤하고 아래에서 이러한 필드를 찾습니다 `requiredResourceAccess` . 값은 아래 스크린샷에 있는 값과 일치 해야 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Azure AD 앱 등록에 대 한 매니페스트의 포털 뷰입니다. ' RequiredResourceAccess ' 아래에 중첩 된 ' resourceAppId ' 값은 0b07f429-9f4b-4714-9392-cc5e8e80c8b0이 고 ' resourceAccess > id ' 값은 4589bd03-58cb-4e6c-b17f-b580e39652f8입니다.":::

### <a name="collect-important-values"></a>중요 한 값 수집

그런 다음 메뉴 모음에서 *개요* 를 선택 하 여 앱 등록에 대 한 세부 정보를 확인 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="앱 등록에 대 한 중요 한 값의 포털 보기":::

**페이지에** 표시 된 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) id* 를 기록해 둡니다. 이러한 값은 나중에 [Azure Digital Twins api에 대해 클라이언트 앱을 인증](how-to-authenticate-client.md)하는 데 필요 합니다. 이러한 응용 프로그램에 대 한 코드를 작성 하는 사람이 아닌 경우이 값을 지정할 사용자와 공유 해야 합니다.

### <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>다음 단계

클라이언트 앱의 인증 코드를 작성 하 여 클라이언트 응용 프로그램을 인스턴스에 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)