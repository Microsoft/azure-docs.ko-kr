---
title: Azure AD Domain Services에 대 한 보안 감사 사용 | Microsoft Docs
description: 보안 감사를 사용 하 여의 분석 및 경고에 대 한 이벤트 로깅을 중앙 집중화 하는 방법에 대해 알아봅니다 Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 98e64f1ea1196b6fd773514d4dad5b6e178a6898
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722537"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대 한 보안 감사 사용

Azure AD DS (Azure Active Directory Domain Services) 보안 감사를 통해 Azure에서 대상 리소스에 대 한 보안 이벤트를 스트리밍할 수 있습니다. 이러한 리소스에는 Azure Storage, Azure Log Analytics 작업 영역 또는 Azure Event Hub가 포함 됩니다. 보안 감사 이벤트를 사용 하도록 설정 하면 Azure AD DS는 선택한 범주의 감사 된 모든 이벤트를 대상 리소스로 보냅니다.

Azure Event Hubs을 사용 하 여 azure storage에 이벤트를 보관 하 고 이벤트를 SIEM (보안 정보 및 이벤트 관리) 소프트웨어 (또는 이와 동등한)로 스트리밍할 수 있습니다. 또는 고유한 분석을 수행 하 고 Azure Portal에서 Azure Log Analytics 작업 영역을 사용할 수 있습니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 Azure Resource Manager 기반 관리 되는 도메인에만 사용할 수 있습니다. 마이그레이션하는 방법에 대 한 자세한 내용은 [클래식 가상 네트워크 모델에서 리소스 관리자 Azure AD DS 마이그레이션을][migrate-azure-adds]참조 하세요.

## <a name="security-audit-destinations"></a>보안 감사 대상

Azure AD DS 보안 감사에 대 한 대상 리소스로 Azure Storage, Azure Event Hubs 또는 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 이러한 대상을 결합할 수 있습니다. 예를 들어 Azure Storage를 사용 하 여 보안 감사 이벤트를 보관할 수 있지만 Azure Log Analytics 작업 영역을 사용 하 여 단기 정보를 분석 하 고 보고할 수 있습니다.

다음 표에서는 각 대상 리소스 종류에 대 한 시나리오를 간략하게 설명 합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사를 사용 하도록 설정 하기 전에 대상 리소스를 만들어야 합니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 이러한 리소스를 만들 수 있습니다.

