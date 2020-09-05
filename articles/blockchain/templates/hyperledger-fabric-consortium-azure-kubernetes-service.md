---
title: Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄
description: Azure Kubernetes Service에서 Hyperledger Fabric consortium 네트워크를 배포 하 고 구성 하는 방법
ms.date: 08/06/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: d23a0120aafb4dc3e6952b40959a20f9a3456614
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226876"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)의 hyperledger 패브릭 컨소시엄

Azure Kubernetes Service (AKS) 템플릿의 Hyperledger 패브릭을 사용 하 여 Azure에서 하이퍼 원장 Fabric consortium 네트워크를 배포 하 고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 하이퍼 원장 패브릭 및 하이퍼 원장 패브릭 blockchain 네트워크의 빌딩 블록을 구성 하는 구성 요소에 대 한 실무 지식이 있어야 합니다.
- 프로덕션 시나리오를 위해 Azure Kubernetes Service에서 하이퍼 원장 Fabric consortium 네트워크를 배포 하 고 구성 하는 방법을 알아봅니다.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure 블록 체인 솔루션 선택

솔루션 템플릿을 사용 하도록 선택 하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교 합니다.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성 된 blockchain 네트워크 토폴로지를 프로 비전 하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 이 템플릿은 blockchain 네트워크 유형에 대해 계산, 네트워킹 및 저장소 서비스 Microsoft Azure 배포 하 고 구성 합니다. 솔루션 템플릿은 서비스 수준 계약 없이 제공 됩니다. [Microsoft Q&페이지를](/answers/topics/azure-blockchain-workbench.html) 사용 하 여 지원을 받을 수 있습니다.
[Azure Blockchain 서비스](../service/overview.md) | PaaS | Azure Blockchain 서비스 미리 보기는 컨소시엄 Blockchain 네트워크의 대형, 관리 및 관리를 간소화 합니다. PaaS, 컨소시엄 관리 또는 계약 및 트랜잭션 개인 정보를 요구 하는 솔루션에 대해 Azure Blockchain 서비스를 사용 합니다.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain 워크 벤치 미리 보기는 블록 체인 응용 프로그램을 만들고 배포 하 여 다른 조직과 비즈니스 프로세스 및 데이터를 공유 하는 데 도움이 되는 Azure 서비스 및 기능 모음입니다. 블록 체인 솔루션을 프로토타입 하거나 블록 체인 응용 프로그램에 대 한 개념 증명을 위해 Azure Blockchain 워크 벤치를 사용 합니다. Azure Blockchain 워크 벤치는 서비스 수준 계약 없이 제공 됩니다. [Microsoft Q&페이지를](/answers/topics/azure-blockchain-workbench.html) 사용 하 여 지원을 받을 수 있습니다.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger 패브릭 컨소시엄 아키텍처

Azure에서 하이퍼 원장 패브릭 네트워크를 빌드하려면 피어 노드가 포함 된 주문 서비스와 조직을 배포 해야 합니다. Azure Kubernetes 서비스 솔루션 템플릿의 Hyperledger 패브릭을 사용 하 여 주문 노드나 피어 노드를 만들 수 있습니다. 만들려는 각 노드에 대 한 템플릿을 배포 해야 합니다.

템플릿 배포의 일부로 생성 되는 기본 구성 요소는 다음과 같습니다.

- **Orderer 노드**: 원장에서 트랜잭션 순서 지정을 담당 하는 노드입니다. 다른 노드와 함께, 정렬 된 노드는 Hyperledger 패브릭 네트워크의 주문 서비스를 형성 합니다.

- **피어 노드**: 주로 네트워크의 기본 요소인 원장 및 스마트 계약을 호스트 하는 노드입니다.

- **패브릭 ca**: Hyperledger 패브릭에 대 한 CA (인증 기관)입니다. 패브릭 CA를 사용 하 여 인증 기관을 호스트 하는 서버 프로세스를 초기화 하 고 시작할 수 있습니다. Id 및 인증서를 관리할 수 있습니다. 템플릿의 일부로 배포 된 각 AKS 클러스터에는 기본적으로 패브릭 CA pod가 있습니다.

