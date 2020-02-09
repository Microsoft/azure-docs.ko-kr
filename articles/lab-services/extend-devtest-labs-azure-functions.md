---
title: Azure Functions를 사용 하 여 Azure DevTest Labs 확장 Microsoft Docs
description: Azure Functions를 사용 하 여 Azure DevTest Labs를 확장 하는 방법을 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014361"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions를 사용 하 여 DevTest Labs 확장
Azure Functions를 사용 하 여 DevTest Labs에서 이미 지 원하는 것 이상의 추가 시나리오를 지원할 수 있습니다. Azure Functions를 사용 하 여 비즈니스 관련 요구에 맞게 서비스의 기본 제공 기능을 확장할 수 있습니다. 다음 목록에서는 가능한 몇 가지 시나리오를 제공 합니다. 이 문서에서는 이러한 샘플 시나리오 중 하나를 구현 하는 방법을 보여 줍니다.

- 랩에서 Vm (가상 머신)에 대 한 최상위 수준 요약 제공
- [원격 데스크톱 게이트웨이를 사용 하도록 랩 구성](configure-lab-remote-desktop-gateway.md)
- 내부 지원 페이지에 대 한 준수 보고
- 사용자가 구독에서 증가 된 사용 권한이 필요한 작업을 완료할 수 있도록 설정
- [DevTest Labs 이벤트 기반 워크플로 시작](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>개요
[Azure Functions](../azure-functions/functions-overview.md) 은 Azure에서 서버를 사용 하지 않는 컴퓨팅 플랫폼입니다. DevTest Labs를 사용 하 여 솔루션에서 Azure Functions를 사용 하면 기존 기능을 사용자 지정 코드를 사용 하 여 확장할 수 있습니다. Azure Functions에 대 한 자세한 내용은 [Azure Functions 설명서](../azure-functions/functions-overview.md)를 참조 하세요. 이 문서에서는 DevTest Labs에서 요구 사항 또는 전체 시나리오를 충족 하 Azure Functions는 데 도움이 되는 방법을 설명 하기 위해 다음과 같이 랩에서 Vm에 대 한 최상위 요약을 제공 하는 예제를 사용 합니다.

**예제 요구 사항/시나리오**: 사용자는 운영 체제, 소유자 및 적용 된 모든 아티팩트를 포함 하 여 랩의 모든 Vm에 대 한 세부 정보를 볼 수 있습니다.  또한 **Windows 업데이트 적용** 아티팩트가 최근에 적용 되지 않은 경우이를 적용 하는 것이 쉬운 방법입니다.

시나리오를 완료 하려면 다음 다이어그램에 설명 된 대로 두 개의 함수를 사용 합니다.  

![전체 흐름](./media/extend-devtest-labs-azure-functions/flow.png)

이러한 샘플 함수의 소스 코드는 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (둘 다 C# 와 PowerShell 구현을 사용할 수 있음)에 있습니다.

- **Updateinternalsupportpage**: 이 함수는 DevTest Labs를 쿼리하고 가상 컴퓨터에 대 한 세부 정보를 사용 하 여 직접 내부 지원 페이지를 업데이트 합니다.
- **ApplyWindowsUpdateArtifact**: 랩에서 VM의 경우이 함수는 **Windows 업데이트** 아티팩트를 적용 합니다.

## <a name="how-it-works"></a>작동 방법
사용자가 DevTest Labs에서 **내부 지원** 페이지를 선택 하면 vm, 랩 소유자 및 지원 연락처에 대 한 정보를 포함 하는 미리 채워진 페이지가 제공 됩니다.  

**새로 고칠 때 여기를 선택** 하세요. 단추를 선택 하면 페이지가 첫 번째 Azure function을 호출 합니다. **Updateinternalsupportpage**입니다. 함수는 DevTest Labs 정보를 쿼리 한 다음 **내부 지원** 페이지를 새 정보로 다시 작성 합니다.

Windows 업데이트 아티팩트가 최근에 적용 되지 않은 모든 Vm에 대해 수행할 수 있는 추가 작업이 있습니다. VM에 Windows 업데이트를 적용 하는 단추가 있습니다. VM에 대 한 ***Windows Update 실행** 단추를 선택 하면 페이지에서 두 번째 Azure Function을 호출 합니다. **ApplyWindowsUpdateArtifact**. 이 함수는 가상 머신이 실행 되 고 있는지 여부를 확인 하 고, 그렇다면 [Windows 업데이트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) 아티팩트를 직접 적용 합니다.

## <a name="step-by-step-walkthrough"></a>단계별 연습
이 섹션에서는 **내부 지원** 페이지를 업데이트 하는 데 필요한 Azure 리소스를 설정 하는 방법에 대 한 단계별 지침을 제공 합니다. 이 연습에서는 DevTest Labs를 확장 하는 한 가지 예를 제공 합니다. 다른 시나리오에이 패턴을 사용할 수 있습니다.

### <a name="step-1-create-a-service-principal"></a>1단계: 서비스 주체 만들기 
첫 번째 단계는 랩을 포함 하는 구독에 대 한 사용 권한이 있는 서비스 주체를 가져오는 것입니다. 서비스 주체는 암호 기반 인증을 사용 해야 합니다. [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)또는 [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)를 사용 하 여이 작업을 수행할 수 있습니다. 사용할 서비스 주체가 이미 있는 경우이 단계를 건너뛸 수 있습니다.

서비스 사용자의 **응용 프로그램 id**, **키**및 **테넌트 id** 를 적어둡니다. 이 연습의 뒷부분에서 필요 합니다. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2단계: 샘플을 다운로드 하 고 Visual Studio 2019에서 열기
리포지토리를 복제 하거나 [여기](https://github.com/Azure/azure-devtestlab/archive/master.zip)에서 리포지토리를 다운로드 하 여 [ C# Azure Functions 샘플](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) 의 복사본을 로컬로 다운로드 합니다.  

1. Visual Studio 2019을 사용 하 여 샘플 솔루션을 엽니다.  
1. 아직 설치 하지 않은 경우 Visual Studio 용 **Azure 개발** 워크 로드를 설치 합니다. **도구** -> **가져오기 도구 및 기능** 메뉴 항목을 통해 설치할 수 있습니다.

    ![Azure 개발 워크로드](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 솔루션을 빌드합니다. **빌드** 및 **솔루션 빌드** 메뉴 항목을 차례로 선택 합니다.

### <a name="step-3-deploy-the-sample-to-azure"></a>3단계: Azure에 샘플 배포
Visual Studio의 **솔루션 탐색기** 창에서 **AzureFunctions** 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **게시**를 선택 합니다. 마법사에 따라 새 또는 기존 Azure 함수 앱에 대 한 게시를 완료 합니다. Visual Studio를 사용 하 여 Azure 함수 개발 및 배포에 대 한 자세한 내용은 [Visual studio를 사용 하 여 Azure Functions 개발](../azure-functions/functions-develop-vs.md)을 참조 하세요.

![게시 대화 상자](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4단계:  응용 프로그램 설정 수집
함수를 게시 한 후에는 Azure Portal에서 이러한 함수에 대 한 Url을 가져와야 합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다. 
1. 함수 앱을 찾습니다.
1. **함수 앱** 페이지에서 함수를 선택 합니다. 
1. 다음 그림에 표시 된 것 처럼 **함수 URL 가져오기** 를 선택 합니다. 

    ![Azure 함수 Url](./media/extend-devtest-labs-azure-functions/function-url.png)
4. URL을 복사하고 저장합니다. 다른 Azure 함수에 대해 이러한 단계를 반복 합니다. 

또한 서비스 주체에 대 한 추가 정보 (예: 응용 프로그램 ID, 키 및 테넌트 ID)가 필요 합니다.


### <a name="step-5--update-application-settings"></a>5단계:  응용 프로그램 설정 업데이트
Visual Studio에서 Azure 함수를 게시 한 후 **작업**아래에서 **편집 Azure App Service 설정을** 선택 합니다. 다음 응용 프로그램 설정 (원격)을 업데이트 합니다.

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (기본값은 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![응용 프로그램 설정](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6단계: Azure function 테스트
이 연습의 마지막 단계는 Azure 함수를 테스트 하는 것입니다.  

1. 3 단계에서 만든 함수 앱의 **Updateinternalsupportpage** 함수로 이동 합니다. 
1. 페이지의 오른쪽에서 **테스트** 를 선택 합니다. 
1. 경로 속성 (랩 이름, RESOURCEGROUPNAME 및 SUBSCRIPTIONID)에를 입력 합니다.
1. **실행** 을 선택 하 여 함수를 실행 합니다.  

    이 함수는 지정 된 랩의 내부 지원 페이지를 업데이트 합니다. 또한 사용자가 다음 번에 함수를 직접 호출 하는 단추도 포함 되어 있습니다.

    ![테스트 함수](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>다음 단계
Azure Functions는 이미 기본 제공 되는 것 외에도 DevTest Labs의 기능을 확장 하 고 고객이 자신의 팀에 고유한 요구 사항을 충족 하도록 도울 수 있습니다. 이 패턴은 더 자세히 다룰 수 있도록 확장 & 확장할 수 있습니다.  DevTest Labs에 대해 자세히 알아보려면 다음 문서를 참조 하세요. 

- [DevTest Labs Enterprise 참조 아키텍처](devtest-lab-reference-architecture.md)
- [질문과 대답](devtest-lab-faq.md)
- [DevTest Labs 확장](devtest-lab-guidance-scale.md)
- [PowerShell을 사용 하 여 DevTest Labs 자동화](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








