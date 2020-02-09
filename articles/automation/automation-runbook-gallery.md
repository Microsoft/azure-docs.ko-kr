---
title: Azure Automation용 Runbook 및 모듈 갤러리
description: Microsoft 및 커뮤니티의 Runbook과 모듈을 Azure Automation 환경에 설치하여 사용할 수 있습니다.  이 글에서는 이러한 리소스에 액세스하는 방법과 자신의 Runbook을 갤러리에 올리는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421479"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation용 Runbook 및 모듈 갤러리

Azure Automation에서 사용자 고유의 Runbook 및 모듈을 만드는 대신 Microsoft 및 커뮤니티에서 이미 빌드한 시나리오에 액세스할 수 있습니다.

스크립트 센터 갤러리에서 PowerShell 갤러리 및 [Python runbook](#python-runbooks) 의 PowerShell runbook 및 [모듈](#modules-in-powershell-gallery) 을 가져올 수 있습니다. 개발 하는 시나리오를 공유 하 여 커뮤니티에 기여할 수도 있습니다. 갤러리에 runbook 추가를 참조 하세요.

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 갤러리의 runbook

[PowerShell 갤러리](https://www.powershellgallery.com/packages) 는 Microsoft와 커뮤니티에서 Azure Automation으로 가져올 수 있는 다양 한 runbook을 제공 합니다. 하나를 사용 하려면 갤러리에서 runbook을 다운로드 하거나 갤러리 또는 Azure Portal의 Automation 계정에서 runbook을 직접 가져올 수 있습니다.

Azure Portal를 사용 하 여 PowerShell 갤러리에서 직접 가져올 수 있습니다. PowerShell을 사용 하 여이 기능을 수행할 수 없습니다.

> [!NOTE]
> PowerShell 갤러리에서 가져온 모든 runbook 내용의 유효성을 검사 하 고 프로덕션 환경에서 설치 및 실행 하는 데 매우 주의 해야 합니다.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure Portal를 사용 하 여 Runbook 갤러리에서 PowerShell runbook을 가져오려면

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화**에서 **Runbook 갤러리**를 클릭합니다.
3. **원본: PowerShell 갤러리**을 선택 합니다.
4. 원하는 갤러리 항목을 찾아 선택하여 세부 정보를 확인합니다. 왼쪽에서 게시자 및 형식에 대한 추가 검색 매개 변수를 입력할 수 있습니다.

   ![갤러리 찾아보기](media/automation-runbook-gallery/browse-gallery.png)

5. **TechNet 스크립트 센터** 의 항목을 확인하려면 [소스 프로젝트 보기](https://gallery.technet.microsoft.com/)를 클릭합니다.
6. 항목을 가져오려면 해당 항목을 클릭하여 세부 정보를 확인한 다음 **가져오기** 단추를 클릭합니다.

   ![가져오기 단추](media/automation-runbook-gallery/gallery-item-detail.png)

7. 선택적으로 Runbook의 이름을 변경한 다음 **확인** 을 클릭하여 해당 Runbook을 가져옵니다.
8. 이 Runbook은 Automation 계정의 **Runbook** 탭에 표시됩니다.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>갤러리에 PowerShell runbook 추가

다른 고객에 게 유용 하다 고 생각 되는 PowerShell 갤러리에 runbook을 추가 하는 것이 좋습니다. PowerShell 갤러리에서는 PowerShell 모듈 및 PowerShell 스크립트를 게시할 수 있습니다. Runbook을 [PowerShell 갤러리에 업로드](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)하 여 추가할 수 있습니다.

> [!NOTE]
> 그래픽 runbook은 PowerShell 갤러리에서 지원 되지 않습니다.

## <a name="modules-in-powershell-gallery"></a>PowerShell 갤러리의 모듈

PowerShell 모듈에는 Runbook에 사용할 수 있는 cmdlet이 있으며, Azure Automation에서 설치할 수 있는 기존 모듈을 [PowerShell 갤러리](https://www.powershellgallery.com)에서 사용할 수 있습니다. Azure Portal에서 이 갤러리를시작하여 Azure Automation에 모듈을 바로 설치하거나, 모듈을 다운로드하여 수동으로 설치할 수 있습니다.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Azure Portal을 사용하여 Automation 모듈 갤러리에서 모듈을 가져오려면

1. Azure Portal에서 Automation 계정을 엽니다.
2. **공유 리소스** 아래에서 **모듈**을 선택하여 모듈 목록을 엽니다.
3. 페이지 위쪽에서 **갤러리 찾아보기**를 클릭합니다.

   ![모듈 갤러리](media/automation-runbook-gallery/modules-blade.png)

4. **갤러리 찾아보기** 페이지에서 다음 필드로 검색할 수 있습니다.

   * 모듈 이름
   * 태그
   * 작성자
   * Cmdlet/DSC 리소스 이름

5. 관심이 있는 모듈을 찾아 선택하여 세부 내용을 확인합니다.

   특정 모듈로 드릴하면 자세한 정보를 확인할 수 있습니다. 이 정보에는 PowerShell 갤러리로 돌아가는 링크, 필요한 종속성 및 모듈에 속하는 모든 cmdlet 또는 DSC 리소스가 포함됩니다.

   ![PowerShell 모듈 세부 정보](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Azure Automation에 직접 모듈을 설치하려면 **가져오기** 단추를 클릭합니다.
7. 가져오기 단추를 클릭하면 **가져오기** 창에 가져오려는 모듈 이름이 표시됩니다. 모든 종속성이 설치되면 **확인** 단추가 활성화됩니다. 종속성이 없는 경우 해당 종속성을 가져와야만 이 모듈을 가져올 수 있습니다.
8. **가져오기** 페이지에서 **확인**을 클릭하여 모듈을 가져옵니다. Azure Automation에서 모듈을 계정에 가져오는 동안 모듈 및 cmdlet에 대한 메타데이터를 추출합니다. 각 활동을 추출해야 하므로 이 작업은 몇 분 정도 걸릴 수 있습니다.
9. 모듈이 배포 중임을 알리는 초기 알림 및 완료 시의 다른 알림이 표시됩니다.
10. 모듈 가져오기가 완료되면 사용 가능한 활동을 볼 수 있습니다. Runbook 및 필요한 상태 구성에 해당 리소스를 사용할 수 있습니다.

> [!NOTE]
> PowerShell 코어만 지원하는 모듈은 Azure Automation에서 지원되지 않으며, Azure Portal에서 가져오거나 PowerShell 갤러리에서 직접 배포할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbooks

Python Runbooks는 [스크립트 센터 갤러리](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)에서 사용할 수 있습니다. **기여 업로드**를 클릭 하 여 스크립트 센터 갤러리에 Python runbook을 제공할 수 있습니다. 이를 제공할 경우 제공 업로드 시 태그 **Python**을 추가해야 합니다.

> [!NOTE]
> [스크립트 센터](https://gallery.technet.microsoft.com/scriptcenter) 에 콘텐츠를 업로드 하기 위해 최소 100 포인트가 필요 합니다.

## <a name="requesting-a-runbook-or-module"></a>Runbook 또는 모듈 요청 중

[사용자 음성](https://feedback.azure.com/forums/246290-azure-automation/)에 요청을 보낼 수 있습니다.  Runbook을 작성하는 데 도움이 필요하거나 PowerShell에 대한 질문이 있으면 [포럼](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)에 질문을 게시하세요.

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Runbook 갤러리에서 사용할 수 있는 일반 솔루션

아래 목록에는 일반적인 시나리오에 대한 솔루션을 제공 하는 몇 가지 runbook이 포함 되어 있습니다. Azure Automation 팀에서 만든 runbook의 전체 목록은 [Azureautomationteam profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam)을 참조 하세요.

* [ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -Automation 계정에 있는 모든 모듈의 PowerShell 갤러리에 대한 최신 버전을 가져옵니다.
* [-AzureDiagnostics 사용](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) -이 스크립트는 Azure 진단를 구성 하 고 작업 상태 및 작업 스트림을 포함 하는 Azure Automation 로그를 받도록 Log Analytics 합니다.
* [ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -이 Runbook은 Windows Azure 가상 컴퓨터에서 원격 파일을 복사 합니다.
* [ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -이 Runbook은 Azure 가상 컴퓨터에 로컬 파일을 복사 합니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 시작하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* Runbook용 PowerShell 및 PowerShell 워크플로 간의 차이점을 이해하려면 [PowerShell 워크플로 학습](automation-powershell-workflow.md)
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
