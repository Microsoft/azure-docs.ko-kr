---
title: Azure HDInsight에서 Apache Hadoop 클러스터에 빈에 지 노드 사용
description: 클라이언트로 사용할 수 있는 HDInsight 클러스터에 빈 에지 노드를 추가한 다음 HDInsight 애플리케이션을 테스트/호스트하는 방법입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/27/2020
ms.openlocfilehash: d7723ea63cbb9bab6adf42d7e92f84a6b8b2ab9b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199010"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>HDInsight의 Apache Hadoop 클러스터에서 빈 에지 노드 사용

HDInsight 클러스터에 빈 에지 노드를 추가하는 방법을 알아봅니다. 빈 에지 노드는 [Apache Hadoop](https://hadoop.apache.org/) 서비스가 실행되지 않지만 헤드 노드에서처럼 설치되고 구성된 동일한 클라이언트 도구를 사용하는 Linux 가상 머신입니다. 클러스터에 액세스하고, 클라이언트 애플리케이션을 테스트하며 클라이언트 애플리케이션을 호스트하는 데 에지 노드를 사용할 수 있습니다.

빈 에지 노드를 기존 HDInsight 클러스터에 추가할 수 있습니다(클러스터를 만들 때는 새 클러스터에 추가). 빈 에지 노드는 Azure Resource Manager 템플릿을 사용하여 추가합니다.  다음 샘플에서는 템플릿을 사용 하 여 작업을 수행 하는 방법을 보여 줍니다.

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "{}"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

샘플에서 보여 주듯이 필요에 따라 [스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md)을 호출하여 추가 구성을 수행할 수 있습니다(예: 에지 노드에서 [Apache Hue](hdinsight-hadoop-hue-linux.md) 설치). 스크립트 작업 스크립트는 웹에서 공개적으로 액세스할 수 있어야 합니다.  예를 들어 스크립트가 Azure Storage 저장 되는 경우 공용 컨테이너 또는 공용 blob을 사용 합니다.

에지 노드 가상 머신 크기는 HDInsight 클러스터 작업자 노드 VM 크기 요구 사항을 충족해야 합니다. 권장되는 작업자 노드 VM 크기에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)를 참조하세요.

에 지 노드를 만든 후에는 SSH를 사용 하 여에 지 노드에 연결 하 고 클라이언트 도구를 실행 하 여 HDInsight의 Hadoop 클러스터에 액세스할 수 있습니다.

> [!WARNING]
> 에지 노드에 설치된 사용자 지정 구성 요소는 Microsoft에서 상업적으로 적절한 지원을 받습니다. 이를 통해 발생한 문제가 해결될 수도 있습니다. 또는 추가 지원을 위해 커뮤니티 리소스가 제공될 수 있습니다. 다음은 커뮤니티의 도움을 받을 수 있는 가장 활발한 사이트 중 일부입니다.
>
> * [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com)를 참조하세요.
>
> Apache 기술을 사용하는 경우 [https://apache.org](https://apache.org)에서 [Apache Hadoop](https://hadoop.apache.org/) 사이트 등의 Apache 프로젝트 사이트를 통해 지원을 찾을 수도 있습니다.

> [!IMPORTANT]
> Ubuntu 이미지는 게시된 날부터 3개월 이내에 새 HDInsight 클러스터를 만드는 데 사용할 수 있게 됩니다. 2019년 1월부터 실행 중인 클러스터(에지 노드 포함)는 **자동 패치되지 않습니다**. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다.  자세한 내용은 [HDInsight의 OS 패치](./hdinsight-os-patching.md)를 참조하세요.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>기존 클러스터에 에지 노드 추가

이 섹션에서는 Resource Manager 템플릿을 사용하여 기존 HDInsight 클러스터에 에지 노드를 추가합니다.  Resource Manager 템플릿은 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)에서 찾을 수 있습니다. 리소스 관리자 템플릿은 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh에 있는 스크립트 작업을 호출 합니다. 스크립트는 어떤 동작도 수행 하지 않습니다.  리소스 관리자 템플릿에서 스크립트 동작을 호출 하는 것을 보여 주기 위한 것입니다.

1. 다음 이미지를 선택 하 여 Azure에 로그인 하 고 Azure Portal에서 Azure Resource Manager 템플릿을 엽니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 다음 속성을 구성합니다.

    |속성 |Description |
    |---|---|
    |Subscription|이 클러스터를 만드는 데 사용되는 Azure 구독을 선택합니다.|
    |Resource group|기존 HDInsight 클러스터에 사용되는 리소스 그룹을 선택합니다.|
    |위치|기존 HDInsight 클러스터의 위치를 선택합니다.|
    |클러스터 이름|기존 HDInsight 클러스터의 이름을 입력합니다.|

1. **위에 명시 된 사용 약관에 동의 함**을 선택한 다음 **구매** 를 선택 하 여에 지 노드를 만듭니다.

