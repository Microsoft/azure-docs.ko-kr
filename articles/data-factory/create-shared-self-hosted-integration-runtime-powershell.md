---
title: PowerShell을 사용하여 자체 호스팅 Integration Runtime 공유 만들기
description: Azure Data Factory에서 자체 호스팅 통합 런타임 공유를 만들어 여러 데이터 팩터리가 통합 런타임에 액세스할 수 있도록 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 28836d0b1109952d8cf81c66b44b1f98d9b770bf
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136051"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 자체 호스팅 Integration Runtime 공유 만들기

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 가이드에서는 Azure Data Factory에서 공유되는 자체 호스팅 Integration Runtime을 만드는 방법을 보여 줍니다. 그런 다음, 다른 데이터 팩터리에서 자체 호스팅 통합 런타임 공유를 사용할 수 있습니다.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Azure Data Factory UI를 사용하여 공유되는 자체 호스팅 IR 만들기

Azure Data Factory UI를 사용하여 공유되는 자체 호스팅 IR을 만들려면 다음 단계를 수행할 수 있습니다.

1. 공유 되는 자체 호스팅 IR에서 **다른 데이터 팩터리에 대 한 권한 부여** 를 선택 하 고 "Integration runtime 설치" 페이지에서 연결 된 IR을 만들 데이터 팩터리를 선택 합니다.
      
    ![공유 탭에서 권한을 부여하는 단추](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. 공유할 자체 호스팅 IR의 위에 있는 "리소스 ID"를 복사 합니다.
         
3. 권한이 부여된 데이터 팩터리에서 연결된 자체 호스팅 IR을 새로 만들고 리소스 ID를 입력합니다.
      
    ![자체 호스팅 통합 런타임을 만드는 단추](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![연결된 자체 호스팅 통합 런타임을 만드는 단추](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![이름 및 리소스 ID 입력용 상자](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Azure PowerShell을 사용하여 공유되는 자체 호스팅 IR 만들기

Azure PowerShell을 사용하여 공유되는 자체 호스팅 IR을 만들려면 다음 단계를 수행할 수 있습니다. 
1. 데이터 팩터리를 만듭니다. 
1. 자체 호스팅 통합 런타임을 만듭니다.
1. 다른 데이터 팩터리와 자체 호스팅 통합 런타임을 공유합니다.
1. 연결된 통합 런타임을 만듭니다.
1. 공유를 취소합니다.

### <a name="prerequisites"></a>필수 구성 요소 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. 

- **Azure PowerShell**. [PowerShellGet을 사용하여 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)의 지침을 따르세요. PowerShell을 사용하여 다른 데이터 팩터리와 공유할 수 있는 자체 호스팅 통합 런타임을 만드는 스크립트를 실행합니다. 

> [!NOTE]  
> Data Factory를 현재 사용할 수 있는 Azure 지역 목록을 보려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)에서 관심 있는 지역을 선택합니다.

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. Windows PowerShell ISE(통합 스크립팅 환경)를 시작합니다.

1. 변수를 만듭니다. 다음 스크립트를 복사하여 붙여넣습니다. **SubscriptionName** 및 **ResourceGroupName**과 같은 변수를 실제 값으로 바꿉니다. 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. 로그인하고 구독을 선택합니다. 스크립트에 다음 코드를 추가하여 로그인하고 Azure 구독을 선택합니다.

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. 리소스 그룹 및 데이터 팩터리를 만듭니다.

    > [!NOTE]  
    > 이 단계는 선택 사항입니다. 데이터 팩터리가 이미 있는 경우 이 단계를 건너뜁니다. 

    [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 WestEurope 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    다음 명령을 실행하여 데이터 팩터리를 만듭니다. 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

> [!NOTE]  
> 이 단계는 선택 사항입니다. 다른 데이터 팩터리와 공유하려는 자체 호스팅 통합 런타임이 이미 있는 경우 이 단계를 건너뜁니다.

다음 명령을 실행하여 자체 호스팅 통합 런타임을 만듭니다.

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>통합 런타임 인증 키 가져오기 및 노드 등록

다음 명령을 실행하여 자체 호스팅 통합 런타임에 대한 인증 키를 가져옵니다.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

응답은 이 자체 호스팅 통합 런타임에 대한 인증 키를 포함합니다. 통합 런타임 노드를 등록할 때 이 키를 사용합니다.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 설치 및 등록

1. [Azure Data Factory Integration Runtime](https://aka.ms/dmg)에서 자체 호스팅 통합 런타임 설치 관리자를 다운로드합니다.

2. 설치 관리자를 실행하여 로컬 컴퓨터에 자체 호스팅 통합을 설치합니다.

3. 이전 단계에서 검색한 인증 키를 사용하여 새로운 자체 호스팅 통합을 등록합니다.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>다른 데이터 팩터리와 자체 호스팅 통합 런타임 공유

#### <a name="create-another-data-factory"></a>다른 데이터 팩터리 만들기

> [!NOTE]  
> 이 단계는 선택 사항입니다. 공유하려는 데이터 팩터리가 이미 있는 경우 이 단계를 건너뜁니다. 그러나 다른 데이터 팩터리에 역할 할당을 추가 하거나 제거 하려면 `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` [사용자 액세스 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 또는 [소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)와 같은 및 사용 권한이 있어야 합니다.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>사용 권한 부여

만들고 등록한 자체 호스팅 통합 런타임에 액세스해야 하는 데이터 팩터리에 권한을 부여합니다.

> [!IMPORTANT]  
> 이 단계를 건너뛰지 마세요!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>연결된 자체 호스팅 통합 런타임 만들기

다음 명령을 실행하여 연결된 자체 호스팅 통합 런타임을 만듭니다.

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

이제 모든 연결된 서비스에서 이 연결된 통합 런타임을 사용할 수 있습니다. 연결된 통합 런타임은 통합 런타임 공유를 사용하여 작업을 실행합니다.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>데이터 팩터리에서 통합 런타임 공유 취소

통합 런타임 공유에서 데이터 팩터리의 액세스를 취소하려면 다음 명령을 실행합니다.

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

기존 연결된 통합 런타임을 제거하려면 통합 런타임 공유에 대해 다음 명령을 실행합니다.

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 통합 런타임 개념](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)을 검토합니다.

- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) 방법을 알아봅니다.
