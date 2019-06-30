---
title: Azure Stream Analytics 작업 정리
description: 이 문서에서는 Azure Stream Analytics 작업을 삭제하기 위한 여러 가지 방법을 보여줍니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: cb81c73f7946a10bae0470a55dcf1c0d55c2b847
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330045"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>중지 하거나 삭제 하 여 Azure Stream Analytics 작업

Azure Stream Analytics 작업을 쉽게 중지 또는 Azure portal, Azure PowerShell, Azure SDK for.Net 또는 REST API를 통해 삭제할 수 있습니다. 삭제 된 후에 Stream Analytics 작업을 복구할 수 없습니다.

>[!NOTE] 
>Stream Analytics 작업을 중지한 경우 Event Hubs 또는 Azure SQL Database와 같은 입력 및 출력 스토리지에서만 데이터가 유지됩니다. Azure에서 데이터를 제거해야 하는 경우 Stream Analytics 작업의 입력 및 출력 리소스에 대해 제거 프로세스를 수행해야 합니다.

## <a name="stop-a-job-in-azure-portal"></a>Azure Portal에서 작업 중지

작업을 중지 하면 리소스가 deprovisionned 및 이벤트 처리를 중지 합니다. 이 작업에 관련 된 요금 서비스도 중지 됩니다. 그러나 모든 구성을 유지 하 고 나중에 작업을 다시 시작 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 실행 중인 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지에서 **중지**를 선택하여 작업을 중지합니다. 

   ![Azure Stream Analytics 작업 중지](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Azure Portal에서 작업 삭제

>[!WARNING] 
>삭제 된 후에 Stream Analytics 작업을 복구할 수 없습니다.

1. Azure 포털에 로그인합니다. 

2. 기존 Stream Analytics 작업을 찾아서 선택합니다.

3. Stream Analytics 작업 페이지에서 **삭제**를 선택하여 작업을 삭제합니다. 

   ![Azure Stream Analytics 작업 삭제](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>PowerShell을 사용하여 작업 중지 또는 삭제

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용 하는 작업을 중지 하려면 사용 합니다 [중지 AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet. PowerShell을 사용 하는 작업을 삭제 하려면 사용 합니다 [제거 AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>.NET용 Azure SDK를 사용하여 작업 중지 또는 삭제

.NET용 Azure SDK를 사용하여 작업을 중지하려면 [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) 메서드를 사용합니다. .NET용 Azure SDK를 사용하여 작업을 삭제하려면 [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) 메서드를 사용합니다.

## <a name="stop-or-delete-a-job-using-rest-api"></a>REST API를 사용하여 작업 중지 또는 삭제

REST API를 사용하여 작업을 중지하려면 [중지](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) 메서드를 참조합니다. REST API를 사용하여 작업을 삭제하려면 [삭제](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) 메서드를 참조합니다.