> [!IMPORTANT]  
> 기존 HDInsight 클러스터에 대한 Azure 리소스 그룹을 선택해야 합니다.  그렇지 않으면 "중첩된 리소스에서 요청한 작업을 수행할 수 없습니다. 부모 리소스 '&lt;ClusterName>' 찾을 수 없음"이라는 오류 메시지를 받습니다.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>클러스터를 만들 때 에지 노드 추가

이 섹션에서는 Resource Manager 템플릿을 사용하여 에지 노드로 HDInsight 클러스터를 만듭니다.  리소스 관리자 템플릿은 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)에서 찾을 수 있습니다. 리소스 관리자 템플릿은 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh에 있는 스크립트 작업을 호출 합니다. 스크립트는 어떤 동작도 수행 하지 않습니다.  리소스 관리자 템플릿에서 스크립트 동작을 호출 하는 것을 보여 주기 위한 것입니다.

1. 아직 없는 경우 HDInsight 클러스터를 만듭니다.  [HDInsight에서 Hadoop 사용 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

1. 다음 이미지를 선택 하 여 Azure에 로그인 하 고 Azure Portal에서 Azure Resource Manager 템플릿을 엽니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 다음 속성을 구성합니다.

    |속성 |Description |
    |---|---|
    |Subscription|이 클러스터를 만드는 데 사용되는 Azure 구독을 선택합니다.|
    |Resource group|클러스터에 사용되는 새 리소스 그룹을 만듭니다.|
    |위치|리소스 그룹의 위치를 선택합니다.|
    |클러스터 이름|만들려는 새 클러스터의 이름을 입력합니다.|
    |클러스터 로그인 사용자 이름|Hadoop HTTP 사용자 이름을 입력합니다.  기본 이름은 **admin**입니다.|
    |클러스터 로그인 암호|Hadoop HTTP 사용자 암호를 입력합니다.|
    |Ssh 사용자 이름|SSH 사용자 이름을 입력합니다. 기본 이름은 **sshuser**입니다.|
    |Ssh 암호|SSH 사용자 암호를 입력합니다.|
    |스크립트 동작 설치|이 문서를 진행 하기 위한 기본값을 유지 합니다.|

    클러스터 유형, 클러스터 작업자 노드 수, 에지 노드 크기 및 에지 노드 이름과 같은 일부 속성은 템플릿에 하드 코드되어 있습니다.

1. **위에 명시 된 사용 약관에 동의 함**을 선택 하 고 **구매** 를 선택 하 여에 지 노드를 사용 하 여 클러스터를 만듭니다.

## <a name="add-multiple-edge-nodes"></a>다중 에지 노드 추가

HDInsight 클러스터에 다중 에지 노드를 추가할 수 있습니다.  다중 에지 노드 구성은 Azure Resource Manager 템플릿을 사용해서만 수행할 수 있습니다.  이 문서의 시작 부분에서 템플릿 샘플을 참조하세요.  만들려는 에지 노드 수를 반영하도록 **targetInstanceCount**를 업데이트해야 합니다.

## <a name="access-an-edge-node"></a>에지 노드 액세스

에지 노드 ssh 엔드포인트는 &lt;EdgeNodeName&gt;.&lt;ClusterName&gt;-ssh.azurehdinsight.net:22입니다.  예: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22

에지 노드는 Azure Portal에서 애플리케이션으로 나타납니다.  포털은 SSH를 사용하여 에지 노드에 액세스하는 정보를 제공합니다.

**에지 노드 SSH 엔드포인트를 확인하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 에지 노드를 사용하여 HDInsight 클러스터를 엽니다.
3. **애플리케이션**을 선택합니다. 에지 노드가 표시됩니다.  기본 이름은 **new-edgenode**입니다.
4. 에 지 노드를 선택 합니다. SSH 엔드포인트가 표시됩니다.

**에지 노드에서 Hive를 사용하려면**

1. SSH를 사용하여 에지 노드에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. SSH를 사용 하 여에 지 노드에 연결한 후 다음 명령을 사용 하 여 Hive 콘솔을 엽니다.

        hive

3. 클러스터의 Hive 테이블을 표시하려면 다음 명령을 실행합니다.

        show tables;

## <a name="delete-an-edge-node"></a>에지 노드를 삭제합니다.

Azure Portal에서 에지 노드를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 에지 노드를 사용하여 HDInsight 클러스터를 엽니다.
3. **애플리케이션**을 선택합니다. 에지 노드의 목록이 표시됩니다.  
4. 삭제할에 지 노드를 마우스 오른쪽 단추로 클릭 한 다음 **삭제**를 선택 합니다.
5. **예**를 선택하여 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는에 지 노드를 추가 하는 방법과에 지 노드에 액세스 하는 방법에 대해 알아보았습니다. 자세히 알아보려면 다음 아티클을 참조하세요.

* [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md): HDInsight 애플리케이션을 클러스터에 설치하는 방법을 알아봅니다.
* [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 애플리케이션을 배포하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 애플리케이션을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 애플리케이션을 정의하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 애플리케이션을 설치하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
