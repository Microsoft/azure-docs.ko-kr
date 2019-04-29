---
title: Azure Machine Learning을 사용한 데이터 분석 | Microsoft Docs
description: Azure Machine Learning을 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 03/22/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7f9500adc6871c4c9f81c32bf456bc36cf91db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439976"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 분석
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

이 자습서는 Azure Machine Learning을 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다. 특히 고객이 자전거를 구매할 가능성 여부를 예측하여 자전거 매장인 Adventure Works에 대한 대상 마케팅 캠페인을 구축합니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

* AdventureWorksDW 샘플 데이터로 미리 로드된 SQL Data Warehouse. 프로비전하려면 [SQL Data Warehouse 만들기][Create a SQL Data Warehouse]를 참조하고 샘플 데이터 로드를 선택합니다. 데이터 웨어하우스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드][load sample data manually]할 수 있습니다.

## <a name="1-get-the-data"></a>1. 데이터 가져오기
데이터는 AdventureWorksDW 데이터베이스의 dbo.vTargetMail 보기에 있습니다. 이 데이터를 읽으려면:

1. [Azure Machine Learning 스튜디오][Azure Machine Learning studio]에 로그인하고 내 실험을 클릭합니다.
2. 클릭 **+ 새로 만들기** 화면 및 선택의 왼쪽 아래에 **빈 실험**합니다.
3. 실험의 이름을 Targeted Marketing으로 입력합니다.
4. 끌기 합니다 **데이터 가져오기** 아래의 모듈 **데이터 입력 및 출력** 캔버스에 모듈 창에서.
5. 속성 창에서 SQL Data Warehouse 데이터베이스에 대한 세부 정보를 지정합니다.
6. 관련 데이터를 읽을 데이터베이스 **쿼리** 를 지정합니다.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

실험 캔버스 아래에서 **실행** 을 클릭하여 실험을 실행합니다.
![실험 실행][1]

실험이 성공적으로 실행되고 나면 판독기 모듈 아래쪽에서 출력 포트를 클릭하고 **시각화** 를 선택하여 가져온 데이터를 확인합니다.
![가져온 데이터 확인][3]

## <a name="2-clean-the-data"></a>2. 데이터 정리
데이터를 정리하려면 모델에 관련되지 않은 일부 열을 삭제합니다. 다음을 수행합니다.

1. 끌어서 합니다 **Select Columns in Dataset** 아래의 모듈 **데이터 변환 < 조작** 캔버스에 합니다. 이 모듈을 연결 합니다 **데이터 가져오기** 모듈입니다.
2. 속성 창에서 **열 선택기 시작** 을 클릭하여 삭제하려는 열을 지정합니다.
   ![프로젝트 열][4]
3. 열 2개(CustomerAlternateKey 및 GeographyKey)를 제외합니다.
   ![불필요한 열 제거][5]

## <a name="3-build-the-model"></a>3. 모델 작성
여기서는 데이터를 80:20 비율로 분할합니다. 80%는 기계 학습 모델을 학습시키는 데 사용되며, 20%는 모델을 테스트하는 데 사용됩니다. 이진 분류 문제에 대해 "2클래스" 알고리즘을 활용합니다.

1. **분할** 모듈을 캔버스로 끌어서 놓습니다.
2. 속성 창에서 첫 번째 출력 데이터 집합의 행 분수에 대해 0.8을 입력 합니다.
   ![훈련 및 테스트 집합으로 데이터 분할][6]
3. **2클래스 향상된 의사 결정 트리** 모듈을 캔버스로 끌어서 놓습니다.
4. 끌기 합니다 **모델 학습** 모듈을 캔버스로 연결 하 여 입력을 지정 하 고는 **2 클래스 승격 된 의사 결정 트리** (ML 알고리즘) 및 **분할** (학습 데이터를 알고리즘) 모듈입니다. 
     ![모델 학습 모듈 연결][7]
5. 그런 다음 속성 창에서 **열 선택기 시작** 을 클릭합니다. **BikeBuyer** 열을 예측할 열로 선택합니다.
   ![예측할 열 선택][8]

## <a name="4-score-the-model"></a>4. 모델 점수 매기기
이제 모델이 테스트 데이터를 수행하는 방법을 테스트합니다. 더 잘 수행하는 알고리즘을 확인하도록 다른 알고리즘을 선택하여 비교합니다.

1. 끌기 **모델 점수 매기기** 모듈을 캔버스로 연결 **모델 학습** 하 고 **분할 데이터** 모듈입니다.
   ![모델 점수 매기기][9]
2. **2클래스 Bayes 지점 컴퓨터** 를 실험 캔버스로 끌어서 놓습니다. 2클래스 향상된 의사 결정 트리와 비교하여 이 알고리즘이 수행하는 방법을 비교합니다.
3. 캔버스에서 모델 학습 및 모델 점수 매기기 모듈을 복사하고 붙여 넣습니다.
4. **모델 평가** 모듈을 캔버스로 끌어서 놓아 두 알고리즘을 비교합니다.
5. **실행** 합니다.
   ![실험 실행][10]
6. 모델 평가 모듈의 아래쪽에서 출력 포트를 클릭하고 시각화를 클릭합니다.
   ![평가 결과 시각화][11]

제공된 메트릭은 ROC 곡선, 정밀도-리콜 다이어그램 및 리프트 곡선입니다. 이러한 메트릭을 살펴보면 첫 번째 모델이 두 번째보다 더 잘 실행하는 것을 볼 수 있습니다. 첫 번째 모델이 예측하는 것을 보려면 모델 점수 매기기의 출력 포트를 클릭하고 시각화를 클릭합니다.
![점수 결과 시각화][12]

테스트 데이터 세트에 두 개의 열이 추가됩니다.

* 점수가 매겨진 확률: 고객이 자전거 구매자일 가능성입니다.
* 점수가 매겨진 레이블: 모델에 의해 분류가 실행되었습니다. – 자전거 구매자(1) 혹은 아님(0) 레이블 지정에 대한 확률 임계값은 50%로 설정되고 조정할 수 있습니다.

점수가 매겨진 레이블(예측)로 열 BikeBuyer(실제) 비교를 통해 모델이 얼마나 잘 실행했는지 확인할 수 있습니다. 다음 단계로 이 모델을 사용하여 새 고객에 대한 예측을 수행하고 이 모델을 웹 서비스로 게시하거나 SQL Data Warehouse에 결과를 다시 작성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
예측 Machine Learning 모델을 구축하는 방법에 대한 자세한 내용은 [Azure의 Machine Learning 소개][Introduction to Machine Learning on Azure]를 참고하세요.

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
