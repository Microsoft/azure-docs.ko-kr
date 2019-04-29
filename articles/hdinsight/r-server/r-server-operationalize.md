---
title: HDInsight에서 ML 서비스 운영 - Azure
description: Azure HDInsight에서 ML 서비스를 운영하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 916c4fae8eed9451080f92e97743876e89bd25ea
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124580"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 ML 서비스 클러스터 운영

HDInsight에서 ML 서비스 클러스터를 사용하여 데이터 모델링을 완료한 후에 모델을 운영하여 예측할 수 있습니다. 이 문서에서는 이 작업을 수행하는 방법에 대한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight의 ML 서비스 클러스터**: 자세한 내용은 [HDInsight에서 ML 서비스 시작](r-server-get-started.md)을 참조하세요.

* **SSH(Secure Shell) 클라이언트**: SSH 클라이언트는 HDInsight 클러스터에 원격으로 연결하여 클러스터에서 직접 명령을 실행하는 데 사용됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>원 박스 구성을 사용하여 ML 서비스 클러스터 운영

> [!NOTE]  
> 아래 단계는 R Server 9.0 및 ML Server 9.1에 적용할 수 있습니다. ML Server 9.3의 경우 [관리 도구를 사용하여 운영 구성 관리](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)를 참조하세요.

1. SSH를 에지 노드로 실행합니다.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Azure HDInsight에서 SSH를 사용하는 방법에 대한 지침은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. 관련 버전의 디렉터리를 변경하고 sudo를 통해 dot net dll을 실행합니다. 

    - Microsoft ML Server 9.1의 경우:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0의 경우:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. 선택할 수 있는 옵션이 표시됩니다. 다음 스크린샷에 표시된 첫 번째 옵션인 **운영을 위해 ML Server 구성**을 선택합니다.

    ![one-box 조작화](./media/r-server-operationalize/admin-util-one-box-1.png)

1. 이제 ML Server를 운영하는 방법을 선택할 수 있는 옵션이 표시됩니다. 표시된 옵션에서 **A**를 입력하여 첫 번째 옵션을 선택합니다.

    ![one-box 조작화](./media/r-server-operationalize/admin-util-one-box-2.png)

1. 메시지가 표시되면 로컬 관리 사용자의 암호를 입력하고 다시 입력합니다.

1. 조작이 성공했음을 알리는 출력이 표시됩니다. 메뉴에서 다른 옵션을 선택하라는 메시지도 표시됩니다. E를 선택하여 주 메뉴로 돌아갑니다.

    ![one-box 조작화](./media/r-server-operationalize/admin-util-one-box-3.png)

1. 필요에 따라 다음과 같이 진단 테스트를 실행하여 진단 검사를 수행할 수 있습니다.

    a. 주 메뉴에서 **6**을 선택하여 진단 테스트를 실행합니다.

    ![one-box 조작화](./media/r-server-operationalize/diagnostic-1.png)

    b. 진단 테스트 메뉴에서 **A**를 선택합니다. 메시지가 표시되면 로컬 관리 사용자에 대해 지정한 암호를 입력합니다.

    ![one-box 조작화](./media/r-server-operationalize/diagnostic-2.png)

    다. 출력에서 전체 상태가 성공으로 표시되는지 확인합니다.

    ![one-box 조작화](./media/r-server-operationalize/diagnostic-3.png)

    d. 표시된 메뉴 옵션에서 **E**를 입력하여 주 메뉴로 돌아간 다음 **8**을 입력하여 관리 유틸리티를 종료합니다.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Apache Spark에서 웹 서비스를 사용할 때 긴 지연 발생

Apache Spark 컴퓨팅 컨텍스트에서 mrsdeploy 함수로 생성된 웹 서비스를 이용하려고 할 때 긴 지연이 발생하면 누락된 폴더를 추가해야 할 수도 있습니다. Spark 애플리케이션은 mrsdeploy 함수를 사용하여 웹 서비스에서 호출될 때마다 '*rserve2*'라는 사용자에게 속합니다. 이 문제를 해결하려면

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


이 단계에서 운영화 구성이 완료되었습니다. 이제 RClient의 `mrsdeploy` 패키지를 사용하여 에지 노드의 조작에 연결하고 [원격 실행](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) 및 [웹 서비스](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)와 같은 기능을 사용할 수 있습니다. 클러스터가 가상 네트워크에 설정되어 있는지 여부에 따라 SSH 로그인을 통해 포트 전달 터널링을 설정할 필요가 있습니다. 다음 섹션에서는 이 터널을 설정하는 방법에 대해 설명합니다.

### <a name="ml-services-cluster-on-virtual-network"></a>가상 네트워크의 ML Server 클러스터

