---
title: 가상 머신의 콘텐츠를 감사하는 방법 알아보기
description: Azure Policy가 게스트 구성 에이전트를 사용하여 가상 머신 내에서 설정을 감사하는 방법에 대해 알아봅니다.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 951960793ebda50fdb87d266c4dc8561f2fcd70f
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756693"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Azure Policy는 Azure에서 실행 되는 컴퓨터와 [Arc 연결 된 컴퓨터](../../../azure-arc/servers/overview.md)에 대 한 컴퓨터 내에서 설정을 감사할 수 있습니다. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- 운영 체제의 구성
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

현재 대부분의 Azure Policy 게스트 구성 정책은 머신 내의 설정만 감사합니다.
구성은 적용하지 않습니다. 단, [아래에 참조된](#applying-configurations-using-guest-configuration) 기본 제공 정책은 예외입니다.

## <a name="enable-guest-configuration"></a>게스트 구성 사용

Azure의 컴퓨터 및 연결 된 컴퓨터를 포함 하 여 사용자 환경 내 컴퓨터의 상태를 감사 하려면 다음 세부 정보를 검토 합니다.

## <a name="resource-provider"></a>리소스 공급자

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털을 통해 게스트 구성 정책의 할당을 완료하면 리소스 공급자가 자동으로 등록됩니다. [포털](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 통해 수동으로 등록할 수 있습니다.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 가상 컴퓨터에 대 한 요구 사항 배포

컴퓨터 내의 설정을 감사 하려면 [가상 머신 확장](../../../virtual-machines/extensions/overview.md) 을 사용 하도록 설정 하 고 컴퓨터에 시스템 관리 id가 있어야 합니다. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다. Id는 게스트 구성 서비스를 읽고 쓸 때 컴퓨터를 인증 하는 데 사용 됩니다. 연결 된 컴퓨터 에이전트에 포함 되어 있으므로 연결 된 컴퓨터에 대 한 확장이 필요 하지 않습니다.

> [!IMPORTANT]
> Azure 가상 머신을 감사 하려면 게스트 구성 확장 및 관리 id가 필요 합니다. 확장을 대규모로 배포 하려면 다음 정책 이니셔티브를 할당 합니다.
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>확장에 설정된 제한

머신 내부에서 실행되는 애플리케이션에 영향을 주지 않도록 확장을 제한하기 위해 게스트 구성은 CPU의 5%를 초과할 수 없습니다. 기본 제공 및 사용자 지정 정의 모두에 대해 이러한 제한이 존재합니다. Arc 연결 된 컴퓨터 에이전트의 게스트 구성 서비스에도 마찬가지입니다.

### <a name="validation-tools"></a>유효성 검사 도구

머신 내에서 게스트 구성 클라이언트는 로컬 도구를 사용하여 감사를 실행합니다.

다음 표에서는 지원 되는 각 운영 체제에서 사용 되는 로컬 도구 목록을 보여 줍니다. 기본 제공 콘텐츠의 경우 게스트 구성은 자동으로 이러한 도구 로드를 처리 합니다.

|운영 체제|유효성 검사 도구|메모|
|-|-|-|
|Windows|[PowerShell 필요한 상태 구성](/powershell/scripting/dsc/overview/overview) v2| Azure Policy에서 사용 하는 폴더에 테스트용으로 로드 됩니다. Windows PowerShell DSC와 충돌 하지 않습니다. PowerShell Core가 시스템 경로에 추가 되지 않았습니다.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Chef InSpec version 2.2.61를 기본 위치에 설치 하 고 시스템 경로에 추가 합니다. Ruby 및 Python을 포함 하 여 InSpec 패키지에 대 한 종속성도 설치 됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당이 수신되면 해당 구성에 대해 15분 간격으로 설정을 다시 확인합니다. 감사가 완료되면 게스트 구성 리소스 공급자로 결과가 전송됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. 이렇게 업데이트하면 Azure Policy에서 Azure Resource Manager 속성을 평가하게 됩니다. 요청 시 Azure Policy 평가에서는 게스트 구성 리소스 공급자에서 최신 값을 검색합니다. 그러나 해당 머신 내 구성의 새 감사는 트리거되지 않습니다.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

게스트 구성 정책은 새 버전을 포함합니다. 게스트 구성 에이전트가 호환 되지 않는 경우 Azure Marketplace에서 사용할 수 있는 이전 버전의 운영 체제가 제외 됩니다.
다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|속성|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04 이상|
|Credativ|Debian|8 이상|
|Microsoft|Windows Server|2012 이상|
|Microsoft|Windows 클라이언트|윈도우 10|
|OpenLogic|CentOS|7.3 이상|
|Red Hat|Red Hat Enterprise Linux|7.4-7.8|
|Suse|SLES|12 SP3-SP5|

사용자 지정 가상 머신 이미지는 위의 표에 나오는 운영 체제 중 하나의 이미지인 경우 게스트 구성 정책에서 지원됩니다.

## <a name="network-requirements"></a>네트워크 요구 사항

Azure의 가상 머신은 해당 로컬 네트워크 어댑터나 개인 링크를 사용 하 여 게스트 구성 서비스와 통신할 수 있습니다.

Azure Arc 컴퓨터는 온-프레미스 네트워크 인프라를 사용 하 여 Azure 서비스에 연결 하 고 규정 준수 상태를 보고 합니다.

### <a name="communicate-over-virtual-networks-in-azure"></a>Azure에서 가상 네트워크를 통해 통신

통신을 위해 가상 네트워크를 사용 하는 가상 머신에는 포트의 Azure 데이터 센터에 대 한 아웃 바운드 액세스가 필요 `443` 합니다. 아웃 바운드 트래픽을 허용 하지 않는 Azure에서 개인 가상 네트워크를 사용 하는 경우 네트워크 보안 그룹 규칙을 사용 하 여 예외를 구성 합니다. 서비스 태그 "GuestAndHybridManagement"를 게스트 구성 서비스를 참조하는 데 사용할 수 있습니다.

### <a name="communicate-over-private-link-in-azure"></a>Azure에서 개인 링크를 통해 통신

가상 머신은 게스트 구성 서비스에 대 한 통신에 [개인 링크](../../../private-link/private-link-overview.md) 를 사용할 수 있습니다. `EnablePrivateNeworkGC`이 기능을 사용 하도록 설정 하려면 이름과 값을 사용 하 여 태그를 적용 `TRUE` 합니다. 태그는 게스트 구성 정책을 컴퓨터에 적용 하기 전이나 후에 적용할 수 있습니다.

Azure platform 리소스를 사용 하 여 안전 하 고 인증 된 채널을 설정 하기 위해 Azure [가상 공용 IP 주소](../../../virtual-network/what-is-ip-address-168-63-129-16.md) 를 사용 하 여 트래픽을 라우팅합니다.

### <a name="azure-arc-connected-machines"></a>Azure Arc 연결 된 컴퓨터

Azure에 연결 된 azure 외부에 있는 노드에는 게스트 구성 서비스에 대 한 연결이 필요 합니다. [Azure Arc 설명서](../../../azure-arc/servers/overview.md)에서 제공 되는 네트워크 및 프록시 요구 사항에 대해 자세히 설명 합니다.

Azure에서 게스트 구성 리소스 공급자와 통신하려면 머신의 **443** 포트에서 아웃바운드로 Azure 데이터 센터에 액세스할 수 있어야 합니다. Azure의 네트워크에서 아웃바운드 트래픽을 허용하지 않는 경우 [네트워크 보안 그룹](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) 규칙을 사용하여 예외를 구성합니다. [서비스 태그](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement"를 게스트 구성 서비스를 참조하는 데 사용할 수 있습니다.

개인 데이터 센터의 Arc 연결 된 서버에 대해 다음 패턴을 사용 하 여 트래픽을 허용 합니다.

- 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
- 전역 URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>관리 ID 요구 사항

[가상 머신에서 게스트 구성 정책을 사용 하도록 설정 하기 위한 필수 구성 요소 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) 이니셔티브의 정책 정의는 시스템 할당 관리 id (있는 경우)를 사용 하도록 설정 합니다. 이니셔티브에는 id 만들기를 관리 하는 두 가지 정책 정의가 있습니다. 정책 정의의 IF 조건은 Azure에서 컴퓨터 리소스의 현재 상태에 따라 올바른 동작을 보장 합니다.

컴퓨터에 현재 관리 되는 id가 없는 경우에는 유효한 정책: [ \[ 미리 보기 \] : id가 없는 가상 컴퓨터에 대 한 게스트 구성 할당을 사용 하도록 시스템 할당 관리 id 추가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

컴퓨터에 현재 사용자 할당 시스템 id가 있는 경우 유효한 정책은 다음과 같습니다. [ \[ 미리 보기 \] : 사용자 할당 id를 사용 하 여 가상 컴퓨터에 대 한 게스트 구성 할당을 사용 하도록 설정 하는 시스템 할당 관리 id를 추가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) 합니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

게스트 구성 정책은 **AuditIfNotExists** 효과를 사용 합니다. 정의가 할당 되 면 백 엔드 서비스는 Azure 리소스 공급자의 모든 요구 사항에 대 한 수명 주기를 자동으로 처리 `Microsoft.GuestConfiguration` 합니다.

**AuditIfNotExists** 정책에 따라 컴퓨터에서 모든 요구 사항이 충족 될 때까지 준수 결과가 반환 되지 않습니다. 요구 사항에 [대 한 자세한 내용은 Azure 가상 컴퓨터에 대 한 요구 사항 배포](#deploy-requirements-for-azure-virtual-machines) 섹션을 참조 하십시오.

> [!IMPORTANT]
> 게스트 구성의 이전 릴리스에서는 **Deployifnoteexists** 및 **AuditIfNotExists** 정의를 결합 하기 위한 이니셔티브를 수행 해야 했습니다. **Deployifnotexists** 정의가 더 이상 필요 하지 않습니다. 정의와 intiaitives에는 레이블이 지정 되어 `[Deprecated]` 있지만 기존 할당은 계속 작동 합니다.
>
> 수동 단계가 필요 합니다. 이전에 category에서 정책 이니셔티브를 할당 한 경우 `Guest Configuration` 정책 할당을 삭제 하 고 새 정의를 할당 합니다. 게스트 구성 정책에는 다음과 같은 이름 패턴이 있습니다. `Audit <Windows/Linux> machines that <non-compliant condition>`

Azure Policy 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용 하 여 **준수 노드의 준수를 보고 합니다.** 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>업계 기준에 따라 운영 체제 설정 감사

Azure Policy의 한 이니셔티브는 "기준"에 따라 운영 체제 설정을 감사하는 기능을 제공합니다. 정의, _\[미리 보기\]: Azure 보안 기준 설정과 일치하지 않는 Windows VM 감사에는_ Active Directory 그룹 정책 기준으로 하는 규칙 세트가 포함되어 있습니다.

대부분의 설정은 매개 변수로 사용할 수 있습니다. 매개 변수를 사용하여 감사할 항목을 사용자 지정할 수 있습니다.
정책을 요구 사항에 맞게 정렬하거나 업계 규정 표준 같은 타사 정보에 정책을 매핑합니다.

일부 매개 변수는 정수 값 범위를 지원합니다. 예를 들어, 최대 암호 사용 기간 설정은 유효 그룹 정책 설정을 감사할 수 있습니다. "1,70" 범위는 사용자에게 1일 이상, 최소 70일마다 암호를 변경하도록 요구합니다.

Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 정책을 할당 하는 경우 매개 변수 파일을 사용 하 여 예외를 관리 합니다. Git와 같은 버전 제어 시스템에 파일을 체크 인합니다. 파일 변경 내용에 대한 설명은 할당이 예상 값에 대한 예외가 되는 이유를 나타내는 증명 정보를 제공합니다.

#### <a name="applying-configurations-using-guest-configuration"></a>게스트 구성을 사용하여 구성 적용

_Windows 컴퓨터의 표준 시간대를 구성_ 하는 정의만 표준 시간대를 구성 하 여 컴퓨터를 변경 합니다. 컴퓨터 내에서 설정을 구성 하기 위한 사용자 지정 정책 정의는 지원 되지 않습니다.

_구성_으로 시작하는 정의를 할당하는 경우 _필수 조건을 배포하여 Windows VM에서 게스트 구성 정책을 사용하도록 설정_ 정의도 할당해야 합니다. 선택하는 경우 이러한 정의를 하나의 이니셔티브에 결합할 수 있습니다.

> [!NOTE]
> 기본 제공 표준 시간대 정책은 컴퓨터 내부에서 설정을 구성 하는 것을 지 원하는 유일한 정의 이며, 컴퓨터 내에서 설정을 구성 하는 사용자 지정 정책은 지원 되지 않습니다.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 외부의 머신에 정책 할당

게스트 구성에 사용할 수 있는 감사 정책에는 **HybrIDCompute/machines** 리소스 종류가 포함됩니다. 정책 할당 범위 내에 있는 [서버용 Azure Arc](../../../azure-arc/servers/overview.md)에 온보딩된 모든 머신이 자동으로 포함됩니다.

### <a name="multiple-assignments"></a>여러 할당

게스트 구성 정책은 정책 할당이 다른 매개 변수를 사용하는 경우에도 머신마다 한번씩 동일한 게스트 할당만 지원합니다.

## <a name="client-log-files"></a>클라이언트 로그 파일

게스트 구성 확장은 다음 위치에 로그 파일을 씁니다.

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

여기서 `<version>`은 현재 버전 번호를 나타냅니다.

### <a name="collecting-logs-remotely"></a>원격으로 로그 수집

게스트 구성 또는 모듈 문제를 해결하는 첫 번째 단계는 `Test-GuestConfigurationPackage` cmdlet을 사용하여 [Windows에 대한 사용자 지정 게스트 구성 감사 정책을 만드는](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows) 방법에 대한 단계를 따르는 것입니다.
실패하는 경우 클라이언트 로그를 수집하여 문제를 진단하는 데 도움을 얻을 수 있습니다.

#### <a name="windows"></a>Windows

[Azure VM Run 명령](../../../virtual-machines/windows/run-command.md)을 사용하여 로그 파일에서 정보를 캡처합니다. 다음 예제 PowerShell 스크립트를 유용하게 사용할 수 있습니다.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM Run 명령](../../../virtual-machines/linux/run-command.md)을 사용하여 로그 파일에서 정보를 캡처합니다. 다음 예제 Bash 스크립트를 유용하게 사용할 수 있습니다.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>게스트 구성 샘플

게스트 구성 기본 제공 정책 샘플은 다음 위치에서 사용할 수 있습니다.

- [기본 제공 정책 정의 - 게스트 구성](../samples/built-in-policies.md#guest-configuration)
- [기본 제공 이니셔티브 - 게스트 구성](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 샘플 GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>다음 단계

- [게스트 구성 규정 준수 보기](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)에서 각 설정에 대한 세부 정보를 보는 방법을 알아봅니다.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](./definition-structure.md)를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
