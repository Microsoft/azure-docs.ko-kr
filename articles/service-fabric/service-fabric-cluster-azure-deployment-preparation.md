---
title: Azure Service Fabric 클러스터 배포 계획
description: Azure에 대 한 프로덕션 Service Fabric 클러스터 배포를 계획 하 고 준비 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 32d48f9ffa056d252bdf762304340f245d80fd26
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834453"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>클러스터 배포 계획 및 준비

프로덕션 클러스터 배포를 계획 하 고 준비 하는 것이 매우 중요 합니다.  고려해 야 할 여러 요인이 있습니다.  이 문서에서는 클러스터 배포를 준비 하는 단계를 안내 합니다.

## <a name="read-the-best-practices-information"></a>모범 사례 정보 읽기
Azure Service Fabric 응용 프로그램 및 클러스터를 성공적으로 관리 하려면 프로덕션 환경의 안정성을 최적화 하기 위해 수행 하는 것이 좋습니다.  자세한 내용은 [Service Fabric 응용 프로그램 및 클러스터 모범 사례](service-fabric-best-practices-overview.md)를 참조 하세요.

## <a name="select-the-os-for-the-cluster"></a>클러스터에 대 한 OS를 선택 합니다.
Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다.  클러스터를 배포 하기 전에 Windows 또는 Linux 운영 체제를 선택 해야 합니다.  클러스터의 모든 노드 (가상 머신)는 동일한 OS를 실행 하며, 동일한 클러스터에서 Windows 및 Linux Vm을 혼합할 수 없습니다.

## <a name="capacity-planning"></a>용량 계획
프로덕션 배포의 경우 용량 계획은 중요한 단계입니다. 다음은 해당 프로세스의 일부로 고려해야 할 몇 가지 사항입니다.

* 클러스터에 대 한 초기 노드 형식 수 
* 각 노드 유형의 속성 (크기, 인스턴스 수, 기본, 인터넷 연결, Vm 수 등)
* 클러스터의 안정성 및 지속성 특성

### <a name="select-the-initial-number-of-node-types"></a>초기 노드 유형 수를 선택 합니다.
먼저, 만드는 클러스터를 어디에 사용할지 결정해야 합니다. 이 클러스터에 어떤 종류의 애플리케이션을 배포할 계획인가요? 애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요? 서비스(애플리케이션을 구성함)에는 더 유용한 RAM 또는 더 높은 CPU 사용률과 같은 서로 다른 인프라가 필요한가요? Service Fabric 클러스터는 하나 이상의 노드 유형 (주 노드 유형 및 하나 이상의 주 노드가 아닌 노드 유형)으로 구성 될 수 있습니다. 각 노드 유형은 가상 머신 확장 집합에 매핑됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. [노드 속성 및 배치 제약 조건을][placementconstraints] 설정 하 여 특정 서비스를 특정 노드 형식으로 제한할 수 있습니다.  자세한 내용은 [클러스터에서 시작 해야 하는 노드 형식의 수](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)를 참조 하세요.

### <a name="select-node-properties-for-each-node-type"></a>각 노드 유형에 대 한 노드 속성 선택
노드 유형은 연결 된 확장 집합에 있는 vm의 VM SKU, 번호 및 속성을 정의 합니다.

각 노드 유형에 대 한 최소 Vm 크기는 노드 유형에 대해 선택한 [내구성 계층][durability] 에 따라 결정 됩니다.

주 노드 유형에 대 한 최소 Vm 수는 선택한 [안정성 계층][reliability] 에 따라 결정 됩니다.

주 [노드 형식에 대 한 최소](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)권장 사항, 주 노드가 [아닌 노드 형식에 대 한 상태 저장 작업](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)및 [주 노드가 아닌 노드 형식에 대 한 상태 비저장 작업](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)을 참조 하세요.

최소 노드 수는이 노드 유형에 서 실행 하려는 응용 프로그램/서비스의 복제본 수를 기반으로 해야 합니다.  [Service Fabric 응용 프로그램에 대 한 용량 계획](service-fabric-capacity-planning.md) 을 통해 응용 프로그램을 실행 하는 데 필요한 리소스를 예상할 수 있습니다. 나중에 응용 프로그램 워크 로드를 변경 하기 위해 클러스터를 확장 하거나 축소할 수 있습니다. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 대 한 사용 후 삭제 OS 디스크 사용

*삭제 된 OS 디스크* 는 로컬 VM (가상 컴퓨터)에 생성 되며 원격 Azure Storage에 저장 되지 않습니다. 기존 영구 OS 디스크, 삭제 된 OS 디스크와 비교할 때 모든 Service Fabric 노드 유형 (기본 및 보조)에 권장 됩니다.

* OS 디스크에 대 한 읽기/쓰기 대기 시간 줄이기
* 빠른 다시 설정/이미지로 다시 설치 노드 관리 작업 사용
* 전체 비용 절감 (디스크가 무료 이며 추가 저장소 비용이 발생 하지 않음)

임시 OS 디스크는 특정 Service Fabric 기능이 아니라 Service Fabric 노드 형식에 매핑되는 Azure *가상 머신 확장 집합* 의 기능입니다. Service Fabric와 함께 사용 하려면 클러스터 Azure Resource Manager 템플릿에서 다음을 수행 해야 합니다.