12800 포트를 통해 에지 노드로 트래픽을 허용하도록 해야 합니다. 이렇게 하면 에지 노드를 사용하여 조작화 기능에 연결할 수 있습니다.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


`remoteLogin()`이 에지 노드에 연결할 수 없지만 에지 노드로 SSH를 실행할 수 있는 경우 12800 포트에서 트래픽을 허용하는 규칙이 올바르게 설정되었는지 확인해야 합니다. 문제가 계속되면 SSH를 통해 포트 전달 터널링을 설정하여 문제를 해결할 수 있습니다. 지침은 다음 섹션을 참조하세요.

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>가상 네트워크에서 설정되지 않은 ML Server 클러스터

클러스터가 VNet에 설정되지 않았거나 VNet을 통한 연결에 문제가 있는 경우 SSH 포트 전달 터널링을 사용할 수 있습니다.

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

SSH 세션이 활성화되면 로컬 머신의 12800 포트에서 발생한 트래픽이 SSH 세션을 통해 에지 노드의 12800 포트로 전달됩니다. `remoteLogin()` 메서드에서 `127.0.0.1:12800`을 사용해야 합니다. 이렇게 하면 포트 전달을 통해 에지 노드의 조작에 로그인됩니다.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight 작업자 노드에서 조작된 계산 노드를 확장합니다.

계산 노드를 확장하려면 먼저 작업자 노드 서비스를 해제한 다음, 서비스 해제된 작업자 노드에서 계산 노드를 구성합니다.

### <a name="step-1-decommission-the-worker-nodes"></a>1단계: 작업자 노드 해제

ML 서비스 클러스터는 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)을 통해 관리되지 않습니다. 작업자 노드 서비스를 해제하지 않으면 YARN 리소스 관리자가 서버에서 사용하는 리소스를 인식하지 못하기 때문에 예상대로 작동하지 않습니다. 이 상황을 피하려면 계산 노드의 크기를 조정하기 전에 작업자 노드의 서비스를 해제하는 것이 좋습니다.

작업자 노드 서비스를 해제하려면 다음 단계를 따르세요.

1. 클러스터의 Ambari 콘솔에 로그인하고 **Hosts** 탭을 클릭합니다.

1. 서비스 해제할 작업자 노드를 선택합니다.

1. **작업** > **선택한 호스트** > **호스트** > **유지 관리 모드 켜기**를 클릭합니다. 예를 들어 다음 이미지에서는 wn3과 wn4를 선택하여 서비스를 해제했습니다.  

   ![작업자 노드 서비스 해제](./media/r-server-operationalize/get-started-operationalization.png)  

* **작업** > **선택한 호스트** > **DataNodes**를 선택하고 > **서비스 해제**를 클릭합니다.
* **작업** > **선택한 호스트** > **NodeManagers**를 선택하고 > **서비스 해제**를 클릭합니다.
* **작업** > **선택한 호스트** > **DataNodes**를 선택하고 > **중지**를 클릭합니다.
* **작업** > **선택한 호스트** > **NodeManagers**를 선택하고 > **중지**를 클릭합니다.
* **작업** > **선택한 호스트** > **호스트**를 선택하고 > **모든 구성 요소 중지**를 클릭합니다.
* 작업자 노드를 선택 취소하고 헤드 노드를 선택합니다.
* **작업** > **선택한 호스트** > **호스트** > **모든 구성 요소 다시 시작**을 선택합니다.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2단계: 서비스 해제된 작업자 노드 각각에 컴퓨팅 노드 구성

1. SSH를 서비스 해제된 각 작업자 노드로 실행합니다.

1. 사용자가 보유한 에게 있는 ML 서비스 클러스터와 관련된 DLL을 사용하여 관리 유틸리티를 실행합니다. ML Server 9.1의 경우 다음을 실행합니다.

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. **1**을 입력하여 **운영을 위해 ML Server 구성** 옵션을 선택합니다.

1. **C**를 입력하여 `C. Compute node` 옵션을 선택합니다. 그러면 작업자 노드에 계산 노드가 구성됩니다.

1. 관리 유틸리티를 종료합니다.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3단계: 웹 노드에 컴퓨팅 노드 세부 정보 추가

서비스가 해제된 모든 작업자 노드에서 계산 노드를 실행하도록 구성한 후에 에지 노드로 돌아가서 ML Server 웹 노드의 구성에 서비스가 해제된 작업자 노드의 IP 주소를 추가합니다.

1. SSH를 에지 노드로 실행합니다.

1. `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`을 실행합니다.

1. “URI” 섹션을 살펴보고 작업자 노드의 IP 및 포트 세부 정보를 추가합니다.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 ML 서비스 클러스터 관리](r-server-hdinsight-manage.md)
* [HDInsight에서 ML Services 클러스터에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 Azure Storage 옵션](r-server-storage.md)
