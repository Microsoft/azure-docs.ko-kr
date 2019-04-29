---
title: Azure-SSIS 통합 런타임을 예약하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Data Factory를 사용하여 Azure-SSIS 통합 런타임의 시작 및 중지를 예약하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 54d7979f9fbe23e9372aa2702b46e42ca64496d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60522510"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>일정에 따라 Azure-SSIS 통합 런타임을 시작하고 중지하는 방법
이 문서에서는 ADF(Azure Data Factory)를 사용하여 Azure-SSIS IR(통합 런타임)의 시작 및 중지를 예약하는 방법을 설명합니다. Azure-SSIS IR은 SSIS(SQL Server Integration Services) 패키지 전용으로 사용되는 ADF 컴퓨팅 리소스입니다. Azure-SSIS IR 실행 시 관련 비용이 발생합니다. 따라서 일반적으로 Azure에서 SSIS 패키지를 실행해야 할 때만 IR을 실행하고, 더 이상 필요 없으면 중지하는 것이 좋습니다. ADF UI(사용자 인터페이스)/앱 또는 Azure PowerShell을 사용하여 [수동으로 IR을 시작 또는 중지](manage-azure-ssis-integration-runtime.md)할 수 있습니다.

또는 ADF 파이프라인에서 일정에 따라 IR을 시작/중지하는 웹 작업을 만들 수 있습니다. 예를 들어 오전에 일일 ETL 워크로드를 실행하기 전에 IR을 시작하고, 오후에 워크로드가 완료되면 IR을 중지할 수 있습니다.  IR을 시작하고 중지하는 두 웹 작업 간에 SSIS 패키지 실행 작업을 연결할 수도 있습니다. 이렇게 하면 주문형으로, 패키지 실행 전/후에 적시에 IR이 시작/중지됩니다. SSIS 패키지 실행 작업에 대한 자세한 내용은 [SSIS 패키지 실행 작업을 사용하여 ADF 파이프라인에서 SSIS 패키지 실행](how-to-invoke-ssis-package-ssis-activity.md) 문서를 참조하세요.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>필수 조건
Azure-SSIS IR을 아직 프로비전하지 않은 경우 [자습서](tutorial-create-azure-ssis-runtime-portal.md)의 지침에 따라 프로비전하세요. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Azure-SSIS IR을 시작 및/또는 중지하는 ADF 파이프라인 만들기 및 예약
이 섹션에서는 ADF 파이프라인에서 웹 작업을 사용하여 일정에 따라 또는 주문형으로 Azure-SSIS IR을 시작/중지하는 방법을 보여줍니다. 세 가지 파이프라인을 만드는 방법을 보여드리겠습니다. 

1. 첫 번째 파이프라인은 Azure-SSIS IR을 시작하는 웹 작업을 포함합니다. 
2. 두 번째 파이프라인은 Azure-SSIS IR을 중지하는 웹 작업을 포함합니다.
3. 세 번째 파이프라인은 Azure-SSIS IR을 시작/중지하는 두 웹 작업 간에 연결된 SSIS 패키지 실행 작업을 포함합니다. 

세 파이프라인을 만들고 테스트한 후에는 일정 트리거를 만들어서 파이프라인과 연결할 수 있습니다. 일정 트리거는 연결된 파이프라인의 실행 일정을 정의합니다. 

예를 들어 두 개의 트리거를 만들 수 있습니다. 첫 번째 트리거는 매일 오전 6시에 실행하도록 예약되고 첫 번째 파이프라인과 연결됩니다. 두 번째 파이프라인은 매일 오후 6시에 실행하도록 예약되고 두 번째 파이프라인과 연결됩니다.  이러한 방식으로 매일 오전 6시~오후 6시 사이에 IR이 실행되고, 일일 ETL 워크로드를 실행할 준비가 완료됩니다.  

매일 자정에 실행하도록 예약된 세 번째 트리거를 만들고 세 번째 파이프라인에 연결하면 파이프라인이 매일 자정에 실행되고, 패키지 실행 직전에 IR을 시작하고, 이어서 패키지를 실행하고, 패키지 실행 후 즉시 IR을 중지하므로 IR이 불필요하게 실행되지 않습니다.

