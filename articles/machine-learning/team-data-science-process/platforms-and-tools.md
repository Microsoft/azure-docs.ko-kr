---
title: 데이터 과학 프로젝트에 대한 플랫폼 및 도구 - Team Data Science Process
description: Team Data Science Process를 표준화하는 기업에서 사용할 수 있는 데이터 및 분석 리소스를 항목별로 정리하고 설명합니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 31c6c1258b6d20eb2d2802cf23244cb59df682c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61428731"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>데이터 과학 프로젝트용 플랫폼 및 도구

Microsoft는 클라우드 또는 온 프레미스 플랫폼 모두에 대해 광범위한 데이터 및 분석 서비스와 리소스를 제공합니다. 데이터 과학 프로젝트의 실행이 효율적이고 확장 가능하게 되도록 배포할 수 있습니다. 추적 가능하고, 버전을 제어하고, 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 구현하는 팀에 대한 지침은 [TDSP(Team Data Science Process)](overview.md)에서 제공됩니다.  이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

TDSP를 사용하는 데이터 과학 팀에서 사용할 수 있는 데이터 및 분석 서비스는 다음과 같습니다.

- 데이터 과학 Virtual Machine(Windows 및 Linux CentOS)
- HDInsight Spark 클러스터
- SQL Data Warehouse
- Azure Data Lake
- HDInsight Hive 클러스터
- Azure File Storage
- SQL Server 2016 R Services

이 문서에서는 리소스를 간략히 설명하고 TDSP 팀에서 게시한 자습서 및 연습에 대한 링크를 제공합니다. 단계별로 사용하는 방법을 배우고, 지능형 애플리케이션을 빌드하는 데 사용할 수 있습니다. 이러한 리소스에 대한 자세한 내용은 해당 제품 페이지에서 확인할 수 있습니다. 

## <a name="data-science-virtual-machine-dsvm"></a>DSVM(데이터 과학 Virtual Machine)

Microsoft에서 Windows 및 Linux 모두에 제공하는 데이터 과학 가상 머신에는 데이터 과학 모델링 및 개발 작업을 위한 인기 있는 도구가 포함되어 있습니다. 이러한 도구는 다음과 같습니다.

- Microsoft R Server Developer Edition 
- Enthought Python 배포
- Python 및 R용 Jupyter 노트북 
- Windows의 Python 및 R 도구와 Linux의 Eclipse 도구가 있는 Visual Studio Community Edition
- Windows용 Power BI Desktop
- Windows의 SQL Server 2016 Developer Edition 및 Linux의 Postgres

또한 **ML 및 AI 도구** xgboost, mxnet 및 Vowpal Wabbit 등입니다.

현재 DSVM은 **Windows** 및 **Linux CentOS** 운영 체제에서 사용할 수 있습니다. 실행하려는 데이터 과학 프로젝트의 요구 사항에 따라 DSVM의 크기(CPU 코어 수 및 메모리 양)를 선택합니다. 

DSVM의 Windows 버전에 대한 자세한 내용은 Azure 마켓플레이스의 [Microsoft 데이터 과학 Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)을 참조하세요. Linux DSVM 버전의 경우 [Linux 데이터 과학 Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)을 참조하세요.

DSVM에서 일반적인 데이터 과학 작업 중 일부를 효율적으로 수행하는 방법을 알아보려면 [데이터 과학 Virtual Machine에서 수행할 수 있는 10가지 작업](../data-science-virtual-machine/vm-do-ten-things.md)을 참조하세요.


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark 클러스터

Apache Spark는 메모리 내 처리를 지원하여 빅데이터 분석 애플리케이션의 성능을 향상시키는 오픈 소스 병렬 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark의 메모리 내 계산 기능은 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다. 또한 Spark는 Azure Blob Storage(WASB)와 호환되므로 Azure에 저장된 기존 데이터를 Spark를 통해 쉽게 처리할 수 있습니다.

HDInsight에서 Spark 클러스터를 만들 때는 설치 및 구성된 Spark로 Azure 계산 리소스를 만듭니다. HDInsight에서 Spark 클러스터를 만드는 데 약 10분 정도 걸립니다. 처리되는 데이터는 Azure Blob Storage에 저장합니다. 클러스터에서 Azure Blob Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop으로 HDFS 호환 Azure Blob 스토리지 사용](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

Microsoft의 TDSP 팀은 Azure HDInsight Spark Clusters를 사용하여 Python과 다른 Scala를 사용하여 데이터 과학 솔루션을 구축하는 방법에 대한 두 가지 포괄적인 연습을 게시했습니다. Azure HDInsight **Spark 클러스터**에 대한 자세한 내용은 [개요: HDInsight Linux의 Apache Spark](../../hdinsight/spark/apache-spark-overview.md)를 참조하세요. Azure HDInsight Spark 클러스터에서 **Python**을 사용하여 데이터 과학 솔루션을 빌드하는 방법은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](spark-overview.md)를 참조하세요. Azure HDInsight Spark 클러스터에서 **Scala**를 사용하여 데이터 과학 솔루션을 빌드하는 방법은 [Azure에서 Scala 및 Spark를 사용하는 데이터 과학](scala-walkthrough.md)을 참조하세요. 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse를 사용하면 과도하게 프로비전하거나 요금을 과도하게 지급하지 않고 계산 리소스를 몇 초 만에 쉽게 크기 조정할 수 있습니다. 또한 계산 리소스의 사용을 일시 중지하는 고유한 옵션을 제공하므로 클라우드 비용을 자유롭게 더 효율적으로 관리할 수 있습니다. 확장 가능한 계산 리소스를 배포하는 기능을 사용하면 모든 데이터를 Azure SQL Data Warehouse로 가져올 수 있습니다. 스토리지 비용은 최소화되며, 분석하려는 데이터 세트 부분에 대해서만 계산을 실행할 수 있습니다. 

