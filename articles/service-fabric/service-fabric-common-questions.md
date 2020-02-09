---
title: Microsoft Azure Service Fabric에 대 한 일반적인 질문
description: 기능, 사용 사례 및 일반적인 시나리오를 비롯 하 여 Service Fabric에 대 한 질문과 대답입니다.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 17c1d05e119df8207c0599283f1d04b869e8297b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293524"
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric에 대해 자주 묻는 질문

Service Fabric으로 수행할 수 있는 작업 및 사용 방법에 대한 여러 가지 자주 묻는 질문이 있습니다. 이 문서에서는 자주 묻는 질문 및 그에 대한 답변을 설명합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>클러스터 설정 및 관리

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서를 롤백하려면 어떻게 해야 하나요?

애플리케이션 업그레이드를 롤백하려면 Service Fabric 클러스터 쿼럼에서 변경 내용을 커밋하기 전에 상태 오류를 감지해야 합니다. 감지된 변경 내용만 롤포워드할 수 있습니다. 모니터링되지 않은 주요 인증서 변경 내용이 있는 경우 고객 지원 서비스의 에스컬레이션 엔지니어가 클러스터를 복구해야 할 수도 있습니다.  [Service Fabric의 애플리케이션 업그레이드](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master)는 [애플리케이션 업그레이드 매개 변수](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)를 적용하고, 가동 중지 시간 0이라는 약속을 이행합니다.  Microsoft의 권장 애플리케이션 업그레이드 모니터링 모드에 따라, 업데이트 도메인을 통한 자동 진행은 상태 검사 통과를 기반으로 하며, 기본 서비스 업데이트가 실패하는 경우 자동으로 롤백됩니다.
 
클러스터에서 여전히 Resource Manager 템플릿의 클래식 인증서 지문 속성을 사용하는 경우 최신 비밀 관리 기능을 활용할 수 있도록 [클러스터를 인증서 지문에서 일반 이름으로 변경](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)하는 것이 좋습니다.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>여러 Azure 지역 또는 나만의 데이터 센터에 걸쳐서 클러스터를 만들 수 있나요?

예. 

코어 Service Fabric 클러스터링 기술은 서로 네트워크로 연결되어 있기만 한다면 전 세계 어디서나 실행되는 컴퓨터를 결합하는 데 사용할 수 있습니다. 그러나 이러한 클러스터를 구축하고 실행하는 것은 복잡할 수 있습니다.

