---
title: Connected Machine Windows 에이전트 개요
description: 이 문서에서는 하이브리드 환경에서 호스트 되는 가상 컴퓨터의 모니터링을 지 원하는 Azure Arc 사용 가능 서버 (미리 보기) 에이전트의 자세한 개요를 제공 합니다.
ms.date: 08/06/2020
ms.topic: conceptual
ms.openlocfilehash: d922652537034bef258c5bcde78fb178b092ed16
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212974"
---
# <a name="overview-of-azure-arc-enabled-servers-preview-agent"></a>Azure Arc 사용 서버 (미리 보기) 에이전트 개요

Azure Arc 사용 서버 (미리 보기)에 연결 된 컴퓨터 에이전트를 사용 하면 회사 네트워크 또는 다른 클라우드 공급자의 Azure 외부에서 호스트 되는 Windows 및 Linux 컴퓨터를 관리할 수 있습니다. 이 문서에서는 에이전트, 시스템 및 네트워크 요구 사항과 다양한 배포 모델을 상세히 살펴봅니다.

## <a name="agent-component-details"></a>에이전트 구성 요소 세부 정보

Azure 연결 된 컴퓨터 에이전트 패키지에는 여러 개의 논리적 구성 요소가 포함 되어 있습니다 .이 구성 요소는 함께 제공 됩니다.

* 하이브리드 인스턴스 메타 데이터 서비스 (HIMDS)는 Azure 및 연결 된 컴퓨터의 Azure id에 대 한 연결을 관리 합니다.

* 게스트 구성 에이전트는 컴퓨터가 필요한 정책을 준수 하는지 여부를 평가 하는 것과 같은 게스트 내 정책 및 게스트 구성 기능을 제공 합니다.

    연결 되지 않은 컴퓨터에 대 한 Azure Policy [게스트 구성과](../../governance/policy/concepts/guest-configuration.md) 관련 된 다음 동작에 유의 하세요.

    * 연결이 끊어진 컴퓨터를 대상으로 하는 게스트 구성 정책 할당은 영향을 받지 않습니다.
    * 게스트 할당은 14 일 동안 로컬에 저장 됩니다. 14 일 기간 내에 연결 된 컴퓨터 에이전트가 서비스에 다시 연결 하는 경우 정책 할당이 다시 적용 됩니다.
    * 할당은 14 일 후에 삭제 되며 14 일 기간 후에는 컴퓨터에 재할당 되지 않습니다.

* 확장 에이전트는 설치, 제거 및 업그레이드를 포함 하 여 VM 확장을 관리 합니다. 확장은 Azure에서 다운로드 되 고 Windows의 `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` 폴더 및 Linux에 복사 됩니다 `/opt/GC_Ext/downloads` . Windows에서는 확장이 다음 경로에 설치 되 `%SystemDrive%\Packages\Plugins\<extension>` 고 Linux에서 확장이에 설치 됩니다 `/var/lib/waagent/<extension>` .

## <a name="download-agents"></a>에이전트 다운로드

Windows 및 Linux용 Azure Connected Machine 에이전트 패키지는 아래에 나열된 위치에서 다운로드할 수 있습니다.

* Microsoft 다운로드 센터에서 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드합니다.

