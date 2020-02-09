---
title: SAP 응용 프로그램용 Azure 근접 배치 그룹 | Microsoft Docs
description: Azure 근접 배치 그룹을 사용 하는 SAP 배포 시나리오에 대해 설명 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277604"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹
Sap NetWeaver 또는 SAP S/4HANA 아키텍처를 기반으로 하는 SAP 응용 프로그램은 SAP 응용 프로그램 계층과 SAP 데이터베이스 계층 간의 네트워크 대기 시간을 구분 합니다. 이러한 민감도는 응용 프로그램 계층에서 실행 되는 대부분의 비즈니스 논리에 대 한 결과입니다. SAP 응용 프로그램 계층은 비즈니스 논리를 실행 하기 때문에 데이터베이스 계층에 대 한 쿼리를 초당 수천 또는 수만 개의 속도로 데이터베이스 계층에 보냅니다. 대부분의 경우 이러한 쿼리의 특성은 간단 합니다. 500 마이크로초 이하로 데이터베이스 계층에서 실행 되는 경우가 많습니다.

응용 프로그램 계층에서 데이터베이스 계층으로 이러한 쿼리를 전송 하 고 결과 집합을 다시 수신 하는 데 네트워크에서 소요 되는 시간은 비즈니스 프로세스를 실행 하는 데 걸리는 시간에 큰 영향을 줍니다. 네트워크 대기 시간으로 인 한 이러한 민감도는 SAP 배포 프로젝트에서 최적의 네트워크 대기 시간을 확보 해야 하는 이유입니다. 네트워크 대기 시간을 분류 하는 방법에 대 한 지침은 [SAP Note #1100926-FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E) 을 참조 하세요.

많은 Azure 지역에서 데이터 센터 수가 증가 했습니다. 이러한 증가는 가용성 영역 도입에 의해 트리거됩니다. 이와 동시에, 특히 고성능 SAP 시스템에 대 한 고객은 M 시리즈 제품군 또는 HANA 큰 인스턴스에서 더 특수 한 VM Sku를 사용 하 고 있습니다. 이러한 Azure virtual machine 유형은 특정 Azure 지역에 있는 모든 데이터 센터에서 사용할 수 없습니다. 이러한 두 성향을 인해 고객은 최적의 범위에 없는 네트워크 대기 시간을 경험 했습니다. 경우에 따라이 대기 시간으로 인해 SAP 시스템의 성능이 저하 될 수도 있습니다.

이러한 문제를 방지 하기 위해 Azure는 [근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)을 제공 합니다. 이 새로운 기능은 다양 한 SAP 시스템을 배포 하는 데 이미 사용 되었습니다. 근접 배치 그룹에 대 한 제한 사항은이 단락 시작 부분에 있는 참조 문서를 참조 하세요. 이 문서에서는 Azure 근접 배치 그룹을 사용 하거나 사용 해야 하는 SAP 시나리오에 대해 설명 합니다.

## <a name="what-are-proximity-placement-groups"></a>근접 배치 그룹 이란? 
Azure 근접 배치 그룹은 논리적 구문입니다. 하나는 정의 된 경우 Azure 지역 및 Azure 리소스 그룹에 바인딩됩니다. Vm을 배포 하는 경우 다음에서 근접 배치 그룹을 참조 합니다.

- 데이터 센터에 배포 된 첫 번째 Azure VM입니다. 첫 번째 가상 머신은 특정 가용성 영역에 대 한 사용자 정의와 최종적으로 결합 되는 Azure 할당 알고리즘을 기반으로 데이터 센터에 배포 되는 "범위 VM"으로 생각할 수 있습니다.
- 근접 배치 그룹을 참조 하는 모든 후속 Vm은 배포 된 모든 Azure Vm을 첫 번째 가상 머신과 동일한 데이터 센터에 배치 합니다.

> [!NOTE]
> 첫 번째 VM이 배치 된 데이터 센터에서 특정 VM 유형을 실행할 수 있는 호스트 하드웨어가 배포 되지 않은 경우 요청 된 VM 유형의 배포에 성공 하지 못합니다. 오류 메시지를 받게 됩니다.

단일 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 에는 여러 개의 근접 배치 그룹이 할당 될 수 있습니다. 그러나 근접 배치 그룹은 하나의 Azure 리소스 그룹에만 할당할 수 있습니다.

근접 배치 그룹을 사용 하는 경우 다음 사항을 염두에 두어야 합니다.

- SAP 시스템에 대 한 최적의 성능을 목표로 하 고, 근접 배치 그룹을 사용 하 여 시스템에 대 한 단일 Azure 데이터 센터로 제한 하는 경우 배치 그룹 내에 있는 모든 유형의 VM 제품군을 결합 하지 못할 수 있습니다. 이러한 제한 사항은 특정 VM 유형을 실행 하는 데 필요한 호스트 하드웨어가 배치 그룹의 "범위 지정 VM"이 배포 된 데이터 센터에 없을 수도 있기 때문에 발생 합니다.
- 이러한 SAP 시스템의 수명 주기 동안 시스템을 다른 데이터 센터로 이동 해야 할 수 있습니다. 이러한 이동은 예를 들어 노드 4 개에서 16 개 노드로 이동 하는 경우 처럼 확장 HANA DBMS 계층을 결정 하 고, 데이터 센터에서 사용한 형식의 추가 12 개 Vm을 얻기에 충분 한 용량이 없는 경우에 필요할 수 있습니다.
- 하드웨어 서비스 해제로 인해 Microsoft는 처음에 사용 했던 것이 아니라 다른 데이터 센터에서 사용한 VM 유형에 대 한 용량을 구축할 수 있습니다. 이 시나리오에서는 모든 근접 배치 그룹의 Vm을 다른 데이터 센터로 이동 해야 할 수 있습니다.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure Vm만 사용 하는 SAP 시스템을 사용 하는 근접 배치 그룹
Azure에서 대부분의 SAP NetWeaver 및 S/4HANA 시스템 배포는 [HANA 큰 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 사용 하지 않습니다. HANA 큰 인스턴스를 사용 하지 않는 배포의 경우 SAP 응용 프로그램 계층과 DBMS 계층 간에 최적의 성능을 제공 하는 것이 중요 합니다. 이렇게 하려면 시스템에 대해서만 Azure 근접 배치 그룹을 정의 합니다.

대부분의 고객 배포에서 고객은 SAP 시스템용 단일 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 을 작성 합니다. 이 경우, 예를 들어 프로덕션 ERP 시스템 리소스 그룹과 해당 근접 배치 그룹 사이에 일 대 일 관계가 있습니다. 경우에 따라 고객은 리소스 그룹을 수평으로 구성 하 고 모든 프로덕션 시스템을 단일 리소스 그룹으로 수집 합니다. 이 경우 프로덕션 SAP 시스템의 리소스 그룹과 프로덕션 SAP ERP, SAP BW 등에 대 한 여러 개의 근접 배치 그룹 간에 일 대 다 관계가 형성 됩니다.

단일 근접 배치 그룹에 여러 SAP 프로덕션 또는 비프로덕션 시스템을 번들로 묶는 것이 좋습니다. 적은 수의 SAP 시스템 또는 SAP 시스템 및 일부 주변 응용 프로그램에서 대기 시간이 짧은 네트워크 통신을 수행 해야 하는 경우 이러한 시스템을 하나의 근접 배치 그룹으로 이동 하는 것을 고려할 수 있습니다. 근접 배치 그룹으로 그룹화 하는 더 많은 시스템이 있으므로 시스템 번들을 사용 하지 않는 것이 좋습니다.

- 근접 배치 그룹의 범위를 지정 하는 특정 데이터 센터에서 실행할 수 없는 VM 유형이 필요 합니다.
- 시간이 지남에 따라 근접 배치 그룹에 소프트웨어를 추가 하기 때문에 더 필요할 때 M 시리즈 Vm과 같은 비 메인스트림 Vm의 리소스는 결국 충족 되지 않을 수 있습니다.

설명 된 대로 이상적인 구성은 다음과 같습니다.

![Azure Vm만 포함 된 근접 배치 그룹](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

이 경우 단일 SAP 시스템은 각각 하나의 근접 배치 그룹을 포함 하는 하나의 리소스 그룹으로 그룹화 됩니다. HANA 확장 또는 DBMS 확장 구성을 사용 하는지 여부에 대 한 종속성은 없습니다.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>근접 배치 그룹 및 HANA Large Instances
일부 SAP 시스템이 응용 프로그램 계층에 대해 [Hana 큰 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 를 사용 하는 경우 [수정 버전 4 행 또는 스탬프](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)에 배포 된 hana 큰 인스턴스 단위를 사용 하는 경우 hana 큰 인스턴스 단위와 Azure vm 간의 네트워크 대기 시간이 크게 향상 될 수 있습니다. 한 가지 향상 된 것은 HANA Large Instances 단위가 배포 되 면 근접 배치 그룹을 사용 하 여 배포 하는 것입니다. 이러한 근접 배치 그룹을 사용 하 여 응용 프로그램 계층 Vm을 배포할 수 있습니다. 따라서 이러한 Vm은 HANA Large Instances 단위를 호스트 하는 동일한 데이터 센터에 배포 됩니다.

HANA 대량 인스턴스 단위가 수정 버전 4 스탬프 또는 행에 배포 되었는지 확인 하려면 [Azure Portal를 통해 AZURE Hana Large instances 제어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)문서를 확인 합니다. Hana Large Instances 단위의 특성 개요에서 HANA 대량 인스턴스 단위가 배포 될 때 생성 되었으므로 근접 배치 그룹의 이름도 확인할 수 있습니다. 특성 개요에 표시 되는 이름은 응용 프로그램 계층 Vm을 배포 해야 하는 근접 배치 그룹의 이름입니다.

Azure virtual machines만 사용 하는 SAP 시스템에 비해 HANA 큰 인스턴스를 사용 하는 경우 사용할 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 의 수를 유연 하 게 결정할 수 있습니다. [Hana Large instances 테넌트](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) 의 모든 Hana large instances 단위는 [이 문서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)에 설명 된 대로 단일 리소스 그룹으로 그룹화 됩니다. 프로덕션 및 비프로덕션 시스템이 나 다른 시스템과 분리 하기 위해 다른 테넌트에 배포 하는 경우를 제외 하 고 모든 HANA Large instances 유닛은 하나의 HANA Large Instances 테넌트에 배포 됩니다. 이 테넌트는 리소스 그룹과 일 대 일 관계를 가집니다. 하지만 개별 근접 배치 그룹은 각 단일 단위에 대해 정의 됩니다.

결과적으로, 단일 테넌트에 대 한 Azure 리소스 그룹 및 근접 배치 그룹 간의 관계는 다음과 같습니다.

![근접 배치 그룹 및 HANA Large Instances](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>근접 배치 그룹을 사용 하는 배포의 예
다음은 Azure 근접 배치 그룹을 사용 하 여 Vm을 배포 하는 데 사용할 수 있는 몇 가지 PowerShell 명령입니다.

첫 번째 단계는 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에 로그인 한 후 배포에 사용 하려는 Azure 구독에 있는지 확인 하는 것입니다.

<pre><code>
Get-AzureRmContext
</code></pre>

다른 구독으로 변경 해야 하는 경우 다음 명령을 실행 하 여이 작업을 수행할 수 있습니다.

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

다음 명령을 실행 하 여 새 Azure 리소스 그룹을 만듭니다.

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

다음 명령을 실행 하 여 새 근접 배치 그룹을 만듭니다.

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

다음과 같은 명령을 사용 하 여 첫 번째 VM을 근접 배치 그룹에 배포 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

위의 명령은 Windows 기반 VM을 배포 합니다. 이 VM이 성공적으로 배포 되 면 근접 배치 그룹의 데이터 센터 범위가 Azure 지역 내에서 정의 됩니다. 이전 명령에 표시 된 것과 같이 근접 배치 그룹을 참조 하는 모든 후속 VM 배포는 동일한 Azure 데이터 센터에 배포 됩니다 .이 경우 해당 데이터 센터에 배치 된 하드웨어에서 VM 유형을 호스트 하 고 해당 VM 유형에 대 한 용량을 사용할 수 있습니다. 있게.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>근접 배치 그룹을 사용 하 여 가용성 집합 및 가용성 영역 결합
SAP 시스템 배포에 가용성 영역를 사용 하는 경우의 단점 중 하나는 특정 영역 내에서 가용성 집합을 사용 하 여 SAP 응용 프로그램 계층을 배포할 수 없다는 것입니다. SAP 응용 프로그램 계층을 DBMS 계층과 동일한 영역에 배포 하려고 합니다. 단일 VM을 배포할 때 가용성 영역 및 가용성 집합을 참조 하는 것은 지원 되지 않습니다. 따라서 이전에는 영역을 참조 하 여 응용 프로그램 계층을 강제로 배포 했습니다. 응용 프로그램 계층 Vm이 다른 업데이트 및 장애 도메인에 걸쳐 분산 되었는지 확인 하는 기능이 손실 됩니다.

근접 배치 그룹을 사용 하 여이 제한을 무시할 수 있습니다. 배포 시퀀스는 다음과 같습니다.

- 근접 배치 그룹을 만듭니다.
- 가용성 영역을 참조 하 여 앵커 VM (일반적으로 DBMS 서버)을 배포 합니다.
- Azure 근접 그룹을 참조 하는 가용성 집합을 만듭니다. 이 문서의 뒷부분에 나오는 명령을 참조 하세요.
- 가용성 집합 및 근접 배치 그룹을 참조 하 여 응용 프로그램 계층 Vm을 배포 합니다.

이전 섹션에서 설명한 대로 첫 번째 VM을 배포 하는 대신 VM을 배포할 때 가용성 영역 및 근접 배치 그룹을 참조 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

이 가상 머신이 성공적으로 배포 되 면 하나의 가용성 영역에서 SAP 시스템의 데이터베이스 인스턴스를 호스팅합니다. 근접 배치 그룹의 범위는 사용자가 정의한 가용성 영역을 나타내는 데이터 센터 중 하나로 고정 됩니다.

동일한 영역 또는 영역 및 동일한 근접 배치 그룹을 참조 하 여 DBMS Vm과 동일한 방식으로 중앙 서비스 Vm을 배포 한다고 가정 합니다. 다음 단계에서는 SAP 시스템의 응용 프로그램 계층에 사용할 가용성 집합을 만들어야 합니다.

근접 배치 그룹을 정의 하 고 만들어야 합니다. 가용성 집합을 만드는 명령은 근접 배치 그룹 ID (이름 아님)에 대 한 추가 참조가 필요 합니다. 다음 명령을 사용 하 여 근접 배치 그룹의 ID를 가져올 수 있습니다.

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

가용성 집합을 만들 때 관리 디스크 (달리 지정 되지 않은 경우 기본값) 및 근접 배치 그룹을 사용 하는 경우 추가 매개 변수를 고려해 야 합니다.

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

세 개의 장애 도메인을 사용 하는 것이 가장 좋습니다. 그러나 지원 되는 장애 도메인의 수는 지역 마다 다를 수 있습니다. 이 경우 특정 지역에 사용할 수 있는 최대 장애 도메인 수는 2입니다. 응용 프로그램 계층 Vm을 배포 하려면 다음과 같이 가용성 집합 이름 및 근접 배치 그룹 이름에 대 한 참조를 추가 해야 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

이 배포의 결과는 다음과 같습니다.
- 특정 가용성 영역 또는 가용성 영역에 있는 SAP 시스템에 대 한 DBMS 계층 및 중앙 서비스입니다.
- DBMS VM 또는 Vm과 동일한 Azure 데이터 센터의 가용성 집합을 통해 위치한 SAP 응용 프로그램 계층입니다.

> [!NOTE]
> 하나의 영역에 하나의 DBMS VM을 배포 하 고 두 번째 DBMS VM을 다른 영역에 배포 하 여 고가용성 구성을 만들기 때문에 각 영역에 대해 서로 다른 근접 배치 그룹이 필요 합니다. 사용 하는 모든 가용성 집합에 대해서도 마찬가지입니다.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>기존 시스템을 근접 배치 그룹으로 이동
SAP 시스템을 이미 배포한 경우에는 중요 한 시스템 중 일부의 네트워크 대기 시간을 최적화 하 고 동일한 데이터 센터에서 응용 프로그램 계층 및 DBMS 계층을 찾을 수 있습니다. 전체 Azure 가용성 집합의 Vm을 이미 범위가 지정 된 기존 근접 배치 그룹으로 이동 하려면 가용성 집합의 모든 Vm을 종료 하 고 Azure Portal를 통해 기존 근접 배치 그룹에 가용성 집합을 할당 해야 합니다. PowerShell 또는 CLI. 가용성 집합의 일부가 아닌 VM을 기존 근접 배치 그룹으로 이동 하려는 경우에는 VM을 종료 하 고 기존 근접 배치 그룹에 할당 하기만 하면 됩니다. 


## <a name="next-steps"></a>다음 단계
설명서를 확인 하세요.

- [Azure의 SAP 워크 로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [미리 보기: Azure CLI을 사용 하 여 근접 배치 그룹에 Vm 배포](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [미리 보기: PowerShell을 사용 하 여 근접 배치 그룹에 Vm 배포](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [SAP 워크 로드에 대 한 Azure Virtual Machines DBMS 배포에 대 한 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