| 대상 리소스 | 시나리오 |
|:---|:---|
|Azure Storage| 이 대상은 보관 목적으로 보안 감사 이벤트를 저장 해야 하는 경우에 사용 해야 합니다. 다른 대상은 보관 목적으로 사용할 수 있지만 이러한 대상은 보관의 기본 요구를 벗어난 기능을 제공 합니다. <br /><br />Azure AD DS 보안 감사 이벤트를 사용 하도록 설정 하기 전에 먼저 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md).|
|Azure Event Hubs| 이 대상은 데이터 분석 소프트웨어 또는 SIEM (보안 정보 & 이벤트 관리) 소프트웨어와 같은 추가 소프트웨어를 사용 하 여 보안 감사 이벤트를 공유 해야 하는 경우에 사용 해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용 하도록 설정 하기 전에 [Azure Portal를 사용 하 여 이벤트 허브를 만듭니다](../event-hubs/event-hubs-create.md) .|
|Azure Log Analytics 작업 영역| 이 대상은 Azure Portal에서 직접 보안 감사를 분석 하 고 검토 해야 하는 경우에 주로 사용 해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용 하도록 설정 하기 전에 [Azure Portal에 Log Analytics 작업 영역을 만듭니다.](../azure-monitor/learn/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Azure Portal를 사용 하 여 보안 감사 이벤트 사용

Azure Portal를 사용 하 여 Azure AD DS 보안 감사 이벤트를 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급 되지 않습니다. 과거에는 이벤트를 검색 하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용 하도록 설정한 후에 발생 하는 이벤트만 보낼 수 있습니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. Azure Portal 맨 위에서 **Azure AD Domain Services**를 검색 하 고 선택 합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. Azure AD DS 창의 왼쪽에서 **진단 설정** 을 선택 합니다.
1. 진단이 기본적으로 구성 되지 않습니다. 시작 하려면 **진단 설정 추가**를 선택 합니다.

    ![Azure AD Domain Services에 대 한 진단 설정 추가](./media/security-audit-events/add-diagnostic-settings.png)

1. 진단 구성에 대 한 이름 (예: *aadds-감사)* 을 입력 합니다.

    원하는 보안 감사 대상의 확인란을 선택 합니다. Azure Storage 계정, Azure 이벤트 허브 또는 Log Analytics 작업 영역에서 선택할 수 있습니다. 이러한 대상 리소스는 Azure 구독에 이미 존재 해야 합니다. 이 마법사에서는 대상 리소스를 만들 수 없습니다.

    ![캡처할 감사 이벤트의 필수 대상과 유형을 사용 하도록 설정 합니다.](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure storage**
        * **저장소 계정에 보관**을 선택한 다음 **구성**을 선택 합니다.
        * 보안 감사 이벤트를 보관 하는 데 사용 하려는 **구독** 및 **저장소 계정을** 선택 합니다.
        * 준비가 되 면 **확인**을 선택 합니다.
    * **Azure 이벤트 허브**
        * **이벤트 허브로 스트림**을 선택한 다음 **구성**을 선택 합니다.
        * **구독** 및 **이벤트 허브 네임 스페이스**를 선택 합니다. 필요한 경우 **이벤트 허브 이름** , **이벤트 허브 정책 이름**을 차례로 선택 합니다.
        * 준비가 되 면 **확인**을 선택 합니다.
    * **Azure 로그 분석 작업 영역**
        * **Log Analytics 보내기**를 선택 하 고 보안 감사 이벤트를 저장 하는 데 사용 하려는 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다.

1. 특정 대상 리소스에 포함 하려는 로그 범주를 선택 합니다. Azure Storage 계정에 감사 이벤트를 보내는 경우 데이터를 보존할 일 수를 정의 하는 보존 정책을 구성할 수도 있습니다. 기본 설정인 *0* 은 모든 데이터를 유지 하 고 일정 시간 후에 이벤트를 회전 하지 않습니다.

    단일 구성 내에서 각 대상 리소스에 대해 서로 다른 로그 범주를 선택할 수 있습니다. 이 기능을 사용 하 여 Log Analytics에 대해 유지할 로그 범주와 보관할 로그 범주를 선택할 수 있습니다 (예:).

1. 완료 되 면 **저장** 을 선택 하 여 변경 내용을 커밋합니다. 대상 리소스는 구성이 저장 된 후 즉시 Azure AD DS 보안 감사 이벤트 수신을 시작 합니다.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 보안 감사 이벤트 사용

Azure PowerShell를 사용 하 여 Azure AD DS 보안 감사 이벤트를 사용 하도록 설정 하려면 다음 단계를 완료 합니다. 필요한 경우 먼저 [Azure PowerShell 모듈을 설치 하 고 Azure 구독에 연결](/powershell/azure/install-az-ps)합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급 되지 않습니다. 과거에는 이벤트를 검색 하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용 하도록 설정한 후에 발생 하는 이벤트만 보낼 수 있습니다.

1. [AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용 하 여 Azure 구독에 인증 합니다. 메시지가 표시 되 면 계정 자격 증명을 입력 합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. 보안 감사 이벤트에 대 한 대상 리소스를 만듭니다.

    * **Azure storage**  -  [Azure PowerShell를 사용 하 여 저장소 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 이벤트 허브**  -  [Azure PowerShell를 사용 하 여 이벤트 허브를 만듭니다](../event-hubs/event-hubs-quickstart-powershell.md). [AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet을 사용 하 여 이벤트 허브 *네임 스페이스*에 Azure AD DS 사용 권한을 부여 하는 권한 부여 규칙을 만들어야 할 수도 있습니다. 권한 부여 규칙에는 **관리**, **수신**및 **전송** 권한이 포함 되어야 합니다.

        > [!IMPORTANT]
        > 이벤트 허브 자체가 아니라 event hubs 네임 스페이스에 대 한 권한 부여 규칙을 설정 했는지 확인 합니다.

    * **Azure 로그 분석 작업 영역**  -  [Azure PowerShell를 사용 하 여 Log Analytics 작업 영역을 만듭니다](../azure-monitor/platform/powershell-workspace-configuration.md).

1. [AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet을 사용 하 여 Azure AD DS 관리 되는 도메인에 대 한 리소스 ID를 가져옵니다. $Aadds 라는 변수를 만듭니다 *. * 값을 보유할 ResourceId:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Azure AD Domain Services 보안 감사 이벤트에 대 한 대상 리소스를 사용 하도록 [AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) cmdlet을 사용 하 여 Azure 진단 설정을 구성 합니다. 다음 예에서는 변수를 *$aadds 합니다. ResourceId* 는 이전 단계에서 사용 됩니다.

    * **Azure storage** - *storageaccountid* 를 저장소 계정 이름으로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure event hubs** - *eventHubName* 을 이벤트 허브의 이름으로, *EVENTHUBRULEID* 를 권한 부여 규칙 ID로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure 로그 분석 작업 영역** - *workspaceId* 을 Log Analytics 작업 영역의 ID로 바꿉니다.

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Azure Monitor를 사용 하 여 보안 감사 이벤트 쿼리 및 보기

로그 분석 작업 영역을 사용 하면 Azure Monitor 및 Kusto 쿼리 언어를 사용 하 여 보안 감사 이벤트를 보고 분석할 수 있습니다. 이 쿼리 언어는 읽기 쉬운 구문으로 전원 분석 기능을 boasts 하는 읽기 전용 사용을 위해 설계 되었습니다. Kusto 쿼리 언어를 시작 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure Monitor 설명서](../azure-monitor/index.yml)
* [Azure Monitor에서 Log Analytics 시작](../azure-monitor/log-query/get-started-portal.md)
* [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)
* [Log Analytics 데이터 대시보드 만들기 및 공유](../azure-monitor/learn/tutorial-logs-dashboards.md)

다음 샘플 쿼리를 사용 하 여 Azure AD DS에서 보안 감사 이벤트 분석을 시작할 수 있습니다.

### <a name="sample-query-1"></a>예제 쿼리 1

지난 7 일간의 모든 계정 잠금 이벤트를 표시 합니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>예제 쿼리 2

5 월 3 일 오전 9 시에 모든 계정 잠금 이벤트 (*4740*) 2020를 표시 합니다. 날짜 및 시간으로 오름차순으로 정렬 된, 2020 년 6 월 10 일:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>예제 쿼리 3

User 라는 계정에 대해 7 일 전 (지금부터) 계정 로그인 이벤트를 확인 합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>예제 쿼리 4

잘못 된 암호 (*0Xc0000006a*)를 사용 하 여 로그인을 시도한 사용자 계정에 대해 지금부터 7 일 전에 계정 로그인 이벤트를 확인 합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>예제 쿼리 5

계정이 잠긴 상태에서 로그인을 시도한 사용자 계정에 대해 지금부터 7 일 전에 계정 로그인 이벤트 보기 (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>예제 쿼리 6

모든 잠긴 사용자에 대해 발생 한 모든 로그인 시도에 대해 지금부터 7 일 전에 계정 로그인 이벤트 수를 확인 합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>감사 이벤트 범주

Azure AD DS 보안 감사는 기존의 AD DS 도메인 컨트롤러에 대 한 일반적인 감사와 맞춥니다. 하이브리드 환경에서는 기존 감사 패턴을 다시 사용할 수 있으므로 이벤트를 분석할 때 동일한 논리를 사용할 수 있습니다. 문제를 해결 하거나 분석 해야 하는 시나리오에 따라 다른 감사 이벤트 범주를 대상으로 해야 합니다.

다음 감사 이벤트 범주를 사용할 수 있습니다.

| 감사 범주 이름 | Description |
|:---|:---|
| 계정 로그온|감사는 도메인 컨트롤러 또는 로컬 SAM (보안 계정 관리자)의 계정 데이터를 인증 하려고 시도 합니다.</p>로그온 및 로그 오프 정책 설정 및 이벤트 트랙은 특정 컴퓨터에 대 한 액세스를 시도 합니다. 이 범주의 설정 및 이벤트는 사용 되는 계정 데이터베이스에 중점을 둡니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[자격 증명 유효성 검사 감사](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 인증 오류 감사](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos 서비스 티켓 작업 감사](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[기타 로그온/로그 오프 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 계정 관리|사용자 및 컴퓨터 계정 및 그룹에 대 한 변경 내용을 감사 합니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[애플리케이션 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[컴퓨터 계정 관리 감사](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[메일 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[다른 계정 관리 감사](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[보안 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[사용자 계정 관리 감사](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 세부 정보 추적|해당 컴퓨터에 있는 개별 응용 프로그램 및 사용자의 작업을 감사 하 고 컴퓨터가 사용 되는 방식을 이해 합니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[DPAPI 활동 감사](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP 작업 감사](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[프로세스 만들기 감사](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[프로세스 종료 감사](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC 이벤트 감사](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 디렉터리 서비스 액세스|감사는 Active Directory Domain Services (AD DS)에서 개체에 액세스 하 고 수정 하려고 합니다. 이러한 감사 이벤트는 도메인 컨트롤러에만 기록 됩니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[세부 디렉터리 서비스 복제 감사](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[디렉터리 서비스 액세스 감사](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[디렉터리 서비스 변경 감사](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[디렉터리 서비스 복제 감사](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 로그온-로그 오프|감사는 대화형으로 또는 네트워크를 통해 컴퓨터에 로그온을 시도 합니다. 이러한 이벤트는 사용자 작업을 추적 하 고 네트워크 리소스에 대 한 잠재적인 공격을 식별 하는 데 유용 합니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[계정 잠금 감사](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[사용자/장치 클레임 감사](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 확장 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[감사 그룹 멤버 자격](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec 주 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec 빠른 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[로그오프 감사](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[로그온 감사](/windows/security/threat-protection/auditing/audit-logon)</li><li>[네트워크 정책 서버 감사](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[기타 로그온/로그 오프 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[특수 로그온 감사](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|개체 액세스| 감사는 네트워크 또는 컴퓨터에서 특정 개체 또는 개체 유형에 액세스를 시도 합니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[애플리케이션 생성됨 감사](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[인증 서비스 감사](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[세부 파일 공유 감사](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[파일 공유 감사](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[파일 시스템 감사](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[필터링 플랫폼 연결 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[필터링 플랫폼 패킷 삭제 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[핸들 조작 감사](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[커널 개체 감사](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[기타 개체 액세스 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[감사 레지스트리](/windows/security/threat-protection/auditing/audit-registry)</li><li>[이동식 저장소 감사](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM 감사](/windows/security/threat-protection/auditing/audit-sam)</li><li>[중앙 액세스 정책 준비 감사](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|정책 변경|로컬 시스템 또는 네트워크에서 중요 보안 정책 변경을 감사 합니다. 정책은 일반적으로 관리자가 네트워크 리소스의 보안을 유지 하도록 설정 됩니다. 변경 내용을 모니터링 하거나 이러한 정책을 변경 하려는 시도는 네트워크에 대 한 보안 관리의 중요 한 측면이 될 수 있습니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[감사 정책 변경 감사](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[인증 정책 변경 감사](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[권한 부여 정책 변경 감사](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[필터링 플랫폼 정책 변경 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC 규칙 수준 정책 변경 감사](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[다른 정책 변경 감사](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|권한 사용| 하나 이상의 시스템에서 특정 사용 권한의 사용을 감사 합니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[중요하지 않은 권한 사용 감사](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[중요한 권한 사용 감사](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[기타 권한 사용 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|시스템| 다른 범주에 포함 되지 않은 컴퓨터에 대 한 시스템 수준 변경을 감사 하 고 보안에 영향을 미칠 수 있습니다. 이 범주에는 다음과 같은 하위 범주가 포함 됩니다.<ul><li>[IPsec 드라이버 감사](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[기타 시스템 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[보안 상태 변경 감사](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[보안 시스템 확장 감사](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[시스템 무결성 감사](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>범주별 이벤트 Id

 Azure AD DS 보안 감사는 특정 작업에서 감사 가능한 이벤트를 트리거할 때 다음 이벤트 Id를 기록 합니다.

| 이벤트 범주 이름 | 이벤트 ID |
|:---|:---|
|계정 로그온 보안|4767, 4774, 4775, 4776, 4777|
|계정 관리 보안|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|세부 정보 추적 보안|None|
|DS 액세스 보안|5136, 5137, 5138, 5139, 5141|
|로그온/로그 오프 보안|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|개체 액세스 보안|None|
|정책 변경 보안|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|권한 사용 보안|4985|
|시스템 보안|4612, 4621|

## <a name="next-steps"></a>다음 단계

Kusto에 대 한 특정 정보는 다음 문서를 참조 하세요.

* Kusto 쿼리 언어의 [개요](/azure/kusto/query/) 입니다.
* 쿼리 기본 사항에 대해 잘 알고 있는 [Kusto 자습서](/azure/kusto/query/tutorial) 입니다.
* 데이터를 볼 수 있는 새로운 방법을 배우는 데 도움이 되는 [샘플 쿼리입니다](/azure/kusto/query/samples) .
* 성공 하기 위해 쿼리를 최적화 하는 [모범 사례](/azure/kusto/query/best-practices) 입니다.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md