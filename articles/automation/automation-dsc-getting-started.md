---
title: Azure Automation 상태 구성 시작하기
description: Azure Automation 상태 구성(DSC)에서 가장 일반적인 작업의 설명 및 예제
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 60cd2d21167739e824489e30ebd187a5fc0cc12d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074572"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Azure Automation 상태 구성 시작하기

이 문서에서는 Azure Automation 상태 구성으로 만들기, 가져오기 및 구성 컴파일링, 관리할 머신 온보딩 및 보고서 보기 등과 같은 가장 일반적인 작업을 수행하는 방법을 설명합니다. Azure Automation 상태 구성에 대한 개요는 [Azure Automation 상태 구성 개요](automation-dsc-overview.md)를 참조하세요. DSC(필요한 상태 구성) 설명서는 [Windows PowerShell 필요한 상태 구성 개요](/powershell/dsc/overview)를 참조하세요.

이 문서에서는 Azure Automation 상태 구성 사용에 대한 단계별 가이드를 제공합니다. 이 문서에 설명된 단계를 수행하지 않고 이미 설정된 샘플 환경을 원하는 경우 다음 Resource Manager 템플릿을 사용할 수 있습니다. [Azure Automation 관리되는 노드 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration) 이 템플릿은 Azure Automation 상태 구성에 의해 관리되는 Azure VM을 포함하는 완료된 Azure Automation 상태 구성 환경을 설정합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 예제를 완료하려면 다음이 필요합니다.

