---
title: Power BI 대시보드에서 Azure IoT Central 데이터 시각화 | Microsoft Docs
description: Azure IoT Central용 Power BI 솔루션을 사용하여 IoT Central 데이터를 시각화하고 분석할 수 있습니다.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886794"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석

*이 항목의 내용은 관리자에게 적용됩니다.*

![Power BI 솔루션 파이프라인](media/howto-connect-powerbi/iot-continuous-data-export.png)

Azure IoT Central용 Power BI 솔루션을 사용하여 IoT 디바이스의 성능을 모니터링하기 위한 강력한 Power BI 대시보드를 만들 수 있습니다. Power BI 대시보드에서 다음을 수행할 수 있습니다.
- 시간이 지남에 따라 디바이스가 보내는 데이터의 양 추적
- 원격 분석, 상태 및 이벤트 간의 데이터 볼륨 비교
- 많은 측정값을 보고하는 디바이스 식별
- 디바이스 측정의 기록 추세 관찰
- 많은 중요한 이벤트를 전송하는 데 문제가 있는 디바이스 식별

이 솔루션은 [연속 데이터 내보내기](howto-export-data.md)에서 Azure Blob Storage 계정의 데이터를 가져오는 파이프라인을 설정합니다. 이 데이터는 Azure Functions, Azure Data Factory 및 Azure SQL Database를 거치며 처리 및 변환됩니다. PBIX 파일로 다운로드할 수 있는 Power BI 보고서에서 출력을 시각화하고 분석할 수 있습니다. 이러한 모든 리소스는 필요에 따라 각 구성 요소를 사용자 지정할 수 있도록 Azure 구독에서 생성됩니다.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Microsoft AppSource에서 [Azure IoT Central용 Power BI 솔루션](https://aka.ms/iotcentralpowerbisolutiontemplate)을 가져옵니다.

## <a name="prerequisites"></a>필수 조건
솔루션을 설정하려면 다음이 필요합니다.
- Azure 구독에 액세스
- IoT Central 앱에서 [연속 데이터 내보내기](howto-export-data.md)를 사용하여 내보낸 데이터. Power BI 대시보드를 최대한 활용하려면 측정, 디바이스 및 디바이스 템플릿 스트림을 켜는 것이 좋습니다.
- Power BI Desktop(최신 버전)
- Power BI Pro(다른 사용자와 대시보드를 공유하려는 경우)

## <a name="reports"></a>보고서

두 보고서는 자동으로 생성됩니다. 

첫 번째 보고서는 디바이스에서 보고하는 측정값의 기록 보기를 보여주고, 측정값 및 디바이스를 다양한 유형으로 세분화하여 가장 높은 측정값을 전송합니다.

![Power BI 보고서 페이지 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

두 번째 보고서는 이벤트를 자세히 설명하고 보고된 오류 및 경고의 기록 보기를 보여줍니다. 또한 특히 오류 이벤트 및 경고 이벤트를 비롯해 가장 많은 이벤트를 모두 보고하는 디바이스를 보여줍니다.

![Power BI 보고서 페이지 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>아키텍처
생성된 모든 리소스는 Azure Portal에서 액세스할 수 있습니다. 모든 리소스가 하나의 리소스 그룹에 있어야 합니다.

![Azure Portal의 리소스 그룹 보기](media/howto-connect-powerbi/azure-deployment.PNG)

각 리소스의 세부 정보 및 리소스가 사용되는 방식이 아래에 설명되어 있습니다.

### <a name="azure-functions"></a>Azure 기능
Azure Function App은 Blob Storage에 새 파일을 쓸 때마다 트리거됩니다. 함수는 각 측정값, 디바이스 및 디바이스 템플릿 파일 내의 필드를 추출하고 Azure Data Factory에서 사용할 여러 개의 중간 SQL 테이블을 채웁니다.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory는 연결된 서비스로 SQL 데이터베이스에 연결됩니다. 데이터를 처리하고 분석 테이블에 저장하는 저장 프로시저 활동을 실행합니다.

### <a name="azure-sql-database"></a>Azure SQL Database
이러한 테이블은 자동으로 생성되어 기본 보고서를 채웁니다. Power BI에서 해당 스키마를 살펴본 다음, 이 데이터를 기반으로 한 사용자 고유의 시각화를 빌드할 수 있습니다.

| 테이블 이름 |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>예상 비용

다음은 관련된 Azure 비용(Azure 함수, Data Factory, Azure SQL)의 추정치입니다. 모든 가격은 USD입니다. 가격은 지역에 따라 다르므로 실제 가격을 확인하려면 항상 개별 서비스의 최신 가격을 조회해야 합니다.
템플릿에는 다음 기본값이 설정되어 있습니다(설정을 마친 후 기본값을 수정할 수 있음).

- Azure Functions: App Service 계획 S1, $74.40/월
- Azure SQL S1, ~$30/월

다양한 가격 책정 옵션을 살펴보고 요구 사항에 맞게 수정하는 것이 좋습니다.

## <a name="resources"></a>리소스

AppSource를 방문하여 [Azure IoT Central용 Power BI 솔루션](https://aka.ms/iotcentralpowerbisolutiontemplate)을 가져옵니다.

## <a name="next-steps"></a>다음 단계

이제 Power BI에서 데이터를 시각화하는 방법을 파악했으므로 다음과 같이 다음 단계를 제안합니다.

> [!div class="nextstepaction"]
> [디바이스를 관리하는 방법](howto-manage-devices.md)