---
title: Windows 가상 데스크톱 (클래식)에 대 한 진단 도구 배포-Azure
description: Windows 가상 데스크톱에 대 한 진단 UX 도구 (클래식)를 배포 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61c31b24b01b40da4d73a308a4f304f6ff242e41
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691414"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Windows 가상 데스크톱 (클래식) 진단 도구 배포

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다.

>[!IMPORTANT]
>2020 년 3 월 16 일부 터 서비스에 대 한 수요 증가로 인해 사용자 환경에 영향을 주는 진단 쿼리를 일시적으로 사용 하지 않도록 설정 했습니다. 이렇게 하면 해당 쿼리가 작동 하는 데 의존 하므로 도구가 작동을 중지 합니다. 진단 쿼리를 다시 사용할 수 있게 되 면이 문서를 업데이트 합니다.
>
>그때 까지는 계속 모니터링을 위해 [Log Analytics를 사용](diagnostics-log-analytics-2019.md) 하는 것이 좋습니다.

Windows 가상 데스크톱에 대 한 진단 도구는 다음과 같은 작업을 수행할 수 있습니다.

- 한 주 동안 단일 사용자에 대 한 진단 작업 (관리, 연결 또는 피드)을 조회 합니다.
- Log Analytics 작업 영역에서 연결 작업에 대 한 세션 호스트 정보를 수집 합니다.
- 특정 호스트에 대 한 VM (가상 머신) 성능 세부 정보를 검토 합니다.
- 세션 호스트에 로그인 한 사용자를 확인 합니다.
- 특정 세션 호스트의 활성 사용자에 게 메시지를 보냅니다.
- 세션 호스트에서 사용자를 로그 아웃 합니다.

## <a name="prerequisites"></a>필수 구성 요소

도구의 Azure Resource Manager 템플릿을 배포 하려면 먼저 Azure Active Directory 앱 등록 및 Log Analytics 작업 영역을 만들어야 합니다. 이러한 작업을 수행 하려면 사용자 또는 관리자에 게 다음 권한이 필요 합니다.

- Azure 구독의 소유자
- Azure 구독에서 리소스를 만들 수 있는 권한
- Azure AD 앱을 만들 수 있는 권한
- RDS 소유자 또는 참가자 권한

또한 시작 하기 전에 다음 두 PowerShell 모듈을 설치 해야 합니다.

