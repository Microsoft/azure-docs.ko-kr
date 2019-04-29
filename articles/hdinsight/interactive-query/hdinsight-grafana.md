---
title: Azure HDInsight에서 Grafana 사용
description: Azure HDInsight에서 Grafana에 액세스하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 42429f0e8e541d0481f991761ead63e4751fcc3d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097881"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight에서 Grafana에 액세스


[Grafana](https://grafana.com/)는 널리 사용되는 오픈 소스 그래프 및 대시보드 작성기입니다. Grafana는 다양한 기능을 수행합니다. 사용자가 사용자 지정 가능하고 공유 가능한 대시보드를 만들 뿐만 아니라 템플릿/스크립팅 대시보드, LDAP 통합, 여러 데이터 원본 등도 제공합니다.

현재, Azure HDInsight에서 Grafana는 Hbase 및 대화형 쿼리 클러스터 형식으로 지원됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop 클러스터 만들기

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 HDInsight에서 대화형 쿼리 클러스터를 만듭니다. 이 문서를 따라하는 데 Resource Manager 템플릿 환경이 필요하지는 않습니다. 

1. 아래 **Azure에 배포** 단추를 클릭하여 Azure에 로그인하고 Azure Portal에서 Resource Manager 템플릿을 엽니다. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 다음 스크린샷에서 설명된 대로 값을 입력하거나 선택합니다.

    > [!NOTE]  
    > 사용자가 제공하는 값은 고유해야 하며 명명 지침을 따라야 합니다. 템플릿은 유효성 검사를 수행하지 않습니다. 사용자가 제공하는 값이 이미 사용 중이거나 지침을 준수하지 않는 경우 템플릿을 제출한 후 오류가 발생합니다.       
    > 
    >
    
    ![HDInsight Linux 포털에서 Resource Manager 템플릿 시작](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Azure Portal과 리소스 그룹 관리자 템플릿을 사용하여 HDInsigut에 Hadoop 클러스터 배포")

    다음 값을 입력하거나 선택합니다.
    
    |자산  |설명  |
    |---------|---------|
    |**구독**     |  Azure 구독을 선택합니다. |
    |**리소스 그룹**     | 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.  리소스 그룹은 Azure 구성 요소의 컨테이너입니다.  이 경우 리소스 그룹에는 HDInsight 클러스터 및 종속 Azure Storage 계정이 포함되어 있습니다. |
    |**위치**:     | 클러스터를 만들려는 Azure 위치를 선택합니다.  성능 향상을 위해 가까운 곳을 선택합니다. |
    |**클러스터 유형**     | **hadoop**을 선택합니다. |
    |**클러스터 이름**     | Apache Hadoop 클러스터에 사용할 이름을 입력합니다. HDInsight의 모든 클러스터는 동일한 DNS 네임스페이스를 공유하므로 이 이름은 고유해야 합니다. 이름은 문자, 숫자 및 하이픈을 포함하여 최대 59자로 구성할 수 있습니다. 이름의 첫 번째 및 마지막 문자에는 하이픈을 사용할 수 없습니다. |
    |**클러스터 로그인 이름 및 암호**     | 기본 로그인 이름은 **admin**입니다. 암호는 10자 이상이어야 하며, 숫자, 대문자, 소문자 및 영숫자가 아닌 문자(' " ` 문자 제외\)를 각각 하나 이상 포함해야 합니다. "Pass@word1"과 같은 일반적인 암호를 **제공하지 않았는지** 확인합니다.|
    |**SSH 사용자 이름 및 암호**     | 기본 사용자 이름은 **sshuser**입니다.  SSH 사용자 이름은 바꿀 수 있습니다.  SSH 사용자 암호에는 클러스터 로그인 암호와 동일한 요구 사항이 적용됩니다.|
       
    일부 속성이 템플릿에 하드 코딩되었습니다.  템플릿에서 이러한 값을 구성할 수 있습니다. 이러한 속성에 대한 자세한 설명은 [HDInsight에서 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

3. **위에 명시된 사용 약관에 동의함** 및 **대시보드에 고정**을 선택한 다음, **구매**를 선택합니다. 포털 대시보드에 **배포 제출 중**이라는 제목의 새 타일이 표시됩니다. 클러스터를 만들려면 20분 정도가 걸립니다.

    ![템플릿 배포 진행률](./media/hdinsight-grafana/deployment-progress-tile.png "Azure 템플릿 배포 진행률")

4. 클러스터가 생성되면 타일의 캡션이 지정한 리소스 그룹 이름으로 변경됩니다. 또한 타일은 리소스 그룹 내에서 만든 HDInsight 클러스터를 나열합니다. 
   
    ![HDInsight Linux 시작 - 리소스 그룹](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight 클러스터 리소스 그룹")
    
5. 또한 타일은 클러스터와 연결된 기본 저장소를 나열합니다. 각 클러스터에는 [Azure Storage 계정](../hdinsight-hadoop-use-blob-storage.md) 또는 [Azure Data Lake 계정](../hdinsight-hadoop-use-data-lake-store.md) 종속성이 있습니다. 이 저장소 계정을 기본 저장소 계정이라고 합니다. HDInsight 클러스터와 해당 기본 스토리지 계정은 같은 Azure 지역에 있어야 합니다. 클러스터를 삭제해도 저장소 계정은 삭제되지 않습니다.
    

> [!NOTE]  
> 기타 클러스터 생성 방법 및 이 자습서에 사용된 속성에 대한 이해는 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 

## <a name="access-the-grafana-dashboard"></a>Grafana 대시보드에 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **HDInsight 클러스터**를 선택한 다음, 마지막 섹션에서 만든 클러스터 이름을 선택합니다.

3. **빠른 링크**에서 **클러스터 대시보드**를 클릭합니다.

    ![HDInsight 클러스터 대시보드 포털](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "포털의 HDInsight 클러스터 대시보드")

4. 대시보드에서 **Grafana** 타일을 클릭합니다. 또는 클러스터 URL의 `/grafana/` 경로로 이동합니다. 예: `https://<clustername>.azurehdinsight.net/grafana/`.

5. Hadoop 클러스터 사용자 자격 증명을 입력합니다.

6. 다음 예제와 같은 Grafana 대시보드가 표시됩니다.

    ![HDInsight Grafana 대시보드](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana 대시보드")

   

## <a name="clean-up-resources"></a>리소스 정리
이 문서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 

> [!NOTE]  
> HDInsight에서 Hadoop을 사용하여 ETL 작업을 실행하는 방법을 알아보기 위해 다음 자습서를 *즉시* 진행하는 경우 클러스터가 실행되도록 유지할 수 있습니다. 자습서에서 Hadoop 클러스터를 다시 만들어야 하기 때문입니다. 그러나 다음 자습서로 바로 진행하지 않는 경우 이제 클러스터를 삭제해야 합니다.

**클러스터 및/또는 기본 저장소 계정을 삭제하려면**

1. Azure Portal이 있는 브라우저 탭으로 돌아갑니다. 그러면 클러스터 개요 페이지가 표시됩니다. 클러스터는 삭제하지만 기본 저장소 계정은 유지하려는 경우 **삭제**를 선택합니다.

    ![HDInsight 클러스터 삭제](./media/hdinsight-grafana/hdinsight-delete-cluster.png "HDInsight 클러스터 삭제")

2. 클러스터와 기본 저장소 계정을 삭제하려는 경우 리소스 그룹 이름(이전 스크린샷에서 강조 표시됨)을 선택하여 리소스 그룹 페이지를 엽니다.

3. **리소스 그룹 삭제**를 선택하여 클러스터와 기본 저장소 계정을 포함하는 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 저장소 계정이 삭제됩니다. 저장소 계정을 유지하려면 클러스터만 삭제하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Resource Manager 템플릿을 사용하여 Linux 기반 HDInsight 클러스터를 만들고, 기본 Apache Hive 쿼리를 수행하는 방법을 알아보았습니다. 다음 문서에서는 HDInsight의 Hadoop을 사용하여 ETL(추출, 변환 및 로드) 작업을 수행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
>[HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드](../hdinsight-analyze-flight-delay-data-linux.md)

자신의 데이터로 작업을 시작할 준비가 된 상태에서 HDInsight에서 데이터를 저장하는 방법과 HDInsight로 데이터를 가져오는 방법에 대해 더 알아보려면 다음 문서를 참조하세요.

* HDInsight에서 Azure Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조하세요.
* 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드](../hdinsight-upload-data.md)를 참조하세요.

HDInsight를 사용하여 데이터를 분석하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* Visual Studio에서 Hive 쿼리를 수행하는 방법을 비롯하여 HDInsight로 Hive를 사용하는 데 대한 자세한 내용은 [HDInsight에서 Apache Hive 사용](../hdinsight-use-hive.md)을 참조하세요.
* 데이터를 변환하는 데 사용되는 언어인 Pig에 대해 알아보려면 [HDInsight에서 Apache Pig 사용](../hdinsight-use-pig.md)을 참조하세요.
* Hadoop에서 데이터를 처리하는 프로그램을 작성하는 방법인 MapReduce에 대해 알아보려면 [HDInsight로 MapReduce 사용](../hdinsight-use-mapreduce.md)을 참조하세요.
* Visual Studio에 HDInsight Tools를 사용하여 HDInsight의 데이터를 분석하는 방법에 대해 알아보려면 [HDInsight용 Visual Studio Hadoop 도구를 사용하여 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.



HDInsight 클러스터를 만들거나 관리하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* Linux 기반 HDInsight 클러스터 관리에 대해 알아보려면 [Ambari를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.
* HDInsight 클러스터를 만들 때 선택하는 옵션에 대해 자세히 알아보려면 [사용자 지정 옵션을 사용하여 Linux에 HDInsight 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
