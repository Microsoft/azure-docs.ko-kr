---
title: Azure Automation으로 변경 내용 추적
description: 변경 내용 추적 솔루션을 사용하면 사용자 환경에서 발생하는 소프트웨어 및 Windows 서비스의 변경 내용을 식별할 수 있습니다.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e997d9e9f38fee52cd9fc007fe12cac68e3aa5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075056"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적

이 문서에서는 변경 내용 추적 솔루션을 사용하여 사용자 환경의 변경 내용을 쉽게 식별할 수 있습니다. 이 솔루션은 Windows 및 Linux 소프트웨어, Windows 및 Linux 파일, Windows 레지스트리 키, Windows 서비스 및 Linux 디먼의 변경 내용을 추적합니다. 구성 변경 내용을 식별하면 운영 문제를 쉽게 특정할 수 있습니다.

Linux 디먼 모니터링 되는 서버에 설치 된 소프트웨어, Windows 서비스, Windows 레지스트리 및 파일을 변경 처리를 위해 클라우드에서 Azure Monitor 서비스에 전송 됩니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 변경 내용 추적 대시보드의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.

> [!NOTE]
> Azure Automation 변경 내용 추적 virtual machines의 변경 내용을 추적합니다. Azure Resource Manager 속성 변경 내용을 추적 하려면 Azure 리소스 그래프를 볼 [변경 내용](../governance/resource-graph/how-to/get-resource-changes.md)합니다.

## <a name="supported-windows-operating-systems"></a>지원되는 Windows 운영 체제

Windows 에이전트에 대해 다음 버전의 Windows 운영 체제가 공식적으로 지원됩니다.

* Windows Server 2008 R2 이상

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

다음 Linux 배포판이 공식적으로 지원됩니다. 하지만 Linux 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다. 다른 설명이 없는 한, 나열된 각 주 버전의 모든 부 버전이 지원됩니다.  

### <a name="64-bit"></a>64비트

* CentOS 6 및 7
* Amazon Linux 2017.09
* Oracle Linux 6 및 7
* Red Hat Enterprise Linux Server 6 및 7
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS, 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32비트

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS 및 16.04 LTS

## <a name="onboard"></a>변경 내용 추적 및 인벤토리 사용

변경 내용 추적을 시작하려면 변경 내용 추적 및 인벤토리 솔루션을 사용하도록 설정해야 합니다. 다양한 방법으로 변경 내용 추적 및 인벤토리에 컴퓨터를 등록할 수 있습니다. 아래에는 권장/지원되는 솔루션 등록 방법이 나와 있습니다.