- Azure Automation 계정. Azure Automation 실행 계정 만들기에 대한 지침은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 참조하세요.
- Azure Resource Manager VM (클래식 아님) 실행 되는 [지원 되는 운영 체제](automation-dsc-overview.md#operating-system-requirements)합니다. VM 만들기에 대한 지침은 [Azure 포털에서 첫 번째 Windows 가상 머신 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>DSC 구성 만들기

노드를 할당하는 방법에 따라 [웹 서버](/powershell/dsc/configurations) Windows 기능(IIS)의 존재 또는 부재를 확인하는 간단한 **DSC 구성**을 만듭니다.

1. [VSCode](https://code.visualstudio.com/docs)(또는 임의의 텍스트 편집기)를 시작합니다.
1. 다음 텍스트를 입력합니다.

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. 파일을 `TestConfig.ps1`(으)로 저장합니다.

이 구성은 각 노드 블록에서 [웹 서버](/powershell/dsc/windowsfeatureresource)기능의 존재 또는 부재를 확인하는 하나의 리소스, **WindowsFeature 리소스** 를 호출합니다.

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Automation으로 구성 가져오기

다음으로 Automation 계정으로 구성을 가져옵니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 선택합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **+ 추가**를 클릭합니다.
1. **구성 가져오기** 페이지에서 컴퓨터의 `TestConfig.ps1` 파일로 이동합니다.

   ![**구성 가져오기** 블레이드의 스크린샷](./media/automation-dsc-getting-started/AddConfig.png)

1. **확인**을 클릭합니다.

## <a name="viewing-a-configuration-in-azure-automation"></a>Azure Automation에서 구성 보기

구성을 가져온 후에 Azure 포털에서 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 선택합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **TestConfig**(이전 절차에서 가져온 구성의 이름)를 클릭합니다.
1. **TestConfig 구성** 페이지에서 **구성 소스 보기**를 클릭합니다.

   ![TestConfig 구성 블레이드의 스크린샷](./media/automation-dsc-getting-started/ViewConfigSource.png)

   **TestConfig 구성 소스** 페이지가 열리고 구성에 대한 PowerShell 코드를 표시합니다.

## <a name="compiling-a-configuration-in-azure-automation"></a>Azure Automation에서 구성 컴파일

노드에 원하는 상태를 적용하려면 먼저 해당 상태를 정의하는 DSC 구성을 하나 이상의 노드 구성(MOF 문서)으로 컴파일한 다음 Automation DSC 끌어오기 서버에 배치해야 합니다. Azure Automation 상태 구성에서 구성 컴파일에 대한 자세한 설명은 [Azure Automation 상태 구성에서 구성을 컴파일](automation-dsc-compile.md)을 참조하세요.
구성 컴파일에 대한 자세한 내용은 [DSC 구성](/powershell/dsc/configurations)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **TestConfig**(이전에 가져온 구성의 이름)를 클릭합니다.
1. **TestConfig 구성** 페이지에서 **컴파일**을 클릭한 다음 **예**를 클릭합니다. 컴파일 작업이 시작됩니다.

   ![컴파일 단추를 강조 표시하는 TestConfig 구성 페이지의 스크린샷](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Automation에서 구성을 컴파일하면 생성된 모든 노드 구성 MOF를 끌어오기 서버에 자동으로 배포합니다.

## <a name="viewing-a-compilation-job"></a>컴파일 작업 보기

컴파일을 시작한 후에 **구성** 페이지의 **컴파일 작업** 타일에서 볼 수 있습니다. **컴파일 작업** 타일은 현재 실행 중인, 완료된 및 실패한 작업을 표시합니다. 컴파일 작업 페이지를 열면 발생한 모든 오류 또는 경고, 구성에서 사용된 입력 매개 변수 및 컴파일 로그를 포함한 해당 작업에 대한 정보를 표시합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **TestConfig**(이전에 가져온 구성의 이름)를 클릭합니다.
1. **컴파일 작업**에서 보려는 컴파일 작업을 선택합니다. 컴파일 작업이 시작된 날짜로 레이블이 지정된 **컴파일 작업** 페이지가 열립니다.

   ![컴파일 작업 페이지의 스크린샷](./media/automation-dsc-getting-started/CompilationJob.png)

1. **컴파일 작업** 페이지에서 타일을 클릭하여 작업에 대한 추가 세부 정보를 확인합니다.

## <a name="viewing-node-configurations"></a>노드 구성 보기

컴파일 작업을 성공적으로 완료하면 하나 이상의 새 노드 구성을 만듭니다. 노드 구성은 끌어오기 서버에 배포되고 하나 이상의 노드에서 가져오고 적용될 준비가 된 MOF 문서입니다. **상태 구성(DSC)** 페이지의 Automation 계정에서 노드 구성을 볼 수 있습니다. 노드 구성은 *ConfigurationName*.*NodeName*의 형식으로 이름을 가집니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **컴파일된 구성** 탭을 클릭합니다.

   ![컴파일된 구성 탭의 스크린샷](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 통한 관리용 Azure VM 온보드

Azure Automation 상태 구성을 사용하여 Azure VM(클래식 및 Resource Manager), 온-프레미스 VM, Linux 머신, AWS VM 및 온-프레미스 물리적 머신을 관리할 수 있습니다. 이 문서에서는 Azure Resource Manager VM만 온보딩하는 방법을 배웁니다. 다른 유형의 머신 온보드에 대한 정보는 [Azure Automation 상태 구성을 통한 관리용 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 통한 관리용 Azure Resource Manager VM 온보드하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지의 **노드** 탭에서 **+ 추가**를 클릭합니다.

   ![Azure VM 추가 단추를 강조 표시하는 DSC 노드 페이지의 스크린샷](./media/automation-dsc-getting-started/OnboardVM.png)

1. **Virtual Machines** 페이지에서 VM을 선택합니다.
1. **Virtual Machine**의 세부 정보 페이지에서 **+ 연결**을 클릭합니다.

   > [!IMPORTANT]
   > 실행 하는 Azure Resource Manager VM 이어야 합니다는 [지원 되는 운영 체제](automation-dsc-overview.md#operating-system-requirements)합니다.

2. **등록** 페이지의 **노드 구성 이름** 상자에서 VM에 적용하려는 노드 구성의 이름을 선택합니다. 이 시점에서 이름을 제공하는 것은 선택 사항입니다. 노드를 온보드한 후 할당된 노드 구성을 변경할 수 있습니다.
   **필요한 경우 노드 다시 부팅**을 선택한 다음, **확인**을 클릭합니다.

   ![등록 블레이드의 스크린샷](./media/automation-dsc-getting-started/RegisterVM.png)

   지정한 노드 구성이 **구성 모드 빈도**에서 지정된 간격으로 VM에 적용되고 VM은 **새로 고침 빈도**에서 지정된 간격으로 노드 구성에 대한 업데이트를 확인합니다. 이러한 값을 사용하는 방법에 대한 자세한 내용은 [로컬 구성 관리자 구성](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)을 참조하세요.

Azure는 VM 온보딩 프로세스를 시작합니다. 완료되면 Automation 계정에서 **상태 구성(DSC)** 페이지의 **노드** 탭에 VM이 표시됩니다.

## <a name="viewing-the-list-of-managed-nodes"></a>관리되는 노드 목록 보기

**상태 구성(DSC)** 페이지의 **노드** 탭의 Automation 계정에서 관리를 위해 온보드된 모든 머신의 목록을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **노드** 탭을 클릭합니다.

## <a name="viewing-reports-for-managed-nodes"></a>관리되는 노드에 대한 보고서 보기

Azure Automation 상태 구성이 관리되는 노드에 대한 일관성 검사를 수행할 때마다 노드는 끌어오기 서버에 상태 보고서를 보냅니다. 해당 노드에 대한 페이지에서 이러한 보고서를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **노드** 탭을 클릭합니다. 여기에서 구성 상태 개요 및 각 노드에 대한 세부 정보를 볼 수 있습니다.

   ![노드 페이지의 스크린 샷](./media/automation-dsc-getting-started/NodesTab.png)

1. **노드** 탭에서 노드 레코드를 클릭하여 보고를 시작합니다. 추가 보고 세부 정보를 보려는 보고서를 클릭합니다.

   ![보고서 블레이드의 스크린샷](./media/automation-dsc-getting-started/NodeReport.png)

개별 보고서에 대한 블레이드에서 해당 일관성 검사에 대한 다음과 같은 상태 정보를 확인할 수 있습니다.

- 보고서 상태 — 노드의 "준수" 여부, 구성 "실패" 또는 노드가 "비준수"입니다(노드가 **ApplyandMonitor** 모드에 있고 머신이 원하는 상태에 있지 않은 경우).
- 일관성 검사에 대한 시작 시간.
- 일관성 검사에 대한 총 런타임.
- 일관성 검사의 형식.
- 오류 코드 및 오류 메시지를 포함하는 모든 오류.
- 구성에서 사용되는 모든 DSC 리소스 및 각 리소스의 상태(노드가 해당 리소스에 대해 원하는 상태 여부) — 각 리소스를 클릭하여 해당 리소스에 대한 자세한 정보를 가져올 수 있습니다.
- 노드의 이름, IP 주소 및 구성 모드.

**원시 보고서 보기** 를 클릭하여 노드가 서버에 전송하는 실제 데이터를 확인할 수 있습니다.
해당 데이터 사용에 대한 자세한 내용은 [DSC 보고서 서버 사용](/powershell/dsc/reportserver)을 참조하세요.

노드가 온보드된 후 첫 번째 보고서를 사용할 수 있기 전까지 다소 시간이 걸릴 수 있습니다. 노드를 온보드한 후 첫 번째 보고서에 대해 최대 30분을 기다려야 합니다.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>다른 노드 구성에 노드 다시 할당

처음에 할당한 것과 다른 노드 구성을 사용하도록 노드를 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **노드** 탭을 클릭합니다.
1. **노드** 탭에서 다시 할당하려는 노드의 이름을 클릭합니다.
1. 해당 노드에 대한 페이지에서 **노드 구성 할당**을 클릭합니다.

    ![노드 구성 할당 단추를 강조 표시하는 노드 세부 정보 페이지의 스크린샷](./media/automation-dsc-getting-started/AssignNode.png)

1. **노드 구성 할당** 페이지에서 노드를 할당하려는 노드 구성을 선택한 다음 **확인**을 클릭합니다.

    ![노드 구성 할당 페이지의 스크린샷](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>노드 등록 취소

Azure Automation DSC에 의해 노드를 더 이상 관리하지 않으려는 경우 등록 취소할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 블레이드의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **노드** 탭을 클릭합니다.
1. **노드** 탭에서 등록 취소하려는 노드의 이름을 클릭합니다.
1. 해당 노드에 대한 페이지에서 **등록 취소**를 클릭합니다.

    ![등록 취소 단추를 강조 표시하는 노드 세부 정보 페이지의 스크린샷](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>관련 문서

- [Azure Automation 상태 구성 개요](automation-dsc-overview.md)
- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](automation-dsc-onboarding.md)
- [Windows PowerShell 필요한 상태 구성 개요](/powershell/dsc/overview)
- [Azure Automation 상태 구성 cmdlets](/powershell/module/azurerm.automation/#automation)
- [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)
