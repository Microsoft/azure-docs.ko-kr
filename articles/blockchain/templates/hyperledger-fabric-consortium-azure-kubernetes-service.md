---
title: Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄
description: Azure Kubernetes Service에서 Hyperledger Fabric consortium 네트워크를 배포 하 고 구성 하는 방법
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 5aed420295fd17cf4e7b26c86e8b84c4687e6545
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029914"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄

AKS (Azure Kubernetes Service) 템플릿에서 HLF (Hyperledger Fabric)를 사용 하 여 Azure에서 하이퍼 원장 Fabric consortium 네트워크를 배포 하 고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 하이퍼 원장 패브릭 및 하이퍼 원장 패브릭 blockchain 네트워크의 빌딩 블록을 구성 하는 다양 한 구성 요소에 대 한 실무 지식을 얻습니다.
- 프로덕션 시나리오를 위해 Azure Kubernetes Service에서 하이퍼 원장 Fabric 컨소시엄을 배포 하 고 구성 하는 방법에 대해 알아봅니다.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger 패브릭 컨소시엄 아키텍처

Azure에서 Hyperledger 패브릭 네트워크를 빌드하려면 피어 노드를 사용 하 여 주문 서비스 및 조직을 배포 해야 합니다. 템플릿 배포의 일부로 생성 되는 다양 한 기본 구성 요소는 다음과 같습니다.

- **Orderer 노드**: 원장에서 트랜잭션 순서 지정을 담당 하는 노드입니다. 다른 노드와 함께, 정렬 된 노드는 Hyperledger 패브릭 네트워크의 주문 서비스를 형성 합니다.

- **피어 노드**: 주로 네트워크의 기본 요소인 원장 및 스마트 계약을 호스트 하는 노드입니다.

- **패브릭 ca**: 패브릭 Ca는 Hyperledger 패브릭에 대 한 Ca (인증 기관)입니다. 패브릭 CA를 사용 하 여 인증 기관을 호스트 하는 서버 프로세스를 초기화 하 고 시작할 수 있습니다. Id 및 인증서를 관리할 수 있습니다. 템플릿의 일부로 배포 된 각 AKS 클러스터에는 기본적으로 패브릭 CA pod가 있습니다.

- **쿠 chdb 또는 leveldb**: 피어 노드에 대 한 세계 상태 데이터베이스를 leveldb 또는 LevelDB는 피어 노드에 포함 된 기본 상태 데이터베이스 이며, chaincode 데이터를 단순 키-값 쌍으로 저장 하 고 키, 키 범위 및 복합 키 쿼리만 지원 합니다. Chaincode 데이터 값이 JSON으로 모델링 될 때 풍부한 쿼리를 지 원하는 대체 상태 데이터베이스 (선택 사항)입니다.

배포의 템플릿은 구독에서 다양 한 Azure 리소스를 회전 합니다. 배포 되는 다양 한 Azure 리소스는 다음과 같습니다.

- **AKS cluster**: 고객이 제공한 입력 매개 변수에 따라 구성 된 Azure Kubernetes cluster입니다. AKS 클러스터에는 하이퍼 원장 패브릭 네트워크 구성 요소를 실행 하기 위해 구성 된 다양 한 pod이 있습니다. 만든 다른 pod는 다음과 같습니다.

  - **패브릭 도구**: 패브릭 도구는 Hyperledger 패브릭 구성 요소를 구성 합니다.
  - **Orderer/피어 pod**: HLF 네트워크의 노드입니다.
  - **프록시**: 클라이언트 응용 프로그램이 AKS 클러스터와 인터페이스 할 수 있는 N워 ix 프록시 pod입니다.
  - **패브릭 ca**: 패브릭 ca를 실행 하는 pod입니다.
- **PostgreSQL**: 패브릭 CA id를 유지 하기 위해 PostgreSQL의 인스턴스가 배포 됩니다.

