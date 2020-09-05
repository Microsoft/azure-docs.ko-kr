---
title: Azure Service Fabric 릴리스
description: Azure Service Fabric에 대 한 릴리스 정보입니다. Service Fabric의 최신 기능 및 향상 된 기능에 대 한 정보를 제공 합니다.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 96a9e22d929c62be2a28b8c46657970e7a403813
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050627"
---
# <a name="service-fabric-releases"></a>Service Fabric 릴리스

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">문제 추적</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">지원 옵션</a> 
- <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">지원 되는 버전</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">코드 샘플</a>

이 문서에서는 최신 릴리스 및 Service Fabric 런타임 및 Sdk 업데이트에 대 한 자세한 정보를 제공 합니다.

## <a name="whats-new-in-service-fabric"></a>Service Fabric의 새로운 기능

### <a name="service-fabric-71"></a>Service Fabric 7.1
현재 COVID-19 위기로 인해 고객이 직면 한 문제를 고려 하 고 있으므로 7.1를 사용할 수 있지만 자동 업그레이드를 수신 하도록 설정 된 클러스터는 자동으로 업그레이드 되지 않습니다. 예기치 않은 중단을 방지 하기 위해 고객에 게 가장 적합 한 업그레이드를 적용할 수 있도록 추가 알림이 제공 될 때까지 자동 업그레이드를 일시 중지 합니다.

[Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) 를 통해 또는 [Azure Resource Manager 배포](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template)를 통해 7.1으로 업데이트할 수 있습니다.