- 예: 피어 노드에 대 한 세계 주 데이터베이스 **입니다.** LevelDB는 피어 노드에 포함 된 기본 상태 데이터베이스입니다. Chaincode 데이터를 단순 키/값 쌍으로 저장 하 고 키, 키 범위 및 복합 키 쿼리만 지원 합니다. Chaincode 데이터 값이 JSON으로 모델링 될 때 풍부한 쿼리를 지 원하는 대체 상태 데이터베이스 (선택 사항)입니다.

배포의 템플릿은 구독에서 다양 한 Azure 리소스를 회전 합니다. 배포 된 Azure 리소스는 다음과 같습니다.

- **AKS cluster**: 고객이 제공한 입력 매개 변수에 따라 구성 된 Azure Kubernetes 서비스 클러스터입니다. AKS 클러스터에는 하이퍼 원장 패브릭 네트워크 구성 요소를 실행 하기 위해 구성 된 다양 한 pod이 있습니다. 만든 pod는 다음과 같습니다.

  - **패브릭 도구**: Hyperledger 패브릭 구성 요소를 구성 하는 도구입니다.
  - **Orderer/피어 pod**: Hyperledger 패브릭 네트워크의 노드입니다.
  - **프록시**: 클라이언트 응용 프로그램에서 AKS 클러스터와 통신할 수 있는 N워 ix 프록시 pod입니다.
  - **패브릭 ca**: 패브릭 ca를 실행 하는 pod입니다.
- **PostgreSQL**: 패브릭 CA id를 유지 관리 하는 데이터베이스 인스턴스입니다.

- **Key vault**: 패브릭 CA 자격 증명과 고객이 제공한 루트 인증서를 저장 하기 위해 배포 된 Azure Key Vault 서비스의 인스턴스입니다. 템플릿 배포를 다시 시도 하는 경우 템플릿 메커니즘을 처리 하려면 자격 증명 모음을 사용 합니다.
- **Managed disk**: 원장 및 피어 노드의 세계 주 데이터베이스에 대 한 영구 저장소를 제공 하는 Azure Managed Disks 서비스의 인스턴스입니다.
- **공용 IP**: 클러스터와의 통신을 위해 배포 된 AKS 클러스터의 끝점입니다.

