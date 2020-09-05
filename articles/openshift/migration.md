---
title: Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션
description: Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: 마이그레이션, aro, openshift, red hat
ms.openlocfilehash: 68e8be24273dfab490d2423b76c372f2ff9f2b38
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513189"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션

OpenShift 4의 Azure Red Hat OpenShift는 Red Hat Core OS, 개인 클러스터에 Kubernetes 1.16를 제공 하 고 사용자 고유의 가상 네트워크 지원 및 전체 클러스터 관리자 역할을 가져옵니다. 또한 이제는 연산자 프레임 워크, 연산자 허브 및 OpenShift 서비스 메시에 대 한 지원과 같은 많은 새로운 기능을 사용할 수 있습니다.

Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 전환 하려면 [저장소, 네트워킹, 로깅, 보안 및 모니터링의 차이점](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)을 검토 해야 합니다.

이 문서에서는 Azure Red Hat OpenShift 3.11 클러스터에서 Azure Red Hat 4 클러스터로 마이그레이션하는 방법을 설명 합니다.

> [!NOTE]
> 제어 평면 마이그레이션 지원 도구 및 클러스터 응용 프로그램 마이그레이션 도구 (CAM)와 같은 red Hat OpenShift 마이그레이션 도구는 Azure Red Hat OpenShift 3.11 클러스터와 함께 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 Azure Red Hat OpenShift 3.11 클러스터가 있다고 가정 합니다.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>대상 Azure Red Hat OpenShift 4 클러스터 만들기

먼저 대상 클러스터로 사용할 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md) . 여기서는 기본 구성을 사용 합니다. 다른 설정에 관심이 있는 경우 [Azure Red Hat OpenShift 4 클러스터 만들기 자습서](tutorial-create-cluster.md)를 참조 하세요.

마스터 및 작업자 노드에 대 한 두 개의 빈 서브넷을 사용 하 여 가상 네트워크를 만듭니다.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

그런 다음, 다음 명령을 사용 하 여 클러스터를 만듭니다.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>대상 OpenShift 4 클러스터 구성

### <a name="authentication"></a>인증

사용자가 Azure Red Hat OpenShift와 상호 작용 하려면 먼저 클러스터에 인증 해야 합니다. 인증 계층은 Azure Red Hat OpenShift API에 대 한 요청과 연결 된 사용자를 식별 합니다. 그러면 권한 부여 계층에서 요청 하는 사용자에 대 한 정보를 사용 하 여 요청이 허용 되는지 확인 합니다.

