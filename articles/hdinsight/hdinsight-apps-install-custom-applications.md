---
title: Azure HDInsight에 사용자 지정 Apache Hadoop 응용 프로그램 설치
description: Azure HDInsight에서 Apache Hadoop 클러스터용 HDInsight 응용 프로그램을 설치 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c109f5309837de8c9b4bd3e4bc5a5da0a6da534e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806867"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight에 사용자 지정 Apache Hadoop 애플리케이션 설치

이 문서에서는 Azure Portal에 게시 되지 않은 Azure HDInsight에 [Apache Hadoop](https://hadoop.apache.org/) 응용 프로그램을 설치 하는 방법을 알아봅니다. 이 문서에 설치할 응용 프로그램은 [색상](https://gethue.com/)입니다.

HDInsight 애플리케이션은 HDInsight 클러스터에 사용자가 설치할 수 있는 애플리케이션입니다.  Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 애플리케이션을 개발할 수 있습니다.  

## <a name="prerequisites"></a>전제 조건

기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치하려면 HDInsight 클러스터가 있어야 합니다. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요. HDInsight 클러스터를 만들 경우 HDInsight 애플리케이션도 설치할 수 있습니다.

## <a name="install-hdinsight-applications"></a>HDInsight 애플리케이션 설치

이렇게 만든 클러스터 또는 기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치할 수 있습니다. Azure Resource Manager 템플릿 정의는 [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx)를 참조하세요.

이 애플리케이션(Hue)을 배포하기 위해 필요한 파일은 다음과 같습니다.

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight 애플리케이션을 설치하기 위한 Azure Resource Manager 템플릿. Azure Resource Manager 템플릿을 직접 개발하려면 [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx)를 참조하세요.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): 에지 노드를 구성하기 위해 Azure Resource Manager 템플릿에 의해 호출되는 스크립트 작업.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh에서 호출되는 Hue 이진 파일.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh에서 호출되는 Hue 이진 파일.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): hui-install_v0.sh에서 호출되는 샘플 웹 애플리케이션(Tomcat).

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>기존 HDInsight 클러스터에 색상을 설치 하려면

1. 다음 이미지를 선택 하 여 Azure에 로그인 하 고 Azure Portal에서 리소스 관리자 템플릿을 엽니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager 템플릿의 위치는 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)입니다.  이 Resource Manager 템플릿을 작성하는 방법을 알아보려면 [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx)를 참조하세요.

1. 드롭다운 목록에서 클러스터를 포함 하는 기존 **리소스 그룹** 을 선택 합니다. 클러스터와 동일한 리소스 그룹을 사용 해야 합니다.

1. 애플리케이션을 설치하려는 클러스터의 이름입니다. 이 클러스터는 기존 클러스터여야 합니다.

1. **위에 명시 된 사용 약관에 동의 함**확인란을 선택 합니다.

1. **구매**를 선택합니다.

포털 대시보드 및 포털 알림에 고정된 타일에서 설치 상태를 확인할 수 있습니다(포털 맨 위에 있는 종 모양 아이콘 클릭).  애플리케이션을 설치하는 데 약 10분이 걸립니다.

### <a name="to-install-hue-while-creating-a-cluster"></a>클러스터를 만드는 동안 색상을 설치 하려면

1. 다음 이미지를 선택 하 여 Azure에 로그인 하 고 Azure Portal에서 리소스 관리자 템플릿을 엽니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager 템플릿의 위치는 [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)입니다.  이 Resource Manager 템플릿을 작성하는 방법을 알아보려면 [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx)를 참조하세요.

2. 지시를 따라서 클러스터를 만들고 Hue를 설치합니다. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

### <a name="other-installation-methods"></a>다른 설치 방법

Azure Portal 외에도 [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) 및 [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli)를 사용하여 Resource Manager 템플릿을 호출할 수도 있습니다.

## <a name="validate-the-installation"></a>설치 유효성 검사

Azure 포털에서 애플리케이션 상태를 확인하여 애플리케이션 설치를 확인할 수 있습니다. 또한 예상 대로 발생 한 모든 HTTP 엔드포인트과 웹 페이지 (있는 경우)의 유효성을 검사할 수도 있습니다.

**색상**의 경우 다음 단계를 사용할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. 응용 프로그램을 설치한 클러스터를 선택 합니다.
1. **설정** 메뉴에서 **응용 프로그램**을 선택 합니다.
1. 목록에서 색 **을 선택 하** 여 속성을 확인 합니다.  
1. 웹 사이트 링크를 선택 하 여 웹 사이트의 유효성을 검사 합니다.

### <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`및 `RESOURCEGROUP`를 관련 값으로 바꾸고 아래 명령을 입력 합니다.

* -HDInsight 클러스터에 대 한 모든 응용 프로그램을 나열 합니다.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* 지정 된 응용 프로그램의 속성을 검색 합니다.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>설치 문제 해결

포털 알림에서 애플리케이션 설치 상태를 확인할 수 있습니다(포털 맨 위에 있는 종 모양 아이콘 클릭).

응용 프로그램 설치에 실패 한 경우 다음 세 위치에서 오류 메시지 및 디버그 정보를 볼 수 있습니다.

* HDInsight 애플리케이션: 일반 오류 정보입니다.

    포털에서 클러스터를 열고 설정에서 응용 프로그램을 선택 합니다.

    ![hdinsight 애플리케이션 애플리케이션 설치 오류](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* HDInsight 스크립트 작업: HDInsight 애플리케이션의 오류 메시지가 스크립트 작업 실패를 나타내는 경우 스크립트 오류에 대한 자세한 내용이 스크립트 작업 창에 표시됩니다.

    설정에서 스크립트 동작을 선택 합니다. 스크립트 작업 기록에 오류 메시지가 표시됩니다.

    ![hdinsight 애플리케이션 스크립트 작업 오류](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari 웹 UI: 설치 스크립트가 오류의 원인인 경우 Ambari 웹 UI를 사용 하 여 설치 스크립트에 대 한 전체 로그를 확인 합니다.

    자세한 내용은 [문제 해결](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)을 참조하세요.

## <a name="remove-hdinsight-applications"></a>HDInsight 애플리케이션 제거

### <a name="azure-portal"></a>Azure Portal

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. 응용 프로그램을 설치한 클러스터를 선택 합니다.
1. **설정** 메뉴에서 **응용 프로그램**을 선택 합니다.
1. 제거 하려는 응용 프로그램을 마우스 오른쪽 단추로 클릭 한 다음 **삭제**를 선택 합니다.
1. **예**를 선택하여 확인합니다.

### <a name="azure-cli"></a>Azure CLI

`NAME`, `CLUSTERNAME`및 `RESOURCEGROUP`를 관련 값으로 바꾸고 아래 명령을 입력 합니다.

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>다음 단계

* [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 애플리케이션을 배포하기 위해 Resource Manager 템플릿을 개발하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md): HDInsight 애플리케이션을 클러스터에 설치하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 애플리케이션을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 애플리케이션을 설치하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드 사용](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 액세스, HDInsight 애플리케이션 테스트 및 HDInsight 애플리케이션 호스팅하는 데 비어 있는 에지 노드를 사용하는 방법을 알아봅니다.