### <a name="create-your-adf"></a>ADF 만들기

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.    
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. **새 데이터 팩터리** 페이지에서 **이름**으로 **MyAzureSsisDataFactory**를 입력합니다. 
      
   ![새 데이터 팩터리 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   ADF 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되는 경우 ADF 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만들어 봅니다. ADF 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. ADF를 만들려는 Azure **구독**을 선택합니다. 
5. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
   - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
   - **새로 만들기**를 선택하고 새 리소스 그룹의 이름을 입력합니다.   
         
   리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md) 문서를 참조하세요.
   
6. **버전**은 **V2**를 선택합니다.
7. **위치**는 드롭다운 목록에서 ADF 만들기에 지원되는 위치 중 하나를 선택합니다.
8. **대시보드에 고정**을 선택합니다.     
9. **만들기**를 클릭합니다.
10. Azure 대시보드에 상태 정보가 포함된 다음 타일이 표시됩니다. **Data Factory 배포 중**. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. 만들기가 완료되면 아래와 같이 ADF 페이지를 볼 수 있습니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. **작성 및 모니터링**을 클릭하여 별도의 탭에서 ADF UI/앱을 시작합니다.

### <a name="create-your-pipelines"></a>파이프라인 만들기

1. **시작하기** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![시작 페이지](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. **작업** 도구 상자에서 **일반** 메뉴를 펼치고, **웹** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 작업 속성 창의 **일반** 탭에서 작업 이름을 **startMyIR**로 변경합니다. **설정** 탭으로 전환하고, 다음 작업을 수행합니다.

    1. **URL**에는 Azure-SSIS IR을 시작하는 REST API에 대한 다음 URL을 입력하고 `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, `{integrationRuntimeName}`을 IR의 실제 값 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`로 바꿉니다. 또는 ADF UI/앱의 모니터링 페이지에서 IR의 리소스 ID를 복사하여 붙여넣고, 위의 URL에서 `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}` 부분을 바꿔도 됩니다.
    
       ![ADF SSIS IR 리소스 ID](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. **메서드**로 **POST**를 선택합니다. 
    3. **본문**에 `{"message":"Start my IR"}`를 입력합니다. 
    4. 에 대 한 **인증**를 선택 **MSI** ADF에 대 한 관리 되는 id를 사용 하려면 참조 [Data Factory에 대 한 관리 id](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) 자세한 문서.
    5. **리소스**에 `https://management.azure.com/`을 입력합니다.
    
       ![ADF 웹 작업 일정 SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. 첫 번째 파이프라인을 복제하여 두 번째 파이프라인을 만들고, 작업 이름을 **stopMyIR**로 변경하고 다음 속성을 바꿉니다.

    1. **URL**에는 Azure-SSIS IR을 중지하는 REST API에 대한 다음 URL을 입력하고 `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, `{integrationRuntimeName}`을 IR의 실제 값 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`로 바꿉니다.
    
    2. **본문**에 `{"message":"Stop my IR"}`를 입력합니다. 

4. 세 번째 파이프라인을 만들고, **작업** 도구 상자의 **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끌어서 놓고, [ADF에서 SSIS 패키지 실행 작업을 사용하여 SSIS 패키지 호출](how-to-invoke-ssis-package-ssis-activity.md) 문서의 지침에 따라 구성합니다.  또는 **저장 프로시저** 작업을 대신 사용하고 [ADF에서 저장 프로시저 작업을 사용하여 SSIS 패키지 호출](how-to-invoke-ssis-package-stored-procedure-activity.md) 문서의 지침에 따라 구성할 수도 있습니다.  다음으로, 첫 번째/두 번째 파이프라인의 웹 작업과 마찬가지로 IR을 시작/중지하는 두 웹 작업 간에 SSIS 패키지/저장 프로시저 실행 작업을 연결합니다.

   ![ADF 웹 작업 주문형 SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. ADF에 대한 관리 ID 자체에 **기여자** 역할을 할당하면 파이프라인의 웹 작업에서 REST API를 호출하여 그 안에 프로비전된 Azure-SSIS IR을 시작/중지할 수 있습니다.  Azure Portal의 ADF 페이지에서 **액세스 제어(IAM)** 를 클릭하고, **+ 역할 할당 추가**를 클릭하고, **역할 할당 추가** 블레이드에서 다음 작업을 수행합니다.

    1. **역할**에 **참가자**를 선택합니다. 
    2. **액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다. 
    3. **선택**에서 ADF 이름을 검색하여 선택합니다. 
    4. **저장**을 클릭합니다.
    
   ![ADF 관리 ID 역할 할당](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. 팩터리/파이프라인 도구 모음에서 **모든 유효성 검사/유효성 검사**를 클릭하여 ADF 및 모든 파이프라인 설정의 유효성을 검사합니다. **>>** 단추를 클릭하여 **팩터리/파이프라인 유효성 검사 출력**을 닫습니다.  

   ![파이프라인 유효성 검사](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>파이프라인 테스트 실행

1. 각 파이프라인의 도구 모음에서 **테스트 실행**을 선택하고 아래쪽 창에서 **출력** 창을 봅니다. 

   ![테스트 실행](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. 세 번째 파이프라인을 테스트하려면 SSMS(SQL Server Management Studio)를 시작합니다. **서버에 연결** 창에서 다음 작업을 수행합니다. 

    1. **서버 이름**의 경우 **&lt;Azure SQL Database 서버 이름&gt;.database.windows.net**을 입력합니다.
    2. **옵션 >>** 을 선택합니다.
    3. **데이터베이스에 연결**에 대해 **SSISDB**를 선택합니다.
    4. **연결**을 선택합니다. 
    5. **Integration Services 카탈로그** -> **SSISDB** -> 사용자 폴더 -> **프로젝트** -> 사용자 SSIS 프로젝트 -> **패키지**를 펼칩니다. 
    6. SSIS 패키지를 마우스 오른쪽 단추로 클릭하고, **보고서** -> **표준 보고서** -> **모든 실행**을 실행 및 선택합니다. 
    7. 실행되었는지 확인합니다. 

   ![SSIS 패키지 실행 확인](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>파이프라인 일정 예약

이제 파이프라인이 예상대로 작동하므로 지정된 일정에 따라 파이프라인을 실행하는 트리거를 만들 수 있습니다. 트리거를 파이프라인에 연결하는 방법에 대한 자세한 내용은 [일정에 따라 파이프라인 트리거](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) 문서를 참조하세요.

1. 파이프라인 도구 모음에서 **트리거**를 선택하고 **새로 만들기/편집**을 선택합니다. 

   ![트리거 -> 새로 만들기/편집](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. **트리거 추가** 창에서 **+ 새로 만들기**를 선택합니다.

   ![트리거 추가 - 새로 만들기](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. **새 트리거** 창에서 다음 작업을 수행합니다. 

    1. **이름**으로 트리거 이름을 입력합니다. 다음 예제에서는 **매일 실행**이 트리거 이름입니다. 
    2. **형식**으로 **예약**을 선택합니다. 
    3. **시작 날짜(UTC)** 에 시작 날짜 및 시간을 UTC로 입력합니다. 
    4. **되풀이**에 트리거 일정을 입력합니다. 다음 예제에서는 **매일** 한 번입니다. 
    5. **종료**에서 **종료 없음**을 선택하거나 **날짜**를 선택한 후 종료 날짜 및 시간을 입력합니다. 
    6. 전체 ADF 설정을 게시하는 즉시 트리거가 활성화되도록 **활성화됨**을 선택합니다. 
    7. **다음**을 선택합니다.

   ![트리거 -> 새로 만들기/편집](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. **트리거 실행 매개 변수** 페이지에서 경고를 검토하고 **마침**을 선택합니다. 
5. 팩터리 도구 모음에서 **모두 게시**를 선택하여 전체 ADF 설정을 게시합니다. 

   ![모두 게시](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Azure Portal에서 파이프라인 및 트리거 모니터링

1. 트리거 실행 및 파이프라인 실행을 모니터링하려면 ADF UI/앱 왼쪽에 있는 **모니터링** 탭을 사용합니다. 자세한 단계는 [파이프라인 모니터링](quickstart-create-data-factory-portal.md#monitor-the-pipeline) 문서를 참조하세요.

   ![파이프라인 실행](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. 파이프라인 실행과 연결된 작업 실행을 보려면 **작업** 열에서 첫 번째 링크(**작업 실행 보기**)를 선택합니다. 세 번째 파이프라인에는 파이프라인의 연결된 작업당 하나씩 총 세 개의 작업 실행이 있습니다(IR을 시작하는 웹 작업, 패키지를 실행하는 저장 프로시저 작업, IR을 중지하는 웹 작업). 파이프라인 실행을 다시 보려면 위쪽의 **파이프라인** 링크를 선택합니다.

   ![작업 실행](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. 트리거 실행을 보려면 상단의 **파이프라인 실행** 아래에 있는 드롭다운 목록에서 **트리거 실행**을 선택합니다. 

   ![트리거 실행](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>PowerShell을 사용하여 파이프라인 및 트리거 모니터링

다음 예제와 같은 스크립트를 사용하여 파이프라인 및 트리거를 모니터링합니다.

1. 파이프라인 실행 상태를 가져옵니다.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. 트리거 정보를 가져옵니다.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. 트리거 실행 상태를 가져옵니다.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Azure-SSIS IR을 시작/중지하는 Azure Automation Runbook 만들기 및 예약

이 섹션에서는 PowerShell 스크립트를 실행하여 일정에 따라 Azure-SSIS IR을 시작/중지하는 Azure Automation Runbook을 만드는 방법을 알아봅니다.  사전/사후 처리에서 IR을 시작/중지하기 전/후에 추가 스크립트를 실행하려는 경우에 유용합니다.

### <a name="create-your-azure-automation-account"></a>Azure Automation 계정 만들기

아직 Azure Automation 계정이 없는 경우 이 단계의 지침에 따라 하나 만듭니다. 자세한 단계는 [Azure Automation 계정 만들기](../automation/automation-quickstart-create-account.md) 문서를 참조하세요. 이 단계의 일부로 **Azure 실행** 계정(Azure Active Directory의 서비스 사용자)을 만들어서 Azure 구독의 **기여자** 역할에 할당합니다. Azure SSIS IR이 있는 ADF를 포함하고 있는 구독과 동일한 구독인지 확인합니다. Azure Automation은 이 계정을 사용하여 Azure Resource Manager에 인증하고 리소스를 작동합니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 ADF UI/앱은 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure 포털](https://portal.azure.com/)에 로그인합니다.    
3. 왼쪽 메뉴에서 **새로 만들기**를 선택하고 **모니터링 + 관리**를 선택한 후 **Automation**을 선택합니다. 

   ![새로 만들기 -> 모니터링 + 관리 -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. **Automation 계정 추가** 창에서 다음 작업을 수행합니다.

    1. **이름**으로 Azure Automation 계정 이름을 입력합니다. 
    2. **구독**으로 Azure-SSIS IR이 있는 ADF가 포함된 구독을 선택합니다. 
    3. **리소스 그룹**에서 **새로 만들기**를 선택하고 새 리소스 그룹을 만들거나, **기존 항목 사용**을 선택하고 기존 리소스 그룹을 선택합니다. 
    4. **위치**로 Azure Automation 계정의 위치를 선택합니다. 
    5. **Azure 실행 계정 만들기**를 **예**로 확인합니다. 서비스 주체가 Azure Active Directory에서 생성되어 Azure 구독의 **기여자** 역할에 할당됩니다.
    6. 영구적으로 Azure 대시보드에 표시하려면 **대시보드에 고정**을 선택합니다. 
    7. **만들기**를 선택합니다. 

   ![새로 만들기 -> 모니터링 + 관리 -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Azure 대시보드 및 알림에 Azure Automation 계정의 배포 상태가 표시됩니다. 
    
   ![Automation 배포 중](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Azure Automation 계정의 홈페이지가 생성되면 홈페이지를 볼 수 있습니다. 

   ![Automation 홈페이지](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF 모듈 가져오기

1. 왼쪽 메뉴의 **공유 리소스** 섹션에서 **모듈**을 선택하고, 모듈 목록에 **AzureRM.DataFactoryV2** + **AzureRM.Profile**이 있는지 확인합니다.

   ![필요한 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  **AzureRM.DataFactoryV2**가 없으면 [AzureRM.DataFactoryV2 모듈](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)의 PowerShell 갤러리로 이동하여 **Azure Automation에 배포**를 선택하고, Azure Automation 계정을 선택하고, **확인**을 선택합니다. 뒤로 돌아가서 왼쪽 메뉴의 **공유 리소스** 섹션에서 **모듈**을 보려면 **AzureRM.DataFactoryV2** 모듈의 **상태**가 **사용 가능**으로 변경될 때까지 대기합니다.

    ![데이터 팩터리 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  **AzureRM.Profile**이 없으면 [AzureRM.Profile 모듈](https://www.powershellgallery.com/packages/AzureRM.profile/)의 PowerShell 갤러리로 이동하여 **Azure Automation에 배포**를 선택하고, Azure Automation 계정을 선택하고, **확인**을 선택합니다. 뒤로 돌아가서 왼쪽 메뉴의 **공유 리소스** 섹션에서 **모듈**을 보려면 **AzureRM.Profile** 모듈의 **상태**가 **사용 가능**으로 변경될 때까지 대기합니다.

    ![프로필 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>PowerShell Runbook 만들기

다음 섹션에서는 PowerShell Runbook을 만드는 단계를 안내합니다. Runbook과 연결된 스크립트는 사용자가 **OPERATION** 매개 변수에 지정한 명령에 따라 Azure-SSIS IR 프로그램을 시작/중지합니다. 이 섹션에서는 Runbook 만들기에 대한 세부 정보를 제공하지 않습니다. 자세한 내용은 [Runbook 만들기](../automation/automation-quickstart-create-runbook.md) 문서를 참조하세요.

1. **Runbook** 탭으로 전환한 후 도구 모음에서 **+ Runbook 추가**를 선택합니다. 

   ![Runbook 추가 단추](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. **새 Runbook 만들기**를 선택하고 다음 작업을 수행합니다. 

    1. **이름**으로 **StartStopAzureSsisRuntime**을 입력합니다.
    2. **Runbook 형식**으로 **PowerShell**을 선택합니다.
    3. **만들기**를 선택합니다.
    
   ![Runbook 추가 단추](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. 다음 PowerShell 스크립트를 복사하여 Runbook 스크립트 창에 붙여넣습니다. 도구 모음에서 **저장** 및 **게시** 단추를 사용하여 Runbook을 저장한 후 게시합니다. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![PowerShell Runbook 편집](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. 도구 모음에서 **시작** 단추를 선택하여 Runbook을 테스트합니다. 

   ![Runbook 시작 단추](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. **Runbook 시작** 창에서 다음 작업을 수행 합니다. 

    1. **리소스 그룹 이름**에는 Azure-SSIS IR이 있는 ADF를 포함하는 리소스 그룹의 이름을 입력합니다. 
    2. **데이터 팩터리 이름**에는 Azure-SSIS IR이 있는 ADF의 이름을 입력합니다. 
    3. **AZURESSISNAME**으로 Azure-SSIS IR의 이름을 입력합니다. 
    4. **OPERATION**에 **START**를 입력합니다. 
    5. **확인**을 선택합니다.  

   ![Runbook 시작 창](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. 작업 창에서 **출력** 타일을 선택합니다. 출력 창에 **##### Starting #####** 메시지가 표시된 후 **##### Completed #####** 메시지가 표시될 때까지 기다립니다. Azure-SSIS IR을 시작하는 데 20분 정도 걸립니다. **작업** 창을 닫고 **Runbook** 창으로 돌아갑니다.

   ![Azure SSIS IR - 시작됨](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. **OPERATION** 값으로 **STOP**을 사용하여 위의 두 단계를 반복합니다. 도구 모음에서 **시작** 단추를 선택하여 Runbook을 다시 시작합니다. 리소스 그룹, ADF 및 Azure-SSIS IR 이름을 입력합니다. **OPERATION**에 대해 **STOP**을 입력합니다. 출력 창에 **##### Stopping #####** 메시지가 표시된 후 **##### Completed #####** 메시지가 표시될 때까지 기다립니다. Azure-SSIS IR 중지는 시작만큼 오래 걸리지 않습니다. **작업** 창을 닫고 **Runbook** 창으로 돌아갑니다.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Azure-SSIS IR을 시작/중지하는 일정 만들기

이전 섹션에서는 Azure-SSIS IR을 시작 또는 중지할 수 있는 Azure Automation Runbook을 만들었습니다. 이번 섹션에서는 Runbook에 대한 두 가지 일정을 만들겠습니다. 첫 번째 일정을 구성할 때는 **OPERATION**에 대해 **START**를 지정합니다. 마찬가지로, 두 번째 일정을 구성할 때는 **OPERATION**에 대해 **STOP**을 지정합니다. 일정을 만드는 자세한 단계는 [일정 만들기](../automation/shared-resources/schedules.md#creating-a-schedule) 문서를 참조하세요.

1. **Runbook** 창에서 **일정**을 선택하고, 도구 모음에서 **+ 일정 추가**를 선택합니다. 

   ![Azure SSIS IR - 시작됨](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. **Runbook 예약** 창에서 다음 작업을 수행합니다. 

    1. **Runbook에 일정 연결**을 선택합니다. 
    2. **새 일정 만들기**를 선택합니다.
    3. **새 일정** 창에서 **이름**으로 **IR 매일 시작**을 입력합니다. 
    4. **시작**에는 현재 시간보다 몇 분 뒤의 시간을 입력합니다. 
    5. **되풀이**에 대해 **정기**를 선택합니다. 
    6. **되풀이 간격**에는 **1**을 입력하고 **일**을 선택합니다. 
    7. **만들기**를 선택합니다. 

   ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. **매개 변수 및 실행 설정** 탭으로 전환합니다. 리소스 그룹, ADF 및 Azure-SSIS IR 이름을 지정합니다. **OPERATION**에 **START**를 입력하고 **확인**을 선택합니다. **확인**을 다시 선택하여 Runbook의 **일정** 페이지에서 일정을 확인합니다. 

   ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. 이전 2단계를 반복해서 **IR 매일 중지**라는 일정을 만듭니다. **IR 매일 시작** 일정에 대해 지정한 시간보다 적어도 30분 뒤의 시간을 입력합니다. **OPERATION**에 **STOP**을 입력하고 **확인**을 선택합니다. **확인**을 다시 선택하여 Runbook의 **일정** 페이지에서 일정을 확인합니다. 

5. **Runbook** 창의 왼쪽 메뉴에서 **작업**을 선택합니다. 지정된 시간에 일정에 따라 만들어지는 작업과 상태를 볼 수 있습니다. Runbook을 테스트한 후에 본 것과 마찬가지로, 출력 같은 작업 세부 정보를 볼 수 있습니다. 

   ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. 테스트를 마친 후에는 일정을 편집하여 사용하지 않도록 설정합니다. 왼쪽 메뉴에서 **일정**을 선택하고, **IR 매일 시작/IR 매일 중지**를 선택하고, **사용**에서 **아니요**를 선택합니다. 

## <a name="next-steps"></a>다음 단계
다음 블로그 게시물을 참조하십시오.
-   [ADF 파이프라인에서 SSIS 작업을 사용하여 ETL/ELT 워크플로 현대화 및 확장](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

SSIS 설명서에서 다음 문서를 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSIS 카탈로그에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
