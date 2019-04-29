---
title: Azure HDInsight의 ML 서비스 소개
description: HDInsight의 ML Services를 사용하여 빅 데이터 분석용 애플리케이션을 만드는 방법에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 56a11a17de3c66a3d45edb71c72be20ce1a1c89e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124733"
---
# <a name="introduction-to-ml-services-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight의 ML Services 및 오픈 소스 기능 소개

> [!NOTE]  
> 2017년 9월 Microsoft R Server가 **Microsoft Machine Learning Server** 또는 ML Server라는 새로운 이름으로 릴리스되었습니다. 이 때문에 HDInsight의 R Server 클러스터를 이제 HDInsight의 **Machine Learning Services** 또는 **ML Services**라고 합니다. R Server 이름 변경에 대한 자세한 내용은 [Microsoft R Server가 이제 Microsoft Machine Learning Server임](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server#get-support-for-r-server)을 참조하세요.

Microsoft Machine Learning Server를 사용하면 Azure에서 HDInsight 클러스터를 만들 때 배포 옵션으로 사용할 수 있습니다. 이 옵션을 제공하는 클러스터 유형을 **ML Services**라고 합니다. 이 기능은 데이터 과학자, 통계학자 및 R 프로그래머에게 HDInsight의 확장 가능한 분산형 분석 방법에 요청 시 액세스할 수 있도록 해줍니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

HDInsight의 ML Services는 Azure Blob 또는 Data Lake Store에 로드된 거의 모든 크기의 데이터 집합에서 R 기반 분석을 위한 최신 기능을 제공합니다. ML Services 클러스터는 오픈 소스 R을 기반으로 하기 때문에 빌드한 R 기반 애플리케이션은 8000개 이상의 오픈 소스 R 패키지를 활용할 수 있습니다. Microsoft의 빅 데이터 분석 패키지인 ScaleR의 루틴도 사용 가능합니다.

클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 위치를 제공합니다. 에지 노드를 사용하는 경우 에지 노드 서버의 코어에서 ScaleR의 병렬화된 분산 함수를 실행하는 옵션이 제공됩니다. 또한 ScaleR의 Hadoop Map Reduce 또는 Apache Spark 컴퓨팅 컨텍스트를 사용하여 클러스터의 노드 전반에 함수를 실행할 수도 있습니다.

분석 결과에서 얻은 모델 또는 예측을 온-프레미스 용도로 다운로드할 수 있습니다. 또한 특히 [Azure Machine Learning Studio](https://studio.azureml.net) [웹 서비스](../../machine-learning/studio/publish-a-machine-learning-web-service.md)를 통해 Azure의 다른 곳에서 작동할 수 있습니다.

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight에서 ML Services 시작

Azure HDInsight에 ML Services 클러스터를 만들려면 Azure Portal을 사용하여 HDInsight 클러스터를 만들 때 **ML Services** 클러스터 유형을 선택합니다. ML Services 클러스터 유형으로 클러스터의 데이터 노드에 있는 ML Services 및 ML Services 기반 분석을 위한 연결 영역으로서 에지 노드에 있는 ML Server가 있습니다. 클러스터를 만드는 방법에 대한 연습은 [HDInsight의 ML Services 시작](r-server-get-started.md)을 참조하세요.

## <a name="why-choose-ml-services-in-hdinsight"></a>HDInsight에서 ML Services를 사용하는 이유

HDInsight의 ML Services는 다음과 같은 이점을 제공합니다.

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Microsoft 및 오픈-소스의 AI 혁신 

  ML Services에는 [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 및 [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)과 같이 실제 메모리보다 더 큰 데이터에 작동할 수 있고, 광범위한 플랫폼에서 분산 방식으로 실행될 수 있는 고도로 확장 가능한 풍부한 분산 알고리즘 집합을 포함합니다. 이 제품에 포함되어 있는 Microsoft의 사용자 지정 [R 패키지](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) 및 [Python 패키지](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) 모음에 대해 자세히 알아보세요.
  
  ML Services는 이러한 Microsoft의 혁신 및 오픈 소스 커뮤니티(R, Python 및 AI 도구 키트)의 기능을 단일 엔터프라이즈급 플랫폼에 구현합니다. 모든 R 또는 Python 오픈 소스 기계 학습 패키지는 Microsoft의 독자적인 혁신 기능과 함께 작동될 수 있습니다.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>간단하고 안전하고 확장성이 뛰어난 운용 및 관리

  기존 운용 패러다임 및 환경에 의존하는 기업은 이 영역으로 전환하기 위해 많은 시간과 노력을 투자해야 합니다. 이로 인해 모델의 변환 시간, 유효한 최신 상태로 유지하기 위한 반복 작업, 정기적인 승인, 운용 전반의 사용 권한 관리 등에 따른 비용 증가와 지연 문제가 발생합니다

  ML Services는 최고 수준의 [운용화](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)를 제공하여, 기계 학습 모델이 완료되면 클릭 몇 번으로 웹 서비스 API를 생성할 수 있습니다. 이러한 [웹 서비스](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)는 클라우드의 서버 그리드에서 호스트되며, LOB(기간 업무) 애플리케이션에 통합될 수 있습니다. 탄력적 그리드에 배포할 수 있으므로 일괄 처리 및 실시간 채점에 대한 비즈니스 요구에 맞춰 원활하게 확장 가능합니다. 지침을 보려면 [HDInsight에서 ML Services 운용](r-server-operationalize.md)을 참조하세요.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight의 ML Services 주요 기능

HDInsight의 ML Services에는 다음 기능이 포함됩니다.

| 기능 범주 | 설명 |
|------------------|-------------|
| R 지원 | R로 작성된 솔루션용 [R 패키지](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)(R의 오픈 소스 배포와 스크립트 실행을 위한 런타임 인프라 포함) |
| Python 지원 | Python으로 작성된 솔루션용 [Python 모듈](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)(Python의 오픈 소스 배포와 스크립트 실행을 위한 런타임 인프라 포함)
| [미리 학습된 모델](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 시각적 분석 및 텍스트 감정 분석(사용자가 제공한 데이터를 채점할 수 있음) |
| [배포 및 사용](r-server-operationalize.md) | 서버를 운용하고 솔루션을 웹 서비스로 배포합니다. |
| [원격 실행](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | 클라이언트 워크스테이션에서 네트워크의 ML Services에 대한 원격 세션을 시작합니다. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>HDInsight의 ML Services에 대한 데이터 저장소 옵션

HDInsight 클러스터의 HDFS 파일 시스템을 위한 기본 스토리지는 Azure Storage 계정 또는 Azure Data Lake Storage와 연결될 수 있습니다. 이러한 연결을 통해 분석 중에 클러스터 저장소에 업로드되는 모든 데이터가 영구적으로 유지되고, 클러스터를 삭제한 후에도 해당 데이터를 사용할 수 있습니다. 저장소 계정의 포털 기반 업로드 기능과 [AzCopy](../../storage/common/storage-use-azcopy.md) 유틸리티를 포함하여 선택한 저장소 옵션에 대한 데이터 전송을 처리할 수 있는 다양한 도구가 있습니다.

사용 중인 기본 저장소 옵션과 관계없이 클러스터 프로비전 프로세스 중에 추가 Blob 및 Data Lake Store에 대한 액세스를 구현하는 옵션이 있습니다. 추가 계정에 액세스 추가에 대한 자세한 내용은 [HDInsight의 ML Services 시작](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started)을 참조하세요. 여러 스토리지 계정 사용에 대해 자세히 알아보려면 [HDInsight의 ML Services에 대한 Azure Storage 옵션](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) 문서를 참조하세요.

[Azure 파일](../../storage/files/storage-how-to-use-files-linux.md) 을 에지 노드용 저장소 옵션으로 사용할 수도 있습니다. Azure 파일을 사용하면 Azure Storage에서 만든 파일 공유를 Linux 파일 시스템에 마운트할 수 있습니다. HDInsight 클러스터의 ML Services에 대한 데이터 스토리지 옵션에 대한 자세한 내용은 [HDInsight의 ML Services에 대한 Azure Storage 옵션](r-server-storage.md)을 참조하세요.

## <a name="access-ml-services-edge-node"></a>ML Services 에지 노드 액세스

브라우저를 사용하여 에지 노드의 Microsoft ML Server에 연결할 수 있습니다. 클러스터를 만드는 동안 기본적으로 설치됩니다. 자세한 내용은 [HDInsight에서 ML Services 시작](r-server-get-started.md)을 참조하세요. R 콘솔에 액세스하기 위해 SSH/PuTTY를 사용하여 명령줄에서 클러스터 에지 노드에 연결할 수도 있습니다.

## <a name="develop-and-run-r-scripts"></a>R 스크립트 개발 및 실행

사용자가 만들고 실행하는 R 스크립트는 ScaleR 라이브러리에서 사용 가능한 병렬화된 분산 루틴 외에 8000개 이상의 오픈 소스 R 패키지를 사용할 수 있습니다. 일반적으로 에지 노드의 ML Services에서 실행되는 스크립트는 해당 노드의 R 인터프리터에서 실행됩니다. 예외는 Hadoop Map Reduce(RxHadoopMR) 또는 Spark(RxSpark)로 설정된 계산 컨텍스트를 사용하여 ScaleR 함수를 호출해야 하는 단계입니다. 이러한 경우 함수는 참조 데이터와 연결된 클러스터의 데이터(작업) 노드에서 분산된 방식으로 실행됩니다. 다양한 계산 컨텍스트 옵션에 대한 자세한 내용은 [HDInsight에서 R Server의 ML Services 컨텍스트 옵션](r-server-compute-contexts.md)을 참조하세요.

## <a name="operationalize-a-model"></a>모델 운영

데이터 모델링이 완료되면 Azure 또는 온-프레미스에서 새 데이터를 예측하는 모델을 운영할 수 있습니다. 이 프로세스를 점수 매기기라고 합니다. 점수 매기기는 HDInsight, Azure Machine Learning 또는 온-프레미스에서 수행할 수 있습니다.

### <a name="score-in-hdinsight"></a>HDInsight에서 점수 매기기

HDInsight에서 점수를 매기려면 모델을 호출하여 저장소 계정에 로드한 새 데이터 파일에 대해 예측하는 R 함수를 작성합니다. 그런 다음 예측을 저장소 계정에 다시 저장합니다. 클러스터의 에지 노드에서 요청 시 루틴을 실행하거나 예약된 작업을 사용하여 루틴을 실행할 수 있습니다.

### <a name="score-in-azure-machine-learning-aml"></a>AML(Azure Machine Learning)에서 점수 매기기

Azure Machine Learning을 사용하여 점수를 매기려면 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)로 알려진 오픈 소스 Azure Machine Learning R 패키지를 사용하여 모델을 Azure 웹 서비스로 게시합니다. 이 패키지는 편의상 에지 노드에 미리 설치됩니다. 다음으로, Azure Machine Learning의 기능을 사용하여 웹 서비스에 대한 사용자 인터페이스를 만든 후 점수 매기기에 필요한 대로 웹 서비스를 호출합니다.

이 옵션을 선택한 경우 ScaleR 모델 개체를 웹 서비스에서 사용할 동등한 오픈 소스 모델 개체로 변환해야 합니다. 이 변환은 앙상블 기반 모델에 대해 ScaleR 강제 변환 함수(예: `as.randomForest()` )를 통해 수행할 수 있습니다.

### <a name="score-on-premises"></a>온-프레미스 점수 매기기

모델을 만든 후 온-프레미스에서 점수를 매기려면 R에서 모델을 serialize하여 다운로드하고 deserialize한 다음 새 데이터의 점수 매기기에 사용합니다. [HDInsight에서 점수 매기기](#score-in-hdinsight)에서 설명한 접근 방식을 사용하여 또는 [웹 서비스](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)를 사용하여 새 데이터의 점수를 매길 수 있습니다.

## <a name="maintain-the-cluster"></a>클러스터 유지 관리

### <a name="install-and-maintain-r-packages"></a>R 패키지 설치 및 유지 관리

R 스크립트의 단계 대부분이 에지 노드에서 실행되므로 사용하는 R 패키지 대부분이 에지 노드에 필요합니다. 에지 노드에 추가 R 패키지를 설치하려면 R에서 `install.packages()` 메서드를 사용할 수 있습니다.

클러스터에 ScaleR 라이브러리의 루틴을 사용하고 있는 경우에는 일반적으로 데이터 노드에 추가 R 패키지를 설치할 필요가 없습니다. 그러나 데이터 노드에서 **rxExec** 또는 **RxDataStep** 실행을 사용하도록 지원하려면 추가 패키지가 필요할 수 있습니다.

이러한 경우 클러스터를 만든 후에 스크립트 작업을 통해 추가 패키지를 설치할 수 있습니다. 자세한 내용은 [HDInsight 클러스터에서 ML Services 관리](r-server-hdinsight-manage.md)를 참조하세요.

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce 메모리 설정 변경

Map Reduce 작업을 실행할 때 ML Services에 사용 가능한 메모리 양을 변경하기 위해 클러스터를 수정할 수 있습니다. 클러스터를 수정하려면 클러스터의 Azure 포털 블레이드를 통해 제공되는 Apache Ambari UI를 사용합니다. 클러스터의 Ambari UI에 액세스하는 방법에 대한 지침은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.

다음과 같이 **RxHadoopMR** 에 대한 호출에서 Hadoop 스위치를 사용하여 ML Services에 사용 가능한 메모리 양을 변경할 수도 있습니다.

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>클러스터 규모 조정

포털을 통해 HDInsight의 기존 ML Services 클러스터를 확장하거나 축소할 수 있습니다. 규모를 강화하여 더 큰 처리 작업에 필요한 추가 용량을 얻거나 유휴 상태일 때 클러스터 크기를 다시 조정할 수 있습니다. 클러스터 크기를 조정하는 방법에 대한 지침은 [HDInsight 클러스터 관리](../hdinsight-administer-use-portal-linux.md)를 참조하세요.

### <a name="maintain-the-system"></a>시스템 유지 관리

OS 패치 및 기타 업데이트를 적용하는 유지 관리는 업무 외 시간 중에 HDInsight 클러스터의 기본 Linux VM에서 수행됩니다. 일반적으로 유지 관리는 월요일과 목요일마다 오전 3시 30분(VM의 로컬 시간에 따라)에 수행됩니다. 업데이트는 한 번에 클러스터의 4분의 1 이상에 영향을 주지 않는 방식으로 수행됩니다.

헤드 노드가 중복되어 있고 모든 데이터 노드가 영향을 받는 것은 아니므로 이 시간 중에 실행되는 작업은 속도가 느려질 수 있습니다. 하지만 완료될 때까지 계속 실행되어야 합니다. 모든 사용자 지정 소프트웨어 또는 로컬 데이터는 클러스터를 다시 빌드해야 하는 심각한 오류가 발생하지 않는 한 이러한 유지 관리에서 보존됩니다.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>HDInsight의 ML Services에 대한 IDE 옵션

HDInsight 클러스터의 Linux 에지 노드는 R 기반 분석의 연결 영역입니다. 최신 버전의 HDInsight는 에지 노드에 RStudio Server의 기본 설치를 브라우저 기반 IDE로 제공합니다. R 스크립트의 개발과 실행을 위한 IDE로 RStudio Server를 사용하면 R 콘솔만 사용하는 것보다 훨씬 더 생산적일 수 있습니다.

또한 데스크톱 IDE를 설치하고 원격 Map Reduce 또는 Spark 계산 컨텍스트를 사용하여 클러스터에 액세스하는 것입니다. Microsoft의 RTVS([Visual Studio용 R 도구](https://www.visualstudio.com/features/rtvs-vs.aspx)), RStudio 및 Walware의 Eclipse 기반 [StatET](http://www.walware.de/goto/statet) 등의 옵션도 있습니다.

또한 SSH 또는 PuTTY를 통해 연결한 후 Linux 명령 프롬프트에서 **R**을 입력하면 에지 노드의 R 콘솔에 액세스할 수 있습니다. 콘솔 인터페이스 사용 시 R 스크립트 개발용 텍스트 편집기를 다른 창에서 실행하고 필요에 따라 스크립트의 섹션을 복사하여 R 콘솔에 붙여 넣으면 편리합니다.

## <a name="pricing"></a>가격

ML Services HDInsight 클러스터와 연관된 요금은 다른 HDInsight 클러스터형식의 가격과 유사한 방식으로 구성됩니다. 이름, 데이터 및 에지 노드에서 기본 VM의 크기 조정을 기반으로 하며 향상된 코어 시간 기능이 추가되었습니다. 자세한 내용은 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터에서 ML Services를 사용하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [HDInsight에서 ML Services 클러스터 시작](r-server-get-started.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [HDInsight에서 ML Services 클러스터에 대한 저장소 옵션](r-server-storage.md)