* Linux 에이전트 패키지는 선호하는 배포 패키지 형식(.RPM 또는 .DEB)을 사용하여 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)를 통해 배포됩니다.

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 자세한 내용은 [여기](manage-agent.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure Connected Machine 에이전트를 공식적으로 지원하는 Windows 및 Linux 운영 체제 버전은 다음과 같습니다. 

- Windows Server 2012 R2 이상(Windows Server Core 포함)
- Ubuntu 16.04 및 18.04 (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

>[!NOTE]
>이 Windows용 Connected Machine 에이전트의 미리 보기 릴리스는 영어를 사용하도록 구성된 Windows Server만 지원합니다.
>

### <a name="required-permissions"></a>필요한 사용 권한

* 머신을 등록하려면 **Azure Connected Machine Onboarding** 역할의 멤버여야 합니다.

* 컴퓨터를 읽고, 수정 하 고, 다시 등록 하거나, 삭제 하려면 **Azure 연결 된 컴퓨터 리소스 관리자** 역할의 구성원입니다. 

### <a name="azure-subscription-and-service-limits"></a>Azure 구독 및 서비스 한도

Azure Arc 사용 서버 (미리 보기)를 사용 하 여 컴퓨터를 구성 하기 전에 Azure Resource Manager [구독 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 및 [리소스 그룹 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) 을 검토 하 여 연결할 컴퓨터 수를 계획 합니다.

### <a name="transport-layer-security-12-protocol"></a>전송 계층 보안 1.2 프로토콜

Azure로 전송되는 데이터의 보안을 보장하려면 TLS(전송 계층 보안) 1.2를 사용하도록 머신을 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**.

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다. | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows Server 2012 R2 이상 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면|

### <a name="networking-configuration"></a>네트워킹 구성

Linux 및 Windows용 Connected Machine 에이전트는 TCP 포트 443을 통해 안전하게 Azure Arc로 아웃바운드 통신을 수행합니다. 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우 아래의 요구 사항을 검토하여 필요한 네트워크 구성을 파악하세요.

방화벽 또는 프록시 서버가 아웃바운드 연결을 제한하는 경우 아래에 나열된 URL이 차단되지 않았는지 확인합니다. 에이전트가 서비스와 통신하는 데 필요한 IP 범위 또는 도메인 이름만 허용하는 경우 다음 서비스 태그와 URL에 대한 액세스도 허용해야 합니다.

서비스 태그:

* AzureActiveDirectory
* AzureTrafficManager

URL:

| 에이전트 리소스 | Description |
|---------|---------|
|`management.azure.com`|Azure 리소스 관리자|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`agentserviceapi.azure-automation.net`|게스트 구성|
|`*-agentservice-prod-1.azure-automation.net`|게스트 구성|
|`*.guestconfiguration.azure.com` |게스트 구성|
|`*.his.arc.azure.com`|하이브리드 ID 서비스|

각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. Microsoft는 각 Azure 서비스 및 여기에 사용되는 IP 범위를 포함하는 주간 업데이트를 게시합니다. 자세한 내용은 [서비스 태그](../../virtual-network/security-overview.md#service-tags)를 검토하세요.

대부분의 서비스에는 현재 서비스 태그 등록이 없기 때문에 서비스 태그 IP 주소 범위 정보 외에도 앞에서 나온 표의 URL이 필요합니다. 따라서 IP 주소는 변경될 수 있습니다. 방화벽 구성에 IP 주소 범위가 필요한 경우 모든 Azure 서비스에 대한 액세스를 허용하기 위해 **AzureCloud** 서비스 태그를 사용해야 합니다. 이러한 URL의 보안 모니터링 또는 검사를 해제하지 말고, 다른 인터넷 트래픽처럼 허용합니다.

### <a name="register-azure-resource-providers"></a>Azure 리소스 공급자 등록

Azure Arc 사용 서버 (미리 보기)는이 서비스를 사용 하기 위해 구독의 다음 Azure 리소스 공급자에 종속 됩니다.

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

리소스 공급자가 등록되어 있지 않으면 다음 명령을 사용하여 등록할 수 있습니다.

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

[Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계에 따라 Azure Portal에서 리소스 공급자를 등록할 수도 있습니다.

## <a name="installation-and-configuration"></a>설치 및 구성

하이브리드 환경의 머신을 Azure에 직접 연결할 때 요구 사항에 따라 다양한 방법을 사용할 수 있습니다. 다음 표를 통해 조직에 가장 적합한 방법을 결정할 수 있습니다.

| 방법 | Description |
|--------|-------------|
| 대화형 | [Azure Portal에서 머신 연결](onboard-portal.md)의 단계에 따라 머신 한 대 또는 약간의 머신에 에이전트를 수동으로 설치합니다.<br> Azure Portal에서 스크립트를 생성하고 머신에서 실행하여 에이전트의 설치 및 구성 단계를 자동화할 수 있습니다.|
| 대규모 | [서비스 주체를 사용하여 머신 연결](onboard-service-principal.md)의 지침에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 방법은 비 대화형으로 머신을 연결하는 서비스 주체를 만듭니다.|
| 대규모 | [Windows PowerShell DSC 사용](onboard-dsc.md) 방법에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 메서드는 서비스 주체를 사용하여 머신을 비 대화형으로 PowerShell DSC와 연결합니다. |

## <a name="connected-machine-agent-technical-overview"></a>연결 된 컴퓨터 에이전트 기술 개요

### <a name="windows-agent-installation-details"></a>Windows 에이전트 설치 세부 정보

다음 세 가지 방법 중 하나를 사용하여 Windows용 Connected Machine 에이전트를 설치할 수 있습니다.

* `AzureConnectedMachineAgent.msi` 파일을 두 번 클릭합니다.
* 명령 셸에서 `AzureConnectedMachineAgent.msi` Windows Installer 패키지를 실행하여 수동으로 설치합니다.
* PowerShell 세션에서 스크립팅된 메서드를 사용합니다.

Windows용 Connected Machine 에이전트를 설치하면 다음과 같은 추가 시스템 차원 구성 변경 내용이 적용됩니다.

* 설치 중에 생성되는 설치 폴더는 다음과 같습니다.

    |폴더 |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |에이전트 지원 파일이 포함되어 있는 기본 설치 경로입니다.|
    |%ProgramData%\AzureConnectedMachineAgent |에이전트 구성 파일이 포함되어 있습니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |가져온 토큰이 포함되어 있습니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |서비스 등록 정보를 기록하는 에이전트 구성 파일 `agentconfig.json`이 포함되어 있습니다.|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | 게스트 구성 에이전트 파일을 포함 하는 설치 경로입니다. |
    |%ProgramData%\GuestConfig |Azure의 (적용) 정책을 포함 합니다.|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | 확장은 Azure에서 다운로드 되 고 여기에 복사 됩니다.|

* 에이전트를 설치하는 동안 대상 머신에 다음 Windows 서비스가 만들어집니다.

    |서비스 이름 |표시 이름 |프로세스 이름 |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds.exe |이 서비스는 azure 인스턴스 메타 데이터 서비스 (IMDS)를 구현 하 여 Azure 및 연결 된 컴퓨터의 Azure id에 대 한 연결을 관리 합니다.|
    |DscService |Guest Configuration Service |dsc_service.exe |게스트 내 정책을 구현 하기 위해 Azure 내에서 사용 되는 DSC v2 (필요한 상태 구성) 코드 베이스입니다.|

* 에이전트 설치 중에 다음 환경 변수가 생성됩니다.

    |속성 |기본값 |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 문제 해결에 사용할 수 있는 로그 파일은 여러 가지가 있습니다. 이 내용은 다음 표에 설명되어 있습니다.

    |로그 |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |에이전트 (HIMDS) 서비스의 세부 정보 및 Azure와의 상호 작용을 기록 합니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |자세한 정보(-v) 인수를 사용하는 경우 azcmagent tool 명령의 출력이 포함됩니다.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |DSC 서비스 활동의 세부 정보,<br> 특히 HIMDS 서비스와 Azure Policy 간의 연결입니다.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |DSC 서비스 원격 분석 및 자세한 정보 로깅에 대한 세부 정보를 기록합니다.|
    |%SystemDrive%\ProgramData\GuestConfig\ ext_mgr_logs|확장 에이전트 구성 요소에 대 한 세부 정보를 기록 합니다.|
    |%SystemDrive%\ProgramData\GuestConfig\ extension_logs\<Extension>|설치 된 확장의 세부 정보를 기록 합니다.|

* 로컬 보안 그룹 **하이브리드 에이전트 확장 애플리케이션**이 만들어집니다.

* 에이전트를 제거하는 동안 다음 아티팩트가 제거되지 않습니다.

    * %ProgramFiles%\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent 및 하위 디렉터리
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux 에이전트 설치 세부 정보

Linux용 Connected Machine 에이전트는 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 호스팅되는 배포(.RPM 또는 .DEB)에 대한 기본 설정 패키지 형식으로 제공됩니다. 에이전트는 셸 스크립트 번들 [Install_linux_azcmagent.sh](https://aka.ms/azcmagent)를 사용하여 설치되고 구성됩니다.

Linux용 Connected Machine 에이전트를 설치하면 다음과 같은 추가 시스템 차원 구성 변경 내용이 적용됩니다.

* 설치 중에 생성되는 설치 폴더는 다음과 같습니다.

    |폴더 |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |에이전트 지원 파일이 포함되어 있는 기본 설치 경로입니다.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | 게스트 구성 에이전트 파일을 포함 하는 설치 경로입니다.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |가져온 토큰이 포함되어 있습니다.|
    |/var/lib/GuestConfig |Azure의 (적용) 정책을 포함 합니다.|
    |/opt/GC_Ext/다운로드|확장은 Azure에서 다운로드 되 고 여기에 복사 됩니다.|

* 에이전트를 설치하는 동안 대상 머신에 다음 디먼이 만들어집니다.

    |서비스 이름 |표시 이름 |프로세스 이름 |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Hybrid Instance Metadata Service |/opt/azcmagent/bin/himds |이 서비스는 azure 인스턴스 메타 데이터 서비스 (IMDS)를 구현 하 여 Azure 및 연결 된 컴퓨터의 Azure id에 대 한 연결을 관리 합니다.|
    |dscd.service |Guest Configuration Service |/opt/DSC/dsc_linux_service |게스트 내 정책을 구현하기 위해 Azure 내부에서 사용되는 DSC(Desired State Configuration) v2 코드베이스입니다.|

* 문제 해결에 사용할 수 있는 로그 파일은 여러 가지가 있습니다. 이 내용은 다음 표에 설명되어 있습니다.

    |로그 |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |에이전트 (HIMDS) 서비스의 세부 정보 및 Azure와의 상호 작용을 기록 합니다.|
    |/var/opt/azcmagent/log/azcmagent.log |자세한 정보(-v) 인수를 사용하는 경우 azcmagent tool 명령의 출력이 포함됩니다.|
    |/opt/logs/dsc.log |DSC 서비스 활동의 세부 정보,<br> 특히 himds 서비스와 Azure Policy 간의 연결을 기록합니다.|
    |/opt/logs/dsc.telemetry.txt |DSC 서비스 원격 분석 및 자세한 정보 로깅에 대한 세부 정보를 기록합니다.|
    |/var/lib/GuestConfig/ext_mgr_logs |확장 에이전트 구성 요소에 대 한 세부 정보를 기록 합니다.|
    |/var/log/GuestConfig/extension_logs|설치 된 확장의 세부 정보를 기록 합니다.|

* 에이전트 설치 중에 다음 환경 변수가 생성됩니다. 이러한 변수는 `/lib/systemd/system.conf.d/azcmagent.conf`에 설정됩니다.

    |속성 |기본값 |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 에이전트를 제거하는 동안 다음 아티팩트가 제거되지 않습니다.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>다음 단계

Azure Arc 사용 서버 (미리 보기) 평가를 시작 하려면 [Azure Portal에서 azure에 하이브리드 컴퓨터 연결](onboard-portal.md)문서를 따르세요.
