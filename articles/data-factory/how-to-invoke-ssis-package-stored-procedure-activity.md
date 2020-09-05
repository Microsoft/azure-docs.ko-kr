---
title: 저장 프로시저 작업을 사용 하 여 SSIS 패키지 실행-Azure
description: 이 문서에서는 Azure Data Factory 파이프라인에서 저장 프로시저 작업을 사용하여 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법에 대해 설명합니다.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05ec19fd2351b3a9ac1f383ce4747404eeead936
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067827"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory에서 저장 프로시저 작업을 사용하여 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 저장 프로시저 작업을 사용하여 Azure Data Factory 파이프라인에서 SSIS 패키지를 실행하는 방법에 대해 설명합니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="azure-sql-database"></a>Azure SQL Database 
이 문서의 연습에서는 Azure SQL Database를 사용 하 여 SSIS 카탈로그를 호스팅합니다. Azure SQL Managed Instance를 사용할 수도 있습니다.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기
Azure-SSIS 통합 런타임이 없는 경우 [자습서: SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md)의 단계별 지침에 따라 만듭니다.

## <a name="data-factory-ui-azure-portal"></a>Data Factory UI(Azure Portal )
이 섹션에서는 Data Factory UI를 사용하여 SSIS 패키지를 호출하는 저장 프로시저 작업이 있는 Data Factory 파이프라인을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기
먼저, Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com)로 이동합니다. 
3. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![사용할 수 없는 이름 - 오류](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
   - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
   - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
     리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
4. **버전**에 대해 **V2**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. Data Factory에서 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 위치에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 다음과 같은 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

     ![데이터 팩터리 배포 중 타일](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
     ![데이터 팩터리 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스) 애플리케이션을 시작합니다. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>저장 프로시저 작업을 사용하여 파이프라인 만들기
이 단계에서는 Data Factory UI를 사용하여 파이프라인을 만듭니다. 저장 프로시저 작업을 파이프라인에 추가하고 sp_executesql 저장 프로시저를 사용하여 SSIS 패키지를 실행하도록 구성합니다. 

1. 시작 페이지에서 **파이프라인 만들기**를 클릭 합니다. 

    ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. **작업** 도구 상자에서 **일반**을 펼치고, **저장 프로시저** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

    ![저장 프로시저 작업 끌어서 놓기](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. 저장 프로시저 작업에 대한 속성 창에서 **SQL 계정** 탭으로 전환한 후 **+ 새로 만들기**를 클릭합니다. SSIS 카탈로그 (SSIDB 데이터베이스)를 호스팅하는 Azure SQL Database에서 데이터베이스에 대 한 연결을 만듭니다. 
   
    ![새 연결된 서비스 단추](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **유형**에 대해 **Azure SQL Database**를 선택합니다.
    2. **기본** Azure Integration Runtime을 선택하여 `SSISDB` 데이터베이스를 호스트하는 Azure SQL Database에 연결합니다.
    3. **서버 이름** 필드에 대해 SSISDB 데이터베이스를 호스트하는 Azure SQL Database를 선택합니다.
    4. **데이터베이스 이름**으로 **SSISDB**를 선택합니다.
    5. **사용자 이름**으로 데이터베이스에 대한 액세스 권한이 있는 사용자의 이름을 입력합니다.
    6. **암호**에 대해 사용자의 암호를 입력합니다. 
    7. **연결 테스트** 단추를 클릭하여 데이터베이스에 대한 연결을 테스트합니다.
    8. **저장** 단추를 클릭하여 연결된 서비스를 저장합니다. 

        ![Azure SQL Database 연결된 서비스](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. 속성 창의 **SQL 계정** 탭에서 **저장 프로시저** 탭으로 전환 하 고 다음 단계를 수행 합니다. 

    1. **편집**을 선택합니다. 
    2. **저장 프로시저 이름** 필드에을 입력 `sp_executesql` 합니다. 
    3. **저장 프로시저 매개 변수** 섹션에서 **+ 새로 만들기**를 클릭합니다. 
    4. 매개 변수의 **이름**으로 **stmt**를 입력합니다. 
    5. 매개 변수의 **형식**으로 **String**을 입력합니다. 
    6. 매개 변수의 **값**으로 다음 SQL 쿼리를 입력합니다.

        SQL 쿼리에서 **folder_name**, **project_name** 및 **package_name** 매개 변수의 올바른 값을 지정합니다. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Database 연결된 서비스](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. 파이프라인 구성에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>** 를 클릭합니다.

    ![파이프라인 유효성 검사](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. **모두 게시** 단추를 클릭하여 Data Factory에 파이프라인을 게시합니다. 

    ![게시](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>파이프라인 실행 및 모니터링
이 섹션에서는 파이프라인 실행을 트리거한 후 모니터링합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거** 를 클릭 하 고 **지금 트리거**를 클릭 합니다. 

    ![지금 트리거](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 
3. 왼쪽의 **모니터** 탭으로 전환합니다. 다른 정보(예: 실행 시작 시간)와 함께 파이프라인 실행 및 해당 상태를 확인합니다. 보기를 새로 고치려면 **새로 고침**을 클릭합니다.

    ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 파이프라인에는 작업이 하나만 있으므로(저장 프로시저 작업) 하나의 작업 실행만 파이프라인으로 표시됩니다.

    ![작업 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. SQL Database에서 SSISDB 데이터베이스에 대해 다음 **쿼리** 를 실행 하 여 패키지가 실행 되었는지 확인할 수 있습니다. 

    ```sql
    select * from catalog.executions
    ```

    ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> 또한 파이프라인이 일정대로(시간별, 일별, 등) 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 호출하는 저장 프로시저 작업이 있는 Data Factory 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다. 

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기
Azure-SSIS IR이 있는 동일한 데이터 팩터리를 사용하거나 별도의 데이터 팩터리를 만들 수 있습니다. 다음 절차에서는 데이터 팩터리를 만드는 단계를 설명합니다. 이 데이터 팩터리의 저장 프로시저 작업을 사용하여 파이프라인을 만듭니다. 저장 프로시저 작업은 SSISDB 데이터베이스의 저장 프로시저를 실행하여 SSIS 패키지를 실행합니다. 

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요. 
3. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. 데이터 팩터리를 만들려면 $ResGrp 변수의 Location 및 ResourceGroupName 속성을 사용하여 다음 **Set-AzDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
SSIS 카탈로그를 호스트 하는 데이터베이스를 데이터 팩터리에 연결 하는 연결 된 서비스를 만듭니다. 데이터 팩터리는 이 연결된 서비스의 정보를 사용하여 SSISDB 데이터베이스에 연결하고 저장 프로시저를 실행하여 SSIS 패키지를 실행합니다. 

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **AzureSqlDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. 

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;servername&gt;, &lt;username&gt; 및 &lt;password&gt;를 Azure SQL Database의 값으로 바꿉니다.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.

3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 연결된 서비스를 만듭니다. **AzureSqlDatabaseLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>저장 프로시저 작업을 사용하여 파이프라인 만들기 
이 단계에서는 저장 프로시저 작업을 사용하여 파이프라인 만듭니다. 이 작업은 sp_executesql 저장 프로시저를 호출하여 SSIS 패키지를 실행합니다. 

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **RunSSISPackagePipeline.json**이라는 JSON 파일을 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;FOLDER NAME&gt;, &lt;PROJECT NAME&gt;, &lt;PACKAGE NAME&gt;을 SSIS 카탈로그에 있는 폴더, 프로젝트 및 패키지의 이름으로 바꿉니다. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. **RunSSISPackagePipeline**파이프라인을 만들려면 **AzDataFactoryV2Pipeline** cmdlet을 실행 합니다.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기
**AzDataFactoryV2Pipeline** cmdlet을 사용 하 여 파이프라인을 실행 합니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>트리거 만들기
이전 단계에서는 파이프라인을 주문형으로 호출했습니다. 일정 트리거를 만들어 파이프라인을 예약형으로 실행할 수도 있습니다(매시간, 매일 등).

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **MyTrigger.json**이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.
3. 트리거를 만드는 **AzDataFactoryV2Trigger** cmdlet을 실행 합니다. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 기본적으로 트리거는 중지된 상태입니다. **AzDataFactoryV2Trigger** cmdlet을 실행 하 여 트리거를 시작 합니다. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. **AzDataFactoryV2Trigger** cmdlet을 실행 하 여 트리거가 시작 되었는지 확인 합니다. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    SQL Database에서 SSISDB 데이터베이스에 대해 다음 쿼리를 실행 하 여 패키지가 실행 되었는지 확인할 수 있습니다. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>다음 단계
Azure Portal을 사용하여 파이프라인을 모니터링 할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.