Azure Red Hat OpenShift 4 클러스터가 만들어지면 임시 관리자가 만들어집니다. [클러스터에 연결](tutorial-connect-cluster.md)하 고, 사용자 및 그룹을 추가 하 고, 둘 다에 대 한 [적절 한 권한을 구성](https://docs.openshift.com/aro/4/authentication/understanding-authentication.html) 합니다.

### <a name="networking"></a>네트워킹

Azure Red Hat OpenShift 4는 클러스터에서 네트워크를 설정 하는 데 [클러스터 네트워크 운영자](https://docs.openshift.com/aro/4/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS 연산자](https://docs.openshift.com/aro/4/networking/dns-operator.html)및 [수신 운영자](https://docs.openshift.com/aro/4/networking/ingress-operator.html)와 같은 몇 가지 연산자를 사용 합니다. Azure Red Hat OpenShift 4 클러스터에서 네트워킹을 설정 하는 방법에 대 한 자세한 내용은 [네트워킹 다이어그램](concepts-networking.md) 및 [네트워킹 이해](https://docs.openshift.com/aro/4/networking/understanding-networking.html)를 참조 하세요.

### <a name="storage"></a>스토리지
Azure Red Hat OpenShift 4는 다음 PersistentVolume 플러그 인을 지원 합니다.

- EBS (AWS 탄력적 블록 저장소)
- Azure 디스크
- Azure 파일
- GCE 영구 디스크
- HostPath
- iSCSI
- 로컬 볼륨
- NFS
- Red Hat OpenShift 컨테이너 저장소

이러한 저장소 유형 구성에 대 한 자세한 내용은 [영구 저장소 구성](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage)을 참조 하세요.

### <a name="registry"></a>레지스트리

Azure Red Hat OpenShift 4는 소스 코드에서 이미지를 빌드하고, 배포 하 고, 해당 수명 주기를 관리할 수 있습니다. 이를 사용 하도록 설정 하기 위해 Azure Red Hat OpenShift는 Azure Red Hat OpenShift 환경에 배포 하 여 이미지를 로컬로 관리할 수 있는 4 개의 [내부 통합 컨테이너 이미지 레지스트리](https://docs.openshift.com/aro/4/registry/registry-options.html) 를 제공 합니다.

[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/), [red hat](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-quay-overview_registry-options), 레지스트리 또는 인증을 사용 하는 [red hat Registry](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)와 같은 외부 레지스트리를 사용 하는 경우 클러스터에서 저장소에 액세스할 수 있도록 클러스터에 자격 증명을 제공 하는 단계를 수행 합니다.

### <a name="monitoring"></a>모니터링

Azure Red Hat OpenShift에는 프로메테우스 오픈 소스 프로젝트 및 더 광범위 한 에코 시스템을 기반으로 하는 미리 구성 되 고 사전 설치 된 자동 업데이트 모니터링 스택이 포함 됩니다. 클러스터 구성 요소에 대 한 모니터링을 제공 하며, 발생 하는 문제와 Grafana 대시보드의 집합에 대해 클러스터 관리자에 게 즉시 알리기 위한 경고 집합을 포함 합니다. 클러스터 모니터링 스택은 Azure Red Hat OpenShift 클러스터 모니터링에만 지원 됩니다. 자세한 내용은 [Azure Red Hat OpenShift에 대 한 클러스터 모니터링](https://docs.openshift.com/aro/4/monitoring/cluster_monitoring/about-cluster-monitoring.html)을 참조 하세요.

[Azure Red Hat openshift 3.11 용 컨테이너에 Azure Monitor](../azure-monitor/insights/container-insights-azure-redhat-setup.md)를 사용한 경우 [Azure Red hat openshift 4 클러스터](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) 의 컨테이너에 대해 Azure Monitor을 사용 하도록 설정 하 고 동일한 Log Analytics 작업 영역을 계속 사용할 수도 있습니다.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>DNS 또는 부하 분산 장치 구성을 새 클러스터로 이동

Azure Traffic Manager을 사용 하는 경우 끝점을 추가 하 여 대상 클러스터를 참조 하 고 이러한 끝점의 우선 순위를 지정 합니다.

## <a name="deploy-application-to-your-target-cluster"></a>대상 클러스터에 응용 프로그램 배포

워크 로드에 대 한 대상 클러스터를 적절히 구성 했으면 [클러스터에 연결](tutorial-connect-cluster.md) 하 고 프로젝트에 필요한 응용 프로그램, 구성 요소 또는 서비스를 만듭니다. Azure Red Hat OpenShift를 사용 하면 Git, 컨테이너 이미지, Red Hat Developer Catalog, Dockerfile, YAML/JSON 정의에서 만들거나 카탈로그에서 데이터베이스 서비스를 선택 하 여 만들 수 있습니다.

## <a name="delete-your-source-cluster"></a>원본 클러스터 삭제
Azure Red Hat OpenShift 4 클러스터가 제대로 설정 되었는지 확인 했으면 Azure Red Hat OpenShift 3.11 클러스터를 삭제 합니다.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>다음 단계
Red Hat이 제공 하는 Azure Red Hat OpenShift 설명서를 [여기](https://docs.openshift.com/aro/4/welcome/index.html)에서 확인 하세요.

