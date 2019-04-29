---
title: 다양한 원본에서 데이터 가져오기
titleSuffix: Azure Machine Learning Studio
description: 다양한 데이터 원본에서 Azure Machine Learning Studio로 데이터를 가져오는 방법. 지원되는 데이터 형식에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 41cc1d6638871f26ae942e724a402e17f52150fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811036"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>다양한 데이터 원본에서 Azure Machine Learning Studio로 학습 데이터를 가져오기

다음 데이터를 사용하면 Machine Learning Studio에 사용자 고유의 데이터를 사용하여 예측 분석 솔루션을 개발 및 학습할 수 있습니다. 

* **로컬 파일** - 하드 드라이브에서 로컬 데이터를 미리 로드하여 사용자의 작업 영역에 데이터 세트 모듈을 만듭니다.
* **온라인 데이터 원본** - [데이터 가져오기][import-data] 모듈을 사용하여 실험을 실행하는 동안 여러 온라인 원본 중 하나에서 데이터에 액세스합니다.
* **Machine Learning Studio 실험** - Machine Learning Studio에서 데이터 세트로 저장된 데이터를 사용합니다.
* [**온-프레미스 SQL Server 데이터베이스**](use-data-from-an-on-premises-sql-server.md) - 수동으로 데이터를 복사하지 않고도 온-프레미스 SQL Server 데이터베이스의 데이터를 사용합니다.

> [!NOTE]
> Machine Learning Studio에는 학습 데이터로 사용할 수 있는 샘플 데이터 세트가 많이 있습니다. 이에 대한 자세한 내용은 [Azure Machine Learning Studio의 샘플 데이터 세트 사용](use-sample-datasets.md)을 참조하세요.

## <a name="prepare-data"></a>데이터 준비

Machine Learning Studio는 데이터베이스에서 구분되었거나 구조화된 데이터인 텍스트 데이터 등의 직사각형 또는 테이블 형식 데이터에 대해 작업하도록 설계되어 있습니다. 단, 경우에 따라 직사각형이 아닌 데이터도 사용할 수 있습니다.

Studio로 가져오기 전에 데이터가 비교적 정리되어 있는 것이 좋습니다. 예를 들어 따옴표가 없는 문자열과 같은 문제를 처리할 수 있습니다.

그러나 Studio에는 데이터를 가져온 후 실험에서 데이터를 조작할 수 있는 모듈도 있습니다. 사용할 기계 학습 알고리즘에 따라 누락된 값 및 스파스 데이터와 같은 데이터 구조 문제 해결 방법을 결정해야 하며, 이때 도움을 줄 수 있는 모듈이 있습니다. 모듈 팔레트의 **데이터 변환** 섹션에서 이러한 함수를 수행하는 모듈을 찾습니다.

실험을 수행하는 동안 언제든지 출력 포트를 클릭하여 모듈에서 생성한 데이터를 보거나 다운로드할 수 있습니다. 모듈에 따라 서로 다른 다운로드 옵션을 사용할 수 있습니다. 또는 Studio의 웹 브라우저 내에서 데이터를 시각화할 수도 있습니다.

## <a name="supported-data-formats-and-data-types"></a>지원되는 데이터 형식 및 데이터 유형

데이터를 가져오는 데 사용하는 메커니즘과 데이터의 출처에 따라 실험에 여러 데이터 형식을 가져올 수 있습니다.

