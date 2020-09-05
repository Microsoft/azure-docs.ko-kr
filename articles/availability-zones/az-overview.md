---
title: Azure의 지역 및 가용성 영역
description: 기술 및 규정 요구 사항을 충족 하기 위해 Azure에서 지역 및 가용성 영역에 대해 알아봅니다.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b9a400862280cbbc219e87695e363c0ec2e75ee6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018674"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure의 지역 및 가용성 영역

Microsoft Azure 서비스는 전역적으로 클라우드 작업을 효율적으로 실행할 수 있습니다. 기술 및 규제 고려 사항 (서비스 기능, 데이터 상주, 준수 요구 사항 및 대기 시간)에 따라 요구에 가장 적합 한 지역을 선택할 수 있습니다.

## <a name="terminology"></a>용어

Azure에서 지역 및 가용성 영역에 대 한 이해를 돕기 위해 주요 용어 또는 개념을 이해 하는 데 도움이 됩니다.

| 용어 또는 개념 | Description |
| --- | --- |
| region | 대기 시간이 정의 된 경계 내에 배포 되 고 전용 지역의 짧은 대기 시간 네트워크를 통해 연결 되는 데이터 센터 집합입니다. |
| geography | 하나 이상의 Azure 지역이 포함 된 전 세계 영역입니다. 지리는 데이터 상주 및 준수 경계를 유지 하는 불연속 시장을 정의 합니다. 지리적 위치를 통해 특정 데이터 상주 및 준수 요구 사항이 있는 고객은 데이터와 애플리케이션을 가깝게 유지할 수 있습니다. 지리적 위치는 전용 고용량 네트워킹 인프라에 대 한 연결을 통해 전체 지역 장애를 견딜 수 있는 내결함성이 있습니다. |
| 가용성 영역 | 지역 내의 고유한 실제 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. |
| 권장 영역 | 가장 광범위 한 서비스 기능을 제공 하 고 가용성 영역 현재 또는 나중에 지원 하도록 설계 된 지역입니다. 이러한 설정은 **권장 사항**에 따라 Azure Portal에서 지정 됩니다. |
| 대체 (기타) 지역 | 권장 지역이 있는 데이터 상주 경계 내에서 Azure의 공간을 확장 하는 지역입니다. 대체 지역은 대기 시간을 최적화 하 고 재해 복구 요구 사항에 대 한 두 번째 지역을 제공 하는 데 도움이 됩니다. Azure는 이러한 지역에 대 한 정기적인 평가를 수행 하 여 권장 지역이 되도록 하는 것은 아니지만 가용성 영역 지원 하도록 설계 되지 않았습니다. 이러한 설정은 Azure Portal에 **다른**것으로 지정 됩니다. |
| 기본 서비스 | 지역이 출시 될 때 모든 지역에서 사용할 수 있는 핵심 Azure 서비스입니다. |
| 기본 서비스 | 대체 지역에서 지역/서비스 일반 공급 또는 수요 기반 가용성의 12 개월 내에 모든 권장 지역에서 사용할 수 있는 Azure 서비스입니다. |
| 특수 서비스 | 사용자 지정/특수화 된 하드웨어에서 지원 되는 지역에 걸쳐 수요 중심의 가용성을 제공 하는 Azure 서비스입니다. |
| 지역 서비스 | 지역적으로에 배포 되 고 고객이 서비스를 배포할 지역을 지정할 수 있도록 하는 Azure 서비스입니다. 전체 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조 하세요. |
| 비 지역별 서비스 | 특정 Azure 지역에 대 한 종속성이 없는 Azure 서비스입니다. 지역이 아닌 서비스는 둘 이상의 지역에 배포 되 고, 지역 오류가 발생 한 경우 다른 지역의 서비스 인스턴스는 계속 해 서 고객 서비스를 제공 합니다. 전체 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조 하세요. |

## <a name="regions"></a>영역

지역은 대기 시간이 정의 된 경계 내에 배포 되 고 전용 지역의 짧은 대기 시간 네트워크를 통해 연결 되는 데이터 센터 집합입니다. Azure는 여러 지역에 걸쳐 지역 간 복원 력을 제공 하는 데 필요한 응용 프로그램을 유연 하 게 배포할 수 있도록 합니다. 자세한 내용은 [복원 력 기둥 개요](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)를 참조 하세요.

## <a name="availability-zones"></a>가용성 영역

가용성 영역은 데이터 센터 오류 로부터 응용 프로그램 및 데이터를 보호 하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 단일 지점 오류에서 보호하기 위해 가용성 영역에서 애플리케이션 및 데이터를 복제합니다. Azure는 가용성 영역을 통해 업계 최고의 99.99% VM 작동 시간 SLA를 제공합니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 예를 들어 Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 여러 영역에 있는 Vm이 동시에 업데이트 되도록 예약 되지 않았는지 확인 하기 위해 업데이트 도메인에서이 배포를 인식 합니다.