* [가상 머신에서 등록](automation-onboard-solutions-from-vm.md)
* [여러 컴퓨터에서 찾아보기](automation-onboard-solutions-from-browse.md)
* [Automation 계정에서 등록](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook 사용](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 구성

솔루션에 컴퓨터를 등록하는 방법을 알아보려면 [Automation 솔루션 온보딩](automation-onboard-solutions-from-automation-account.md)을 방문하세요. 변경 내용 추적과 인벤토리 솔루션을 컴퓨터에 등록한 후에는 추적할 항목을 구성할 수 있습니다. 새 파일 또는 레지스트리 키를 추적하도록 설정하면 변경 내용 추적 및 인벤토리가 모두 활성화됩니다.

Windows 및 Linux 모두에서 파일의 변경 내용 추적을 위해 파일의 MD5 해시를 사용합니다. 그런 다음, 이 해시를 사용하여 마지막 인벤토리 이후 변경이 있는지 검색합니다.

### <a name="configure-linux-files-to-track"></a>추적할 Linux 파일 구성

다음 단계를 사용하여 Linux 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Linux 파일**을 선택한 다음, **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
3. **변경 내용 추적에 대해 Linux 파일 추가**에서 추적할 파일 또는 디렉터리에 대한 정보를 입력하고, **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 파일의 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인할 경로입니다. 예: "/etc/* .conf"       |
|경로 유형     | 추적할 항목 유형이며, 가능한 값은 File 및 Directory입니다.        |
|재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
|sudo 사용     | 항목을 확인할 때 sudo가 사용되는지 여부를 결정합니다.         |
|링크     | 디렉터리를 트래버스할 때 기호화된 링크에서 처리하는 방법을 결정합니다.<br> **무시** - 바로 가기 링크가 무시되고 참조된 파일/디렉터리가 포함되지 않습니다.<br>**Follow** - 재귀 중에 기호화된 링크를 따르고 참조된 파일/디렉터리도 포함합니다.<br>**관리** - 기호화된 링크를 따르고 반환된 콘텐츠를 변경할 수 있도록 허용합니다.     |
|모든 설정에 대한 파일 콘텐츠 업로드| 추적된 변경 내용에 대해 파일 콘텐츠 업로드를 설정 또는 해제합니다. 사용 가능한 옵션은 **True** 또는 **False**입니다.|

> [!NOTE]
> "Manage" 링크 옵션은 권장되지 않습니다. 파일 콘텐츠 검색은 지원되지 않습니다.

### <a name="configure-windows-files-to-track"></a>추적할 Windows 파일 구성

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 파일을 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Windows 파일**을 선택한 다음, **+ 추가**를 클릭하여 추적할 새 파일을 추가합니다.
3. **변경 내용 추적에 대해 Windows 파일 추가**에서 추적할 파일에 대한 정보를 입력하고, **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 파일의 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인할 경로입니다(예: "c:\temp\\\*.txt").<br>"%winDir%\System32\\\*.*"와 같은 환경 변수도 사용할 수 있습니다.       |
|재귀     | 추적할 항목을 찾을 때 재귀가 사용되는지 결정합니다.        |
|모든 설정에 대한 파일 콘텐츠 업로드| 추적된 변경 내용에 대해 파일 콘텐츠 업로드를 설정 또는 해제합니다. 사용 가능한 옵션은 **True** 또는 **False**입니다.|

## <a name="wildcard-recursion-and-environment-settings"></a>와일드 카드, 재귀 및 환경 설정

재귀를 사용하면 와일드 카드를 지정하여 디렉터리 추적을 간소화할 수 있고, 환경 변수를 사용하면 여러 또는 동적 드라이브 이름이 있는 환경에서 파일을 추적할 수 있습니다. 다음 목록에는 재귀를 구성할 때 알고 있어야 하는 일반적인 정보가 나와 있습니다.

* 와일드 카드는 여러 파일을 추적하는 데 필요
* 와일드 카드를 사용하는 경우 경로의 마지막 세그먼트에만 사용할 수 있습니다. (예: `c:\folder\*file*` 또는 `/etc/*.conf`)
* 환경 변수의 경로가 잘못된 경우 유효성 검사는 성공하지만 인벤토리를 실행하면 해당 경로가 실패합니다.
* 경로를 설정할 때 `c:\*.*` 같은 일반 경로를 사용하면 너무 많은 폴더가 트래버스되므로 사용하면 안 됩니다.

## <a name="configure-file-content-tracking"></a>파일 콘텐츠 추적 구성

파일 콘텐츠 변경 내용 추적을 사용하여 파일의 변경 이전과 이후의 콘텐츠를 볼 수 있습니다. Windows/Linux 파일에 이 기능을 사용할 수 있습니다. 파일을 변경할 때마다 파일 콘텐츠가 스토리지 계정에 저장되며 변경 전/후의 파일이 가로나 세로로 표시됩니다. 자세한 내용은 [추적된 파일의 콘텐츠 보기](change-tracking-file-contents.md)를 참조하세요.

![파일의 변경 내용 보기](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>추적할 Windows 레지스트리 키 구성

다음 단계를 사용하여 Windows 컴퓨터에서 추적할 레지스트리 키를 구성합니다.

1. Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택합니다. **설정 편집**(기어 기호)을 클릭합니다.
2. **변경 내용 추적** 페이지에서 **Windows 레지스트리**를 선택한 다음, **+ 추가**를 클릭하여 추적할 새 레지스트리 키를 추가합니다.
3. **변경 내용 추적에 대해 Windows 레지스트리 추가**에서 추적할 키에 대한 정보를 입력하고 **저장**을 클릭합니다.

|자산  |설명  |
|---------|---------|
|사용     | 설정이 적용되는지 여부를 결정합니다.        |
|Item Name     | 추적할 레지스트리 키의 식별 이름입니다.        |
|그룹     | 논리적으로 레지스트리 키를 그룹화하는 그룹 이름입니다.        |
|Windows 레지스트리 키   | 레지스트리 키를 확인할 경로입니다. 예를 들면 다음과 같습니다. "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>제한 사항

현재 변경 내용 추적 솔루션은 다음 항목을 지원하지 않습니다.

* Windows 레지스트리 추적을 위한 재귀
* 네트워크 파일 시스템

기타 제한 사항은 다음과 같습니다.

* **최대 파일 크기** 열과 값은 현재 구현에서 사용되지 않습니다.
* 30분 수집 주기에서 2500개 이상의 파일을 수집하는 경우 솔루션 성능이 저하될 수 있습니다.
* 네트워크 트래픽이 많으면 변경 레코드를 표시하는 데 최대 6시간이 걸릴 수 있습니다.
* 컴퓨터를 종료하면서 구성이 수정되었으면 컴퓨터에서 이전 구성에 속한 변경 내용을 게시할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

변경 내용 추적 솔루션에 현재 다음과 같은 문제가 있습니다.

* Windows Server 2016 Core RS3 컴퓨터에서 핫픽스 업데이트가 수집되지 않습니다.
* Linux 데몬 변경 된 경우에 변경 된 상태를 표시할 수 있습니다. 이 방법으로 인해는 `SvcRunLevels` 필드 캡처됩니다.

## <a name="change-tracking-data-collection-details"></a>변경 내용 추적 데이터 수집 정보

다음 테이블에서는 변경 형식에 대한 데이터 컬렉션 빈도를 보여 줍니다. 모든 유형에 대해 현재 상태의 데이터 스냅숏은 최소한 24시간마다 새로 고쳐집니다.

| **변경 유형** | **Frequency(빈도)** |
| --- | --- |
| Windows 레지스트리 | 50분 |
| Windows 파일 | 30분 |
| Linux 파일 | 15분 |
| Windows 서비스 | 10초에서 30분</br> Default: 30분 |
| Linux 데몬 | 5분 |
| Windows 소프트웨어 | 30분 |
| Linux 소프트웨어 | 5분 |

다음 표는 변경 내용 추적에 대한 머신당 추적된 항목 한도를 보여 줍니다.

| **리소스** | **제한**| **참고 사항** |
|---|---|---|
|파일|500||
|레지스트리|250||
|Windows 소프트웨어|250|소프트웨어 업데이트를 포함하지 않음|
|Linux 패키지|1250||
|Services|250||
|디먼|250||

변경 내용 추적 및 인벤토리를 사용하는 머신의 평균 Log Analytics 데이터 사용량은 월별 약 40MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 사용자 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다.

### <a name="windows-service-tracking"></a>Windows 서비스 추적

Windows 서비스에 대한 기본 컬렉션 빈도는 30분입니다. 빈도를 구성하려면 **변경 내용 추적**으로 이동합니다. **Windows 서비스** 탭의 **설정 편집**에는 Windows 서비스의 컬렉션 빈도를 10초에서 30분 사이로 변경할 수 있는 슬라이더가 있습니다. 원하는 빈도에 슬라이더 막대를 이동하면 자동으로 저장됩니다.

![Windows 서비스 슬라이더](./media/automation-change-tracking/windowservices.png)

에이전트는 변경 내용을 추적하고, 에이전트의 성능을 최적화합니다. 임계값을 너무 높게 설정하여 서비스가 원래 상태로 돌아가면 변경 내용이 누락될 수 있습니다. 빈도를 더 작은 값으로 설정하면 누락될 수 있는 변경 내용을 파악할 수 있습니다.

> [!NOTE]
> 에이전트가 10초 간격으로 변경 내용을 추적하는 동안 데이터를 포털에 표시하는 데 몇 분 정도가 걸립니다. 포털에 표시하는 시간 동안 변경 내용이 추적되고 기록됩니다.
  
### <a name="registry-key-change-tracking"></a>레지스트리 키 변경 내용 추적

레지스트리 키의 변경 내용을 모니터링하는 목적은 타사 코드 및 맬웨어가 활성화될 수 있는 확장성 지점을 정확하게 찾기 위한 것입니다. 다음 목록에서는 미리 구성된 레지스트리 키의 목록을 보여 줍니다. 이러한 키는 구성되어 있지만 사용할 수 없습니다. 이러한 레지스트리 키를 추적하려면 각 레지스트리 키를 사용하도록 설정해야 합니다.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;시작 시 실행되는 스크립트를 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;종료 시 실행되는 스크립트를 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;사용자가 Windows 계정에 로그인하기 전에 로드되는 키를 모니터링합니다. 키는 64비트 컴퓨터에서 실행되는 32비트 프로그램에 사용됩니다.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;애플리케이션 설정의 변경 내용을 모니터링합니다.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 탐색기에 직접 연결되고 일반적으로 Explorer.exe를 사용하여 프로세스 내에서 실행되는 일반적인 자동 시작 항목을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;아이콘 오버레이 처리기 등록을 모니터링합니다.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 아이콘 오버레이 처리기 등록을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer에 대한 새 브라우저 도우미 개체 플러그 인을 모니터링합니다. 64비트 컴퓨터에서 실행되는 32비트 프로그램에 대해 현재 페이지의 DOM(문서 개체 모델)에 액세스하고 탐색을 제어하는 데 사용됩니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 사용자 지정 도구 메뉴 및 사용자 지정 도구 모음 단추와 같은 새 Internet Explorer 확장을 모니터링합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;wavemapper, wave1 및 wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;64비트 컴퓨터에서 실행되는 32비트 프로그램에 대한 wavemapper, wave1 and wave2, msacm.imaadpcm, .msadpcm, .msgsm610 및 vidc와 관련된 32비트 드라이버를 모니터링합니다. SYSTEM.INI 파일의 [drivers] 섹션과 유사합니다.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;알려졌거나 일반적으로 사용되는 시스템 DLL 목록을 모니터링합니다. 이 시스템은 시스템 DLL의 트로이 목마 버전을 삭제하여 사용자가 취약한 애플리케이션 디렉터리에 대한 권한을 악용하지 못하게 합니다.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows 운영 체제에 대한 대화형 로그온 지원 모델인 Winlogon에서 이벤트 알림을 받을 수 있는 패키지 목록을 모니터링합니다.|

## <a name="network-requirements"></a>네트워크 요구 사항

다음 주소는 변경 내용 추적을 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 수행됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>변경 내용 추적 사용

솔루션을 사용하도록 설정되면 Automation 계정의 **구성 관리** 아래에서 **변경 내용 추적**을 선택하여 모니터링되는 컴퓨터에 대한 변경 내용 요약을 볼 수 있습니다.

컴퓨터에 대한 변경 내용을 확인한 다음, 각 이벤트에 대한 세부 정보를 살펴볼 수 있습니다. 차트 위쪽의 드롭다운을 사용하여 변경 유형 및 시간 범위에 따라 차트 및 세부 정보를 제한할 수 있습니다. 또한 차트를 클릭하고 끌어서 사용자 지정 시간 범위를 선택할 수도 있습니다.

![변경 내용 추적 대시보드의 이미지](./media/automation-change-tracking/change-tracking-dash01.png)

변경 내용 또는 이벤트를 클릭하면 해당 변경 내용에 대한 자세한 정보가 표시됩니다. 예제에서 알 수 있듯이 서비스의 시작 유형은 수동에서 자동으로 변경되었습니다.

![변경 내용 추적 세부 정보의 이미지](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>로그 검색

포털에서 제공하는 세부 정보 외에도 로그에 대한 검색을 수행할 수 있습니다. 사용 하 여는 **변경 내용 추적** 페이지 열기를 클릭 **Log Analytics**, 그러면 합니다 **로그** 페이지.

### <a name="sample-queries"></a>샘플 쿼리

다음 표에서는 이 솔루션에서 수집된 변경 레코드에 대한 로그 검색 샘플을 제공합니다.

|쿼리  |설명  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "자동"<br>&#124; where SvcState == "중지됨"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Auto로 설정되었지만 Stopped로 보고된 Windows 서비스에 대한 최근의 인벤토리 레코드를 표시합니다.<br>결과는 해당 SoftwareName 및 Computer에 대한 최근의 레코드로 제한됩니다.      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "소프트웨어" and ChangeCategory == "제거됨"<br>&#124; order by TimeGenerated desc|제거된 소프트웨어에 대한 변경 레코드를 표시합니다.|

## <a name="alert-on-changes"></a>변경 시 경고 표시

변경 내용 추적 및 인벤토리의 주요 기능은 하이브리드 환경의 구성 상태 변경 내용과 구성 상태에 대해 경고를 표시하는 것입니다.  

다음 예제의 스크린샷에는 컴퓨터에서 `C:\windows\system32\drivers\etc\hosts` 파일이 수정되었다는 내용이 표시되어 있습니다. 이 파일이 중요한 이유는, 호스트 파일은 Windows에서 호스트 이름을 IP 주소로 확인하는 데 사용되며 DNS보다도 우선 순위가 높기 때문입니다. 이로 인해 연결 문제가 발생할 수 있으며 악성 웹 사이트나 기타 위험한 웹 사이트로 트래픽이 리디렉션될 수도 있습니다.

![호스트 파일 변경 내용을 보여 주는 차트](./media/automation-change-tracking/changes.png)

이 변경 내용을 자세히 분석하려면 **Log Analytics**를 클릭하여 로그 검색으로 이동합니다. 로그 검색이 표시되면 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 쿼리를 사용하여 호스트 파일의 콘텐츠 변경 내용을 검색합니다. 이 쿼리는 정규화된 경로에 "hosts"라는 단어가 포함된 파일의 파일 콘텐츠 변경 내용을 포함하는 변경 내용을 찾습니다. 경로 부분을 정규화된 형식(예: `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`)으로 변경하여 특정 파일을 요청할 수도 있습니다.

쿼리에서 원하는 결과가 반환되면 로그 검색 환경에서 **새 경고 규칙** 단추를 클릭하여 경고 만들기 페이지를 엽니다. Azure Portal에서 **Azure Monitor**를 통해 이 환경으로 이동할 수도 있습니다. 경고 만들기 환경에서 쿼리를 다시 확인하고 경고 논리를 수정합니다. 여기서는 환경의 모든 컴퓨터에서 변경 내용이 여러 개 검색되면 경고를 트리거할 수 있습니다.

![호스트 파일의 변경 내용 추적을 위한 변경 쿼리를 보여 주는 이미지](./media/automation-change-tracking/change-query.png)

조건 논리를 설정한 후에는 트리거되는 경고에 대응하여 작업을 수행할 작업 그룹을 할당합니다. 여기서는 전송할 이메일과 생성할 ITSM 티켓을 설정했습니다.  Azure Function, Automation Runbook, webhook, Logic App 등을 트리거하는 것처럼 기타 여러 유용한 작업을 수행할 수도 있습니다.

![변경 시 경고를 생성하도록 작업 그룹을 구성하는 방법을 보여 주는 이미지](./media/automation-change-tracking/action-groups.png)

모든 매개 변수와 논리를 설정하고 나면 환경에 경고를 적용할 수 있습니다.

### <a name="alert-suggestions"></a>경고 제안

호스트 파일 변경 시에 경고를 표시하는 기능은 변경 내용 추적 또는 인벤토리 데이터에 경고를 적용하는 좋은 예 중 하나입니다. 하지만 아래 섹션에 정의된 사례와 해당 예제 쿼리를 비롯하여 기타 여러 경고 시나리오를 적용할 수도 있습니다.

|쿼리  |설명  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|중요한 파일 시스템의 변경 내용을 추적하는 데 유용 합니다.|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|주요 구성 파일의 수정 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|시스템에 중요한 서비스의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|시스템에 중요한 서비스의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|잠긴 소프트웨어 구성이 필요한 환경에 유용합니다.|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|오래되었거나 규정을 준수하지 않는 소프트웨어 버전이 설치된 컴퓨터를 확인하는 데 유용합니다. 변경 내용이 아닌 마지막으로 보고된 구성 상태를 보고합니다.|
|ConfigurationChange <br>&#124; where RegistryKey == "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 중요한 바이러스 백신 키의 변경 내용을 추적하는 데 유용합니다.|
|ConfigurationChange <br>&#124; where RegistryKey contains "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| 방화벽 설정의 변경 내용을 추적하는 데 유용합니다.|

## <a name="next-steps"></a>다음 단계

솔루션 사용에 대한 자세한 내용은 변경 내용 추적에 대한 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [환경 변경 문제 해결](automation-tutorial-troubleshoot-changes.md)

* 사용 하 여 [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md) 자세한 변경 내용 추적 데이터를 볼 수 있습니다.