## <a name="deploy-the-orderer-and-peer-organization"></a>가져오므로 및 피어 조직 배포

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [무료 azure 계정을 만들](https://azure.microsoft.com/free/)수 있습니다.

하이퍼 원장 패브릭 네트워크 구성 요소의 배포를 시작 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다.

1. **리소스**  >  **블록 체인**만들기를 선택한 다음 **Azure Kubernetes Service (미리 보기)에서 hyperledger 패브릭을**검색 합니다.

2. **기본 사항** 탭에서 프로젝트 세부 정보를 입력 합니다.

    ![기본 탭을 보여 주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 다음 세부 정보를 입력합니다.
    - **구독**: Hyperledger 패브릭 네트워크 구성 요소를 배포 하려는 구독 이름을 선택 합니다.
    - **리소스 그룹**: 새 리소스 그룹을 만들거나 비어 있는 기존 리소스 그룹을 선택 합니다. 리소스 그룹은 템플릿의 일부로 배포 된 모든 리소스를 보유 합니다.
    - **지역**: 하이퍼 원장 패브릭 구성 요소에 대 한 Azure Kubernetes 서비스 클러스터를 배포 하려는 azure 지역을 선택 합니다. 이 템플릿은 AKS을 사용할 수 있는 모든 지역에서 사용할 수 있습니다. 구독이 VM (가상 머신) 할당량 제한에 도달 하지 않는 지역을 선택 합니다.
    - **리소스 접두사**: 배포 되는 리소스의 이름에 대 한 접두사를 입력 합니다. 6 자 미만 이어야 하며, 문자 조합에는 숫자와 문자를 모두 포함 해야 합니다.
4. **패브릭 설정** 탭을 선택 하 여 배포 될 hyperledger 패브릭 네트워크 구성 요소를 정의 합니다.

    ![패브릭 설정 탭을 보여 주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 다음 세부 정보를 입력합니다.
    - **조직 이름**: 다양 한 데이터 평면 작업에 필요한 Hyperledger 패브릭 조직의 이름을 입력 합니다. 조직 이름은 배포 마다 고유 해야 합니다.
    - **패브릭 네트워크 구성 요소**: 설정 하려는 blockchain 네트워크 구성 요소를 기준으로 **정렬 서비스** 또는 **피어 노드**를 선택 합니다.
    - **노드 수**: 다음은 두 가지 유형의 노드입니다.
        - **주문 서비스**: 네트워크에 내결함성을 제공할 노드 수를 선택 합니다. 지원 되는 주문 노드 수는 3, 5, 7입니다.
        - **피어 노드**: 요구 사항에 따라 1 ~ 10 개의 노드를 선택할 수 있습니다.
    - **피어 노드 전 세계 상태 데이터베이스**: leveldb와 **패브릭 네트워크 구성 요소** 드롭다운 목록에서 **피어 노드** 를 선택 하면이 필드가 표시 됩니다.
    - **패브릭 ca 사용자 이름**: 패브릭 ca 인증에 사용 되는 사용자 이름을 입력 합니다.
    - **패브릭 ca 암호**: 패브릭 ca 인증에 대 한 암호를 입력 합니다.
    - **암호 확인**: 패브릭 CA 암호를 확인 합니다.
    - **인증서**: 자체 루트 인증서를 사용 하 여 패브릭 ca를 초기화 하려면 **패브릭 ca에 루트 인증서 업로드** 옵션을 선택 합니다. 그렇지 않으면 패브릭 CA가 기본적으로 자체 서명 된 인증서를 만듭니다.
    - **루트 인증서**: 패브릭 CA를 초기화 해야 하는 루트 인증서 (공개 키)를 업로드 합니다. Pem 형식의 인증서가 지원 됩니다. 인증서는 유효 하며 UTC 표준 시간대에 있어야 합니다.
    - **루트 인증서 개인 키**: 루트 인증서의 개인 키를 업로드 합니다. 공용 키와 개인 키를 결합 한. pem 인증서가 있는 경우 여기에도 업로드 합니다.


6. **AKS 클러스터 설정** 탭을 선택 하 여 하이퍼 원장 패브릭 네트워크 구성 요소가 설정 되는 기본 인프라의 Azure Kubernetes Service 클러스터 구성을 정의 합니다.

    ![A K S 클러스터 설정 탭을 보여 주는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 다음 세부 정보를 입력합니다.
    - **Kubernetes cluster name**: 필요한 경우 AKS 클러스터의 이름을 변경 합니다. 이 필드는 제공 된 리소스 접두사에 따라 미리 채워져 있습니다.
    - **Kubernetes version**: 클러스터에 배포할 Kubernetes의 버전을 선택 합니다. **기본** 탭에서 선택한 지역에 따라 지원 되는 버전을 변경할 수 있습니다.
    - **Dns 접두사**: AKS 클러스터에 대 한 Dns (Domain name System) 이름 접두사를 입력 합니다. 클러스터를 만든 후 컨테이너를 관리할 때 DNS를 사용 하 여 Kubernetes API에 연결 합니다.
    - **노드 크기**: Kubernetes 노드의 크기에 대해 Azure에서 사용할 수 있는 VM sku (재고 유지 단위) 목록에서 선택할 수 있습니다. 성능을 최적화 하려면 Standard DS3 v2를 권장 합니다.
    - **노드 수**: 클러스터에 배포할 Kubernetes 노드 수를 입력 합니다. 이 노드 수를 **패브릭 설정** 탭에 지정 된 Hyperledger 패브릭 노드 수와 동일 하 게 유지 하는 것이 좋습니다.
    - **서비스 사용자 클라이언트 id**: 기존 서비스 주체의 클라이언트 id를 입력 하거나 새 서비스 주체의 클라이언트 id를 만듭니다. AKS 인증에는 서비스 주체가 필요 합니다. [서비스 주체를 만드는 단계](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)를 참조 하세요.
    - **서비스 사용자 클라이언트 암호**: 서비스 사용자의 클라이언트 ID에 제공 된 서비스 주체의 클라이언트 암호를 입력 합니다.
    - **클라이언트 암호 확인**: 서비스 사용자에 대 한 클라이언트 암호를 확인 합니다.
    - **컨테이너 모니터링 사용**: AKS 모니터링을 사용 하도록 선택 하 여 AKS 로그를 지정 된 Log Analytics 작업 영역에 푸시할 수 있습니다.
    - **Log Analytics 작업 영역**: 모니터링을 사용 하는 경우 Log Analytics 작업 영역이 생성 된 기본 작업 영역으로 채워집니다.

8. **검토 및 만들기** 탭을 선택 합니다. 이 단계에서는 사용자가 제공한 값에 대 한 유효성 검사를 트리거합니다.
9. 유효성 검사를 통과 한 후 **만들기**를 선택 합니다.

    배포에는 일반적으로 10 ~ 12 분이 소요 됩니다. 시간은 지정 된 AKS 노드의 크기와 수에 따라 달라질 수 있습니다.
10. 성공적으로 배포 된 후에는 오른쪽 위 모퉁이에 있는 Azure 알림을 통해 알림을 받습니다.
11. **리소스 그룹으로 이동** 을 선택 하 여 템플릿 배포의 일부로 생성 된 모든 리소스를 확인 합니다. 모든 리소스 이름은 **기본** 탭에 제공 된 접두사로 시작 됩니다.

## <a name="build-the-consortium"></a>컨소시엄 빌드

주문 서비스 및 피어 노드를 배포한 후 blockchain 컨소시엄을 빌드하려면 다음 단계를 순서 대로 수행 합니다. Azure Hyperledger Fabric 스크립트 (azhlf)를 사용 하면 컨소시엄을 설정 하 고, 채널을 만들고, chaincode 작업을 수행할 수 있습니다.

> [!NOTE]
> Azure Hyperledger Fabric (azhlf) 스크립트가 추가 기능을 제공 하도록 업데이트 되었습니다. 이전 스크립트를 참조 하려면 [GitHub의 추가 정보](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)를 참조 하세요. 이 스크립트는 Azure Kubernetes 서비스 템플릿 버전 2.0.0 이상에서 Hyperledger 패브릭과 호환 됩니다. 배포 버전을 확인 하려면 [문제 해결](#troubleshoot)의 단계를 따르세요.

> [!NOTE]
> 이 스크립트는 데모, 개발 및 테스트 시나리오에만 도움이 되도록 제공 됩니다. 이 스크립트에 생성 되는 채널과 컨소시엄에는 데모, 개발 및 테스트 시나리오를 간소화 하는 기본 Hyperledger 패브릭 정책이 있습니다. 프로덕션 설정의 경우 기본 Hyperledger 패브릭 Api를 사용 하 여 조직의 규정 준수 요구 사항에 따라 channel/consortium Hyperledger 패브릭 정책을 업데이트 하는 것이 좋습니다.


Azure Hyperledger Fabric 스크립트를 실행 하는 모든 명령은 Azure Bash CLI (명령줄 인터페이스)를 통해 실행할 수 있습니다. ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure Portal의 오른쪽 위 모퉁이에 있는 Azure Kubernetes 서비스 템플릿의 하이퍼 원장 패브릭 옵션을 통해 Azure Cloud Shell에 로그인 할 수 있습니다. 명령 프롬프트에서를 입력 하 `bash` 고 Enter 키를 선택 하 여 BASH CLI로 전환 하거나 Cloud Shell 도구 모음에서 **bash** 를 선택 합니다.

자세한 내용은 [Azure Cloud Shell](../../cloud-shell/overview.md) 를 참조 하세요.

![Azure Cloud Shell의 명령을 표시 하는 스크린샷](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


다음 이미지는 가져오므로 조직과 피어 조직 간에 컨소시엄을 빌드하는 단계별 프로세스를 보여 줍니다. 다음 섹션에서는 이러한 단계를 완료 하는 자세한 명령을 보여 줍니다.

![컨소시엄을 빌드하는 프로세스의 다이어그램입니다.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

초기 설정을 완료 한 후 클라이언트 응용 프로그램을 사용 하 여 다음 작업을 수행 합니다.  

- 채널 관리
- 컨소시엄 관리
- Chaincode 관리

### <a name="download-client-application-files"></a>클라이언트 응용 프로그램 파일 다운로드

첫 번째 설정은 클라이언트 응용 프로그램 파일을 다운로드 하는 것입니다. 다음 명령을 실행 하 여 필요한 모든 파일 및 패키지를 다운로드 합니다.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

이러한 명령은 공용 GitHub 리포지토리에서 Azure Hyperledger 패브릭 클라이언트 응용 프로그램 코드를 복제 한 다음 모든 종속 npm 패키지를 로드 합니다. 명령을 성공적으로 실행 한 후에는 현재 디렉터리에서 node_modules 폴더를 볼 수 있습니다. 필요한 모든 패키지가 node_modules 폴더에 로드 됩니다.

### <a name="set-up-environment-variables"></a>환경 변수 설정

모든 환경 변수는 Azure 리소스 명명 규칙을 따릅니다.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>가져오므로 조직의 클라이언트에 대 한 환경 변수 설정

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>피어 조직의 클라이언트에 대 한 환경 변수 설정

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

컨소시엄의 피어 조직 수에 따라 피어 명령을 반복 하 고 환경 변수를 적절 하 게 설정 해야 할 수 있습니다.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Azure storage 계정에 대 한 환경 변수 설정

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

다음 명령을 사용 하 여 Azure storage 계정을 만듭니다. Azure storage 계정이 이미 있는 경우이 단계를 건너뜁니다.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

다음 명령을 사용 하 여 Azure 저장소 계정에서 파일 공유를 만듭니다. 파일 공유가 이미 있는 경우이 단계를 건너뜁니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

다음 명령을 사용 하 여 Azure 파일 공유에 대 한 연결 문자열을 생성 합니다.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>조직 연결 프로필, 관리 사용자 id 및 MSP 가져오기

다음 명령을 사용 하 여 Azure Kubernetes Service 클러스터에서 조직의 연결 프로필, 관리 사용자 id 및 MSP (관리 서비스 공급자)를 인출 하 고 이러한 id를 클라이언트 응용 프로그램의 로컬 저장소에 저장 합니다. 로컬 저장소의 예로는 *azhlfTool/stores* 디렉터리가 있습니다.

가져오므로 조직의 경우:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

피어 조직의 경우:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>채널 만들기

가져오므로 조직의 클라이언트에서 다음 명령을 사용 하 여 가져오므로 조직을 포함 하는 채널을 만듭니다.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>컨소시엄 관리를 위한 피어 조직 추가

>[!NOTE]
> 컨소시엄 작업을 시작 하기 전에 클라이언트 응용 프로그램의 초기 설정을 완료 했는지 확인 합니다.  

채널 및 컨소시엄에서 피어 조직을 추가 하려면 지정 된 순서로 다음 명령을 실행 합니다. 

1.  피어 조직의 클라이언트에서 Azure Storage에 대 한 피어 조직의 MSP를 업로드 합니다.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  가져오므로 조직의 클라이언트에서 Azure Storage에서 피어 조직의 MSP를 다운로드 합니다. 그런 다음 명령을 실행 하 여 채널 및 컨소시엄에서 피어 조직을 추가 합니다.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  가져오므로 조직의 클라이언트에서이 연결 프로필을 사용 하 여 피어 조직이 가져오므로 노드에 연결할 수 있도록 Azure Storage에 가져오므로의 연결 프로필을 업로드 합니다.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  피어 조직의 클라이언트에서 orderer의 연결 프로필을 Azure Storage에서 다운로드 합니다. 그런 다음 명령을 실행 하 여 채널에서 피어 노드를 추가 합니다.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

마찬가지로, 채널에 피어 조직을 더 추가 하려면 필요한 피어 조직에 따라 피어 환경 변수를 업데이트 하 고 1 ~ 4 단계를 다시 실행 합니다.

### <a name="set-anchor-peers"></a>앵커 피어 설정

피어 조직의 클라이언트에서 명령을 실행 하 여 지정 된 채널의 피어 조직에 대 한 앵커 피어를 설정 합니다.

>[!NOTE]
> 이 명령을 실행 하기 전에 컨소시엄 관리 명령을 사용 하 여 채널에 피어 조직을 추가 해야 합니다.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` 는 앵커 피어로 설정할 피어 노드의 공백으로 구분 된 목록입니다. 예:

  - `<anchorPeersList>` `"peer1"` Peer1 노드만 앵커 피어로 설정 하려는 경우로 설정 합니다.
  - `<anchorPeersList>` `"peer1" "peer3"` Peer1 및 peer3 노드를 앵커 피어로 설정 하려는 경우로 설정 합니다.

## <a name="chaincode-management-commands"></a>Chaincode 관리 명령

>[!NOTE]
> Chaincode 작업을 시작 하기 전에 클라이언트 응용 프로그램의 초기 설정을 완료 했는지 확인 합니다.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Chaincode 환경 변수 설정

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Chaincode 설치  

다음 명령을 실행 하 여 피어 조직에 chaincode를 설치 합니다.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
명령은 환경 변수에 설정 된 피어 조직의 모든 피어 노드에 chaincode를 설치 `ORGNAME` 합니다. 두 개 이상의 피어 조직이 채널에 있고 chaincode를 모두 설치 하려면 각 피어 조직에 대해이 명령을 별도로 실행 합니다.  

다음 단계를 수행합니다.  

1.  `ORGNAME` `USER_IDENTITY` 에 따라 및을 설정 하 `peerOrg1` 고 `./azhlf chaincode install` 명령을 실행 합니다.  
2.  `ORGNAME` `USER_IDENTITY` 에 따라 및을 설정 하 `peerOrg2` 고 `./azhlf chaincode install` 명령을 실행 합니다.  

### <a name="instantiate-chaincode"></a>Chaincode 인스턴스화  

피어 클라이언트 응용 프로그램에서 다음 명령을 실행 하 여 채널에서 chaincode를 인스턴스화합니다.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

각각 및에서 인스턴스화 함수 이름과 공백으로 구분 된 인수 목록을 전달 `<instantiateFunc>` `<instantiateFuncArgs>` 합니다. 예를 들어 chaincode_example02를 인스턴스화하려면를로 설정 하 고를로 설정 합니다. `<instantiateFunc>` `init` `<instantiateFuncArgs>` `"a" "2000" "b" "1000"`

플래그를 사용 하 여 컬렉션의 구성 JSON 파일을 전달할 수도 있습니다 `--collections-config` . 또는 `-t` 전용 트랜잭션에 사용 되는 chaincode를 인스턴스화하는 동안 플래그를 사용 하 여 임시 인수를 설정 합니다.

예:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`파트는 개인 데이터 chaincode의 인스턴스화에 대해 정의 된 컬렉션을 포함 하는 JSON 파일에 대 한 경로입니다. 다음 경로에서 *azhlfTool* 디렉터리에 상대적인 샘플 컬렉션의 구성 JSON 파일을 찾을 수 있습니다 `./samples/chaincode/src/private_marbles/collections_config.json` .
`<transientArgs>`유효한 JSON을 문자열 형식으로 전달 합니다. 특수 문자를 이스케이프 합니다. 예: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> 채널의 한 피어 조직에서 명령을 한 번 실행 합니다. 트랜잭션이 가져오므로에 성공적으로 전송 된 후 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 그런 다음 Chaincode은 채널에 있는 모든 피어 조직의 모든 피어 노드에서 인스턴스화됩니다.  

### <a name="invoke-chaincode"></a>Chaincode 호출  

피어 조직의 클라이언트에서 다음 명령을 실행 하 여 chaincode 함수를 호출 합니다.  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

각각 및에서 호출 함수 이름과 공백으로 구분 된 인수 목록을 전달  `<invokeFunction>`    `<invokeFuncArgs>`   합니다. Chaincode_example02로 계속 진행 하 여 호출 작업을 수행 하  `<invokeFunction>`    `invoke`   고를  `<invokeFuncArgs>`   로 설정 합니다. `"a" "b" "10"`  

>[!NOTE]
> 채널의 한 피어 조직에서 명령을 한 번 실행 합니다. 트랜잭션이 가져오므로에 성공적으로 전송 된 후 가져오므로는이 트랜잭션을 채널의 모든 피어 조직에 배포 합니다. 그러면 세계 상태가 채널에 있는 모든 피어 조직의 모든 피어 노드에서 업데이트 됩니다.  


### <a name="query-chaincode"></a>Chaincode 쿼리  

다음 명령을 실행 하 여 chaincode를 쿼리 합니다.  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
보증 피어는 chaincode가 설치 되 고 트랜잭션 실행을 위해 호출 되는 피어입니다. `<endorsingPeers>`현재 피어 조직의 피어 노드 이름을 포함 하도록를 설정 해야 합니다. 지정 된 chaincode 및 채널 조합에 대 한 보증 피어를 공백으로 구분 하 여 나열 합니다. 예: `-p "peer1" "peer3"`

*AzhlfTool* 를 사용 하 여 chaincode를 설치 하는 경우 피어 노드 이름을 보증 피어 인수에 값으로 전달 합니다. Chaincode는 해당 조직의 모든 피어 노드에 설치 됩니다. 

각각 및에서 쿼리 함수 이름과 공백으로 구분 된 인수 목록을 전달  `<queryFunction>`    `<queryFuncArgs>`   합니다. 다시 chaincode_example02를 참조로 이동 하 여 전 세계 상태에서 "a" 값을  `<queryFunction>` 쿼리하고를로 설정 합니다.    `query`  `<queryArgs>` `"a"`  

## <a name="troubleshoot"></a>문제 해결

다음 명령을 실행 하 여 템플릿 배포의 버전을 찾습니다.

템플릿이 배포 된 리소스 그룹에 따라 환경 변수를 설정 합니다.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
다음 명령을 실행 하 여 템플릿 버전을 인쇄 합니다.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Blockchain 엔지니어링 팀에서 blockchain 서비스 제공 및 정보를 최신 상태로 유지 하려면 [Azure blockchain 블로그](https://azure.microsoft.com/blog/topics/blockchain/)를 방문 하세요.

제품 피드백을 제공하거나 새로운 기능을 요청하려면 [블록체인에 대한 Azure 피드백 포럼](https://aka.ms/blockchainuservoice)을 통해 아이디어를 게시하거나 투표하세요.

### <a name="community-support"></a>커뮤니티 지원

Microsoft 엔지니어와 Azure Blockchain 커뮤니티 전문가와의 협력:

- [Microsoft Q&페이지](/answers/topics/azure-blockchain-workbench.html) 
   
  블록 체인 템플릿에 대 한 엔지니어링 지원은 배포 문제로 제한 됩니다.
- [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