* 일반 텍스트(.txt)
* 헤더가 있거나(.csv) 없는(.nh.csv) 쉼표로 구분된 값(CSV)
* 헤더가 있거나(.tsv) 없는(.nh.tsv) 탭으로 구분된 값(TSV)
* Excel 파일
* Azure 테이블
* Hive 테이블
* SQL 데이터베이스 테이블
* OData 값
* SVMLight 데이터(.svmlight)(형식 정보는 [SVMLight 정의](http://svmlight.joachims.org/) 참조)
* 특성 관계 파일 형식(ARFF) 데이터(.arff)(형식 정보는 [ARFF 정의](https://weka.wikispaces.com/ARFF) 참조)
* Zip 파일(.zip)
* R 개체 또는 작업 영역 파일(. RData)

메타데이터를 포함하는 ARFF와 같은 형식으로 데이터를 가져오는 경우, Studio에서는 이 메타데이터를 사용하여 각 열의 머리글과 데이터 형식을 정의합니다.

이 메타데이터를 포함하지 않는 TSV 또는 CSV 형식과 같은 데이터를 가져오는 경우, Studio에서는 데이터를 샘플링하여 각 열의 데이터 형식을 유추합니다. 데이터에 열 머리글이 없는 경우 Studio에서 기본 이름을 제공합니다.

[메타데이터 편집][edit-metadata] 모듈을 사용하여 열의 머리글과 데이터 유형을 명시적으로 지정하거나 변경할 수 있습니다.

Studio에서 인식하는 데이터 유형은 다음과 같습니다.

* String
* 정수 
* double
* Boolean
* DateTime
* TimeSpan

Studio에서는 ***데이터 테이블***이라는 내부 데이터 형식을 사용하여 모듈 간에 데이터를 전달합니다. [데이터 세트로 변환][convert-to-dataset] 모듈을 사용하여 명시적으로 데이터를 데이터 테이블 형식으로 변환할 수 있습니다.

데이터 테이블 이외의 형식을 허용하는 모든 모듈에서는 다음 모듈에 데이터를 전달하기 전에 데이터 테이블로 자동 변환합니다.

필요한 경우 다른 변환 모듈을 사용하여 데이터 테이블 형식을 다시 CSV, TSV, ARFF 또는 SVMLight 형식으로 변환할 수 있습니다.
모듈 팔레트의 **데이터 형식 변환** 섹션에서 이러한 함수를 수행하는 모듈을 찾습니다.

## <a name="data-capacities"></a>데이터 용량

Machine Learning Studio의 모듈은 일반적인 사용 사례의 경우 최대 10GB 숫자 데이터의 데이터 세트를 지원합니다. 모듈에서 둘 이상의 입력을 사용하는 경우에는 모든 입력 크기의 합계 값이 10GB입니다. Hive 또는 Azure SQL Database 쿼리를 사용하여 더 큰 데이터 세트를 샘플링하거나, 데이터를 가져오기 전에 Learning by Counts 전처리를 사용할 수 있습니다.  

다음 데이터 형식은 기능 정규화 중에 확장할 수 있으며 10GB 미만으로 제한됩니다.

* 스파스
* 범주
* 문자열
* 이진 데이터

다음 모듈은 10GB 미만의 데이터 세트로 제한됩니다.

* Recommender 모듈
* SMOTE(Synthetic Minority Oversampling Technique) 모듈
* 스크립팅 모듈: R, Python, SQL
* 출력 데이터 크기가 입력 데이터 크기보다 클 수 있는 모듈(예: Join 또는 Feature Hashing)
* Cross-validation, Tune Model Hyperparameters, Ordinal Regression 및 One-vs-All Multiclass(반복 횟수가 매우 많은 경우)

몇 GB보다 큰 데이터 세트의 경우 로컬 파일에서 직접 업로드하지 않고 Azure Storage 또는 Azure SQL Database에 데이터를 업로드하거나 Azure HDInsight를 사용합니다.

[이미지 가져오기](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) 모듈 참조에서 이미지 데이터에 대한 정보를 확인할 수 있습니다.

## <a name="import-from-a-local-file"></a>로컬 파일에서 가져오기

Studio에서 학습 데이터로 사용하도록 하드 드라이브의 데이터 파일을 업로드할 수 있습니다. 데이터 파일을 가져올 때 작업 영역의 실험에 사용할 수 있도록 데이터 세트 모듈을 만들 수 있습니다.

로컬 하드 드라이브에서 데이터를 가져오려면 다음을 수행합니다.

1. Studio 창 하단에 있는 **+새로 만들기**를 클릭합니다.
2. **데이터 세트** 및 **로컬 파일에서**를 선택합니다.
3. **새 데이터 세트 업로드** 대화 상자에서 업로드할 파일을 찾아봅니다.
4. 이름을 입력하고, 데이터 형식을 식별하며, 선택적으로 설명을 입력합니다. 설명을 사용하면 나중에 데이터를 사용할 때 기억하려는 데이터에 대한 특성을 기록할 수 있으므로 좋습니다.
5. **기존 데이터 세트의 새로운 버전** 확인란을 사용하면 새 데이터로 기존 데이터 세트를 업데이트할 수 있습니다. 이렇게 하려면 이 확인란을 클릭한 다음, 기존 데이터 세트의 이름을 입력합니다.

![새 데이터 세트 업로드](./media/import-data/upload-dataset-from-local-file.png)

업로드 시간은 데이터의 크기와 서비스에 대한 연결 속도에 따라 달라집니다. 파일이 오래 걸릴 것 같다면 기다리는 동안 Studio에서 다른 작업을 수행할 수 있습니다. 그러나 데이터 업로드가 완료되기 전에 브라우저를 닫으면 업로드에 실패하게 됩니다.

데이터가 업로드되면 데이터 세트 모듈에 저장되고 작업 영역의 모든 실험에서 사용할 수 있습니다.

실험을 편집할 때 모듈 팔레트에 있는 **저장된 데이터 세트** 목록의 **내 데이터 세트** 목록에서 업로드한 데이터 세트를 찾을 수 있습니다. 추가 분석 및 기계 학습을 위해 데이터 세트를 사용하려고 할 때 데이터 세트를 실험 캔버스에 끌어서 놓을 수 있습니다.

## <a name="import-from-online-data-sources"></a>온라인 데이터 원본 가져오기

[데이터 가져오기][import-data] 모듈을 사용하여 실험을 실행하는 동안 여러 온라인 데이터 원본에서 데이터를 가져올 수 있습니다.

> [!NOTE]
> 이 문서에서는 [데이터 가져오기][import-data] 모듈에 대한 일반 정보를 제공합니다. 액세스할 수 있는 데이터 형식, 형식, 매개변수 및 일반 질문의 응답에 대한 자세한 내용은 [데이터 가져오기][import-data] 모듈의 모듈 참조 항목을 참조하세요.

[데이터 가져오기][import-data] 모듈을 사용하여 실험을 실행하는 동안 여러 온라인 데이터 원본 중 하나에서 데이터에 액세스할 수 있습니다.

* HTTP을 사용하는 웹 URL
* HiveQL을 사용하는 Hadoop
* Azure Blob Storage
* Azure 테이블
* Azure SQL 데이터베이스 또는 Azure VM의 SQL Server
* 온-프레미스 SQL Server 데이터베이스
* 데이터 피드 공급자, OData 현재
* Azure Cosmos DB

실험이 실행 중인 동안 이 학습 데이터에 액세스하므로 데이터는 해당 실험에서만 사용할 수 있습니다. 그에 비해 데이터 세트 모듈에 저장된 데이터는 작업 영역의 아무 실험에나 사용할 수 있습니다.

Studio 실험에서 온라인 데이터 원본에 액세스하려면 [데이터 가져오기][import-data] 모듈을 실험에 추가합니다. 그런 다음, 단계별 안내 지침서의 **속성**에서 **데이터 가져오기 마법사 시작**을 선택하여 데이터 원본을 선택하고 구성합니다. 또는 **속성**에서 **데이터 원본**을 수동으로 선택하여 데이터에 액세스하는 데 필요한 매개 변수를 제공할 수 있습니다.

지원되는 온라인 데이터 원본을 아래 표에 항목별로 나타냈습니다. 또한 이 테이블은 지원되는 파일 형식 및 데이터에 액세스하는 데 사용되는 매개 변수도 요약합니다.

> [!IMPORTANT]
> 현재 [데이터 가져오기][import-data] 및 [데이터 내보내기][export-data] 모듈은 클래식 배포 모델을 사용하여 만든 Azure Storage의 데이터를 읽고 쓸 수 있습니다. 다시 말해, 핫 스토리지 액세스 계층 또는 쿨 스토리지 액세스 계층을 제공하는 새로운 Azure Blob Storage 계정 유형은 아직 지원되지 않습니다.
>
> 일반적으로 이 서비스 옵션이 제공되기 전에 만들었을 수 있는 모든 Azure 저장소 계정은 영향을 받지 않습니다.
> 새 계정을 만들려면 배포 모델에 대해 **클래식**을 선택하거나 Resource Manager를 사용하고 **계정 종류**에는 **Blob Storage** 대신 **범용**을 선택합니다.
>
> 자세한 내용은 [Azure Blob Storage: 핫 및 쿨 스토리지 계층](../../storage/blobs/storage-blob-storage-tiers.md)을 참조하세요.

### <a name="supported-online-data-sources"></a>지원되는 온라인 데이터 원본
Azure Machine Learning Studio **데이터 가져오기** 모듈은 다음과 같은 데이터 원본을 지원합니다.

| 데이터 원본 | 설명 | 매개 변수 |
| --- | --- | --- |
| HTTP를 통한 웹 URL |HTTP를 사용하는 어떤 웹 URL에서도 데이터를 쉼표로 구분된 값(CSV), 탭으로 구분된 값(TSV), 특성-관계 파일 형식(ARFF) 및 지원 벡터 컴퓨터(SVM-light) 형식으로 읽습니다. |<b>URL</b>: 사이트 URL과 파일 이름을 포함한 파일의 전체 이름을 확장명과 함께 지정합니다. <br/><br/><b>데이터 형식</b>: 지원되는 데이터 형식(CSV, TSV, ARFF 또는 SVM-light) 중 하나를 지정합니다. 데이터에 머리글 행이 있는 경우 해당 행을 사용하여 열 이름을 할당합니다. |
| Hadoop/HDFS |Hadoop의 분산 저장소에서 데이터를 읽습니다. SQL과 유사한 쿼리 언어인 HiveQL을 사용하여 원하는 데이터를 지정합니다. 또한 HiveQL을 사용하여 Studio에 데이터를 추가하기 전에 데이터를 집계하고 데이터 필터링을 수행할 수 있습니다. |<b>Hive 데이터베이스 쿼리</b>: 데이터 생성에 사용되는 Hive 쿼리를 지정합니다.<br/><br/><b>HCatalog 서버 URI</b>: *&lt;클러스터 이름&gt;.azurehdinsight.net* 형식을 사용하여 클러스터 이름을 지정합니다.<br/><br/><b>Hadoop 사용자 계정 이름</b>: 클러스터 프로비전에 사용되는 Hadoop 사용자 계정 이름을 지정합니다.<br/><br/><b>Hadoop 사용자 계정 암호</b>: 클러스터를 프로비전할 때 사용되는 자격 증명을 지정합니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)를 참조하세요.<br/><br/><b>출력 데이터 위치</b>: 데이터를 Hadoop 분산 파일 시스템(HDFS) 또는 Azure 중 어디에 저장할지 지정합니다. <br/><ul>출력 데이터를 HDFS에 저장하는 경우 HDFS 서버 URI를 지정합니다. (HHDInsight 클러스터 이름을 TTPS:// 접두사 없이 사용해야 합니다.) <br/><br/>Azure에 출력 데이터를 저장하는 경우 Azure Storage 계정 이름, Storage 액세스 키 및 Storage 컨테이너 이름을 지정해야 합니다.</ul> |
| SQL 데이터베이스 |Azure 가상 머신에서 실행되는 SQL Server 데이터베이스 또는 Azure SQL 데이터베이스에 저장된 데이터를 읽습니다. |<b>데이터베이스 서버 이름</b>: 데이터베이스가 실행되는 서버의 이름을 지정합니다.<br/><ul>Azure SQL Database인 경우 생성된 서비스 이름을 입력합니다. 일반적인 양식은 *&lt;generated_identifier&gt;.database.windows.net*입니다. <br/><br/>Azure 가상 머신에서 호스팅되는 SQL server의 경우 입력 *tcp:&lt;가상 컴퓨터 DNS 이름이&gt;, 1433*</ul><br/><b>데이터베이스 이름</b>: 서버의 데이터베이스 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 이름</b>: 데이터베이스에 대한 액세스 권한이 있는 계정의 사용자 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 암호</b>: 사용자 계정의 암호를 지정합니다.<br/><br/><b>데이터베이스 쿼리</b>: 읽을 데이터를 설명하는 SQL 문을 입력합니다. |
| 온-프레미스 SQL 데이터베이스 |온-프레미스 SQL 데이터베이스에 저장된 데이터를 읽습니다. |<b>데이터 게이트웨이</b>: SQL Server 데이터베이스에 액세스할 수 있는 컴퓨터에 설치된 데이터 관리 게이트웨이의 이름을 지정합니다. 게이트웨이 설정에 대한 자세한 내용은 [온-프레미스 SQL Server 데이터베이스의 데이터를 사용하여 Azure Machine Learning Studio를 통해 고급 분석 수행](use-data-from-an-on-premises-sql-server.md)을 참조하세요.<br/><br/><b>데이터베이스 서버 이름</b>: 데이터베이스가 실행되는 서버의 이름을 지정합니다.<br/><br/><b>데이터베이스 이름</b>: 서버의 데이터베이스 이름을 지정합니다. <br/><br/><b>서버 사용자 계정 이름</b>: 데이터베이스에 대한 액세스 권한이 있는 계정의 사용자 이름을 지정합니다. <br/><br/><b>사용자 이름 및 암호</b>: <b>값 입력</b>을 클릭하여 데이터베이스 자격 증명을 입력합니다. 온-프레미스 SQL Server가 구성된 방식에 따라 Windows 통합 인증 또는 SQL Server 인증을 사용할 수 있습니다.<br/><br/><b>데이터베이스 쿼리</b>: 읽을 데이터를 설명하는 SQL 문을 입력합니다. |
| Azure 테이블 |Azure Storage의 Table service에서 데이터를 읽습니다.<br/><br/>대량의 데이터를 가끔 읽는 경우 Azure 테이블 서비스를 사용해 보세요. 이는 유연하고, 비관계형(NoSQL)이고, 확장성이 매우 뛰어나고, 저렴하고, 가용성이 높은 저장소 솔루션을 제공합니다. |**데이터 가져오기**의 옵션은 공용 정보에 액세스하는지 로그인 자격 증명이 필요한 개인 저장소 계정에 액세스하는지에 따라 달라집니다. 이는 각각 자체의 매개 변수 집합을 가지고 있는 "PublicOrSAS" 또는 "Account"의 값을 가질 수 있는 <b>인증 유형</b>에 의해 결정됩니다. <br/><br/><b>공용 또는 SAS(공유 액세스 서명) URI</b>: 매개 변수는 다음과 같습니다.<br/><br/><ul><b>테이블 URI</b>: 테이블에 대한 공용 또는 SAS URL을 지정합니다.<br/><br/><b>속성 이름에 대해 검색할 행 지정</b>: 지정된 행 수를 검색하려면 값이 <i>TopN</i>이고 테이블의 모든 행을 가려오려면 값이 <i>ScanAll</i>입니다. <br/><br/>데이터가 같은 유형이고 예측이 가능한 경우 *TopN*을 선택하고 N에 해당하는 숫자를 입력하는 것이 좋습니다. 테이블이 큰 경우 이렇게 하면 읽기 시간이 단축될 수 있습니다.<br/><br/>데이터가 테이블의 깊이와 위치에 따라 달라지는 속성 집합으로 구조화되어 있는 경우 *ScanAll* 옵션을 선택하여 모든 행을 검색합니다. 이렇게 하면 결과로 얻는 속성과 메타데이터 변환의 무결성이 확보됩니다.<br/><br/></ul><b>개인 스토리지 계정</b>: 매개 변수는 다음과 같습니다. <br/><br/><ul><b>계정 이름</b>: 읽을 테이블을 포함하는 계정의 이름을 지정합니다.<br/><br/><b>계정 키</b>: 계정과 연결된 스토리지 키를 지정합니다.<br/><br/><b>테이블 이름</b>: 읽을 데이터를 포함하는 테이블의 이름을 지정합니다.<br/><br/><b>속성 이름에 대해 검색할 행</b>: 지정된 행 수를 검색하려면 값이 <i>TopN</i>이고 테이블의 모든 행을 가려오려면 값이 <i>ScanAll</i>입니다.<br/><br/>데이터가 같은 유형이고 예측이 가능한 경우 *TopN*을 선택하고 N에 해당하는 숫자를 입력하는 것이 좋습니다. 테이블이 큰 경우 이렇게 하면 읽기 시간이 단축될 수 있습니다.<br/><br/>데이터가 테이블의 깊이와 위치에 따라 달라지는 속성 집합으로 구조화되어 있는 경우 *ScanAll* 옵션을 선택하여 모든 행을 검색합니다. 이렇게 하면 결과로 얻는 속성과 메타데이터 변환의 무결성이 확보됩니다.<br/><br/> |
| Azure Blob Storage |Azure Storage의 Blob service에 저장된 이미지, 구조화되지 않은 텍스트 또는 이진 데이터 등의 데이터를 읽습니다.<br/><br/>또한 Blob service를 사용하여 데이터를 공용으로 표시하거나 애플리케이션 데이터를 개인적으로 저장할 수 있습니다. HTTP 또는 HTTPS 연결을 사용하여 어디서나 데이터에 액세스할 수 있습니다. |**데이터 가져오기** 모듈의 옵션은 공용 정보에 액세스하는지 로그인 자격 증명이 필요한 개인 저장소 계정에 액세스하는지에 따라 달라집니다. 이것은 <b>인증 유형</b>에 따라 결정되며 "PublicOrSAS" 또는 "Account" 중 하나의 값을 가질 수 있습니다.<br/><br/><b>공용 또는 SAS(공유 액세스 서명) URI</b>: 매개 변수는 다음과 같습니다.<br/><br/><ul><b>URI</b>: Storage Blob에 대한 공용 또는 SAS URL을 지정합니다.<br/><br/><b>파일 형식</b>: Blob service의 데이터 형식을 지정합니다. 지원되는 형식은 CSV, TSV 및 ARFF입니다.<br/><br/></ul><b>개인 스토리지 계정</b>: 매개 변수는 다음과 같습니다. <br/><br/><ul><b>계정 이름</b>: 읽으려는 Blob을 포함하는 계정의 이름을 지정합니다.<br/><br/><b>계정 키</b>: 계정과 연결된 스토리지 키를 지정합니다.<br/><br/><b>컨테이너, 디렉터리 또는 Blob 경로</b>: 읽을 데이터를 포함하는 Blob의 이름을 지정합니다.<br/><br/><b>Blob 파일 형식</b>: Blob service의 데이터 형식을 지정합니다. 지원되는 데이터 형식은 CSV, TSV, ARFF, 지정된 인코딩의 CSV 및 Excel입니다. <br/><br/><ul>형식이 CSV 또는 TSV인 경우 파일에 머리글 행이 포함되는지 여부를 나타내야 합니다.<br/><br/>Excel 옵션을 사용하여 Excel 통합 문서에서 데이터를 읽을 수 있습니다. <i>Excel 데이터 형식</i> 옵션에서는 데이터가 Excel 워크시트 범위에 있는지 아니면 Excel 테이블에 있는지 여하를 나타냅니다. <i>Excel 시트 또는 포함된 테이블</i> 옵션에서는 읽을 시트 또는 테이블의 이름을 지정합니다.</ul><br/> |
| 데이터 피드 공급자 |지원되는 피드 공급자에서 데이터를 읽습니다. 현재 ODP(Open Data Protocol) 형식만 지원됩니다. |<b>데이터 콘텐츠 형식</b>: OData 형식을 지정합니다.<br/><br/><b>원본 URL</b>: 데이터 피드에 대한 전체 URL을 지정합니다. <br/>예를 들어 다음 URL은 Northwind 샘플 데이터베이스에서 읽습니다. https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>다른 실험에서 가져오기

한 실험의 중간 결과를 다른 실험의 일부로 사용하려는 경우가 있습니다. 이렇게 하려면 모듈을 데이터 세트로 저장합니다.

1. 데이터 세트로 저장할 모듈의 출력을 클릭합니다.
2. **데이터 세트로 저장**을 클릭합니다.
3. 메시지가 표시되면 데이터 세트를 쉽게 식별할 수 있는 이름과 설명을 입력합니다.
4. **확인** 확인 표시를 클릭합니다.

저장이 완료되면 작업 영역의 모든 실험에서 데이터 세트를 사용할 수 있습니다. 모듈 팔레트의 **저장된 데이터 세트** 목록에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[데이터 가져오기 및 데이터 내보내기 모듈을 사용하는 Azure Machine Learning Studio 웹 서비스 배포](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