이 시나리오에 관심이 있는 경우 [Service Fabric GitHub 문제 목록](https://github.com/azure/service-fabric-issues)을 확인하거나 지원 담당자를 통해 추가 지침을 얻는 것이 좋습니다. Service Fabric 팀은 이 시나리오에 대해 추가적인 설명, 지침 및 권장 사항을 제공하기 위해 작업 중입니다. 

고려할 사항은 다음과 같습니다. 

1. 현재 Azure에서 Service Fabric 클러스터 리소스는 클러스터가 작성된 가상 머신 확장 집합이므로 지역에 국한됩니다. 즉, 하위 지역에서 오류가 발생할 경우 Azure Resource Manager 또는 Azure Portal을 통해 클러스터를 관리하지 못하게 될 수 있습니다. 클러스터는 계속 실행되는데도 이러한 문제가 발생할 수도 있으며, 이 경우 직접 개입하는 것이 나을 수 있습니다. 또한 현재 Azure에서는 하위 지역에 걸쳐 사용할 수 있는 단일 가상 네트워크를 유지하는 기능을 제공하지 않습니다. 즉, Azure의 다중 지역 클러스터에는 [VM Scale Sets의 각 VM에 대한 공용 IP 주소](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) 또는 [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)가 필요합니다. 이러한 네트워킹 선택 항목은 비용, 성능 및 애플리케이션 디자인에 어느 정도 다르게 영향을 미치므로 이러한 환경을 구성하기 전에 신중하게 분석하고 계획해야 합니다.
2. 이러한 컴퓨터의 유지 관리, 관리, 모니터링은 복잡할 수 있으며 다른 클라우드 공급자 간 또는 온-프레미스 리소스와 Azure 간 등, 여러 환경 _유형_ 간에 걸쳐 있는 경우에는 특히 복잡할 수 있습니다. 이러한 환경에서 프로덕션 워크로드를 실행하기 전에 클러스터 및 애플리케이션 둘 다에 대해 업그레이드, 모니터링, 관리 및 진단을 파악하도록 해야 합니다. Azure 또는 자체 데이터 센터에서 이러한 문제를 이미 해결한 경험이 있으면 Service Fabric 클러스터를 빌드하거나 실행할 때도 이러한 동일한 해결 방법을 적용할 수 있습니다. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric 노드에서 OS 업데이트를 자동으로 수신하나요?

일반적으로 요즘 제공되는 기능인 [Virtual Machine Scale Set 자동 OS 이미지 업데이트](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)를 사용할 수 있습니다.

Azure에서 실행되지 않는 클러스터의 경우 Service Fabric 노드 아래의 운영 체제를 패치하기 위한 [을 제공](service-fabric-patch-orchestration-application.md)하고 있습니다.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>SF 클러스터에서 큰 가상 머신 확장 집합을 사용할 수 있나요? 

**간단한 대답** - 아니요 

**자세한 대답** - 대형 Virtual Machine Scale Sets에서는 가상 머신 확장 세트 하나가 최대 1000개의 VM 인스턴스를 포함하도록 크기를 조정할 수 있지만, 이 과정에는 PG(배치 그룹)가 사용됩니다. FD(장애 도메인) 및 UD(업그레이드 도메인)는 Service Fabric이 서비스 복제본/서비스 인스턴스의 배치 결정을 내리는 데 사용하는 배치 그룹 내에서만 일관됩니다. FD 및 UD는 배치 그룹 내에서만 비교할 수 있으므로 SF에서 사용할 수 없습니다. 예를 들어 PG1의 VM1에 FD=0 토폴로지가 있고 PG2의 VM9에 FD=4 토폴로지가 있는 경우 VM1과 VM2가 두 개의 다른 하드웨어 랙에 있다는 의미는 아니므로 이 경우 SF에서 FD 값을 사용하여 배치 결정을 내릴 수 없습니다.

현재 큰 가상 머신 확장 집합에는 수준 4 부하 분산 지원 부족과 같은 기타 문제가 있습니다. [큰 크기 집합에 대한 세부 정보](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)를 참조하세요.



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Service Fabric 클러스터의 최소 크기는 어떻게 되나요? 작으면 안되는 이유는 무엇인가요?

프로덕션 워크로드를 시행하는 Service Fabric 클러스터에 지원되는 최소 크기는 5개 노드입니다. 개발 시나리오의 경우 노드 1개(Visual Studio에서 빠른 개발 환경에 최적화됨) 및 노드 클러스터 5개를 지원합니다.

다음 세 가지 이유로 인해 프로덕션 클러스터에는 5개의 이상의 노드가 있어야 합니다.
1. 실행 중인 사용자 서비스가 없는 경우에도 Service Fabric 클러스터는 Naming Service 및 장애 조치(Failover) 관리자 서비스를 포함한 일련의 상태 저장 시스템 서비스를 실행합니다. 클러스터가 계속 작동하려면 이러한 시스템 서비스가 필수입니다.
2. 항상 노드당 하나의 서비스 복제본이 배치되므로 클러스터 크기는 서비스(실제로는 파티션)에 포함될 수 있는 최대 복제본 수입니다.
3. 클러스터 업그레이드는 하나 이상의 노드를 중지시키기 때문에 노드 하나 이상의 버퍼를 포함하려고 하므로 프로덕션 클러스터에 최솟값 ‘이외에’ 두 개 이상의 노드를 포함하려고 합니다. 최솟값은 아래 설명된 대로 시스템 서비스의 쿼럼 크기입니다.  

두 개의 노드가 동시에 실패하는 경우 클러스터를 사용 가능하게 하려고 합니다. Service Fabric 클러스터가 사용 가능하려면 시스템 서비스를 사용할 수 있어야 합니다. Naming Service 및 장애 조치(Failover) 관리자 서비스 같은 상태 저장 시스템 서비스는 클러스터에 어떤 서비스가 배포되었고 현재 호스트되는 위치를 추적하며 강력한 일관성을 따릅니다. 한편 강력한 일관성은 해당 서비스 상태로 특정 업데이트를 위해 *쿼럼*을 획득하는 기능에 의존하는데, 여기서 쿼럼은 지정된 서비스에 대한 복제본(N/2 +1)의 엄격한 다수성을 나타냅니다. 따라서 두 개의 노드가 동시에 손실되어 시스템 서비스의 복제본 두 개가 동시에 손실되는 경우 탄력적으로 대응하려면 ClusterSize에서 QuorumSize를 뺀 값이 2보다 크거나 같아야(ClusterSize - QuorumSize >= 2) 합니다. 이 경우 최솟값이 5가 됩니다. 이를 확인하기 위해 클러스터에 N개 노드가 있고 각 노드에 하나씩 시스템 서비스의 N개 복제본이 있는 경우를 살펴보겠습니다. 시스템 서비스의 쿼럼 크기는 (N/2 + 1)입니다. 위의 부등식은 N - (N/2 + 1) >= 2 같이 표시됩니다. N이 짝수인 경우와 N이 홀수인 경우를 고려해야 합니다. N이 짝수인 경우 N = 2\*m이라고 합니다. 여기서 m >= 1입니다. 부등식은 2\*m - (2\*m/2 + 1) >= 2 또는 m >= 3 같이 표시됩니다. N의 최솟값은 6이고 이 값은 m = 3인 경우 달성됩니다. 한편, N이 홀수인 경우 N = 2\*m+1이라고 합니다. 여기서 m >= 1입니다. 부등식은 2\*m+1 - ( (2\*m+1)/2 + 1 ) >= 2, 2\*m+1 - (m+1) >= 2 또는 m >= 2 같이 표시됩니다. N의 최솟값은 5이고 이 값은 m = 2인 경우 달성됩니다. 따라서 부등식 ClusterSize - QuorumSize >= 2를 충족하는 N의 모든 값 중에 최솟값은 5입니다.

위의 인수에서는 모든 노드에 시스템 서비스의 복제본이 있다고 가정하므로 쿼럼 크기는 클러스터의 노드 수에 따라 계산됩니다. 그러나 *TargetReplicaSetSize*를 변경하여 쿼리 크기를 (N/2+1)보다 작게 설정하면 5개 노드보다 더 작은 클러스터가 있고 쿼럼 크기 외에 두 개의 추가 노드가 있다는 느낌을 줄 수 있습니다. 예를 들어 4개 노드가 포함된 클러스터에서 TargetReplicaSetSize를 3으로 설정하는 경우 TargetReplicaSetSize를 기반으로 하는 쿼럼 크기는 (3/2 + 1) 또는 2이므로 ClusterSize - QuorumSize = 4-2 >= 2입니다. 그러나 노드 쌍이 동시에 손실되는 경우 시스템 서비스가 쿼럼보다 작거나 같을 것이라고 보장할 수 없습니다. 손실된 두 개의 노드가 두 개의 복제본을 호스트 중이어서 시스템 서비스가 쿼럼 손실 상태(단일 복제본만 남아 있음)가 되고 사용할 수 없게 될 수 있습니다.

배경 정보로 몇 가지 가능한 클러스터 구성을 살펴보겠습니다.

**한 개 노드**: 이 옵션에서는 어떤 이유로든 단일 노드 손실이 전체 클러스터의 손실을 의미하므로 고가용성을 제공하지 않습니다.

**두 개 노드**: 두 노드(N = 2) 간에 배포된 서비스의 쿼럼은 2(2/2 + 1 = 2)입니다. 단일 복제본이 손실되면 쿼럼을 만들 수 없습니다. 서비스 업그레이드를 수행하려면 복제본을 일시적으로 종료해야 하므로 유용한 구성이 아닙니다.

**세 개 노드**: 세 개 노드(N=3)를 사용하며 쿼럼을 만들기 위한 요건은 계속해서 두 개 노드입니다(3/2 + 1 = 2). 이는 개별 노드가 손실되어도 쿼럼을 유지할 수 있지만, 노드 두 개가 동시에 실패하면 시스템 서비스가 쿼럼 손실 상태가 되므로 클러스터를 사용할 수 없게 됩니다.

**네 개 노드**: 네 개 노드(N=4)를 사용하면 쿼럼을 만들기 위한 요구 사항은 세 개 노드입니다(4/2 + 1 = 3). 이는 개별 노드가 손실되어도 쿼럼을 유지할 수 있지만, 노드 두 개가 동시에 실패하면 시스템 서비스가 쿼럼 손실 상태가 되므로 클러스터를 사용할 수 없게 됩니다.

**5개 노드**: 5개 노드(N=5)를 사용하면 쿼럼을 만들기 위한 요구 사항은 계속해서 세 개 노드입니다(5/2 + 1 = 3). 이는 두 개 노드가 동시에 손실되어도 시스템 서비스의 쿼럼을 유지할 수 있음을 의미합니다.

프로덕션 워크로드의 경우 두 개 이상의 노드가 동시에 실패하는 경우(예: 클러스터 업그레이드로 인해 하나, 다른 이유로 인해 하나)에 탄력적으로 대응해야 하므로 5개 노드가 필요합니다.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>비용 절감을 위해 야간/주말에 내 클러스터를 해제할 수 있나요?

일반적으로 그렇지 않습니다. Service Fabric은 임시 로컬 디스크에 상태를 저장하므로 가상 머신이 다른 호스트로 이동하는 경우 데이터가 이동하지 않습니다. 정상 작동 시에는 새 노드가 다른 노드에 의해 최신 상태가 되므로 문제가 되지 않습니다. 그러나 모든 노드를 중지하고 나중에 다시 시작하는 경우 대부분의 노드가 새 호스트에서 실행되고 시스템을 복구할 수 없을 가능성이 높습니다.

배포하기 전 애플리케이션 테스트를 위해 클러스트를 만드는 경우 해당 클러스터를 [연속 통합/연속 배포 파이프라인](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)의 일부로 동적으로 만드는 것이 좋습니다.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>운영 체제를 업그레이드(예: Windows Server 2012를 Windows Server 2016으로)하려면 어떻게 해야 하나요?

Microsoft는 환경 개선을 위해 노력하고 있지만 업그레이드에 대한 책임은 귀하에게 있습니다. 클러스터의 가상 머신에서 OS 이미지를 업그레이드하고 한 번에 하나의 VM에서 수행해야 합니다. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>클러스터 노드 형식(가상 머신 확장 집합)의 연결된 데이터 디스크를 암호화할 수 있나요?
예.  자세한 내용은 [연결 된 데이터 디스크를 사용 하 여 클러스터 만들기](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) 및 [Virtual Machine Scale Sets에 대 한 Azure Disk Encryption](../virtual-machine-scale-sets/disk-encryption-overview.md)를 참조 하세요.

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>클러스터 노드 형식(가상 머신 확장 집합)에서 우선 순위가 낮은 VM을 사용할 수 있나요?
아닙니다. 우선 순위가 낮은 VM은 지원되지 않습니다. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>클러스터에서 바이러스 백신 프로그램을 실행하는 경우 제외해야 하는 디렉터리 및 프로세스는 무엇입니까?

| **바이러스 백신 제외된 디렉터리** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot(클러스터 구성에서) |
| FabricLogRoot(클러스터 구성에서) |

| **바이러스 백신 제외된 프로세스** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>비밀을 가져오도록 내 애플리케이션을 KeyVault로 어떻게 인증하나요?
다음은 애플리케이션을 keyVault로 인증하기 위해 자격 증명을 얻기 위한 방법입니다.

A. 애플리케이션 빌드/압축 작업을 하는 동안 인증서를 SF 앱의 데이터 패키지로 가져오고, 이를 사용하여 KeyVault에 인증할 수 있습니다.
B. 가상 머신 확장 집합 MSI 사용 호스트의 경우 SF 앱에 대 한 간단한 PowerShell SetupEntryPoint를 개발 하 여 [msi 엔드포인트에서 액세스 토큰](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)을 가져온 다음, [keyvault에서 비밀을 검색할](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret)수 있습니다.

## <a name="application-design"></a>애플리케이션 설계

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Reliable Collection의 파티션에 대해 데이터를 쿼리하는 가장 좋은 방법은 무엇인가요?

일반적으로 Reliable Collection은 더 나은 성능 및 처리량을 달성하도록 규모 확장하기 위해 [분할](service-fabric-concepts-partitioning.md)됩니다. 즉, 지정된 서비스의 상태가 수십 또는 수백 대의 컴퓨터에 분배될 수 있습니다. 전체 데이터 집합에 대한 작업을 수행하기 위한 몇 가지 옵션이 있습니다.

- 다른 서비스의 모든 파티션을 쿼리하는 서비스를 만들어 필요한 데이터를 가져옵니다.
- 다른 서비스의 모든 파티션에서 데이터를 수신할 수 있는 서비스를 만듭니다.
- 각 서비스에서 외부 저장소로 주기적으로 데이터를 푸시합니다. 이 방법은 외부 저장소의 데이터가 유효 하지 않기 때문에 수행 중인 쿼리가 핵심 비즈니스 논리의 일부가 아닌 경우에만 적합 합니다.
- 또는 신뢰할 수 있는 컬렉션이 아닌 데이터 저장소에서 직접 모든 레코드의 쿼리를 지 원하는 데이터를 저장 합니다. 이로 인해 오래 된 데이터에 대 한 문제는 제거 되지만 신뢰할 수 있는 컬렉션의 이점을 활용 하는 것은 허용 되지 않습니다.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>내 행위자에 대해 데이터를 쿼리하는 가장 좋은 방법은 무엇인가요?

행위자는 독립적인 상태 및 컴퓨팅 단위로 설계되므로 런타임에는 행위자 상태에 대한 광범위한 쿼리를 수행하지 않는 것이 좋습니다. 전체 행위자 상태 집합에 대해 쿼리를 해야 하는 경우 다음을 고려합니다.

- 행위자 서비스를 상태 저장 신뢰할 수 있는 서비스로 바꿔서 해당 네트워크 요청 수로 해당 행위자 수의 모든 데이터를 서비스의 해당 파티션 수로 수집하도록 합니다.
- 상태를 외부 저장소로 주기적으로 푸시하도록 행위자를 설계하여 간편하게 쿼리할 수 있도록 합니다. 위에서처럼 이 방법은 수행 중인 쿼리가 런타임 동작에 대해 필요하지 않은 경우에만 필요합니다.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Reliable Collection에 저장할 수 있는 데이터는 얼마나 되나요?

Reliable Services는 일반적으로 분할되므로 저장할 수 있는 양은 클러스터에 있는 컴퓨터 수와 해당 컴퓨터에서 사용할 수 있는 메모리 양에 의해서만 제한됩니다.

예를 들어 평균 1kb 크기의 개체를 저장하는 100개 파티션과 3개 복제본을 포함하는 신뢰할 수 있는 컬렉션이 서비스에 있다고 가정합니다. 이제 컴퓨터당 16gb의 메모리가 있는 10개의 컴퓨터 클러스터가 있다고 가정합니다. 간단하고 무난한 작업을 위해 운영 체제 및 시스템 서비스, Service Fabric 런타임 및 사용자 서비스가 이 중에서 6gb를 사용하며 컴퓨터당 10gb(클러스터의 경우 100gb)가 사용 가능하다고 가정합니다.

각 개체는 세 번(주에서 1번, 복제본에서 2번) 저장되어야 하며 전체 용량으로 작동할 때 컬렉션에서 약 3천 500만 개체에 대해 충분한 메모리를 포함합니다. 하지만 오류 도메인 및 업그레이드 도메인의 동시 손실에 복원력을 갖추는 것이 좋습니다. 동시 손실의 경우 용량의 약 1/3을 나타내므로 약 2천 300만 개체로 감소합니다.

이 계산에서는 다음 사항도 가정합니다.

- 파티션 간 데이터 분포는 대략적으로 균일하거나 부하 메트릭을 Cluster Resource Manager에 보고합니다. 기본적으로 Service Fabric은 복제본 수에 따라 부하 분산을 수행합니다. 앞의 예제에서는 클러스터의 각 노드에 주 복제본 10개와 보조 복제본 20개를 배치합니다. 파티션 간에 부하가 고르게 분포된 경우에는 원활하게 작동합니다. 부하가 고르게 분포되지 않은 경우 로드를 보고하여 Resource Manager에서 작은 크기의 복제본을 함께 패킹하여 큰 복제본이 개별 노드에서 더 많은 메모리를 사용하도록 합니다.

- 문제가 되는 Reliable Services는 클러스터에서 유일한 저장 상태입니다. 여러 서비스를 클러스터에 배포할 수 있으므로 각각 해당 상태를 실행 및 관리해야 하는 리소스에 유념해야 합니다.

- 클러스터 자체는 증가 또는 감소하지 않습니다. 더 많은 컴퓨터를 추가하는 경우 개별 복제본은 컴퓨터 간에 걸쳐 있을 수 없으므로 컴퓨터 수가 서비스의 파티션 수를 초과할 때까지 Service Fabric은 복제본의 부하를 다시 조정하여 추가 용량을 활용합니다. 이와 반대로 컴퓨터를 제거하여 클러스터 크기를 줄이는 경우 복제본을 보다 조밀하게 압축하면 전체 용량이 감소합니다.

### <a name="how-much-data-can-i-store-in-an-actor"></a>행위자에 저장할 수 있는 데이터는 얼마나 되나요?

Reliable Services와 마찬가지로 행위자 서비스에 저장할 수 있는 데이터 양은 클러스터에 있는 노드 간에 사용 가능한 총 디스크 공간 및 메모리 양에 의해서만 제한됩니다. 하지만 개별 행위자는 적은 양의 상태 및 관련 비즈니스 논리를 캡슐화하는 데 사용할 경우 가장 효과적입니다. 일반적으로 개별 행위자는 킬로바이트 단위로 측정되는 상태를 포함하게 됩니다.

## <a name="other-questions"></a>기타 질문

### <a name="how-does-service-fabric-relate-to-containers"></a>Service Fabric은 컨테이너와 어떻게 연결되나요?

컨테이너는 서비스와 해당 종속성을 패키지하는 간단한 방법을 제공하여 모든 환경에서 일관성 있게 실행되고 단일 컴퓨터에서 격리된 방식으로 작동할 수 있도록 합니다. Service Fabric은 [컨테이너에 패키지된 서비스](service-fabric-containers-overview.md)를 비롯하여 서비스를 배포 및 관리하는 방법을 제공합니다.

### <a name="are-you-planning-to-open-source-service-fabric"></a>Service Fabric의 오픈 소스 계획이 있나요?

GitHub의 Service Fabric 일부에 대해 오픈 소스를 제공하였으며([신뢰할 수 있는 서비스 프레임워크](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [신뢰할 수 있는 작업자 프레임워크](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core 통합 라이브러리](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) 및 [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)), 해당 프로젝트에 대한 커뮤니티 참여를 받고 있습니다. 

Service Fabric 런타임의 오픈 소스를 제공할 계획임을 [최근에 발표했습니다](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/). 현재 GitHub의 Linux 빌드 및 테스트 도구가 포함된 [Service Fabric 리포지토리](https://github.com/Microsoft/service-fabric/)가 있습니다. 즉, 리포지토리를 복제하고, Linux용 Service Fabric을 빌드하고, 기본 테스트를 실행하고, 현안을 공개하고, 끌어오기 요청을 제출할 수 있습니다. 마이그레이션된 Windows 빌드 환경과 함께 완벽한 CI 환경을 제공하기 위해 최선을 다하고 있습니다.

공지되면 [Service Fabric 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 자세한 내용을 확인하세요.

## <a name="next-steps"></a>다음 단계

[핵심 Service Fabric 개념](service-fabric-technical-overview.md) 및 [모범 사례](service-fabric-best-practices-overview.md)에 대해 알아보기
