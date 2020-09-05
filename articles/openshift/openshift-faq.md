---
title: Azure Red Hat OpenShift에 대 한 질문과 대답
description: Microsoft Azure Red Hat OpenShift에 대 한 일반적인 질문에 대 한 답변은 다음과 같습니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a8b5ec48b64341ad9eabd087d7ee20bb703198c6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816238"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

이 문서에서는 Red Hat OpenShift Microsoft Azure에 대 한 Faq (질문과 대답)에 답변 합니다.

## <a name="installation-and-upgrade"></a>설치 및 업그레이드

### <a name="which-azure-regions-are-supported"></a>지원 되는 Azure 지역은 무엇 인가요?

Azure Red Hat OpenShift 4.x에 대해 지원 되는 지역 목록은 [사용 가능한 지역](https://docs.openshift.com/aro/4/welcome/index.html#available-regions)을 참조 하세요.

Azure Red Hat OpenShift 3.11에 대해 지원 되는 지역 목록은 [지역별 사용 가능한 제품](supported-resources.md#azure-regions)을 참조 하세요.

### <a name="what-virtual-machine-sizes-can-i-use"></a>어떤 가상 머신 크기를 사용할 수 있나요?

Azure Red Hat OpenShift 4에 대해 지원 되는 가상 머신 크기 목록은 [Azure Red Hat openshift 4에 대해 지원 되는 리소스](support-policies-v4.md)를 참조 하세요.

Azure Red Hat OpenShift 3.11에 대해 지원 되는 가상 머신 크기 목록은 [Azure Red Hat openshift 3.11에 대해 지원 되는 리소스](supported-resources.md)를 참조 하세요.

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 클러스터에서 pod의 최대 수는 얼마 인가요?  Azure Red Hat OpenShift에서 노드당 최대 pod 수는 얼마 인가요?

지원 되는 실제 pod 수는 응용 프로그램의 메모리, CPU 및 저장소 요구 사항에 따라 달라 집니다.

Azure Red Hat OpenShift 4.x에는 250 pod 노드당 제한 및 100 계산 노드 제한이 있습니다. 클러스터에서 지원 되는 최대 pod 수는 250 &times; 100 = 25000입니다.

Azure Red Hat OpenShift 3.11에는 50 pod 노드당 제한 및 20 개의 계산 노드 제한이 있습니다. 이는 클러스터에서 지원 되는 최대 pod 수를 50 &times; 20 = 1000로 표시 합니다.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>클러스터가 여러 Azure 지역에 계산 노드를 가질 수 있나요?

아니요. Azure Red Hat OpenShift 클러스터의 모든 노드는 동일한 Azure 지역에 있어야 합니다.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>클러스터를 여러 가용성 영역에 배포할 수 있나요?

예. 이는 클러스터를 가용성 영역을 지 원하는 Azure 지역에 배포 하는 경우 자동으로 발생 합니다. 자세한 내용은 [가용성 영역](../availability-zones/az-overview.md#availability-zones)을 참조하세요.

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)를 사용 하는 경우 제어 평면 노드가 추상화 되어 있나요?

아니요. 클러스터 마스터 노드를 비롯 한 모든 리소스는 고객 구독에서 실행 됩니다. 이러한 유형의 리소스는 읽기 전용 리소스 그룹에 배치 됩니다.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>클러스터가 고객 구독에 상주 합니까? 

Azure 관리 되는 응용 프로그램은 고객 구독과 함께 잠긴 리소스 그룹에 상주 합니다. 고객은 해당 리소스 그룹에서 개체를 볼 수 있지만 수정할 수는 없습니다.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>다른 고객과 공유 하는 Azure Red Hat OpenShift 요소가 있나요? 또는 모두 독립적 입니까?

각 Azure Red Hat OpenShift 클러스터는 특정 고객을 전담 하며 고객의 구독 내에 있습니다. 

### <a name="are-infrastructure-nodes-available"></a>인프라 노드를 사용할 수 있나요?

Azure Red Hat OpenShift 4.x 클러스터에서 인프라 노드는 현재 사용할 수 없습니다.

Azure Red Hat OpenShift 3.11 클러스터에서 인프라 노드는 기본적으로 포함 되어 있습니다.

## <a name="how-do-i-handle-cluster-upgrades"></a>클러스터 업그레이드를 처리 어떻게 할까요??

업그레이드, 유지 관리 및 지원 되는 버전에 대 한 자세한 내용은 [지원 주기 가이드](support-lifecycle.md)를 참조 하세요.

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>호스트 운영 체제 및 OpenShift 소프트웨어는 어떻게 업데이트 되나요?

Azure Red Hat OpenShift는 업스트림 OpenShift Container Platform의 부 릴리스 버전 및 패치를 사용 하므로 호스트 운영 체제 및 OpenShift 소프트웨어가 업데이트 됩니다.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>업데이트 된 노드를 다시 부팅 하는 프로세스는 무엇 인가요?

업그레이드의 일부로 노드가 다시 부팅 됩니다.

## <a name="cluster-operations"></a>클러스터 작업

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>프로메테우스를 사용 하 여 내 응용 프로그램을 모니터링할 수 있나요?

프로메테우스는 Azure Red Hat OpenShift 4.x 클러스터에 대해 미리 설치 되 고 구성 됩니다. [클러스터 모니터링](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html)에 대해 자세히 알아보세요.

Azure Red Hat OpenShift 3.11 클러스터의 경우 네임 스페이스에 프로메테우스를 배포 하 고 네임 스페이스에서 응용 프로그램을 모니터링할 수 있습니다. 자세한 내용은 [Azure Red Hat OpenShift 클러스터에 프로메테우스 인스턴스 배포](howto-deploy-prometheus.md)를 참조 하세요.

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>프로메테우스를 사용 하 여 클러스터 상태 및 용량과 관련 된 메트릭을 모니터링할 수 있나요?

Azure Red Hat OpenShift 4.x: 예.

Azure Red Hat OpenShift 3.11: 아니요.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>기본 Vm의 로그를 고객 로그 분석 시스템으로 스트리밍할 수 있나요?

기본 Vm의 로그는 관리 서비스에서 처리 되며 고객에 게 노출 되지 않습니다.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>사용자가 노드 수준에서 CPU/메모리와 같은 메트릭에 액세스 하 여 크기를 조정 하 고 문제를 디버그 하는 작업을 수행 하는 방법 Azure Red Hat OpenShift 클러스터에서 kubectl top을 실행할 수 없습니다.

Azure Red Hat OpenShift 4.x 클러스터의 경우 OpenShift 웹 콘솔에는 노드 수준의 모든 메트릭이 포함 됩니다. 자세한 내용은 [클러스터 정보 보기](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html)에 대 한 Red Hat 설명서를 참조 하세요.

Azure Red Hat OpenShift 3.11 클러스터의 경우 고객은 명령 `oc adm top nodes` 또는 `kubectl top nodes` 고객 관리 클러스터 역할을 사용 하 여 노드 수준에서 CPU/메모리 메트릭에 액세스할 수 있습니다. 또한 `pods` 또는 명령을 사용 하 여의 CPU/메모리 메트릭에 액세스할 수 `oc adm top pods` 있습니다 `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>배포를 강화 하는 경우 Azure 장애 도메인을 pod 배치로 매핑하여 서비스에 대 한 모든 pod 단일 장애 도메인의 오류로 인해 끊으면 되지 않도록 할 수 있나요?

Azure에서 가상 머신 확장 집합을 사용 하는 경우 기본적으로 5 개의 장애 도메인이 있습니다. 확장 집합의 각 가상 머신 인스턴스는 이러한 장애 도메인 중 하나에 배치 됩니다. 이렇게 하면 클러스터의 계산 노드에 배포 된 응용 프로그램이 별도의 오류 도메인에 배치 됩니다.

자세한 내용은 [가상 머신 확장 집합의 올바른 장애 도메인 수 선택](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)을 참조 하세요.

### <a name="is-there-a-way-to-manage-pod-placement"></a>Pod 배치를 관리 하는 방법이 있나요?

고객은 노드를 가져오고 레이블을 고객 관리로 볼 수 있습니다. 이렇게 하면 확장 집합의 모든 VM을 대상으로 지정할 수 있습니다.

특정 레이블을 사용 하는 경우 주의 해야 합니다.

- 호스트 이름을 사용 하지 않아야 합니다. 호스트 이름은 업그레이드 및 업데이트로 자주 회전 되며 변경 될 수 있습니다.
- 고객이 특정 레이블이나 배포 전략에 대 한 요청을 수행 하는 경우이 작업은 가능 하지만 엔지니어링 노력이 필요 하며 현재 지원 되지 않습니다.

자세한 내용은 [pod 배치 제어](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html)를 참조 하세요.

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>이미지 레지스트리는 외부에서 사용할 수 있으므로 Jenkins와 같은 도구를 사용할 수 있나요?

4.x 클러스터의 경우 보안 레지스트리를 노출 하 고 인증을 구성 해야 합니다. 자세한 내용은 다음 Red Hat 설명서를 참조 하세요.

- [레지스트리 노출](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [레지스트리에 액세스](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

3.11 클러스터의 경우 Docker 이미지 레지스트리를 사용할 수 있습니다. Docker 레지스트리는에서 사용할 수 있습니다 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Azure Container Registry를 사용할 수도 있습니다.

## <a name="networking"></a>네트워킹

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>클러스터를 기존 가상 네트워크에 배포할 수 있나요?

4.x 클러스터에서 기존 VNet에 클러스터를 배포할 수 있습니다.

3.11 클러스터에서는 기존 VNet에 클러스터를 배포할 수 없습니다. 피어 링을 통해 Azure Red Hat OpenShift 3.11 클러스터를 기존 VNet에 연결할 수 있습니다.

### <a name="is-cross-namespace-networking-supported"></a>네임 스페이스 간 네트워킹이 지원 되나요?

고객 및 개별 프로젝트 관리자는 개체를 사용 하 여 프로젝트 별로 상호 네임 스페이스 네트워킹 (거부 포함)을 사용자 지정할 수 있습니다 `NetworkPolicy` .

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>다른 구독의 가상 네트워크에 피어 링 하려고 했지만 VNet CIDR 오류를 가져오지 못했습니다.

가상 네트워크가 있는 구독에서 `Microsoft.ContainerService` 다음 명령을 사용 하 여 공급자를 등록 해야 합니다. `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>사설 VNet에서 배포 하기 위한 IP 범위를 지정 하 여 다른 회사 Vnet 피어 링 한 후 충돌을 방지할 수 있나요?

4.x 클러스터에서 고유한 IP 범위를 지정할 수 있습니다.

3.11 클러스터에서 Azure Red Hat OpenShift는 VNet 피어 링을 지원 하며, 고객이 피어 링을 제공 하 고 OpenShift 네트워크가 작동 하는 VNet CIDR을 제공할 수 있도록 합니다.

Azure Red Hat OpenShift에서 만든 VNet은 보호 되 고 구성 변경을 허용 하지 않습니다. 피어 링 된 VNet은 고객에 의해 제어 되며 구독에 상주 합니다.

### <a name="is-the-software-defined-network-module-configurable"></a>소프트웨어 정의 네트워크 모듈을 구성할 수 있나요?

소프트웨어 정의 네트워크는 이며 `openshift-ovs-networkpolicy` 구성할 수 없습니다.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift에서 사용 되는 Azure 부하 분산 장치는 무엇 인가요?  Standard 또는 Basic 이며 구성 가능 한가요?

Azure Red Hat OpenShift는 표준 Azure Load Balancer를 사용 하며 구성할 수 없습니다.

## <a name="permissions"></a>사용 권한

### <a name="can-an-admin-manage-users-and-quotas"></a>관리자가 사용자 및 할당량을 관리할 수 있나요?

예. Azure Red Hat OpenShift 관리자는 사용자가 만든 모든 프로젝트에 액세스 하는 것 외에도 사용자 및 할당량을 관리할 수 있습니다.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>클러스터를 특정 Azure AD 사용자로만 제한할 수 있나요?

예. Azure ad 응용 프로그램을 구성 하 여 클러스터에 로그인 할 수 있는 Azure AD 사용자를 제한할 수 있습니다. 자세한 내용은 [방법: 사용자 집합으로 앱 제한](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)을 참조 하세요.

### <a name="can-i-restrict-users-from-creating-projects"></a>사용자가 프로젝트를 만들지 못하게 제한할 수 있나요?

예. 관리자 권한으로 클러스터에 로그인 하 고 다음 명령을 실행 합니다.

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

자세한 내용은 OpenShift 설명서에서 클러스터 버전에 대 한 자체 프로 비전 비활성화를 참조 하세요.

- [4.3 클러스터에서 자체 프로 비전 사용 안 함](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [3.11 클러스터에서 자체 프로 비전 사용 안 함](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>마스터/인프라/앱 노드에 사용할 수 있는 UNIX 권한 (IaaS)은 무엇 인가요?

4.x 클러스터의 경우 클러스터 관리자 역할을 통해 노드 액세스를 사용할 수 있습니다. 자세한 내용은 [RBAC 개요](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)를 참조 하세요.

3.11 클러스터의 경우 노드 액세스는 사용할 수 없습니다.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>어떤 OCP 권한이 있나요? 클러스터 관리자 인가요? 프로젝트 관리자 인가요?

4.x 클러스터의 경우 클러스터 관리자 역할을 사용할 수 있습니다. 자세한 내용은 [RBAC 개요](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html)를 참조 하세요.

3.11 클러스터는 [클러스터 관리 개요](https://docs.openshift.com/aro/admin_guide/index.html) 에서 자세한 내용을 참조 하세요.

### <a name="which-identity-providers-are-available"></a>사용할 수 있는 id 공급자는 무엇입니까?

4.x 클러스터의 경우 고유한 id 공급자를 구성 합니다. 자세한 내용은 [id prodivers 구성](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html)에 대 한 Red Hat 설명서를 참조 하세요.

3.11 클러스터의 경우 Azure AD 통합을 사용할 수 있습니다. 

## <a name="storage"></a>Storage

### <a name="is-data-on-my-cluster-encrypted"></a>내 클러스터의 데이터가 암호화 되나요?

기본적으로 데이터는 미사용 시 암호화 됩니다. Azure Storage 플랫폼은 데이터를 유지 하기 전에 자동으로 암호화 하 고 검색 하기 전에 데이터를 해독 합니다. 자세한 내용은 [미사용 데이터에 대 한 서비스 암호화 Azure Storage](../storage/common/storage-service-encryption.md)를 참조 하세요.

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에서 암호화 된 데이터가 etcd에 저장 되나요?

Azure Red Hat OpenShift 4 클러스터의 경우 데이터는 기본적으로 암호화 되지 않지만 암호화를 사용 하도록 설정 하는 옵션이 있습니다. 자세한 내용은 [etcd 암호화](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html)가이드를 참조 하세요.

3.11 클러스터의 경우 데이터가 etcd 수준에서 암호화 되지 않습니다. 암호화를 설정 하는 옵션은 현재 지원 되지 않습니다. OpenShift는이 기능을 지원 하지만도로 지도에서 엔지니어링 노력을 해야 합니다. 데이터는 디스크 수준에서 암호화 됩니다. 자세한 내용은 [데이터 저장소 계층에서 데이터 암호화](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) 를 참조 하세요.

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>OCS와 같은 영구 저장소 솔루션을 선택할 수 있나요? 

4.x 클러스터의 경우 Azure Disk (Premium_LRS)는 기본 저장소 클래스로 구성 됩니다. 추가 저장소 공급자 및 구성 세부 정보 (Azure 파일 포함)는 [영구 저장소](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html)에 대 한 red Hat 설명서를 참조 하세요.

3.11 클러스터의 경우 기본적으로 두 개의 저장소 클래스 (예: Azure Disk (Premium_LRS)와 Azure File 용)가 제공 됩니다.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO는 고객 데이터를 클러스터 영역 외부에 저장 하나요?

아니요. ARO 클러스터에서 생성 된 모든 데이터는 클러스터의 지역 내에서 유지 관리 됩니다.