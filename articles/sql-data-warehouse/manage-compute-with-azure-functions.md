---
title: '자습서: Azure SQL Data Warehouse에서 Azure Functions로 컴퓨팅 관리 | Microsoft Docs'
description: Azure Functions를 사용하여 데이터 웨어하우스의 계산을 관리하는 방법에 대해 설명합니다.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b94e4c6f178119d6205c302cf35a9effaf2aa885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083864"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Azure Functions를 사용하여 Azure SQL Data Warehouse에서 계산 리소스 관리

이 자습서에서는 Azure Functions를 사용하여 Azure SQL Data Warehouse에서 데이터 웨어하우스의 계산 리소스를 관리합니다.

SQL Data Warehouse에 Azure Function App을 사용하려면 데이터 웨어하우스 인스턴스와 동일한 구독에 속한 참가자 액세스 권한으로 [서비스 사용자 계정](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)을 만들어야 합니다. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 타이머 기반 크기 조정 배포

템플릿을 배포하려면 다음 정보가 필요합니다.

- SQL DW 인스턴스가 속한 리소스 그룹의 이름
- SQL DW 인스턴스가 속한 논리 서버의 이름
- SQL DW 인스턴스의 이름
- Azure Active Directory의 테넌트 ID(디렉터리 ID)
- 구독 ID 
- 서비스 사용자 애플리케이션 ID
- 서비스 사용자 비밀 키

앞에서 설명한 정보가 있으면 다음 템플릿을 배포합니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

템플릿이 배포되었으면 새로운 세 가지 리소스인 무료 Azure App Service 계획, 사용량 기준 Function App 계획, 로깅 및 작업 큐를 처리하는 저장소 계정을 찾아야 합니다. 다른 섹션을 계속 읽고 배포된 기능을 요구 사항에 맞게 수정하는 방법을 알아보세요.

## <a name="change-the-compute-level"></a>계산 수준 변경

1. Function App 서비스로 이동합니다. 템플릿을 기본값으로 배포한 경우 이 서비스의 이름은 *DWOperations*입니다. Function App을 열면 Function App 서비스에 5개 함수가 배포되었을 것입니다. 

   ![템플릿을 사용하여 배포되는 함수](media/manage-compute-with-azure-functions/five-functions.png)

2. 강화 또는 규모 축소 시간을 변경할 것인지 여부에 따라 *DWScaleDownTrigger* 또는 *DWScaleUpTrigger*를 선택합니다. 드롭다운 메뉴에서 [통합]을 선택합니다.

   ![함수에 대해 통합 선택](media/manage-compute-with-azure-functions/select-integrate.png)

3. 현재 표시되는 값이 *%ScaleDownTime%* 또는 *%ScaleUpTime%* 여야 합니다. 이러한 값은 일정이 [애플리케이션 설정][Application Settings]에 정의된 값을 따른다는 의미입니다. 지금은 이 값을 무시하고 다음 단계에 따라 일정을 원하는 시간으로 변경할 수 있습니다.

4. 일정 영역에서 원하는 SQL Data Warehouse 강화 주기를 반영하도록 CRON 식의 시간을 추가합니다. 

   ![함수 일정 변경](media/manage-compute-with-azure-functions/change-schedule.png)

   `schedule`의 값은 이러한 6개 필드를 포함하는 [CRON 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)입니다. 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   예를 들어 *"0 30 9 * * 1-5"* 는 평일 오전 9시 30분에 트리거를 반영합니다. 자세한 내용은 Azure Functions [일정 예][schedule examples]를 방문하세요.


## <a name="change-the-time-of-the-scale-operation"></a>크기 조정 작업의 시간 변경

1. Function App 서비스로 이동합니다. 템플릿을 기본값으로 배포한 경우 이 서비스의 이름은 *DWOperations*입니다. Function App을 열면 Function App 서비스에 5개 함수가 배포되었을 것입니다. 

2. 강화 또는 규모 축소 계산 값을 변경할 것인지 여부에 따라 *DWScaleDownTrigger* 또는 *DWScaleUpTrigger*를 선택합니다. 함수를 선택하는 즉시 창에 *index.js* 파일이 표시됩니다.

   ![함수 트리거 계산 수준 변경](media/manage-compute-with-azure-functions/index-js.png)