영역 내에서 컴퓨팅, 스토리지, 네트워킹 및 데이터 리소스를 공동 배치하고 다른 영역에 복제하여 애플리케이션 아키텍처에 고가용성을 빌드합니다. 가용성 영역을 지원하는 Azure 서비스는 다음의 두 범주로 나뉩니다.

- 영역 **서비스** – 리소스가 특정 영역 (예: 가상 머신, 관리 디스크, 표준 IP 주소)에 고정 되어 있는 경우
- **영역 중복 서비스** – Azure 플랫폼이 영역에서 자동으로 복제 되는 경우 (예: 영역 중복 저장소, SQL Database)

Azure에서 포괄적인 비즈니스 연속성을 구현하려면 Azure 지역 쌍과 가용성 영역의 조합을 사용하여 애플리케이션 아키텍처를 빌드하십시오. 고가용성에 대한 Azure 지역 내의 가용성 영역을 사용하여 애플리케이션 및 데이터를 동기적으로 복제하고 재해 복구 보호에 대한 Azure 지역에서 비동기적으로 복제할 수 있습니다.
 
![지역에서 차례로 한 영역의 개념 보기](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 가용성 영역 식별자 (위 그림의 숫자 1, 2, 3)는 논리적으로 각 구독의 실제 실제 영역에 독립적으로 매핑됩니다. 즉, 지정 된 구독의 가용성 영역 1 다른 구독의 가용성 영역 1와 다른 실제 영역을 참조할 수 있습니다. 결과적으로 가상 머신 배치에 대해 다른 구독에서 가용성 영역 Id를 사용 하지 않는 것이 좋습니다.

## <a name="region-and-service-categories"></a>영역 및 서비스 범주

지역에서 Azure 서비스를 사용 하는 방법에 대 한 azure의 접근 방식은 권장 지역 및 대체 지역에서 제공 되는 서비스를 표현 하는 것이 좋습니다.

- **권장 지역** -가장 광범위 한 서비스 기능을 제공 하 고 가용성 영역 현재 또는 향후 지원 하도록 설계 된 지역입니다. 이러한 설정은 **권장 사항**에 따라 Azure Portal에서 지정 됩니다.
- **대체 (기타) 지역** -권장 지역이 있는 데이터 상주 경계 내에서 Azure의 공간을 확장 하는 영역입니다. 대체 지역은 대기 시간을 최적화 하 고 재해 복구 요구 사항에 대 한 두 번째 지역을 제공 하는 데 도움이 됩니다. Azure는 이러한 지역에 대 한 정기적인 평가를 수행 하 여 권장 지역이 되도록 하는 것은 아니지만 가용성 영역 지원 하도록 설계 되지 않았습니다. 이러한 설정은 Azure Portal에 **다른**것으로 지정 됩니다.

### <a name="comparing-region-types"></a>지역 유형 비교

Azure 서비스는 기본, 일반 및 특수 서비스의 세 가지 범주로 그룹화 됩니다. 지정 된 지역에 서비스를 배포 하는 방법에 대 한 Azure의 일반 정책은 주로 지역 유형, 서비스 범주 및 고객 수요를 기반으로 합니다.

- 기본 **– 지역이** 일반 공급 되는 경우 모든 권장 및 대체 지역에서 사용할 수 있으며, 새 기본 서비스의 12 개월 이내에 일반 공급 될 수 있습니다.
- **메인스트림** – 지역/서비스 일반 공급의 12 개월 이내에 권장 되는 모든 지역에서 사용할 수 있습니다. 대체 지역에서 요청 기반 (대부분은 다른 지역의 많은 하위 집합에 이미 배포 됨).
- **특수** – 대상이 지정 된 서비스 제품으로, 종종 업계에서 집중 하거나 사용자 지정/특수 하드웨어로 지원 됩니다. 지역 간 수요 기반 가용성 (대부분은 이미 권장 지역의 많은 하위 집합에 배포 됨).

지정 된 지역에 배포 되는 서비스 및 미리 보기에 대 한 향후 로드맵 및 지역에서 서비스의 일반 공급을 보려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all)을 참조 하세요.

특정 지역에서 서비스 제공을 사용할 수 없는 경우 Microsoft 영업 담당자에 게 연락 하 여 관심을 공유할 수 있습니다.

