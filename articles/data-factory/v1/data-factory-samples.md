---
title: Azure 데이터 팩터리 - 샘플
description: Azure Data Factory 서비스와 함께 제공 되는 샘플에 대 한 세부 정보를 제공 합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61260039"
---
# <a name="azure-data-factory---samples"></a>Azure 데이터 팩터리 - 샘플
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory의 PowerShell 샘플](../samples-powershell.md) 및 [Azure Code Samples 갤러리의 코드 샘플](https://azure.microsoft.com/resources/samples/?service=data-factory)을 참조하세요.


## <a name="samples-on-github"></a>GitHub의 샘플
[GitHub Azure-DataFactory 리포지토리](https://github.com/azure/azure-datafactory) 에는 신속하게 Azure Data Factory 서비스의 규모를 확장하거나 스크립트를 수정하고 자체 애플리케이션에서 사용하는 데 도움이 되는 여러 샘플이 포함되어 있습니다. Samples\JSON 폴더에는 일반적인 시나리오에 대한 JSON 조각이 포함되어 있습니다.

| 샘플 | 설명 |
|:--- |:--- |
| [ADF 연습](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |이 샘플에서는 Azure Data Factory를 사용하여 로그 파일을 유용한 정보로 변환하는 완전한 연습을 제공합니다. <br/><br/>이 연습에서 Data Factory 파이프라인은 샘플 로그를 수집하고, 참조 데이터를 사용하여 로그의 데이터를 처리 및 보강하고, 데이터를 변환하여 최근 시작한 마케팅 캠페인의 효과를 평가합니다. |
| [JSON 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |이 예제에서는 일반적인 시나리오에 대한 JSON 예제를 제공합니다. |
| [HTTP 데이터 다운로더 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |이 샘플에서는 사용자 지정 .NET 작업을 사용하여 HTTP 엔드포인트에서 Azure Blob Storage로 데이터를 다운로드하는 과정을 보여 줍니다. |
| [크로스 AppDomain .Net 작업 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |이 샘플에서는 ADF 시작 관리자에서 사용하는 어셈블리 버전(예: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x 등)의 제약을 받지 않는 사용자 지정 .NET 작업을 만드는 방법을 보여 줍니다. |
| [R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |이 샘플에서는 RScript.exe를 호출하는 데 사용할 수 있는 Data Factory 사용자 지정 작업이 포함되어 있습니다. 이 샘플은 R이 이미 설치되어 있는 사용자 고유(주문형 아님) HDInsight 클러스터에만 작동합니다. |
| [HDInsight Hadoop 클러스터에서 Spark 작업 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |이 샘플에서는 MapReduce 작업을 사용하여 Spark 프로그램을 호출하는 방법을 보여 줍니다. Spark 프로그램은 단순히 Azure Blob 컨테이너에서 다른 컨테이너로 데이터를 복사합니다. |
| [Azure Machine Learning Batch 평가 작업을 사용한 Twitter 분석](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |이 샘플에서는 AzureMLBatchScoringActivity를 사용하여 Twitter 감성 분석, 점수 지정, 예측 등을 수행하는 Azure Machine Learning 모델을 호출하는 방법을 보여 줍니다. |
| [사용자 지정 작업을 사용한 Twitter 분석](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |이 샘플에서는 사용자 지정 .NET 작업을 사용하여 Twitter 감성 분석, 점수 지정, 예측 등을 수행하는 Azure Machine Learning 모델을 호출하는 방법을 보여 줍니다. |
| [Azure Machine Learning에 대한 매개 변수가 있는 파이프라인](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |이 샘플에서는 지역 목록을 이 샘플에 포함된 parameters.txt 파일에서 가져오는 다른 지역 매개 변수를 사용하여 각각의 점수를 매기고 제한하기 위해 N 파이프라인을 배포하는 종단 간 C# 코드를 제공합니다. |
| [Azure Stream Analytics 작업에 대한 참조 데이터 새로 고침](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |이 샘플에서는 Azure Data Factory 및 Azure Stream Analytics를 함께 사용하여 참조 데이터로 쿼리를 실행하고 일정에 따라 참조 데이터에 대한 새로 고침을 설정하는 방법을 보여 줍니다. |
| [온-프레미스 Hortonworks Hadoop을 사용한 하이브리드 파이프라인](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |이 샘플에서는 클라우드의 HDInsight 기반 Hadoop 클러스터와 같은 기타 계산 대상을 추가하는 경우처럼 Data Factory에서 작업을 실행하기 위한 계산 대상으로 온-프레미스 Hadoop 클러스터를 사용합니다. |
| [JSON 변환 도구](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |이 도구를 사용하면 Json을 2015-07-01-preview 이전 버전에서 2015-07-01-preview(기본값)로 변환할 수 있습니다. |
| [U-SQL 샘플 입력 파일](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |U-SQL 작업에서 사용되는 샘플 파일입니다. |
| [Blob 파일 삭제](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | 이 샘플에서는 ADF 사용자 지정 .net 작업의 일부로 사용하여 파일이 복사되면 원본 Azure Blob 위치에서 파일을 삭제할 수 있는 C# 파일을 보여 줍니다.|

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿
GitHub의 데이터 팩터리에 대한 다음 Azure Resource Manager 템플릿을 찾을 수 있습니다.

| Template | 설명 |
| --- | --- |
| [Azure Blob Storage에서 Azure SQL Database로 데이터 복사](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |이 템플릿을 배포하면 지정된 Azure Blob 스토리지에서 Azure SQL 데이터베이스에 데이터를 복사하는 파이프라인으로 Azure Data Factory가 만들어집니다. |
| [Salesforce에서 Azure Blob Storage로 데이터 복사](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |이 템플릿을 배포하면 지정된 Salesforce 계정에서 Azure Blob Storage에 데이터를 복사하는 파이프라인으로 Azure Data Factory가 만들어집니다. |
| [Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터 변환](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |이 템플릿을 배포하면 Azure HDInsight Hadoop 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 파이프라인으로 Azure Data Factory가 만들어집니다. |

## <a name="samples-in-azure-portal"></a>Azure Portal의 샘플
데이터 팩터리의 홈 페이지에 있는 **샘플 파이프라인** 을 사용하여 데이터 팩터리에 샘플 파이프라인 및 관련된 엔터티(데이터 세트 및 연결된 서비스)를 배포합니다.

1. 데이터 팩터리를 만들거나 기존 데이터 팩터리를 엽니다. Data Factory를 만드는 단계는 [Data Factory를 사용하여 Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
2. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **샘플 파이프라인** 타일을 클릭합니다.

    ![샘플 파이프라인 타일](./media/data-factory-samples/SamplePipelinesTile.png)
3. **샘플 파이프라인** 블레이드에서 배포할 **샘플**을 클릭합니다.

    ![샘플 파이프라인 블레이드](./media/data-factory-samples/SampleTile.png)
4. 샘플에 대한 구성 설정을 지정합니다. 예를 들어 Azure Storage 계정 이름 및 계정 키, Azure SQL Server 이름, 데이터베이스, 사용자 ID, 암호 등입니다.

    ![샘플 블레이드](./media/data-factory-samples/SampleBlade.png)
5. 구성 설정 지정을 마쳤으면 **만들기** 를 클릭하여 샘플 파이프라인 및 파이프라인에서 사용되는 연결된 서비스/테이블을 만듭니다/배포합니다.
6. 이전에 **샘플 파이프라인** 블레이드에서 클릭한 샘플 타일에 배포 상태가 표시됩니다.

    ![배포 상태](./media/data-factory-samples/DeploymentStatus.png)
7. 샘플 타일에 **배포 성공** 메시지가 표시되면 **샘플 파이프라인** 블레이드를 닫습니다.  
8. **데이터 팩터리** 블레이드에서 연결된 서비스, 데이터 집합 및 파이프라인이 데이터 팩터리에 추가된 것을 확인할 수 있습니다.  

    ![데이터 팩터리 블레이드](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Visual Studio의 샘플
### <a name="prerequisites"></a>필수 조건
다음 항목이 컴퓨터에 설치되어 있어야 합니다.

* Visual Studio 2013 또는 Visual Studio 2015
* Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015**를 클릭합니다.
* Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. Visual Studio 2013을 사용 중인 경우 다음 단계를 수행하여 플러그 인을 업데이트할 수도 있습니다. 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트**를 클릭합니다.

### <a name="use-data-factory-templates"></a>데이터 팩터리 템플릿 사용
1. 메뉴에서 **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다.
2. **새 프로젝트** 대화 상자에서 다음 단계를 수행합니다.

   1. **템플릿** 아래의 **데이터 팩터리**를 선택합니다.
   2. 오른쪽 창의 **데이터 팩터리 템플릿** 을 선택합니다.
   3. 프로젝트의 **이름** 을 입력합니다.
   4. 프로젝트의 **위치** 를 선택합니다.
   5. **확인**을 클릭합니다.

      ![새 프로젝트 대화 상자](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. **데이터 팩터리 템플릿** 대화 상자에서 **사용 사례 템플릿** 섹션에 있는 샘플 템플릿을 선택하고 **다음**을 클릭합니다. 다음 단계는 **고객 프로파일링** 템플릿 사용 방법에 대해 안내합니다. 단계는 다른 샘플에 대해서도 유사합니다.

    ![데이터 팩터리 템플릿 대화 상자](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. **데이터 팩터리 구성** 대화 상자에서 **데이터 팩터리 기본 사항** 페이지에 있는 **다음**을 클릭합니다.
5. **데이터 팩터리 구성** 페이지에서 다음 단계를 수행합니다.
   1. **새 데이터 팩터리 만들기**를 선택합니다. **기존 데이터 팩터리 사용**을 선택할 수도 있습니다.
   2. 데이터 팩터리의 **이름** 을 입력합니다.
   3. 데이터 팩터리를 만들려는 위치에 **Azure 구독** 을 선택합니다.
   4. 데이터 팩터리의 **리소스 그룹** 을 선택합니다.
   5. **지역**에서 **미국 서부**, **미국 동부** 또는 **북유럽**을 선택합니다.
   6. **다음**을 클릭합니다.
6. **데이터 저장소 구성** 페이지에서 기존 **Azure SQL 데이터베이스** 및 **Azure 스토리지 계정**을 지정하거나, 데이터베이스/스토리지를 만들고 다음을 클릭합니다.
7. **계산 구성** 페이지에서 기본값을 선택하고 **다음**을 클릭합니다.
8. **요약** 페이지에서 모든 설정을 검토하고 **다음**을 클릭합니다.
9. **배포 상태** 페이지에서 배포가 완료될 때까지 대기하고 **마침**을 클릭합니다.
10. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.
11. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인**을 클릭합니다.
12. 다음 대화 상자가 표시됩니다.

    ![게시 대화 상자](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. **데이터 팩터리 구성** 페이지에서 다음 단계를 수행합니다.

    1. **기존 데이터 팩터리 사용** 옵션을 확인합니다.
    2. 템플릿을 사용할 때 선택한 **데이터 팩터리**를 선택합니다.
    3. **다음**을 클릭하여 **항목 게시** 페이지로 전환합니다. **다음** 단추를 사용할 수 없는 경우 **TAB** 키를 눌러 이름 필드에서 나갑니다.
14. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음**을 클릭하여 **요약** 페이지로 전환합니다.     
15. 요약을 검토한 후 **다음**을 클릭하여 배포 프로세스를 시작하고 **배포 상태**를 봅니다.
16. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다.

Visual Studio를 사용하여 Data Factory 엔터티를 작성하고 Azure에 게시하는 방법에 대한 자세한 내용은 [첫 번째 데이터 팩터리(Visual Studio) 빌드](data-factory-build-your-first-pipeline-using-vs.md) 를 참조하세요.          
