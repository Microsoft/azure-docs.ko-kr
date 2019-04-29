---
title: HDInsight의 Kafka로 Azure Kubernetes Service 사용
description: AKS(Azure Kubernetes Service)에서 호스트되는 컨테이너 이미지에서 HDInsight의 Kafka를 사용하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 35ef708cdcedc2d7bafedb8bf3686e4b468177df
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115354"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>HDInsight의 Apache Kafka에서 Azure Kubernetes Service 사용

HDInsight 클러스터의 [Apache Kafka](https://kafka.apache.org/)에서 AKS(Azure Kubernetes Service)를 사용하는 방법을 알아봅니다. 이 문서의 단계에서는 AKS에서 호스트되는 Node.js 애플리케이션을 사용하여 Kafka와의 연결을 확인합니다. 이 애플리케이션은 [kafka-node](https://www.npmjs.com/package/kafka-node) 패키지를 사용하여 Kafka와 통신합니다. AKS에서 호스트되는 백 엔드와 브라우저 클라이언트 간의 이벤트 구동 메시징에 [Socket.io](https://socket.io/)를 사용합니다.

[Apache Kafka](https://kafka.apache.org)는 실시간 스트리밍 데이터 파이프라인과 애플리케이션을 만드는 데 사용할 수 있는 오픈 소스 분산형 스트리밍 플랫폼입니다. Azure Kubernetes Service를 사용하면 호스트하는 Kubernetes 환경이 관리되고 컨테이너화된 애플리케이션을 쉽고 빠르게 배포할 수 있습니다. Azure Virtual Network를 사용하여 두 서비스를 연결할 수 있습니다.

> [!NOTE]  
> 이 문서에서는 Azure Kubernetes Services가 HDInsight의 Kafka와 통신하도록 설정하는 데 필요한 단계에 중점을 두고 있습니다. 예제 자체는 구성이 작동하는 것을 보여주는 기본적인 Kafka 클라이언트입니다.

## <a name="prerequisites"></a>필수 조건

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure 구독

이 문서에서는 사용자가 다음 Azure 서비스를 만들고 사용하는 것에 익숙하다고 가정합니다.

* HDInsight의 Kafka
* Azure Kubernetes Service
* Azure Virtual Networks

또한 이 문서에서는 [Azure Kubernetes Services 자습서](../../aks/tutorial-kubernetes-prepare-app.md)를 살펴보았다고 가정합니다. 이 자습서에서는 컨테이너 서비스를 만들고, Kubernetes 클러스터와 컨테이너 레지스트리를 생성하고, `kubectl` 유틸리티를 구성합니다.

## <a name="architecture"></a>아키텍처

### <a name="network-topology"></a>네트워크 토폴로지

HDInsight와 AKS 모두 Azure Virtual Network를 계산 리소스의 컨테이너로 사용합니다. HDInsight와 AKS 간의 통신을 가능하게 하려면 해당 네트워크 간의 통신이 가능하도록 설정해야 합니다. 이 문서의 단계에서는 네트워크에 가상 네트워크 피어링을 사용합니다. VPN과 같은 다른 연결도 작동합니다. 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md) 문서를 참조하세요.


다음 다이어그램은 이 문서에서 사용한 네트워크 토폴로지를 보여줍니다.

![한 가상 네트워크의 HDInsight, 다른 네트워크의 AKS 및 피어링을 사용하여 연결된 네트워크](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> 피어링된 네트워크 간에 이름 확인을 사용할 수 없으므로 IP 주소 지정이 사용됩니다. 기본적으로 HDInsight의 Kafka는 클라이언트 연결 시 IP 주소 대신 호스트 이름을 반환하도록 구성됩니다. 이 문서의 단계에서는 IP 보급을 대신 사용하도록 Kafka를 수정합니다.

## <a name="create-an-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service) 만들기

AKS 클러스터가 아직 없으면 다음 문서 중 하나를 사용하여 클러스터를 만드는 방법을 알아보세요.

* [AKS(Azure Kubernetes Service) 클러스터 배포-포털](../../aks/kubernetes-walkthrough-portal.md)
* [AKS(Azure Kubernetes Service) 클러스터 배포-CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> AKS는 설치 중에 가상 네트워크를 만듭니다. 이 네트워크는 다음 섹션에서 HDInsight용으로 생성되는 네트워크와 피어링됩니다.

## <a name="configure-virtual-network-peering"></a>가상 네트워크 피어링 구성

1. [Azure Portal](https://portal.azure.com)에서 __리소스 그룹__을 선택한 다음, AKS 클러스터용 가상 네트워크를 포함하는 리소스 그룹을 찾습니다. 리소스 그룹 이름은 `MC_<resourcegroup>_<akscluster>_<location>`입니다. `resourcegroup` 및 `akscluster` 항목은 클러스터가 생성된 리소스 그룹의 이름 및 클러스터의 이름입니다. `location`은 클러스터가 생성된 위치입니다.

2. 리소스 그룹에서 __가상 네트워크__ 리소스를 선택합니다.

3. __주소 공간__을 선택합니다. 나열된 주소 공간에 유의합니다.

4. HDInsight용 가상 네트워크를 만들려면 __+ 리소스 만들기__, __네트워킹__을 선택한 다음, __가상 네트워크__를 선택합니다.

    > [!IMPORTANT]  
    > 새 가상 네트워크에 값을 입력할 때 AKS 클러스터 네트워크에 사용되는 값과 겹치지 않는 주소 공간을 사용해야 합니다.

    AKS 클러스터에 사용한 가상 네트워크와 동일한 __위치__를 사용합니다.

    다음 단계로 이동하기 전에 가상 네트워크가 생성될 때까지 기다립니다.

5. HDInsight 네트워크와 AKS 클러스터 네트워크 간에 피어링을 구성하려면 가상 네트워크를 선택한 다음, __피어링__을 선택합니다. __+ 추가__를 선택하고 다음 값을 사용하여 양식에 정보를 입력합니다.

   * __이름__: 피어링 구성에 대한 고유 이름을 입력합니다.
   * __가상 네트워크__: 이 필드를 사용하여 **AKS 클러스터**의 가상 네트워크를 선택합니다.

     다른 필드는 모두 기본값으로 남겨두고 __확인__을 선택하여 피어링을 구성합니다.

6. AKS 클러스터 네트워크와 HDInsight 네트워크 간에 피어링을 구성하려면 __AKS 클러스터 가상 네트워크__를 선택한 다음, __피어링__을 선택합니다. __+ 추가__를 선택하고 다음 값을 사용하여 양식에 정보를 입력합니다.

   * __이름__: 피어링 구성에 대한 고유 이름을 입력합니다.
   * __가상 네트워크__: 이 필드를 사용하여 __HDInsight 클러스터__의 가상 네트워크를 선택합니다.

     다른 필드는 모두 기본값으로 남겨두고 __확인__을 선택하여 피어링을 구성합니다.

## <a name="install-apache-kafka-on-hdinsight"></a>HDInsight에 Apache Kafka 설치

HDInsight 클러스터에 Kafka를 생성할 때 이전에 HDInsight용으로 만든 가상 네트워크에 가입해야 합니다. Kafka 클러스터 만들기에 대한 자세한 내용은 [Apache Kafka 클러스터 만들기](apache-kafka-get-started.md) 문서를 참조하세요.

> [!IMPORTANT]  
> 클러스터를 생성할 때 __고급 설정__을 사용하여 HDInsight용으로 만든 가상 네트워크에 가입해야 합니다.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP 보급 구성

다음 단계에 따라 도메인 이름 대신 IP 주소를 보급하도록 Kafka를 구성합니다.

1. 웹 브라우저를 사용하여 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. __CLUSTERNAME__을 HDInsight 클러스터의 Kafka 이름으로 바꿉니다.

    메시지가 표시되면, 클러스터의 HTTPS 사용자 이름 및 암호를 입력합니다. 클러스터에 대한 Ambari Web UI가 표시됩니다.

2. Kafka에 대한 정보를 보려면 왼쪽 목록에서 __Kafka__를 선택합니다.

    ![Kafka가 강조 표시된 서비스 목록](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Kafka 구성을 보려면 위쪽 가운데에서 __Configs__를 선택합니다.

    ![Kafka에 대한 링크 구성](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. __kafka-env__ 구성을 찾으려면 오른쪽 위에 있는 __필터__ 필드에 `kafka-env`를 입력합니다.

    ![kafka-env의 Kafka 구성](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. IP 주소를 보급하도록 Kafka를 구성하려면 __kafka-env-template__ 맨 아래에 다음 텍스트를 추가합니다.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka에서 수신 대기하는 인터페이스를 구성하려면 오른쪽 위의 __필터__ 필드에 `listeners`를 입력합니다.

7. 모든 네트워크 인터페이스에서 수신 대기하도록 Kafka를 구성하려면 __수신기__ 필드의 값을 `PLAINTEXT://0.0.0.0:9092`로 변경합니다.

8. 구성 변경 내용을 저장하려면 __저장__ 단추를 사용합니다. 변경 내용을 설명하는 텍스트 메시지를 입력합니다. 변경 내용이 저장되면 __확인__을 선택합니다.

    ![구성 저장 단추](./media/apache-kafka-azure-container-services/save-button.png)

9. Kafka를 다시 시작할 때 오류를 방지하려면 __서비스 작업__ 단추를 사용하여 __유지 관리 모드 켜기__를 선택합니다. 확인을 선택하여 이 작업을 완료합니다.

    ![유지 관리 모드 켜기가 강조 표시된 서비스 작업](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Kafka를 다시 시작하려면 __다시 시작__ 단추를 사용하고 __영향 받은 모든 항목 다시 시작__을 선택합니다. 다시 시작을 확인하고 작업이 완료되면 __확인__ 단추를 사용합니다.

    ![영향 받은 모든 항목 다시 시작이 강조 표시된 다시 시작 단추](./media/apache-kafka-azure-container-services/restart-button.png)

11. 유지 관리 모드를 사용하지 않도록 설정하려면 __서비스 작업__ 단추를 사용하고 __유지 관리 모드 끄기__를 선택합니다. **확인**을 선택하여 이 작업을 완료합니다.

## <a name="test-the-configuration"></a>구성 테스트

이제 Kafka와 Azure Kubernetes Service는 피어링된 가상 네트워크를 통해 통신합니다. 이 연결을 테스트하려면 다음 단계를 사용합니다.

1. 테스트 애플리케이션에서 사용되는 Kafka 항목을 만듭니다. Kafka 토픽 만들기에 대한 내용은 [Apache Kafka 클러스터 만들기](apache-kafka-get-started.md) 문서를 참조하세요.

2. [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)에서 예제 애플리케이션을 다운로드합니다.

3. `index.js` 파일을 편집하고 다음 줄을 변경합니다.

    * `var topic = 'mytopic'`: `mytopic`을 이 애플리케이션에 사용되는 Kafka 항목의 이름으로 바꿉니다.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13`을 클러스터의 broker 호스트 중 하나의 내부 IP 주소와 바꿉니다.

        클러스터에서 broker 호스트(작업자 노드)의 내부 IP 주소를 찾으려면 [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) 문서를 참조하세요. 도메인 이름이 `wn`으로 시작하는 항목 중 하나의 IP 주소를 선택합니다.

4. `src` 디렉터리의 명령줄에서 종속성을 설치하고 Docker를 사용하여 배포용 이미지를 빌드합니다.

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > 이 애플리케이션에 필요한 패키지가 리포지토리로 체크인되기 때문에 `npm` 유틸리티를 사용하여 설치할 필요가 없습니다.

5. ACR(Azure Container Registry)에 로그인하여 loginServer 이름을 찾습니다.

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Azure Container Registry 이름을 모르거나 Azure CLI를 통해 Azure Kubernetes Service를 사용하는 것에 익숙하지 않은 경우 [AKS 자습서](../../aks/tutorial-kubernetes-prepare-app.md)를 참조하세요.

6. 로컬 `kafka-aks-test` 이미지에 ACR의 loginServer로 태그를 지정합니다. 또한 끝에 `:v1`을 추가하여 이미지 버전을 나타냅니다.

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. 이미지를 레지스트리로 푸시합니다.

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    이 작업을 완료하는 데 몇 분이 걸립니다.

8. Kubernetes manifest 파일(`kafka-aks-test.yaml`)을 편집하고 `microsoft`를 4단계에서 검색된 ACR loginServer 이름으로 바꿉니다.

9. 다음 명령을 사용하여 매니페스트에서 애플리케이션 설정을 배포합니다.

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. 다음 명령을 사용하여 애플리케이션의 `EXTERNAL-IP`를 감시합니다.

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    외부 IP 주소가 할당되면 __CTRL + C__를 사용하여 감시를 종료합니다.

11. 웹 브라우저를 열고 서비스의 외부 IP 주소를 입력합니다. 다음 이미지와 유사한 페이지가 열립니다.

    ![웹 페이지의 이미지](./media/apache-kafka-azure-container-services/test-web-page.png)

12. 필드에 텍스트를 입력하고 __보내기__ 단추를 선택합니다. 데이터가 Kafka로 전송됩니다. 그러면 애플리케이션의 Kafka 소비자가 메시지를 읽고 이를 __Kafka의 메시지__ 섹션에 추가합니다.

    > [!WARNING]  
    > 메시지의 복사본을 여러 개 받을 수 있습니다. 이 문제는 대개 연결 후 브라우저를 새로 고치거나, 애플리케이션에 대한 브라우저 연결을 여러 개 여는 경우 발생합니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 HDInsight에서 Apache Kafka를 사용하는 방법에 대해 알아봅니다.

* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)

* [MirrorMaker를 사용하여 HDInsight에 Apache Kafka 복제본 만들기](apache-kafka-mirroring.md)

* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)