| 영역 형식 | 비 지역별 | 기본 | 일반 | 특수화 | 가용성 영역 | 데이터 상주 |
| --- | --- | --- | --- | --- | --- | --- |
| 권장 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 수요 중심 | :heavy_check_mark: | :heavy_check_mark: |
| 또 | :heavy_check_mark: | :heavy_check_mark: | 수요 중심 | 수요 중심 | N/A | :heavy_check_mark: |

### <a name="services-by-category"></a>범주별 서비스

앞서 언급 했 듯이 Azure는 서비스를 기본, 메인스트림 및 전문화의 세 가지 범주로 분류 합니다. 서비스 범주는 일반 공급으로 할당 됩니다. 일반적으로 서비스는 특수 한 서비스로 수명 주기를 시작 하 고 수요 및 사용률이 늘어남에 따라 기본 또는 기본으로 승격 될 수 있습니다. 다음 표에서는 기본, 일반 또는 특수 서비스의 범주를 나열 합니다. 테이블에 대 한 다음 사항에 유의 해야 합니다.

- 일부 서비스는 비 지역입니다. 지역별 서비스에 대 한 자세한 내용 및 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.
- 이전 세대 가상 컴퓨터는 나열 되지 않습니다. 자세한 내용은 [가상 머신 크기의 이전 세대](../virtual-machines/sizes-previous-gen.md)에 대 한 설명서를 참조 하세요.

> [!div class="mx-tableFixed"]
> | 기본 | 일반 | 특수화 |
> | --- | --- | --- |
> | 계정 저장소 | API Management | FHIR용 Azure API |
> | Application Gateway | 앱 구성 | Azure Analysis Services |
> | Azure Backup | App Service | Azure Blockchain 서비스 |
> | Azure Cosmos DB | Automation | Azure Blueprints |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure Database for MariaDB |
> | Azure ExpressRoute | Azure Bastion | Azure 전용 HSM |
> | Azure SQL Database | Azure Cache for Redis | Azure Dev Spaces |
> | Cloud Services | Azure Cognitive Search | Azure Digital Twins |
> | Cloud Services: Av2 시리즈 | Azure Data Explorer | Azure Lab Services |
> | Cloud Services: Dv2 시리즈 | Azure Data Share | Azure NetApp Files |
> | Cloud Services: Dv3 시리즈 | Azure Database for MySQL | Azure 퀀텀 |
> | Cloud Services: Ev3 시리즈 | Azure Database for PostgreSQL | Azure 스프링 클라우드 서비스 |
> | Cloud Services: 인스턴스 수준 Ip | Azure Database Migration Service | Azure Time Series Insights |
> | Cloud Services: 예약된 IP | Azure Databricks | Azure VMware Solution by CloudSimple |
> | Disk Storage | Azure DDoS Protection | Cloud Services: G 시리즈 |
> | Event Hubs | Azure DevTest Labs | Cloud Services: H 시리즈 |
> | Key Vault | Azure Firewall | Cognitive Services: Custom Vision |
> | 부하 분산 장치 | Azure Firewall Manager | Cognitive Services: Speaker Recognition |
> | Service Bus | Azure Functions | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | Data Catalog |
> | Virtual Machine Scale Sets | Azure IoT Hub | Data Factory: Data Factory V1 |
> | Virtual Machines | AKS(Azure Kubernetes Service) | Data Lake Analytics |
> | Virtual Machines: Av2 시리즈 | Azure Machine Learning | Machine Learning Studio |
> | Virtual Machines: Bs 시리즈 | Azure Private Link | Microsoft Genomics |
> | Virtual Machines: DSv2 시리즈 | Azure Red Hat OpenShift | Remote Rendering |
> | Virtual Machines: DSv3 시리즈 | Azure SignalR Service | Spatial Anchors |
> | Virtual Machines: Dv2 시리즈 | Azure Site Recovery | StorSimple |
> | Virtual Machines: Dv3 시리즈 | Azure Stack Hub | 비디오 인덱서 |
> | Virtual Machines: ESv3 시리즈 | Azure Stream Analytics | Virtual Machines: DASv4 시리즈 |
> | Virtual Machines: Ev3 시리즈 | Azure Synapse Analytics | Virtual Machines: DAv4 시리즈 |
> | Virtual Machines: F 시리즈 | Batch | Virtual Machines: DCsv2 시리즈 |
> | Virtual Machines: FS 시리즈 | Cloud Services: M 시리즈 | Virtual Machines: EASv4 시리즈 |
> | Virtual Machines: 인스턴스 수준 Ip | Cognitive Services | Virtual Machines: EAv4 시리즈 |
> | Virtual Machines: 예약된 IP | Cognitive Services: Computer Vision | Virtual Machines: G 시리즈 |
> | Virtual Network | Cognitive Services: Content Moderator | Virtual Machines: GS 시리즈 |
> | VPN Gateway | Cognitive Services: 얼굴 | Virtual Machines: HBv1 시리즈 |
> |  | Cognitive Services: 폼 인식기 | Virtual Machines: HBv2 시리즈 |
> |  | Cognitive Services: Language Understanding | Virtual Machines: HCv1 시리즈 |
> |  | Cognitive Services: QnA Maker | Virtual Machines: H 시리즈 |
> |  | Cognitive Services: Speech Services | Virtual Machines: LS 시리즈 |
> |  | Container Instances | Virtual Machines: LSv2 시리즈 |
> |  | Container Registry | Virtual Machines: Mv2 시리즈 |
> |  | Data Factory | Virtual Machines: NC 시리즈 |
> |  | Event Grid | Virtual Machines: NCv2 시리즈 |
> |  | HDInsight | Virtual Machines: NCv3 시리즈 |
> |  | Logic Apps | Virtual Machines: NDs 시리즈 |
> |  | Media Services | Virtual Machines: NDv2 시리즈 |
> |  | Network Watcher | Virtual Machines: NV 시리즈 |
> |  | Notification Hubs | Virtual Machines: NVv3 시리즈 |
> |  | Power BI Embedded | Virtual Machines: NVv4 시리즈 |
> |  | 프리미엄 Blob Storage | Virtual Machines: Azure의 SAP HANA(대규모 인스턴스) |
> |  | 프리미엄 파일 저장소 | Visual Studio App Center |
> |  | 저장소: Archive Storage |  |
> |  | 울트라 디스크 저장소 |  |
> |  | Virtual Machines: Ddsv4 시리즈 |  |
> |  | Virtual Machines: Ddv4 시리즈 |  |
> |  | Virtual Machines: Dsv4 시리즈 |  |
> |  | Virtual Machines: Dv4 시리즈 |  |
> |  | Virtual Machines: Edsv4 시리즈 |  |
> |  | Virtual Machines: Edv4 시리즈 |  |
> |  | Virtual Machines: Esv4 시리즈 |  |
> |  | Virtual Machines: Ev4 시리즈 |  |
> |  | Virtual Machines: Fsv2 시리즈 |  |
> |  | Virtual Machines: M 시리즈 |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>서비스 복원 력