- [Azure PowerShell 모듈](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD 모듈](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

로그인 할 때 구독 ID가 준비 되어 있는지 확인 합니다.

모든 항목을 순서 대로 완료 한 후에는 Azure AD 앱 등록을 만들 수 있습니다.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록 만들기

이 섹션에서는 PowerShell을 사용 하 여 서비스 주체를 사용 하 여 Azure Active Directory 앱을 만들고이에 대 한 API 권한을 가져오는 방법을 보여 줍니다.

>[!NOTE]
>API 권한은 Windows 가상 데스크톱 이며 Log Analytics Microsoft Graph API 권한은 Azure Active Directory 응용 프로그램에 추가 됩니다.

1. 관리자 권한으로 PowerShell을 엽니다.
2. 진단 도구에 사용할 Azure 구독에 대 한 소유자 또는 참가자 권한이 있는 계정을 사용 하 여 Azure에 로그인 합니다.
   ```powershell
   Login-AzAccount
   ```
3. 동일한 계정을 사용 하 여 Azure AD에 로그인 합니다.
   ```powershell
   Connect-AzureAD
   ```
4. [RDS 템플릿 GitHub 리포지토리](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 로 이동 하 고 PowerShell에서 **CreateADAppRegistrationforDiagnostics.ps1** 스크립트를 실행 합니다.
5.  스크립트에서 앱의 이름을 입력 하 라는 메시지가 표시 되 면 고유한 앱 이름을 입력 합니다.


스크립트가 성공적으로 실행 되 면 출력에 다음과 같은 항목이 표시 됩니다.

-  이제 앱을 확인 하는 메시지에 서비스 사용자 역할 할당이 있습니다.
-  진단 도구를 배포할 때 필요한 클라이언트 ID 및 클라이언트 비밀 키입니다.

앱을 등록 했으므로 이제 Log Analytics 작업 영역을 구성 해야 합니다.

## <a name="configure-your-log-analytics-workspace"></a>Log Analytics 작업 영역 구성

가능한 최상의 환경을 위해 원격 세션에서 사용자 환경에 대 한 문을 파생 시킬 수 있는 다음 성능 카운터를 사용 하 여 Log Analytics 작업 영역을 구성 하는 것이 좋습니다. 권장 임계값을 사용 하는 권장 카운터 목록은 [Windows 성능 카운터 임계값](deploy-diagnostics.md#windows-performance-counter-thresholds)을 참조 하세요.

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>PowerShell을 사용 하 여 Azure Log Analytics 작업 영역 만들기

PowerShell 스크립트를 실행 하 여 Log Analytics 작업 영역을 만들고 권장 되는 Windows 성능 카운터를 구성 하 여 사용자 환경 및 앱 성능을 모니터링할 수 있습니다.

>[!NOTE]
>PowerShell 스크립트를 사용 하지 않고 기존 Log Analytics 작업 영역을 이미 만든 경우 [에는 Azure Portal에서 스크립트 결과의 유효성 검사](#validate-the-script-results-in-the-azure-portal)로 건너뜁니다.

PowerShell 스크립트를 실행 하려면 다음을 수행 합니다.

1.  관리자 권한으로 PowerShell을 엽니다.
2.  [RDS 템플릿 GitHub 리포지토리](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 로 이동 하 고 PowerShell에서 **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** 스크립트를 실행 합니다.
3. 매개 변수에 대해 다음 값을 입력합니다.

    - **ResourceGroupName**에 대해 리소스 그룹의 이름을 입력 합니다.
    - **LogAnalyticsWorkspaceName**의 경우 Log Analytics 작업 영역에 대 한 고유한 이름을 입력 합니다.
    - **위치**에 사용 중인 Azure 지역을 입력 합니다.
    - **구독**아래 Azure Portal에서 찾을 수 있는 **Azure 구독 ID**를 입력 합니다.

4. 위임 된 관리자 액세스 권한이 있는 사용자의 자격 증명을 입력 합니다.
5. 동일한 사용자의 자격 증명을 사용 하 여 Azure Portal에 로그인 합니다.
6. 나중에 기억 LogAnalyticsWorkspace ID를 기록 합니다.
7. PowerShell 스크립트를 사용 하 여 Log Analytics 작업 영역을 설정 하는 경우 성능 카운터가 이미 구성 되어 있으며 [Azure Portal 스크립트 결과의 유효성 검사](#validate-the-script-results-in-the-azure-portal)를 건너뛸 수 있습니다. 그렇지 않으면 다음 섹션으로 계속 진행 합니다.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역에서 Windows 성능 카운터 구성

이 섹션은 이전 섹션에서 PowerShell 스크립트를 사용 하지 않고 만든 기존 Azure Log Analytics 작업 영역을 사용 하려는 사용자를 위한 것입니다. 스크립트를 사용 하지 않은 경우에는 권장 되는 Windows 성능 카운터를 수동으로 구성 해야 합니다.

권장 되는 성능 카운터를 수동으로 구성 하는 방법은 다음과 같습니다.

1. 인터넷 브라우저를 열고 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 다음으로 **Log Analytics 작업 영역** 으로 이동 하 여 구성 된 Windows 성능 카운터를 검토 합니다.
3. **설정** 섹션에서 **고급 설정**을 선택 합니다.
4. 그런 후 **데이터**  >  **Windows 성능 카운터** 로 이동 하 여 다음 카운터를 추가 합니다.

    -   논리 디스크 ( \* ) \\ % 사용 가능한 공간
    -   논리 디스크 (C: \\ Avg. Disk Queue Length)
    -   Memory ( \* ) \\ 사용 가능한 공간 (mb)
    -   프로세서 정보 ( \* ) \\ 프로세서 시간
    -   세션당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연

[Azure Monitor의 Windows 및 Linux 성능 데이터 원본](/azure/azure-monitor/platform/data-sources-performance-counters)에서 성능 카운터에 대해 자세히 알아보세요.

>[!NOTE]
>구성 하는 추가 카운터는 진단 도구 자체에 표시 되지 않습니다. 진단 도구에 표시 되도록 하려면 도구의 구성 파일을 구성 해야 합니다. 고급 관리를 사용 하 여이 작업을 수행 하는 방법에 대 한 지침은 GitHub의 이후 날짜로 제공 됩니다.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Azure Portal에서 스크립트 결과의 유효성을 검사 합니다.

진단 도구 배포를 계속 하려면 먼저 Azure Active Directory 응용 프로그램에 API 권한이 있는지 확인 하 고 Log Analytics 작업 영역에 미리 구성 된 Windows 성능 카운터가 있는지 확인 하는 것이 좋습니다.

### <a name="review-your-app-registration"></a>앱 등록 검토

앱 등록에 API 권한이 있는지 확인 하려면 다음을 수행 합니다.

1. 브라우저를 열고 관리자 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 연결 합니다.
2. **Azure Active Directory**로 이동합니다.
3. **앱 등록** 로 이동 하 여 **모든 응용 프로그램**을 선택 합니다.
4. [Azure Active Directory 앱 등록 만들기](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)의 5 단계에서 입력 한 것과 동일한 앱 이름을 사용 하 여 Azure AD 앱 등록을 찾습니다.

### <a name="review-your-log-analytics-workspace"></a>Log Analytics 작업 영역 검토

Log Analytics 작업 영역에 미리 구성 된 Windows 성능 카운터가 있는지 확인 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Log Analytics 작업 영역** 으로 이동 하 여 구성 된 Windows 성능 카운터를 검토 합니다.
2. **설정**아래에서 **고급 설정**을 선택 합니다.
3. 그런 다음 **데이터**  >  **Windows 성능 카운터**로 이동 합니다.
4. 다음 카운터가 미리 구성 되어 있는지 확인 합니다.

   - 논리 디스크 ( \* ) \\ % 사용 가능한 공간: 디스크에서 사용 가능한 총 공간 (%)의 사용 가능한 공간 크기를 표시 합니다.
   - 논리 디스크 (C: \\ Avg. Disk Queue length: c 드라이브에 대 한 디스크 전송 요청의 길이입니다. 짧은 기간 동안 값은 2를 초과 해서는 안 됩니다.
   - Memory ( \* ) \\ available mb: 시스템에 사용할 수 있는 메모리 (mb)입니다.
   - 프로세서 정보 ( \* ) \\ 프로세서 시간: 프로세서가 비 유휴 스레드를 실행 하는 데 걸린 시간의 백분율입니다.
   - 세션당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Log Analytics 작업 영역에서 Vm에 연결

Vm의 상태를 볼 수 있으려면 Log Analytics 연결을 사용 하도록 설정 해야 합니다. Vm을 연결 하려면 다음 단계를 수행 합니다.

1. 브라우저를 열고 관리자 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. Log Analytics 작업 영역으로 이동 합니다.
3. 왼쪽 패널의 작업 영역 데이터 원본에서 **virtual machines**를 선택 합니다.
4. 연결 하려는 VM의 이름을 선택 합니다.
5. **연결**을 선택합니다.

## <a name="deploy-the-diagnostics-tool"></a>진단 도구 배포

진단 도구에 대 한 Azure 리소스 관리 템플릿을 배포 하려면 다음을 수행 합니다.

1.  [GitHub Azure RDS 템플릿 페이지](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)로 이동합니다.
2.  템플릿을 Azure에 배포 하 고 템플릿의 지침을 따릅니다. 다음 정보를 사용할 수 있는지 확인 합니다.

    -   클라이언트 Id
    -   클라이언트-비밀
    -   Log Analytics 작업 영역 ID

3.  입력 매개 변수가 제공 되 면 사용 약관에 동의한 다음 **구매**를 선택 합니다.

배포에는 2 ~ 3 분이 소요 됩니다. 성공적으로 배포 되 면 리소스 그룹으로 이동 하 여 웹 앱 및 app service 계획 리소스가 있는지 확인 합니다.

그 후에는 리디렉션 URI를 설정 해야 합니다.

### <a name="set-the-redirect-uri"></a>리디렉션 URI 설정

리디렉션 URI를 설정 하려면:

1.  [Azure Portal](https://portal.azure.com/)에서 **App Services** 으로 이동 하 여 만든 응용 프로그램을 찾습니다.
2.  개요 페이지로 이동 하 여 찾은 URL을 복사 합니다.
3.  **앱 등록** 으로 이동 하 여 배포 하려는 앱을 선택 합니다.
4.  왼쪽 패널의 관리 섹션에서 **인증**을 선택 합니다.
5.  **리디렉션 uri** 텍스트 상자에 원하는 리디렉션 uri를 입력 한 다음 메뉴의 왼쪽 위 모서리에서 **저장** 을 선택 합니다.
6. 유형 아래의 드롭다운 메뉴에서 **웹** 을 선택 합니다.
7. 앱 개요 페이지에서 URL을 입력 하 고 끝에 **/security/signin-callback** 를 추가 합니다. 예를 들어 `https://<yourappname>.azurewebsites.net/security/signin-callback`을 참조하십시오.

   > [!div class="mx-imgBorder"]
   > ![URI 리디렉션 페이지](../media/redirect-uri-page.png)

8. 이제 Azure 리소스로 이동 하 여 템플릿에서 제공한 이름으로 Azure 앱 Services 리소스를 선택 하 고 연결 된 URL로 이동 합니다. 예를 들어 템플릿에서 사용한 앱 이름이 인 경우 `contosoapp45` 연결 된 URL은입니다 <http://contoso.azurewebsites.net> .
9. 적절한 Azure Active Directory 사용자 계정을 사용하여 로그인합니다.
10.   **수락**을 선택합니다.

## <a name="distribute-the-diagnostics-tool"></a>진단 도구 배포

진단 도구를 사용자에 게 제공 하기 전에 다음 권한이 있는지 확인 합니다.

- 사용자는 log analytics에 대 한 읽기 권한이 필요 합니다. 자세한 내용은 Azure Monitor를 사용 하 여 [역할, 권한 및 보안 시작](/azure/azure-monitor/platform/roles-permissions-security)을 참조 하세요.
-  또한 사용자에 게는 Windows 가상 데스크톱 테 넌 트 (RDS Reader 역할)에 대 한 읽기 권한이 필요 합니다. 자세한 내용은 [Windows 가상 데스크톱의 위임 된 액세스](delegated-access-virtual-desktop-2019.md)를 참조 하세요.

또한 사용자에 게 다음 정보를 제공 해야 합니다.

- 앱의 URL
- 액세스할 수 있는 테 넌 트 그룹 개별 테 넌 트의 이름입니다.

## <a name="use-the-diagnostics-tool"></a>진단 도구 사용

조직에서 받은 정보를 사용 하 여 계정에 로그인 한 후에는 활동을 쿼리 하려는 사용자에 대해 UPN을 준비 해야 합니다. 검색을 수행 하면 지난 주에 발생 한 지정 된 활동 유형에 따라 모든 활동이 제공 됩니다.

### <a name="how-to-read-activity-search-results"></a>활동 검색 결과를 읽는 방법

작업은 타임 스탬프를 기준으로 정렬 되며 가장 먼저 최신 활동을 사용 합니다. 결과에서 오류를 반환 하는 경우 먼저 서비스 오류 인지 확인 하십시오. 서비스 오류의 경우 문제를 디버그 하는 데 도움이 되는 활동 정보를 사용 하 여 지원 티켓을 만듭니다. 다른 모든 오류 유형은 일반적으로 사용자 또는 관리자가 해결할 수 있습니다. 가장 일반적인 오류 시나리오와 이러한 시나리오를 해결 하는 방법에 대 한 자세한 내용은 [문제 식별 및 진단](diagnostics-role-service-2019.md#common-error-scenarios)을 참조 하세요.

>[!NOTE]
>서비스 오류를 연결 된 설명서의 "외부 오류" 라고 합니다. 이는 PowerShell 참조를 업데이트할 때 변경 됩니다.

연결 작업에는 두 개 이상의 오류가 있을 수 있습니다. 활동 유형을 확장 하 여 사용자가 제공한 다른 오류를 볼 수 있습니다. 오류 코드의 이름을 선택 하 여 대화 상자를 열고이에 대 한 자세한 정보를 확인 합니다.

### <a name="investigate-the-session-host"></a>세션 호스트 조사

검색 결과에서 정보를 보려는 세션 호스트를 찾아 선택 합니다.

세션 호스트 상태를 분석할 수 있습니다.

- 미리 정의 된 임계값에 따라 쿼리를 Log Analytics 하는 세션 호스트 상태 정보를 검색할 수 있습니다.
- 활동이 없거나 세션 호스트가 Log Analytics에 연결 되어 있지 않으면 정보를 사용할 수 없습니다.

세션 호스트에서 사용자와 상호 작용할 수도 있습니다.

- 로그인 하거나 로그인 한 사용자에 게 메시지를 보낼 수 있습니다.
- 처음에 검색 한 사용자가 기본적으로 선택 되어 있지만 메시지를 보내거나 여러 사용자를 동시에 로그 아웃 하도록 추가 사용자를 선택할 수도 있습니다.

### <a name="windows-performance-counter-thresholds"></a>Windows 성능 카운터 임계값

- 논리 디스크 ( \* ) \\ 사용 가능한 공간 (%):

    - 논리 디스크에서 사용 가능한 총 공간의 비율을 표시 합니다.
    - 임계값: 20% 미만은 비정상으로 표시 됩니다.

- 논리 디스크 (C:) \\ Avg. Disk Queue Length:

    - 저장소 시스템 조건을 나타냅니다.
    - 임계값: 5 보다 크면 비정상으로 표시 됩니다.

- Memory ( \* ) \\ 사용 가능 공간 (mb):

    - 시스템에 사용할 수 있는 메모리입니다.
    - 임계값: 비정상으로 표시 된 500 메가바이트 미만.

- 프로세서 정보 ( \* ) \\ 프로세서 시간:

    - 임계값: 80% 이상이 비정상으로 표시 되어 있습니다.

- [세션당 사용자 입력 지연 ( \* ) \\ 최대 입력 지연](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - 임계값: 2000 밀리초 이상 비정상으로 표시 되어 있습니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics를 사용 하 여 사용 진단](diagnostics-log-analytics-2019.md)에서 활동 로그를 모니터링 하는 방법을 알아봅니다.
- 일반적인 오류 시나리오 및 [문제를 식별 하 고 진단](diagnostics-role-service-2019.md)하는 방법에 대 한 자세한 내용은을 참조 하세요.
