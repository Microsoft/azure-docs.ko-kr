---
title: Azure Automation의 내 첫 번째 Python Runbook
description: 간단한 Python Runbook의 생성, 테스트, 게시 방법을 안내하는 자습서입니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f5408ce82b16d72dde364238d427a798441dfc8b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340827"
---
# <a name="my-first-python-runbook"></a>내 첫 번째 Python Runbook

> [!div class="op_single_selector"]
> - [그래픽](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell 워크플로](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [Python Runbook](automation-runbook-types.md#python-runbooks)을 만드는 과정을 안내합니다. 먼저 간단한 Runbook을 만들어서 테스트하고 게시합니다. 그런 다음 실제로 Azure 리소스를 관리하도록 Runbook을 수정합니다. 이 경우에 Azure Virtual Machine을 시작합니다. 마지막으로 Runbook 매개 변수를 추가하여 Runbook을 더욱 강력하게 개선합니다.

> [!NOTE]
> Python runbook을 시작 하는 웹 후크를 사용 하 여 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
- [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
- Azure 가상 머신. 프로덕션 VM이 되지 않도록 이 가상 머신을 중지하고 시작합니다.

## <a name="create-a-new-runbook"></a>새 Runbook 만들기

먼저 *헬로 월드*라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

    Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 대부분의 해당 자산은 새 Automation 계정에 자동적으로 포함되어 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.<br>

1. **프로세스 관리** 아래에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
1. **+ Runbook 추가**를 선택하여 새 Runbook을 만듭니다.
1. Runbook 이름을 *MyFirstRunbook-Python*으로 지정합니다.
1. 이 예제에서는 [Python Runbook](automation-runbook-types.md#python-runbooks)을 만들 예정이므로 **Runbook 형식**으로 **Python 2**를 선택합니다.
1. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="add-code-to-the-runbook"></a>Runbook에 코드 추가

이제 "헬로 월드" 텍스트를 인쇄하기 위한 간단한 명령을 추가합니다.

```python
print("Hello World!")
```

**저장**을 클릭하여 Runbook을 저장합니다.

## <a name="test-the-runbook"></a>Runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 제대로 작동하는지 확인하기 위해 테스트합니다. Runbook을 테스트할 때 **초안** 버전을 실행하고 해당 출력을 대화형으로 봅니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
1. 이 창에서 [runbook 작업](automation-runbook-execution.md) 이 생성되고 상태를 보여줍니다.
   작업 상태는 클라우드의 Runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타내는 *대기 중*으로 시작됩니다. 작업자가 작업을 요구한 경우 *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.
1. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 이 예에서는 *헬로 월드*가 표시됩니다.
1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="publish-and-start-the-runbook"></a>Runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하려면 먼저 게시해야 합니다.
Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다.
이 예에서는 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. 이제 **Runbooks** 창에서 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
1. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Python**창이 표시됩니다.
   맨 위에 있는 옵션은 runbook을 시작할 runbook을 보거나 나중에 특정 시간에 시작 하도록 예약할 수 있습니다.
2. Runbook을 시작하려면 Runbook 시작 블레이드가 열린 후 **시작**을 클릭하고 **확인**을 클릭합니다.
3. 만든 Runbook 작업에 대한 작업 창이 열립니다. 이 창을 닫아도 되지만, 이 예에서는 작업 진행 상황을 볼 수 있도록 열어 놓겠습니다.
1. Runbook을 테스트할 때와 동일한 작업 상태가 **작업 요약**에 표시됩니다.
2. Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. 출력 창이 열리고 *헬로 월드*가 표시됩니다.
3. 출력 창을 닫습니다.
4. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 *Hello World*만이 표시되어야 하지만 Runbook이 자세한 정보 표시, 오류와 같은 Runbook 작업에 대한 다른 스트림에 작성된 경우 해당 스트림이 표시될 수 있습니다.
5. MyFirstRunbook-Python 창으로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
6. **작업** 을 클릭하여 이 Runbook에 대한 작업 창을 엽니다. runbook으로 만든 모든 작업을 나열합니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.
7. Runbook을 시작했을 때 표시된 동일한 작업창을 열려면 이 작업을 클릭하면 됩니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="add-authentication-to-manage-azure-resources"></a>Azure 리소스를 관리하는 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다.
Azure 리소스를 관리하려면 스크립트가 Automation 계정의 자격 증명을 사용하여 인증해야 합니다. [Azure Automation 유틸리티 패키지](https://github.com/azureautomation/azure_automation_utility)를 사용하면 더욱 간편하게 Azure 리소스를 인증하고 조작할 수 있습니다.

> [!NOTE]
> 실행 인증서를 만들려면 서비스 주체 기능을 사용하여 Automation 계정을 만들어야 합니다.
> 서비스 주체를 사용하여 자동화 계정을 만들지 않은 경우 [Python용 Azure 관리 라이브러리를 사용하여 인증](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate)에서 설명하는 방법을 사용하여 인증할 수 있습니다.

1. MyFirstRunbook-Python 창에서 **편집**을 클릭하여 텍스트 편집기를 엽니다.

2. 다음 코드를 추가하여 Azure에 인증합니다.

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Python 계산 클라이언트를 만들고 VM을 시작하는 코드 추가

Azure VM을 사용하려면 [Python용 Azure 계산 클라이언트](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python)의 인스턴스를 만듭니다.

계산 클라이언트를 사용하여 VM을 시작합니다. 이렇게 하려면 Runbook에 다음 코드를 추가합니다.

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

여기서 _MyResourceGroup_은 VM을 포함하는 리소스 그룹의 이름이고 _TestVM_은 시작하려는 VM의 이름입니다.

Runbook을 다시 테스트 및 실행하여 VM이 시작되는지 확인합니다.

## <a name="use-input-parameters"></a>입력 매개 변수 사용

Runbook은 현재 VM 및 리소스 그룹의 이름에 하드 코드된 값을 사용합니다.
이제 입력 매개 변수에서 이러한 값을 가져오는 코드를 추가해 보겠습니다.

`sys.argv` 변수를 사용하여 매개 변수 값을 가져옵니다.
Runbook에서 다른 `import` 문 바로 뒤에 다음 코드를 추가합니다.

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

이 코드는 `sys` 모듈을 가져오며 리소스 그룹 및 VM 이름을 포함할 변수 두 개를 만듭니다.
인수 목록의 요소 `sys.argv[0]`는 스크립트의 이름이며 사용자가 입력하는 내용이 아닙니다.

이제 하드 코드된 값을 사용하는 대신 입력 매개 변수 값을 사용하도록 Runbook의 마지막 두 줄을 수정할 수 있습니다.

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

**테스트** 페이지에서 또는 게시된 Runbook으로 Python Runbook을 시작할 때 **Runbook 시작** 페이지의 **매개 변수** 아래에서 매개 변수의 값을 입력할 수 있습니다.

첫 번째 상자에 값을 입력하기 시작하면 두 번째 상자가 나타나는 식으로 상자가 계속 표시되므로 매개 변수 값을 필요한 수만큼 입력할 수 있습니다.

방금 추가한 코드와 같이 `sys.argv` 배열로 값을 스크립트에 제공할 수 있습니다.

첫 번째 매개 변수의 값으로는 리소스 그룹 이름을 입력하고 두 번째 매개 변수의 값으로는 시작하려는 VM의 이름을 입력합니다.

![매개 변수 값 입력](media/automation-first-runbook-textual-python/runbook-python-params.png)

**확인**을 클릭하여 Runbook을 시작합니다. Runbook이 실행되고 지정한 VM이 시작됩니다.

## <a name="next-steps"></a>다음 단계

- PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)
- 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
- PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)
- Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
- Python을 사용하여 Azure를 개발하는 방법에 대해 알아보려면 [Python 개발자용 Azure](https://docs.microsoft.com/python/azure/?view=azure-python)를 참조하세요.
- 샘플 Python 2 Runbook을 보려면 [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)를 참조하세요.