Azure SQL Data Warehouse에 대한 자세한 내용은 [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) 웹 사이트를 참조하세요. SQL Data Warehouse를 사용하여 종단 간 고급 분석 솔루션을 빌드하는 방법은 [실행 중인 Team Data Science Process: SQL Data Warehouse 사용](sqldw-walkthrough.md)을 참조하세요.


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake는 공식 요구 사항이나 스키마가 적용되기 전에 모든 형식의 데이터를 단일 위치에 수집하는 엔터프라이즈 수준의 리포지토리입니다. 이러한 유연성을 통해 크기, 구조 또는 수집 속도에 관계 없이 모든 형식의 데이터를 데이터 레이크에 보관할 수 있습니다. 그런 다음 조직에서는 Hadoop 또는 고급 분석을 사용하여 이러한 데이터 레이크에서 패턴을 찾을 수 있습니다. 또한 데이터 레이크는 데이터를 큐레이팅하고 데이터 웨어하우스로 이동하기 전에 저렴한 비용으로 데이터를 준비하기 위한 리포지토리 역할을 수행할 수 있습니다.

Azure Data Lake에 대한 자세한 내용은 [Azure Data Lake 소개](https://azure.microsoft.com/blog/introducing-azure-data-lake/)를 참조하세요. Azure Data Lake를 사용하여 확장 가능한 엔드투엔드 데이터 과학 솔루션을 빌드하는 방법은 [Azure Data Lake의 확장 가능한 데이터 과학: 엔드투엔드 연습](data-lake-walkthrough.md)을 참조하세요.


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive(Hadoop) 클러스터

Apache Hive는 HiveQL(SQL과 유사한 쿼리 언어)을 사용하여 데이터를 요약, 쿼리 및 분석할 수 있게 하는 Hadoop용 데이터 웨어하우스 시스템입니다. 쌍방향으로 데이터를 검토하거나 다시 사용할 수 있는 일괄 처리 작업을 만드는 데 하이브를 사용할 수 있습니다.

Hive를 사용하면 크게 구조가 없는 데이터에 구조를 투영할 수 있습니다. 구조를 정의한 후에 Java 또는 MapReduce를 사용하거나 알 필요 없이 Hive를 사용하여 Hadoop 클러스터에서 해당 데이터를 쿼리할 수 있습니다. HiveQL(Hive 쿼리 언어)을 사용하면 T-SQL과 비슷한 문이 포함된 쿼리를 작성할 수 있습니다.

데이터 과학자를 위해 Hive는 Hive 쿼리에서 Python UDF(사용자 정의 함수)를 실행하여 레코드를 처리할 수 있습니다. 이 기능은 데이터 분석에서 Hive 쿼리의 기능을 상당히 확장합니다. 특히 데이터 과학자들이 SQL과 비슷한 HiveQL 및 Python과 같이 친숙한 언어로 확장 가능한 기능 엔지니어링을 수행할 수 있습니다. 

Azure HDInsight Hive 클러스터에 대한 자세한 내용은 [HDInsight의 Hadoop에서 Hive 및 HiveQL 사용](../../hdinsight/hadoop/hdinsight-use-hive.md)을 참조하세요. Azure HDInsight Hive 클러스터를 사용하여 확장 가능한 종단 간 데이터 과학 솔루션을 빌드하는 방법은 [실행 중인 Team Data Science Process: Azure HDInsight Hadoop 클러스터 사용](hive-walkthrough.md)을 참조하세요.


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage는 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 클라우드에서 파일 공유를 제공하는 서비스입니다. SMB 2.1과 SMB 3.0 모두를 지원합니다. Azure File Storage를 사용하여 파일 공유에 의존하는 레거시 응용 프로그램을 비경제적인 다시 쓰기 작업 없이 빠르게 Azure로 마이그레이션할 수 있습니다. Azure 가상 머신 또는 클라우드 서비스 또는 온-프레미스 클라이언트에서 실행되는 애플리케이션은 데스크톱 애플리케이션이 일반적인 SMB 공유를 탑재하는 것처럼 클라우드에 파일 공유를 탑재할 수 있습니다. File Storage 공유를 동시에 탑재하고 액세스할 수 있는 응용 프로그램 구성 요소 수에는 제한이 없습니다.

Azure 파일 저장소를 프로젝트 팀 구성원과 프로젝트 데이터를 공유할 수 있는 장소로 만드는 기능은 특히 데이터 과학 프로젝트에 유용합니다. 그러면 각 구성원이 Azure File Storage에 있는 동일한 데이터 복사본에 액세스할 수 있습니다. 또한 이 파일 저장소를 사용하여 프로젝트 실행 중에 생성된 기능 집합을 공유할 수도 있습니다. 클라이언트가 참여하는 프로젝트인 경우 클라이언트에서 자체의 Azure 구독으로 Azure File Storage를 만들어 프로젝트 데이터와 기능을 공유할 수 있습니다. 클라이언트에는 이러한 방식으로 프로젝트 데이터 자산에 대한 모든 권한이 있게 됩니다. Azure File Storage에 대한 자세한 내용은 [Windows에서 Azure File Storage 시작](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) 또는 [Linux에서 Azure File Storage를 사용하는 방법](../../storage/files/storage-how-to-use-files-linux.md)을 참조하세요.


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services(In-Database)는 새 정보를 발견할 수 있는 지능형 애플리케이션을 개발하고 배포하기 위한 플랫폼을 제공합니다. R 커뮤니티에서 제공하는 많은 패키지를 포함하여 풍부하고 강력한 R 언어를 사용하여 SQL Server 데이터에서 모델을 만들고 예측을 생성할 수 있습니다. R Services(In-Database)는 R 언어를 SQL Server와 통합하므로 분석이 데이터와 가깝게 유지되며, 이에 따라 데이터 이동과 관련된 비용 및 보안 위험이 제거됩니다.

R Services(In-Database)는 포괄적인 SQL Server 도구 및 기술 집합을 통해 오픈 소스 R 언어를 지원합니다. 탁월한 성능, 보안, 안정성 및 관리 효율성을 제공합니다. 편리하고 친숙한 도구를 사용하여 R 솔루션을 배포할 수 있습니다. 프로덕션 애플리케이션에서 R 런타임을 호출하고 Transact-SQL을 사용하여 예측 및 시각적 개체를 검색할 수 있습니다. 또한 ScaleR 라이브러리를 사용하여 R 솔루션의 규모와 성능을 향상시킵니다. 자세한 내용은 [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

Microsoft의 TDSP 팀은 SQL Server 2016 R Services에서 R 프로그래머용 및 SQL 개발자용 데이터 과학 솔루션을 빌드하는 방법을 보여 주는 두 가지 종단 간 연습을 게시했습니다. **R 프로그래머**의 경우 [데이터 과학 종단 간 연습](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)을 참조하세요. **SQL 개발자**의 경우 [SQL 개발자용 In-Database 고급 분석(자습서)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)을 참조하세요.


## <a name="appendix"></a>부록: 데이터 과학 프로젝트를 설정하는 도구

### <a name="install-git-credential-manager-on-windows"></a>Windows에 Git Credential Manager 설치

**Windows**에서 TDSP를 수행하는 경우 Git 리포지토리와 통신하기 위해 **GCM(Git Credential Manager)** 을 설치해야 합니다. GCM을 설치하려면 먼저 **Chocolaty**를 설치해야 합니다. Chocolatey 및 GCM을 설치하려면 Windows PowerShell에서 **관리자** 권한으로 다음 명령을 실행합니다.  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Linux(CentOS) 컴퓨터에 Git 설치

Linux(CentOS) 컴퓨터에 Git를 설치하려면 다음 bash 명령을 실행합니다.

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Linux(CentOS) 컴퓨터에서 공개 SSH 키 생성

Linux(CentOS) 컴퓨터를 사용하여 git 명령을 실행하는 경우 Azure DevOps Services에 공개 SSH 키를 추가해야 Azure DevOps Services에서 이 컴퓨터를 인식할 수 있습니다. 먼저 공개 SSH 키를 생성한 다음, Azure DevOps Services 보안 설정 페이지의 SSH 공개 키에 이 키를 추가해야 합니다. 

- SSH 키를 생성하려면 다음 두 명령을 실행합니다. 

        ssh-keygen
        cat .ssh/id_rsa.pub

![SSH 키를 생성하는 명령](./media/platforms-and-tools/resources-1-generate_ssh.png)

- *ssh-rsa*가 포함된 ssh 키 전체를 복사합니다. 
- Azure DevOps Services에 로그인합니다. 
- 페이지의 오른쪽 위 모서리에 있는 **<사용자 이름\>** 을 클릭하고 **보안**을 클릭합니다. 
    
    ![이름을 클릭한 다음, 보안 클릭](./media/platforms-and-tools/resources-2-user-setting.png)

- **SSH 공개 키**를 클릭하고 **+ 추가**를 클릭합니다. 

    ![SSH 공개 키를 클릭한 다음, +추가 클릭](./media/platforms-and-tools/resources-3-add-ssh.png)

- 텍스트 상자에서 방금 복사한 ssh 키를 붙여넣고 저장합니다.


## <a name="next-steps"></a>다음 단계

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) 학습 경로를 참조하세요.