1. 노드 형식에서 임시 OS 디스크에 대해 [지원 되는 AZURE VM 크기](../virtual-machines/windows/ephemeral-os-disks.md) 를 지정 하 고 VM 크기에는 os 디스크 크기를 지원 하기에 충분 한 캐시 크기가 있는지 확인 합니다 (아래 *참고* 참조). 예를 들어:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 캐시 크기가 VM 자체의 OS 디스크 크기 보다 크거나 같은 VM 크기를 선택 해야 합니다. 그렇지 않으면 Azure 배포에 오류가 발생할 수 있습니다 (처음에 허용 되는 경우에도).

2. `2018-06-01` 이상의 가상 머신 확장 집합 버전 (`vmssApiVersion`)을 지정 합니다.

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 배포 템플릿의 가상 머신 확장 집합 섹션에서 `diffDiskSettings`에 대 한 `Local` 옵션을 지정 합니다.

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "vhdContainers": ["[concat(reference(concat('Microsoft.Storage/storageAccounts/', parameters('vmStorageAccountName')), variables('storageApiVersion')).primaryEndpoints.blob, parameters('vmStorageAccountContainerName'))]"],
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

자세한 정보 및 추가 구성 옵션은 [Azure vm에 대 한 임시 OS 디스크](../virtual-machines/windows/ephemeral-os-disks.md) 를 참조 하세요. 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>클러스터의 내구성 및 안정성 수준 선택
지속성 계층은 기본 Azure 인프라와 함께 VM에 있는 권한을 이 시스템에 표시하는 데 사용됩니다. 주 노드 유형에서 이 권한을 사용하면 Service Fabric이 시스템 서비스 및 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(VM 다시 부팅, VM 이미지로 다시 설치, 또는 VM 마이그레이션 등)을 일시 중지할 수 있습니다. 주가 아닌 노드 형식에서 이 권한을 사용하면 Service Fabric이 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(예: VM 다시 부팅, VM 이미지로 다시 설치, VM 마이그레이션)을 일시 중지할 수 있습니다.  사용할 수준 및 시기에 대 한 다양 한 수준 및 권장 사항의 장점은 [클러스터의 내구성 특성][durability]을 참조 하세요.

안정성 계층은 주 노드 유형에서 이 클러스터에서 실행하려는 시스템 서비스의 복제본 수를 설정하는 데 사용됩니다. 복제본 수가 많을수록 클러스터에서 시스템 서비스를 신뢰할 수 있습니다.  사용할 수준 및 시기에 대 한 다양 한 수준 및 권장 사항의 장점은 [클러스터의 안정성 특성][reliability]을 참조 하세요. 

## <a name="enable-reverse-proxy-andor-dns"></a>역방향 프록시 및/또는 DNS 사용
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. 서비스 간 연결을 용이 하 게 하기 위해 Service Fabric는 추가 서비스 ( [DNS 서비스](service-fabric-dnsservice.md) 및 [역방향 프록시 서비스](service-fabric-reverseproxy.md))를 제공 합니다.  클러스터를 배포할 때 두 서비스를 모두 사용할 수 있습니다.

많은 서비스, 특히 컨테이너 화 된 서비스는 기존 URL 이름을 가질 수 있으며, 특히 응용 프로그램 "리프트 앤 시프트" 시나리오에서 Naming Service 프로토콜이 아닌 표준 DNS 프로토콜을 사용 하 여 이러한 이름을 확인할 수 있습니다. 바로 이것이 DNS 서비스의 용도입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다.

역방향 프록시는 HTTP 엔드포인트을 노출 하는 클러스터의 서비스 (HTTPS 포함)를 처리 합니다. 역방향 프록시는 특정 URI 형식을 제공 하 여 다른 서비스에 대 한 호출을 크게 간소화 합니다.  또한 역방향 프록시는 한 서비스에서 다른 서비스와 통신 하는 데 필요한 확인, 연결 및 재시도 단계를 처리 합니다.

## <a name="prepare-for-disaster-recovery"></a>재해 복구 준비
고가용성 전달의 중요한 부분은 서비스가 다른 모든 유형의 오류를 감당할 수 있는지 확인하는 것입니다. 계획되지 않으며 사용자 통제 하에 있지 않은 오류의 경우 특히 이러한 과정이 중요합니다. [재해 복구 준비](service-fabric-disaster-recovery.md) 에서는 올바르게 모델링 및 관리 되지 않는 경우 재해가 발생할 수 있는 몇 가지 일반적인 오류 모드에 대해 설명 합니다. 또한 재해가 발생 한 경우 수행 해야 하는 완화 작업과 작업에 대해서도 설명 합니다.

## <a name="production-readiness-checklist"></a>프로덕션 준비 검사 목록
애플리케이션 및 클러스터가 프로덕션 트래픽을 허용할 준비가 되었나요? 프로덕션 환경에 클러스터를 배포 하기 전에 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)을 실행 합니다. 이 검사 목록의 항목을 통해 작업 하 여 응용 프로그램 및 클러스터를 원활 하 게 실행 하세요. 프로덕션으로 전환 하기 전에 이러한 모든 항목을 체크 아웃 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [Windows를 실행 하는 Service Fabric 클러스터 만들기](service-fabric-best-practices-overview.md)
* [Linux를 실행 하는 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster