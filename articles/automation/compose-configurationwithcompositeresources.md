---
title: 복합 리소스를 사용하여 Azure Automation 상태 구성(DSC)에서 DSC 구성 작성
description: Azure Automation 상태 구성(DSC)에서 복합 리소스를 사용하여 구성을 작성하는 방법 알아보기
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure, 복합 리소스
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303966"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>복합 리소스를 사용하여 Azure Automation 상태 구성(DSC)에서 DSC 구성 작성

리소스가 하나를 초과하는 DSC(필요한 상태 구성) 구성을 통해 관리되어야 하는 경우 최상의 경로는 [복합 리소스](/powershell/dsc/authoringresourcecomposite)를 사용하는 것입니다. 복합 리소스는 다른 구성 내에서 DSC 리소스로 사용되는 중첩되고 매개 변수화된 구성입니다. 이 복합 리소스는 개별적으로 관리되고 빌드된 기본 복합 리소스(매개 변수화된 구성)를 허용하는 동안 복잡한 구성을 만들 수 있습니다.

Azure Automation은 [복합 리소스 가져오기 및 컴파일](automation-dsc-compile.md#composite-resources)을 사용합니다. 복합 리소스를 Automation 계정으로 가져온 후 **상태 구성(DSC)** 페이지에서 **구성 작성** 환경을 사용할 수 있습니다.

## <a name="composing-a-configuration-from-composite-resources"></a>복합 리소스에서 구성 작성

Azure Portal의 복합 리소스에서 구성을 할당하려면 먼저 구성을 작성해야 합니다. 이 작업은 **상태 구성(DSC)** 페이지에서 또한 **구성** 또는 **컴파일된 구성** 탭에서 **구성 작성**을 사용하여 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **모든 리소스**를 클릭한 다음 Automation 계정의 이름을 클릭합니다.
1. **Automation 계정** 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 선택합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 또는 **컴파일된 구성** 탭을 클릭한 다음, 페이지 맨 위에 있는 메뉴에서 **구성 작성**을 클릭합니다.
1. **기본** 단계에서 새 구성 이름(필수)을 제공하고 새 구성에 포함하려는 각 복합 리소스의 행에서 아무 곳이나 클릭한 후, **다음**을 클릭하거나 **소스 코드** 단계를 클릭합니다. 다음 단계에서 **PSExecutionPolicy** 및 **RenameAndDomainJoin** 복합 리소스를 선택했습니다.
   ![구성 작성 페이지의 기본 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **소스 코드** 단계에서는 선택한 복합 리소스의 작성된 구성의 모양을 보여 줍니다. 모든 매개 변수의 병합 및 매개 변수가 복합 리소스에 전달되는 방법을 확인할 수 있습니다. 새 소스 코드 검토가 완료되면 **다음**을 클릭하거나 **매개 변수** 단계를 클릭합니다.
   ![구성 작성 페이지의 소스 코드 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. **매개 변수** 단계에서는 각 복합 리소스가 보유한 매개 변수가 공개되므로 매개 변수를 제공할 수 있습니다. 매개 변수에 대한 설명이 있는 경우 매개 변수 필드 옆에 표시됩니다. 필드가 **PSCredential** 형식 매개 변수인 경우 구성할 드롭다운은 현재 Automation 계정의 **자격 증명** 개체 목록을 제공합니다. **+ 자격 증명 추가** 옵션도 사용할 수 있습니다. 모든 필수 매개 변수를 입력한 후 **저장 및 컴파일**을 클릭합니다.
   ![구성 작성 페이지의 매개 변수 단계의 스크린샷](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

새 구성이 저장되면 이 구성은 컴파일을 위해 제출됩니다. 가져온 모든 구성처럼 컴파일 작업 상태를 볼 수 있습니다. 자세한 내용은 [컴파일 작업 보기](automation-dsc-getting-started.md#viewing-a-compilation-job)를 참조하세요.

컴파일이 성공적으로 완료되면 새 구성이 **컴파일된 구성** 탭에 표시됩니다. 새 구성이 이 탭에 표시되면 [다른 노드 구성에 노드 다시 할당](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration) 단계를 사용하여 관리되는 노드에 해당 구성을 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- 노드를 온보드하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성을 통해 머신을 관리하기 위한 머신 온보딩](automation-dsc-onboarding.md)을 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