모든 Azure 관리 서비스는 지역 수준 오류에서 탄력적으로 대처할 수 있도록 설계 되었습니다. 오류가 발생 하는 경우 지역 내에서 하나 이상의 가용성 영역 오류는 전체 지역 오류와 비교 하 여 더 작은 오류 반지름이 있습니다. Azure는 지역 내에서 또는 다른 Azure 지역에서 관리 서비스의 영역 수준 오류 로부터 복구할 수 있습니다. Azure는 지역 내에서 한 번에 한 영역 내에서 중요 한 유지 관리를 수행 하 여 지역 내에서 가용성 영역에 배포 된 고객 리소스에 영향을 주는 오류를 방지 합니다.

### <a name="pricing-for-vms-in-availability-zones"></a>가용성 영역 Vm에 대 한 가격 책정

가용성 영역에 배포된 가상 머신에 대한 추가 비용은 없습니다. Azure 지역 내에서 두 개 이상의 가용성 영역에 두 개 이상의 VM을 배포하면 VM 작동 시간 SLA 99.99%가 제공됩니다. 내부 가용성 영역 VM 대 VM 데이터 전송 요금이 추가됩니다. 자세한 내용은 [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/) 페이지를 검토하세요.

### <a name="get-started-with-availability-zones"></a>가용성 영역 시작하기

- [가상 머신 만들기](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell을 사용하여 Managed Disk 추가](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [영역 중복 가상 머신 확장 집합 만들기](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역에서 VM 부하 분산](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역 내에 VM 부하 분산](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [영역 중복 스토리지](../storage/common/storage-redundancy-zrs.md)
- [SQL 데이터베이스](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [Event Hubs 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus 지리적 재해 복구](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [영역 중복 가상 네트워크 게이트웨이 만들기](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB에 대 한 영역 중복 영역 추가](../cosmos-db/high-availability.md#availability-zone-support)
- [Redis 가용성 영역에 대 한 Azure 캐시 시작](https://aka.ms/redis/az/getstarted)
- [Azure Active Directory Domain Services 인스턴스 만들기](../active-directory-domain-services/tutorial-create-instance.md)
- [가용성 영역를 사용 하는 AKS (Azure Kubernetes Service) 클러스터 만들기](../aks/availability-zones.md)

## <a name="next-steps"></a>다음 단계

- [Azure에서 가용성 영역를 지 원하는 지역](az-region.md)
- [빠른 시작 템플릿](https://aka.ms/azqs)
