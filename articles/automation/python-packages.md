---
title: Azure Automation에서 Python 2 패키지 관리
description: 이 문서에서는 Azure Automation에서 Python 2 패키지를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417637"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation에서 Python 2 패키지 관리

Azure Automation을 사용하면 Linux Hybrid Runbook Worker 및 Azure에서 Python 2 Runbook을 실행할 수 있습니다. Runbook을 간편하게 실행하기 위해 Python 패키지를 사용하여 필요한 모듈을 가져올 수 있습니다. 이 문서에서는 Azure Automation에서 Python 패키지를 관리하고 사용하는 방법을 설명합니다.

## <a name="import-packages"></a>패키지 가져오기

Automation 계정의 **공유 리소스** 아래에서 **Python 2 패키지**를 선택합니다. **+ Python 2 패키지 추가**를 클릭합니다.

![Python 패키지 추가](media/python-packages/add-python-package.png)

**Python 2 패키지 추가** 페이지에서 업로드할 로컬 패키지를 선택합니다. 패키지는 `.whl` 파일 또는 `.tar.gz` 파일일 수 있습니다. 패키지를 선택한 다음 **확인**을 클릭하여 업로드합니다.

![Python 패키지 추가](media/python-packages/upload-package.png)

패키지를 가져온 후에는 Automation 계정의 **Python 2 패키지** 페이지에 나열 됩니다. 패키지를 제거해야 하는 경우 해당 패키지를 선택하고 패키지 페이지에서 **삭제**를 선택합니다.

![패키지 목록](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>종속성이 있는 패키지 가져오기

Azure automation은 가져오기 프로세스 중에 python 패키지에 대한 종속성을 확인 하지 않습니다. 모든 종속성이 있는 패키지를 가져오는 방법에는 두 가지가 있습니다. 다음 단계 중 하나만 사용 하 여 패키지를 Automation 계정으로 가져올 수 있습니다.

### <a name="manually-download"></a>수동으로 다운로드

[Python 2.7](https://www.python.org/downloads/release/latest/python2) 및 [pip](https://pip.pypa.io/en/stable/) 가 설치 된 Windows 64 비트 컴퓨터에서 다음 명령을 실행 하 여 패키지 및 해당 종속성을 모두 다운로드 합니다.

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

패키지가 다운로드 되 면 automation 계정으로 가져올 수 있습니다.

### <a name="runbook"></a>Runbook

Python runbook [가져오기 python 2 패키지를 pypi에서 Azure Automation 계정으로](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) 갤러리에서 Automation 계정으로 가져옵니다. 실행 설정이 **Azure** 로 설정 되었는지 확인 하 고 매개 변수를 사용 하 여 runbook을 시작 합니다. Runbook은 Automation 계정이 작동 하려면 실행 계정이 필요 합니다. 각 매개 변수에 대해 다음 목록과 이미지에 표시 된 것 처럼 스위치를 사용 하 여 시작 하도록 합니다.

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![패키지 목록](media/python-packages/import-python-runbook.png)

Runbook을 사용 하면 다운로드할 패키지 (예: 네 번째 매개 변수)를 지정 하 여 모든 Azure 모듈과 해당 종속성 (105) `Azure`을 다운로드할 수 있습니다.

Runbook이 완료 되 면 Automation 계정의 **공유 리소스** 에서 **Python 2 패키지** 페이지를 확인 하 여 패키지를 올바르게 가져왔는지 확인할 수 있습니다.

## <a name="use-a-package-in-a-runbook"></a>Runbook에서 패키지 사용

패키지를 가져온 후에는 runbook에서 패키지를 사용할 수 있습니다. 다음 예제에서는 [ Azure Automation 유틸리티 패키지](https://github.com/azureautomation/azure_automation_utility)를 사용합니다. 이 패키지가 있으면 Azure Automation에서 Python을 더욱 쉽게 사용할 수 있습니다. 이 패키지를 사용하려면 GitHub 리포지토리의 지침에 따라 `from azure_automation_utility import get_automation_runas_credential` 등의 명령(실행 계정을 검색하는 함수를 가져오는 명령)을 사용하여 Runbook에 패키지를 추가합니다.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>오프라인에서 Runbook 개발 및 테스트

오프라인에서 Python 2 Runbook을 개발하고 테스트하려는 경우 GitHub의 [Azure Automation python 에뮬레이트된 자산](https://github.com/azureautomation/python_emulated_assets) 모듈을 사용할 수 있습니다. 이 모듈에서는 자격 증명, 변수, 연결, 인증서 등의 공유 리소스를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Python 2 Runbook 사용을 시작하려면 [내 첫 번째 Python 2 Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.