3. *ServiceLevelObjective* 값을 원하는 수준으로 변경하고 저장을 누릅니다. 이 값은 [통합] 섹션에 정의된 일정에 따라 데이터 웨어하우스 인스턴스의 크기가 조정되는 계산 수준입니다.

## <a name="use-pause-or-resume-instead-of-scale"></a>확장 대신 일시 중지 또는 다시 시작 사용 

현재 함수는 기본적으로 *DWScaleDownTrigger* 및 *DWScaleUpTrigger*입니다. 일시 중지 및 다시 시작 함수를 대신 사용하려면 *DWPauseTrigger* 또는 *DWResumeTrigger*를 사용하도록 설정하면 됩니다.

1. Functions 창으로 이동합니다.

   ![Functions 창](media/manage-compute-with-azure-functions/functions-pane.png)



2. 사용하도록 설정할 트리거의 슬라이딩 토글을 클릭합니다.

3. 각 트리거의 *통합* 탭으로 이동하여 일정을 변경합니다.

   > [!NOTE]
   > 크기 조정 트리거와 일시 중지/다시 시작 트리거 간의 기능상의 차이는 큐로 보내는 메시지에 있습니다. 자세한 내용은 [새 트리거 함수 추가][Add a new trigger function]를 참조하세요.


## <a name="add-a-new-trigger-function"></a>새 트리거 함수 추가

현재는 템플릿에 크기 조정 함수가 두 개밖에 없습니다. 이러한 함수를 사용하면 강화 및 규모 축소를 각각 하루에 한 번만 수행할 수 있습니다. 하루에 여러 번 규모를 축소하거나 주말에 다른 크기 조정 동작을 수행하는 등 더 세밀한 제어가 필요한 경우 또 다른 트리거를 추가해야 합니다.

1. 빈 함수를 만듭니다. Functions 위치 근처에 있는 *+* 단추를 선택하여 함수 템플릿 창을 표시합니다.

   ![새 함수 만들기](media/manage-compute-with-azure-functions/create-new-function.png)

2. 언어에서 *Javascript*를 선택한 후 *TimerTrigger*를 선택합니다.

   ![새 함수 만들기](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. 함수 이름을 지정하고 일정을 설정합니다. 다음 이미지는 매주 토요일 자정(금요일 늦은 밤)에 함수를 트리거하는 방법을 보여줍니다.

   ![토요일에 규모 축소](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. 다른 트리거 함수 중 하나에서 *index.js*의 내용을 복사합니다.

   ![index js 복사](media/manage-compute-with-azure-functions/index-js.png)

5. 다음과 같이 작업 변수를 원하는 동작으로 설정합니다.

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>복잡한 일정 계획

이 섹션에서는 일시 중지, 다시 시작 및 크기 조정에 대한 더 복잡한 일정 계획에 필요한 사항을 간략하게 보여 줍니다.

### <a name="example-1"></a>예제 1:

매일 오전 8시에 DW600으로 강화하고 오후 8시에 DW200으로 규모 축소합니다.

| 함수  | 일정     | 작업(Operation)                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>예 2: 

매일 오전 8시에 DW1000으로 강화하고, 오후 4시에 규모를 DW600으로 한 번 축소하고, 오후 10시에 DW200으로 축소합니다.

| 함수  | 일정     | 작업(Operation)                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>예 3: 

평일 오전 8시에 DW1000으로 강화하고, 오후 4시에 DW600으로 규모 축소합니다. 금요일 오후 11시에 일시 중지하고 월요일 오전 7시에 다시 시작합니다.

| 함수  | 일정       | 작업(Operation)                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>다음 단계

[타이머 트리거](../azure-functions/functions-create-scheduled-function.md) Azure 함수에 대해 자세히 알아봅니다.

SQL Data Warehouse [샘플 리포지토리](https://github.com/Microsoft/sql-data-warehouse-samples)를 확인합니다.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function