- **Azure 키 자격 증명 모음**: 키 자격 증명 모음 인스턴스는 패브릭 CA 자격 증명과 고객이 제공한 루트 인증서를 저장 하기 위해 배포 됩니다 .이 인증서는 템플릿 배포 다시 시도 시 사용 되며 템플릿의 메커니즘을 처리 하는 데 사용 됩니다.
- **Azure managed disk**: azure 관리 디스크는 원장 및 피어 노드 세계 상태 데이터베이스용 영구 저장소에 대 한 것입니다.
- **공용 ip**: 클러스터와의 상호 작용을 위해 배포 된 AKS 클러스터의 공용 ip 엔드포인트입니다.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger 패브릭 Blockchain 네트워크 설정

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

다음 단계를 사용 하 여 Hyperledger 패브릭 Blockchain 네트워크를 설정 합니다.

- [Orderer/피어 조직 배포](#deploy-the-ordererpeer-organization)
- [컨소시엄 빌드](#build-the-consortium)
- [네이티브 HLF 작업 실행](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Orderer/피어 조직 배포

HLF 네트워크 구성 요소 배포를 시작 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다. **Azure Kubernetes Service에서 하이퍼 원장 패브릭을**검색 > **리소스 만들기 > blockchain** 을 선택 합니다.

1. **만들기** 를 선택 하 여 템플릿 배포를 시작 합니다. **Azure Kubernetes에서 Hyperledger 패브릭 만들기 서비스가** 표시 됩니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. **기본 사항** 페이지에서 프로젝트 세부 정보를 입력 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 다음 세부 정보를 입력합니다.
    - **구독**: HLF 네트워크 구성 요소를 배포 하려는 구독 이름을 선택 합니다.
    - **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 빈 리소스 그룹을 선택 하면 리소스 그룹에 템플릿의 일부로 배포 된 모든 리소스가 저장 됩니다.
    - **지역**: HLF 구성 요소에 대 한 azure Kubernetes 클러스터를 배포 하려는 azure 지역을 선택 합니다. 이 템플릿은 AKS을 사용할 수 있는 모든 지역에서 사용할 수 있습니다. 구독에서 VM (가상 머신) 할당량 한도에 도달 하지 않는 지역을 선택 해야 합니다.
    - **리소스 접두사**: 배포 되는 리소스의 이름에 대 한 접두사입니다. 리소스 접두사의 길이는 6 자 미만 이어야 하 고 문자 조합에는 숫자와 문자를 모두 포함 해야 합니다.
4. **패브릭 설정** 탭을 선택 하 여 배포 될 HLF 네트워크 구성 요소를 정의 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 다음 세부 정보를 입력합니다.
    - **조직 이름**: 다양 한 데이터 평면 작업에 필요한 패브릭 조직의 이름입니다.
    - **패브릭 네트워크 구성 요소**: 설치 하려는 blockchain 네트워크 구성 요소를 기준으로 정렬 서비스 또는 피어 노드를 선택 합니다.
    - **노드 수** -다음은 두 가지 유형의 노드입니다.
        - 서비스 순서 지정-네트워크에 내결함성을 제공한 노드 수를 선택 합니다. 지원 되는 가져오므로 노드 수는 3, 5, 7만 지원 됩니다.
        - 피어 노드-요구 사항에 따라 1-10 노드를 선택할 수 있습니다.
    - **피어 노드 전 세계 상태 데이터베이스**: leveldb와 사용자가 패브릭 네트워크 구성 요소 드롭다운에서 피어 노드를 선택 하면이 필드가 표시 됩니다.
    - **패브릭 사용자 이름**: 패브릭 CA 인증에 사용 되는 사용자 이름을 입력 합니다.
    - **패브릭 ca 암호**: 패브릭 ca 인증에 대 한 암호를 입력 합니다.
    - **암호 확인**: 패브릭 CA 암호를 확인 합니다.
    - **인증서**: 사용자 고유의 루트 인증서를 사용 하 여 패브릭 ca를 초기화 하려면 패브릭 ca에 루트 인증서 업로드 옵션을 선택 합니다. 그 외의 경우에는 기본적으로 패브릭 ca가 자체 서명 된 인증서를 만듭니다.
    - **루트 인증서**: 패브릭 CA를 초기화 해야 하는 루트 인증서 (공개 키)를 업로드 합니다. Pem 형식의 인증서가 지원 되며, 인증서는 UTC 표준 시간대로 유효 해야 합니다.
    - **루트 인증서 개인 키**: 루트 인증서의 개인 키를 업로드 합니다. 공개 키와 개인 키가 모두 결합 된 pem 인증서가 있는 경우 여기에도 업로드 합니다.


6. **AKS 클러스터 설정** 탭을 선택 하 여 패브릭 네트워크 구성 요소를 설치할 기본 인프라로 Azure Kubernetes 클러스터 구성을 정의 합니다.

    ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 다음 세부 정보를 입력합니다.
    - **Kubernetes cluster name**: 생성 되는 AKS 클러스터의 이름입니다. 이 필드는 제공 된 리소스 접두사에 따라 미리 채워져 있으므로 필요한 경우 변경할 수 있습니다.
    - **Kubernetes version**: 클러스터에 배포할 Kubernetes의 버전입니다. **기본** 탭에서 선택한 지역에 따라 지원 되는 버전을 변경할 수 있습니다.
    - **Dns 접두사**: AKS 클러스터에 대 한 dns (Domain name System) 이름 접두사입니다. 클러스터를 만든 후 컨테이너를 관리할 때 DNS를 사용 하 여 Kubernetes API에 연결 합니다.
    - **노드 크기**: Kubernetes 노드의 크기 이며, Azure에서 사용할 수 있는 VM Sku (재고 보존 유닛) 목록에서 선택할 수 있습니다. 성능을 최적화 하려면 Standard DS3 v2를 권장 합니다.
    - **노드 수**: 클러스터에 배포할 Kubernetes 노드의 수입니다. 이 노드 수를 패브릭 설정에 지정 된 HLF 노드 수 이상 이하로 유지 하는 것이 좋습니다.
    - **서비스 사용자 클라이언트 id**: 기존 서비스 주체의 클라이언트 id를 입력 하거나 AKS 인증에 필요한 새를 만듭니다. [서비스 주체를 만드는](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)단계를 참조 하세요.
    - **서비스 사용자 클라이언트 암호**: 서비스 사용자 클라이언트 ID에 제공 된 서비스 주체의 클라이언트 암호를 입력 합니다.
    - **클라이언트 암호 확인**: 서비스 사용자 클라이언트 암호에 제공 된 클라이언트 암호를 확인 합니다.
    - **컨테이너 모니터링 사용**: AKS 모니터링을 사용 하도록 선택 하 여 AKS 로그를 지정 된 Log Analytics 작업 영역에 푸시할 수 있습니다.
    - **Log Analytics 작업 영역**: 모니터링을 사용 하는 경우 Log Analytics 작업 영역이 생성 된 기본 작업 영역으로 채워집니다.

8. 위의 세부 정보를 모두 제공한 후 **검토 및 만들기** 탭을 선택 합니다. 검토 및 만들기에서는 사용자가 제공한 값에 대 한 유효성 검사를 트리거합니다.
9. 유효성 검사가 통과 되 면 **만들기**를 선택할 수 있습니다.
배포는 일반적으로 10-12 분 정도 걸리며, 지정 된 AKS 노드의 크기와 수에 따라 달라질 수 있습니다.
10. 성공적으로 배포 된 후에는 오른쪽 위 모퉁이에 있는 Azure 알림을 통해 알림을 받습니다.
11. **리소스 그룹으로 이동** 을 선택 하 여 템플릿 배포의 일부로 생성 된 모든 리소스를 확인 합니다. 모든 리소스 이름은 **기본** 설정에 제공 된 접두사로 시작 됩니다.

## <a name="build-the-consortium"></a>컨소시엄 빌드

주문 서비스 및 피어 노드를 배포 하는 블록 체인 consortium 게시물을 빌드하려면 아래 단계를 순서 대로 수행 해야 합니다. **네트워크** 스크립트 (byn.sh)를 빌드하여 컨소시엄을 설정 하 고, 채널을 만들고, chaincode을 설치 하는 데 도움이 됩니다.

> [!NOTE]
> 제공 된 byn (네트워크) 스크립트는 demo/devtest 시나리오에만 사용할 수 있습니다. 프로덕션 등급을 설정 하려면 기본 HLF Api를 사용 하는 것이 좋습니다.

Byn 스크립트를 실행 하는 모든 명령은 Azure Bash CLI (명령줄 인터페이스)를 통해 실행할 수 있습니다. 를 통해 Azure shell 웹 버전에 로그인 할 수 있습니다. ![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 옵션을 Azure Portal 선택 합니다. 명령 프롬프트에서 bash를 입력 하 고를 입력 하 여 bash CLI로 전환 합니다.

자세한 내용은 [Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) 을 참조 하세요.

![Azure Kubernetes의 hyperledger 패브릭 서비스 템플릿](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Byn.sh 및 fabric-admin 파일을 다운로드 합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Azure CLI Bash 셸에서 아래 환경 변수를 설정**합니다.

채널 정보 및 가져오므로 조직 정보 설정

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
피어 조직 정보 설정

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

피어 및 가져오므로 조직 간에 다양 한 공용 인증서를 공유 하기 위해 하나의 Azure 파일 공유를 만듭니다.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**채널 관리 명령**

가져오므로 조직 AKS 클러스터로 이동 하 고 명령을 실행 하 여 새 채널을 만듭니다.

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**컨소시엄 관리 명령**

채널 및 컨소시엄에서 피어 조직을 추가 하려면 지정 된 순서로 아래 명령을 실행 합니다.

1. 피어 조직 AKS 클러스터로 이동 하 여 Azure File Storage에서 해당 구성원 서비스 제공 (MSP)을 업로드 합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. 가져오므로 조직 AKS 클러스터로 이동 하 여 채널 및 컨소시엄에서 피어 조직을 추가 합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. 피어 조직으로 돌아가서 채널에서 피어 노드를 조인 하는 명령을 실행 합니다.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

마찬가지로, 채널에 피어 조직을 더 추가 하려면 필요한 피어 조직에 따라 피어 AKS 환경 변수를 업데이트 하 고 1 ~ 3 단계를 실행 합니다.

**Chaincode 관리 명령**

아래 명령을 실행 하 여 chaincode 관련 작업을 수행 합니다. 이러한 명령은 demo chaincode에서 모든 작업을 수행 합니다. 이 데모 chaincode에는 "a"와 "b" 라는 두 개의 변수가 있습니다. Chaincode를 인스턴스화할 때 "a"는 1000로 초기화 되 고 "b"는 2000으로 초기화 됩니다. Chaincode의 각 호출에서 10 개 단위는 "a"에서 "b"로 전송 됩니다. Chaincode에 대 한 쿼리 작업은 "a" 변수의 세계 상태를 표시 합니다.

피어 조직 AKS 클러스터에서 실행 되는 다음 명령을 실행 합니다.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Chaincode operation 명령**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>네이티브 HLF 작업 실행

고객이 AKS의 HLF 네트워크에서 하이퍼 원장 기본 명령을 실행 하는 작업을 시작 하는 데 도움이 됩니다. 패브릭 NodeJS SDK를 사용 하 여 HLF 작업을 수행 하는 샘플 응용 프로그램이 제공 됩니다. 새 사용자 id를 만들고 사용자 고유의 chaincode를 설치 하기 위한 명령이 제공 됩니다.

### <a name="before-you-begin"></a>시작하기 전에

응용 프로그램의 초기 설정에 대해 아래 명령을 수행 합니다.

- 응용 프로그램 파일 다운로드
- 연결 프로필 및 관리자 프로필 생성
- 관리 사용자 id 가져오기

초기 설정을 완료 한 후 SDK를 사용 하 여 다음과 같은 작업을 수행할 수 있습니다.

- 사용자 id 생성
- Chaincode 작업

위에서 언급 한 명령은 Azure Cloud Shell에서 실행할 수 있습니다.

### <a name="download-application-files"></a>응용 프로그램 파일 다운로드

응용 프로그램을 실행 하기 위한 첫 번째 설정은 폴더에 있는 모든 응용 프로그램 파일을 다운로드 하는 것입니다.

**앱 폴더를 만들고 폴더에 입력 합니다**.

```bash
mkdir app
cd app
```
다음 명령을 실행 하 여 필요한 모든 파일 및 패키지를 다운로드 합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
이 명령은 모든 패키지를 로드 하는 데 시간이 걸립니다. 명령을 성공적으로 실행 한 후에는 현재 디렉터리에서 `node_modules` 폴더를 볼 수 있습니다. 필요한 모든 패키지가 `node_modules` 폴더에 로드 됩니다.

### <a name="generate-connection-profile-and-admin-profile"></a>연결 프로필 및 관리자 프로필 생성

`app` 폴더 안에 `profile` 디렉터리 만들기

```bash
cd app
mkdir ./profile
```
Azure cloud shell에서 이러한 환경 변수 설정

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

아래 명령을 실행 하 여 조직의 연결 프로필 및 관리자 프로필을 생성 합니다.

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

이름 `<orgname>-ccp.json` 및 `<orgname>-admin.json`을 사용 하 여 프로필 폴더에 조직의 연결 프로필 및 관리자 `profile`를 만듭니다.

마찬가지로 각 가져오므로 및 피어 조직에 대해 연결 프로필 및 관리자 프로필을 생성 합니다.


### <a name="import-admin-user-identity"></a>관리 사용자 id 가져오기

마지막 단계는 작은 사람에 게 조직의 관리 사용자 id를 가져오는 것입니다.

```bash
npm run importAdmin -- -o <orgName>

```
위의 명령은 importAdmin을 실행 하 여 관리 사용자 id를 전자 지갑로 가져옵니다. 이 스크립트는 관리자 프로필 `<orgname>-admin.json`에서 관리 id를 읽고 HLF 작업을 실행 하는 데 사용할 수 있는 작은 수로 가져옵니다.

스크립트는 파일 시스템을 사용 하 여 id를 저장 합니다. 연결 프로필에서 ". 작은" 필드에 지정 된 경로에 따라 전자 지갑을 만듭니다. 기본적으로 ". m e x" 필드는 `<orgname>`를 사용 하 여 초기화 됩니다. 즉, id를 저장 하기 위해 현재 디렉터리에 `<orgname>` 라는 폴더가 만들어집니다. 다른 경로에서 전자 지갑을 만들려면 admin 사용자 등록 및 기타 HLF 작업을 실행 하기 전에 연결 프로필에서 ". 작은" 필드를 수정 합니다.

마찬가지로 각 조직에 대 한 관리 사용자 id를 가져옵니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하세요.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>사용자 id 생성

HLF 구성에 대 한 새 사용자 id를 생성 하려면 아래 명령을 지정 된 순서 대로 실행 합니다.

> [!NOTE]
> 사용자 id 생성 단계를 시작 하기 전에 응용 프로그램의 초기 설정이 완료 되었는지 확인 합니다.

Azure cloud shell에서 아래 환경 변수 설정

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

새 사용자 등록 및 등록

새 사용자를 등록 하 고 등록 하려면 registerUser .js를 실행 하는 아래 명령을 실행 합니다. 이는 생성 된 사용자 id를 전자 지갑에 저장 합니다.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 관리자 사용자 id는 새 사용자에 대 한 등록 명령을 실행 하는 데 사용 됩니다. 따라서이 명령을 실행 하기 전에 작은 사용자 id를 사용 해야 합니다. 그렇지 않으면이 명령은 실패 합니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하세요.

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode 작업


> [!NOTE]
> Chaincode 작업을 시작 하기 전에 응용 프로그램의 초기 설정이 완료 되었는지 확인 합니다.

Azure Cloud shell에서 아래 chaincode 특정 환경 변수를 설정 합니다.

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

아래 chaincode 작업을 수행할 수 있습니다.

- Chaincode 설치
- Chaincode 인스턴스화
- Chaincode 호출
- Chaincode 쿼리

### <a name="install-chaincode"></a>Chaincode 설치

아래 명령을 실행 하 여 피어 조직에 chaincode를 설치 합니다.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
`ORGNAME` 환경 변수에 설정 된 조직의 모든 피어 노드에 chaincode를 설치 합니다. 채널에 피어 조직이 두 개 이상 있는 경우 해당 조직 모두에 chaincode를 설치 하려면 각 피어 조직에 대해 개별적으로 명령을 실행 합니다.

다음 단계를 수행 합니다.

- `ORGNAME`을 `<peerOrg1Name>`로 설정 하 고 `installCC` 명령을 실행 합니다.
- `ORGNAME`을 `<peerOrg2Name>`로 설정 하 고 `installCC` 명령을 실행 합니다.

  각 피어 조직에 대해이를 실행 합니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하십시오.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Chaincode 인스턴스화

아래 명령을 실행 하 여 피어에서 chaincode를 인스턴스화합니다.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
각각 `<instantiateFunc>` 및 `<instantiateFuncArgs>`에서 인스턴스화 함수 이름 및 쉼표로 구분 된 인수 목록을 전달 합니다. 예를 들어 [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)에서 chaincode set `"Init"` `<instantiateFunc>`를 인스턴스화하고 빈 문자열 `""`로 `<instantiateFuncArgs>` 합니다.

> [!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행 합니다.
> 트랜잭션이 가져오므로에 성공적으로 제출 되 면 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 따라서 chaincode은 채널의 모든 피어 조직에 있는 모든 피어 노드에서 인스턴스화됩니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하세요.

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Chaincode 호출

아래 명령을 실행 하 여 chaincode 함수를 호출 합니다.

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
각각 `<invokeFunction>` 및 `<invokeFuncArgs>`에서 호출 함수 이름 및 쉼표로 구분 된 인수 목록을 전달 합니다. Fabcar chaincode 예제를 사용 하 여 `"initLedger"` `<invokeFunction>` initLedger 함수 집합을 호출 하 고 `""`를 `<invokeFuncArgs>` 합니다.

> [!NOTE]
> 채널의 한 피어 조직에서 한 번 명령을 실행 합니다.
> 트랜잭션이 가져오므로에 성공적으로 제출 되 면 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 따라서 세계 상태는 채널에 있는 모든 피어 조직의 모든 피어 노드에서 업데이트 됩니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하세요.

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode 쿼리

아래 명령을 실행 하 여 chaincode를 쿼리 합니다.

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

각각 `<queryFunction>` 및 `<queryFuncArgs>`에서 쿼리 함수 이름 및 쉼표로 구분 된 인수 목록을 전달 합니다. Chaincode를 참조로 `fabcar` 하 여 전 세계 상태 집합의 모든 자동차를 `"queryAllCars"` `<queryFunction>` 하 고 `""`로 `<queryArgs>` 쿼리 합니다.

명령에 전달 된 인수에 대 한 자세한 내용은 명령 도움말을 참조 하세요.

```bash
npm run queryCC -- -h

```