자동 업그레이드를 사용 하도록 설정 된 Service Fabric 클러스터는 표준 롤아웃 절차를 다시 시작 하면 7.1 업데이트를 자동으로 수신 하기 시작 합니다. [Service Fabric 기술 커뮤니티 사이트](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 표준 출시가 시작 되기 전에 다른 알림을 제공 합니다.
또한 [여기](./service-fabric-versions.md#supported-versions)에서 6.5부터 7.1까지 주 릴리스를 위한 지원 날짜의 끝에 업데이트를 게시 했습니다. 

## <a name="what-is-new-in-service-fabric-71"></a>Service Fabric 7.1의 새로운 기능
Service Fabric의 다음 릴리스를 발표 하 게 되어 기쁘게 생각 합니다. 이 릴리스는 주요 기능 및 향상 된 기능으로 로드 됩니다. 주요 기능 중 일부는 아래에 강조 표시 되어 있습니다.
## <a name="key-announcements"></a>주요 공지 사항
- **General Availability** [ **Service Fabric 응용 프로그램에 대 한 Service Fabric 관리 id** 의 일반 공급](./concepts-managed-identity.md)
- [**Ubuntu 18.04에 대 한 지원**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**미리 보기: 가상 머신 확장 집합 임시 os 디스크 지원**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: 삭제 된 os 디스크는 로컬 가상 머신에서 생성 된 저장소 이며 원격 Azure Storage에 저장 되지 않습니다. 기존 영구 OS 디스크, 삭제 된 OS 디스크와 비교할 때 모든 Service Fabric 노드 유형 (기본 및 보조)에 권장 됩니다.
      -  OS 디스크에 대 한 읽기/쓰기 대기 시간 줄이기
      -  빠른 다시 설정/이미지 다시 설정 노드 관리 작업 사용
      -  전체 비용 절감 (디스크가 무료 이며 추가 저장소 비용이 발생 하지 않음)
- [**주체 일반 이름을 기준으로 Service Fabric 응용 프로그램의 서비스 끝점 인증서**](./service-fabric-service-manifest-resources.md)선언 지원.
- [**컨테이너 화 된 services에 대 한 상태 프로브 지원**](./probes-codepackage.md): 컨테이너 화 된 응용 프로그램에 대 한 선거의 프로브 메커니즘에 대 한 지원입니다. 선거의 프로브는 컨테이너 화 된 응용 프로그램의 선거의을 발표 하는 데 도움이 되며, 적시에 응답 하지 않으면 다시 시작 됩니다. 
- [컨테이너](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) 및 [게스트 실행 파일](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) 응용 프로그램에 대 한 [**이니셜라이저 코드 패키지 지원**](./initializer-codepackages.md) . 이렇게 하면 코드 패키지 (예: 컨테이너)를 지정 된 순서로 실행 하 여 서비스 패키지 초기화를 수행할 수 있습니다.
- **FabricObserver 및 ClusterObserver** 는 SF 클러스터의 여러 측면과 관련 된 Service Fabric 원격 분석을 캡처하는 상태 비저장 응용 프로그램입니다. 이러한 응용 프로그램은 모두 Windows 프로덕션 클러스터에 배포할 준비가 되어 ApplicationInsights, EventSource 및 LogAnalytics에 대해 구현 된 지원으로 다양 한 원격 분석을 캡처할 수 있습니다.
    - [**FabricObserver () 2.0**](https://github.com/microsoft/service-fabric-observer)-모든 노드에서 실행 되 고, 상태 이벤트를 생성 하 고, 사용자가 구성한 리소스 사용 임계값에 도달 하면 원격 분석을 내보냅니다. 이 릴리스에는 모니터링, 데이터 관리, 상태 이벤트 세부 정보, 구조화 된 원격 분석 등 여러 가지 향상 된 기능이 포함 되어 있습니다.
     - [**Clusterobserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -한 노드에서 실행 되 고 클러스터 수준 상태 원격 분석을 캡처합니다. 이 릴리스에서는 노드 상태를 모니터링 하 고 사용자 지정 기간 보다 오랫동안 노드가 다운/비활성화/사용 안 함으로 설정 된 경우 원격 분석을 내보냅니다.

### <a name="improve-application-life-cycle-experience"></a>응용 프로그램 수명 주기 환경 개선

- **[미리 보기: 요청 드레이닝](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: 서비스 업그레이드 또는 노드 비활성화와 같은 계획 된 서비스 유지 관리를 수행 하는 동안 서비스에서 연결을 정상적으로 드레이닝 하는 것을 허용 하려고 합니다. 이 기능은 서비스 구성에서 인스턴스 종결 지연 기간을 추가 합니다. 계획 된 작업을 수행 하는 동안 SF는 검색에서 서비스의 주소를 제거 하 고 서비스를 종료 하기 전에이 기간을 기다립니다.
- **[자동 Subcluster 검색 및 분산](./cluster-resource-manager-subclustering.md)**: 배치 제약 조건이 서로 다른 서비스에 공통 [부하 메트릭이](./service-fabric-cluster-resource-manager-metrics.md)있는 경우 하위 클러스터링이 발생 합니다. 다른 노드 집합에 대 한 부하가 현저 하 게 다를 경우 Service Fabric 클러스터 리소스 관리자 배치 제약 조건으로 인해 클러스터가 불균형 하 게 분산 된 것으로 간주 합니다. 결과적으로 클러스터의 균형을 다시 조정 하 고 잠재적으로 불필요 한 서비스 이동을 발생 시킵니다. "불균형"은 크게 향상 될 수 없기 때문입니다. 이 릴리스부터 클러스터 리소스 관리자는 이제 이러한 종류의 구성을 자동으로 감지 하 고 움직임을 통해 불균형을 수정할 수 있는 경우를 이해 하 고, 대신 성능을 크게 향상 시킬 수 없기 때문에 작업을 단독으로 유지 해야 합니다.  
- [**보조 복제본에 대 한 다양 한 이동 비용**](./service-fabric-cluster-resource-manager-movement-cost.md): 보조 복제본에 대해 별도의 이동 비용을 사용 해야 하는지 여부를 정의 하는 몇 가지 시나리오에서 추가 유연성을 제공 하는 새 이동 비용 값 VeryHigh 도입 되었습니다.
- 컨테이너 화 된 응용 프로그램에 대해 [**선거의 프로브**](./probes-codepackage.md) 메커니즘을 사용 하도록 설정 했습니다. 선거의 프로브는 컨테이너 화 된 응용 프로그램의 선거의을 발표 하는 데 도움이 되며, 적시에 응답 하지 않으면 다시 시작 됩니다.
- [**서비스에 대해 완료까지 실행**](./run-to-completion.md)**

### <a name="image-store-improvements"></a>Image Store 개선 사항
 - Service Fabric 7.1는 **기본적으로 사용자 지정 전송을 사용 하 여 노드 간 파일 전송을 보호**합니다. SMB 파일 공유에 대 한 종속성은 버전 7.1에서 제거 됩니다. 보안 SMB 파일 공유는 고객의 선택에 대 한 Image Store 서비스 복제본이 포함 된 노드에 계속 포함 되어 있으며, 기본적으로 선택 취소 하 고 이전 버전으로 다운 그레이드할 수 있습니다.
       
 ### <a name="reliable-collections-improvements"></a>안정적인 컬렉션 향상

- [**신뢰할 수 있는 컬렉션을 사용 하는 상태 저장 서비스에 대 한 메모리 전용 저장소 지원**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): 일시적으로 신뢰할 수 있는 컬렉션을 사용 하면 대량 데이터 손실이 허용 되는 경우와 같이 복제 된 캐시와 같은 워크 로드에 데이터를 유지할 수 있습니다. 일시적으로 신뢰할 수 있는 [컬렉션의 제한 사항](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)에 따라 쿼럼을 손실 하는 드문 경우를 처리 하는 서비스에 대해 지 속성이 필요 하지 않은 워크 로드에 대해이 작업을 수행 하는 것이 좋습니다.
- [**미리 보기: Service Fabric 백업 탐색기**](https://github.com/microsoft/service-fabric-backup-explorer): Service Fabric 상태 저장 응용 프로그램에 대해 신뢰할 수 있는 컬렉션 백업을 쉽게 관리 하기 위해 Service Fabric Backup 탐색기를 사용 하면 사용자가
    - 신뢰할 수 있는 컬렉션의 내용을 감사 하 고 검토 합니다.
    - 현재 상태를 일관 된 뷰로 업데이트 합니다.
    - 신뢰할 수 있는 컬렉션의 현재 스냅숏 백업 만들기
    - 데이터 손상 수정
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 릴리스
| 릴리스 날짜 | Release | 추가 정보 |
|---|---|---|
| 4 월 20 일, 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [릴리스 정보](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 6 월 16 일, 2020 | [Microsoft Azure Service Fabric 7.1 최초 새로 고침](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020 년 7 월 20 일 | [Microsoft Azure Service Fabric 7.1 초 새로 고침](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020년 8월 12일 | [Microsoft Azure Service Fabric 7.1 세 번째 새로 고침](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)

### <a name="service-fabric-70"></a>Service Fabric 7.0

이제 Azure Service Fabric 7.0를 사용할 수 있습니다. Azure Portal 또는 Azure Resource Manager 배포를 통해 7.0으로 업데이트할 수 있습니다. 명절 기간에 대 한 고객의 의견으로 인해 1 월까지 자동 업그레이드를 받도록 설정 된 클러스터 자동 업데이트를 시작 하지 않습니다.
1 월에는 표준 롤아웃 절차를 다시 시작 하 고 자동 업그레이드를 사용 하도록 설정 된 클러스터는 7.0 업데이트를 자동으로 수신 하기 시작 합니다. 롤아웃이 시작 되기 전에 다른 알림을 제공 합니다.
또한 계획 된 릴리스 날짜를 업데이트 하 여이 정책을 고려 하 고 있음을 나타낼 것입니다. 향후 [릴리스 일정](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)에 대 한 업데이트는 여기를 참조 하세요.
 
이는 Service Fabric 최신 릴리스 이며 주요 기능 및 향상 된 기능을 통해 로드 됩니다.

### <a name="key-announcements"></a>주요 공지 사항
 - [**응용 프로그램 비밀 (미리 보기)에 대 한 KeyVaultReference 지원**](./service-fabric-keyvault-references.md): [관리 되는 id](./concepts-managed-identity.md) 를 사용 하는 Service Fabric 응용 프로그램은 이제 환경 변수, 응용 프로그램 매개 변수 또는 컨테이너 리포지토리 자격 증명으로 Key Vault 비밀 URL을 직접 참조할 수 있습니다. Service Fabric는 응용 프로그램의 관리 되는 id를 사용 하 여 암호를 자동으로 확인 합니다. 
     
- **상태 비저장 서비스의 업그레이드 안전성 향상**: 응용 프로그램을 업그레이드 하는 동안 가용성을 보장 하기 위해 [상태 비저장 서비스에 대 한 최소 인스턴스 수](/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) 를 정의 하는 새로운 구성을 도입 했습니다. 이전에는이 값이 모든 서비스에 대해 1 였 고 변경 되지 않았습니다. 이 새로운 서비스별 안전 검사를 사용 하 여 서비스는 응용 프로그램 업그레이드 중에 최소 인스턴스 수를 유지 하 고, 클러스터를 업그레이드 하 고, Service Fabric의 상태 및 안전 검사에 의존 하는 기타 유지 관리를 수행할 수 있습니다.
  
- [**사용자 서비스에 대 한 리소스 제한**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): 사용자는 노드의 사용자 서비스에 대 한 리소스 제한을 설정 하 여 Service Fabric 시스템 서비스의 리소스 고갈 등의 시나리오를 방지할 수 있습니다. 
  
- 복제본 유형에 대 한 [**서비스 이동 비용이 매우 높습니다**](./service-fabric-cluster-resource-manager-movement-cost.md) . 이동 비용이 매우 높은 복제본은 다른 방법으로는 해결할 수 없는 제약 조건 위반이 클러스터에 있는 경우에만 이동 됩니다. "매우 높음" 이동 비용을 사용 하는 경우와 추가 고려 사항에 대 한 자세한 내용은 연결 된 문서를 참조 하세요.
  
-  **추가 클러스터 안전 검사**:이 릴리스에서는 구성 가능한 시드 노드 쿼럼 안전 검사를 도입 했습니다. 이를 통해 클러스터 수명 주기 및 관리 시나리오에서 사용할 수 있어야 하는 시드 노드 수를 사용자 지정할 수 있습니다. 클러스터를 구성 된 값 아래로 이동 하는 작업이 차단 됩니다. 현재 기본값은 시드 노드의 쿼럼입니다. 예를 들어, 시드 노드가 7 개 있는 경우 5 개 시드 노드 아래에서 수행 하는 작업은 기본적으로 차단 됩니다. 이와 같이 변경 하면 한 번에 하나의 시드 노드만 중지 되도록 최소 안전 값 6을 만들 수 있습니다.
   
- [**Service Fabric Explorer에서 백업 및 복원 서비스를 관리**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)하기 위한 지원이 추가 되었습니다. 이렇게 하면 SFX 내에서 다음 작업을 직접 수행할 수 있습니다. 즉, 백업 및 복원 서비스를 검색 하 고, 백업 정책을 만들고, 자동 백업을 사용 하 고, 임시 백업을 수행 하 고, 복원 작업을 트리거하여 기존 백업을 검색 합니다.

- [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)의 가용성 발표:이 도구는 이동 응용 프로그램을 업그레이드 하는 동안 신뢰할 수 있는 컬렉션에서 사용 된 형식이 이전 버전과 호환 되는지 확인 하는 데 도움이 됩니다. 이를 통해 누락 되거나 호환 되지 않는 형식으로 인해 업그레이드 실패 또는 데이터 손실 및 데이터 손상을 방지할 수 있습니다.

- [**보조 복제본에서 안정적인 읽기 사용**](./service-fabric-reliable-services-configuration.md#configuration-names-1): 안정적인 읽기는 내보냄 된 값을 반환 하도록 보조 복제본을 제한 합니다.

또한이 릴리스에는 다른 새로운 기능, 버그 수정, 지원 가능성, 안정성 및 성능 향상이 포함 되어 있습니다. 전체 변경 내용 목록은 [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)를 참조 하세요.

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 릴리스

| 릴리스 날짜 | Release | 추가 정보 |
|---|---|---|
| 2019년 11월 18일 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020 년 1 월 30 일 | [Azure Service Fabric 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020 년 2 월 6 일 | [Azure Service Fabric 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020년 3월 2일 | [Azure Service Fabric 7.0 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Service Fabric 6.5

이 릴리스에는 지원 가능성, 안정성 및 성능 향상, 새로운 기능, 버그 수정 및 향상 된 클러스터 및 응용 프로그램 수명 주기 관리를 위한 향상 된 기능이 포함 되어 있습니다.

> [!IMPORTANT]
> Service Fabric 6.5는 Visual Studio 2015에서 Service Fabric 도구를 지 원하는 최종 릴리스입니다. 고객은 앞으로 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 로 전환 하는 것이 좋습니다.

Service Fabric 6.5의 새로운 같습니다는 다음과 같습니다.

- Service Fabric Explorer는 이미지 저장소에 업로드 한 응용 프로그램을 검사 하기 위한 [Image Store 뷰어](service-fabric-visualizing-your-cluster.md#image-store-viewer) 를 포함 합니다.

- [POA (패치 오케스트레이션 응용 프로그램)](service-fabric-patch-orchestration-application.md) 버전 [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) 에는 많은 자체 진단 기능이 포함 되어 있습니다. POA의 고객은이 버전으로 이동 하는 것이 좋습니다.

- 옵트아웃 (opt out) 하지 않으면 Eventstore 서비스는 Service Fabric 6.5 클러스터에 대해 [기본적으로 사용 하도록 설정 됩니다](service-fabric-visualizing-your-cluster.md#event-store) .

- 상태 저장 서비스에 대 한 [복제본 수명 주기 이벤트](service-fabric-diagnostics-event-generation-operational.md#replica-events) 를 추가 했습니다.

- 시드 노드가 비정상 (*다운*, *제거* 또는 *알 수 없음*) 인 경우 클러스터 수준 경고를 비롯 하 여 [시드 노드 상태를 더 잘 표시](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)합니다.

- [Service Fabric 응용 프로그램 재해 복구 도구](https://github.com/Microsoft/Service-Fabric-AppDRTool) 를 사용 하면 주 클러스터에 재해가 발생할 때 Service Fabric 상태 저장 서비스가 신속 하 게 복구할 수 있습니다. 주 클러스터의 데이터는 정기적인 백업 및 복원을 사용 하 여 보조 대기 응용 프로그램에서 지속적으로 동기화 됩니다.

- [Linux 기반 클러스터에 .Net Core 앱을 게시](service-fabric-how-to-publish-linux-app-vs.md)하기 위한 Visual Studio 지원

- Azure에서 새 Linux 클러스터를 업그레이드 하거나 만들 때 [azure SERVICE FABRIC CLI (SFCTL)](./service-fabric-cli.md) 는 Service Fabric 6.5 이상 버전에 대해 자동으로 설치 됩니다.

- [Sfctl](./service-fabric-cli.md) 은 기본적으로 macos/Linux onebox 클러스터에 설치 됩니다.

자세한 내용은 [Service Fabric 6.5 릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)를 참조 하세요.

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 릴리스

| 릴리스 날짜 | Release | 추가 정보 |
|---|---|---|
| 2019년 6월 11일 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019년 7월 2일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019년 7월 29일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019 년 10 월 14 일 | [Azure Service Fabric 6.5 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [릴리스 정보] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>이전 버전

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 릴리스

| 릴리스 날짜 | Release | 추가 정보 |
|---|---|---|
| 2018 년 11 월 30 일 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018년 12월 12일 | [Windows 클러스터에 대 한 Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 년 2 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 년 3 월 4 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 년 4 월 8 일 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 5 월 2 일, 2019 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 5 월 28 일, 2019 | [Azure Service Fabric 6.4 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [릴리스 정보](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)