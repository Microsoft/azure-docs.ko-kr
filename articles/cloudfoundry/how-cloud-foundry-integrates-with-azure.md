---
title: Azure와 Cloud Foundry를 통합하는 방법 | Microsoft Docs
description: Cloud Foundry Azure 서비스를 사용 하 여 엔터프라이즈 환경을 개선 하는 방법을 설명 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277339"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Azure와 Cloud Foundry 통합

[Cloud Foundry](https://docs.cloudfoundry.org/)는 클라우드 공급자의 IaaS 플랫폼에서 실행되는 PaaS 플랫폼입니다. 클라우드 공급자 간에 일관된 애플리케이션 배포 환경을 제공합니다. 또한 엔터프라이즈급 HA, 확장성 및 비용 절감을 통해 다양 한 Azure 서비스와 통합할 수 있습니다.
Cloud Foundry에는 라우팅, 인증, 응용 프로그램 수명 주기 관리, 서비스 관리, 메시징 및 모니터링을 포함 하 여 유연 하 게 확장할 수 있는 [6 개의 하위 시스템이](https://docs.cloudfoundry.org/concepts/architecture/)있습니다. 각 하위 시스템에 대해 대응 Azure 서비스를 사용 하도록 Cloud Foundry를 구성할 수 있습니다. 

![Azure 통합 아키텍처의 Cloud Foundry](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. 고가용성 및 확장성
### <a name="managed-disk"></a>관리 디스크:
Bosh는 디스크 만들기 및 삭제 루틴에 대해 Azure CPI (클라우드 공급자 인터페이스)를 사용 합니다. 기본적으로 관리되지 않는 디스크가 사용됩니다. 고객이 수동으로 스토리지 계정을 만든 다음, CF 매니페스트 파일에서 계정을 구성해야 합니다. 이는 저장소 계정 당 디스크 수 제한 때문입니다.
이제 [관리 디스크](https://azure.microsoft.com/services/managed-disks/)를 사용할 수 있으므로 가상 머신에 대해 관리되는 안전하고 신뢰할 수 있는 디스크 스토리지를 제공합니다. 고객은 더 이상 크기 조정 및 HA에서 스토리지 계정을 사용하지 않아도 됩니다. Azure에서는 디스크를 자동으로 정렬합니다. 새 배포 또는 기존 배포의 경우에는 Azure CPI가 CF 배포 중에 관리 디스크의 생성 또는 마이그레이션을 처리 합니다. PCF 1.11에서 지원 됩니다. 참조는 오픈 소스 Cloud Foundry [관리 디스크 지침](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks)을 탐색할 수도 있습니다. 
### <a name="availability-zone-"></a>가용성 영역 *
클라우드-네이티브 애플리케이션 플랫폼인 Cloud Foundry는 [네 가지 수준의 고가용성](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html)을 포함하여 설계되었습니다. 처음 세 가지 수준의 소프트웨어 오류가 CF 시스템 자체에서 처리될 수 있는 반면 플랫폼 내결함성은 클라우드 공급자에 의해 제공됩니다. 주요 CF 구성 요소는 클라우드 공급자의 플랫폼 HA 솔루션으로 보호되어야 합니다. 여기에는 GoRouters, Diego Brains, CF 데이터베이스 및 서비스 타일이 포함됩니다. 기본적으로 [Azure 가용성 집합](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets)은 데이터 센터의 클러스터 간 내결함성에 사용됩니다.
이제 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview )이 릴리스되었으므로 내결함성에서 데이터 센터 간에 대기 시간 중복이 짧아집니다.
Azure 가용성 영역이 VM 집합을 2개 이상의 데이터 센터에 배치하여 HA를 달성하면 각 VM 집합은 다른 집합에 중복됩니다. 하나의 영역이 다운된 경우 다른 집합이 아직 작동하므로 재해로부터 격리됩니다.
> [!NOTE] 
> Azure 가용성 영역은 아직 일부 지역에서 제공되지 않습니다. 최신 [지원되는 지역 목록에 대한 발표](https://docs.microsoft.com/azure/availability-zones/az-overview)를 확인하세요. 오픈 소스 Cloud Foundry의 경우 [오픈 소스 Cloud Foundry 지침에 대한 Azure 가용성 영역](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)을 확인하세요.

## <a name="2-network-routing"></a>2. 네트워크 라우팅
기본적으로 Azure 기본 부하 분산 장치는 들어오는 CF API/앱 요청에 사용되어 Gorouters에 전달합니다. Diego Brain, MySQL, ERT와 같은 CF는 부하 분산 장치를 사용하여 HA를 위해 트래픽을 분산할 수도 있습니다. 또한 Azure는 완전히 관리 되는 부하 분산 솔루션 집합을 제공 합니다. TLS 종료 ("SSL 오프 로드") 또는 HTTP/HTTPS 요청 응용 프로그램 계층 처리를 찾고 있는 경우 Application Gateway 고려 하십시오. 계층 4에 대한 고가용성 및 확장성 부하 분산은 표준 부하 분산 장치를 사용하세요.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)에서는 SSL 오프로딩, 종단 간 SSL, 웹 애플리케이션 방화벽, 쿠키 기반 세션 선호도 등을 비롯한 다양한 계층 7 부하 분산 기능을 제공합니다. [오픈 소스 Cloud Foundry에서 Application Gateway를 구성](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway)할 수 있습니다. PCF의 경우 POC 테스트에 대한 [PCF 2.1 릴리스 정보](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway)를 확인합니다.

### <a name="azure-standard-load-balancer-"></a>Azure 표준 Load Balancer *
Azure Load Balancer는 계층 4 부하 분산 장치입니다. 부하 분산 된 집합의 서비스 인스턴스 간에 트래픽을 분산 하는 데 사용 됩니다. 표준 버전에서는 기본 버전을 기반으로 [고급 기능](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)을 제공합니다. 예: 1 백 엔드 풀 최대한도는 100대에서 1000대 VM으로 증가되었습니다.  2. 이제 엔드포인트는 단일 가용성 집합 대신 여러 가용성 집합을 지원합니다.  3. HA 포트, 다양 한 모니터링 데이터 등과 같은 추가 기능 Azure 가용성 영역으로 이동 하는 경우 표준 부하 분산 장치가 필요 합니다. 새 배포에서 Azure 표준 Load Balancer를 시작하는 것이 좋습니다. 

## <a name="3-authentication"></a>3. 인증 
[Cloud Foundry 사용자 계정 및 인증](https://docs.cloudfoundry.org/concepts/architecture/uaa.html)은 CF 및 다양한 해당 구성 요소에 대한 중앙 ID 관리 서비스입니다. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 기본적으로 UAA는 Cloud Foundry 인증에 사용됩니다. 고급 옵션으로 UAA는 Azure AD를 외부 사용자 저장소로도 지원합니다. Azure AD 사용자는 Cloud Foundry 계정 없이 해당 LDAP ID를 사용하여 Cloud Foundry에 액세스할 수 있습니다. 다음 단계에 따라 [PCF에서 UAA용 Azure AD를 구성합니다](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Cloud Foundry 런타임 시스템용 데이터 저장소
Cloud Foundry에서는 애플리케이션 런타임 시스템 스토리지에 Azure Blobstore 또는 Azure MySQL/PostgreSQL 서비스를 사용하는 뛰어난 확장성을 제공합니다.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Cloud Foundry 클라우드 컨트롤러에 대한 Azure Blobstore
클라우드 컨트롤러 Blobstore는 buildpacks, droplets, 패키지 및 리소스 풀에 대한 중요한 데이터 저장소입니다. 기본적으로 NFS 서버는 클라우드 컨트롤러 Blobstore에 사용됩니다. 단일 실패 지점을 방지하려면 Azure Blob Storage를 외부 저장소로 사용합니다. 백그라운드용 [Cloud Foundry 설명서](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) 및 [Pivotal Cloud Foundry의 옵션](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)을 체크 아웃합니다.

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Cloud Foundry 탄력적인 런타임 데이터베이스인 MySQL/PostgreSQL *
CF 탄력적 런타임에는 두 가지 주요 시스템 데이터베이스가 필요합니다.
#### <a name="ccdb"></a>CCDB 
클라우드 컨트롤러 데이터베이스입니다.  클라우드 컨트롤러는 시스템에 액세스하는 클라이언트에 대한 REST API 엔드포인트를 제공합니다. CCDB는 클라우드 컨트롤러에서 조직, 공백, 서비스, 사용자 역할 등에 대한 테이블을 저장합니다.
#### <a name="uaadb"></a>UAADB 
사용자 계정 및 인증에 대한 데이터베이스입니다. 사용자 인증 관련 데이터(예: 암호화된 사용자 이름 및 암호)를 저장합니다.

기본적으로 로컬 시스템 데이터베이스(MySQL)를 사용할 수 있습니다. HA의 크기를 조정하기 위해 Azure 관리되는 MySQL 또는 PostgreSQL 서비스를 활용합니다. [오픈 소스 Cloud Foundry에서 CCDB, UAADB 및 다른 시스템 데이터베이스에 대해 Azure MySQL/PostgreSQL 활성화](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service) 지침은 다음과 같습니다.

## <a name="5-open-service-broker"></a>5. Service Broker 열기
Azure Service Broker는 Azure 서비스에 대한 애플리케이션의 액세스를 관리하기 위해 일관된 인터페이스를 제공합니다. 새 [Azure 프로젝트용 Service Broker 열기](https://github.com/Azure/open-service-broker-azure)에서는 Cloud Foundry, OpenShift 및 Kubernetes의 애플리케이션에 서비스를 제공하는 단일하고 간단한 방법을 제공합니다. PCF에 대한 배포 지침은 [PCF 타일용 Azure 오픈 Service Broker](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure)를 참조하세요.

## <a name="6-metrics-and-logging"></a>6. 메트릭 및 로깅
Azure Log Analytics 노즐은 [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) 에서 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/)로 메트릭을 전달 하는 Cloud Foundry 구성 요소입니다. Nozzle을 사용하면 여러 배포에서 CF 시스템 상태와 성능 메트릭을 수집, 확인 및 분석할 수 있습니다.
[여기](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) 를 클릭 하 여 오픈 소스 및 Pivotal Cloud Foundry 환경 모두에 Azure Log Analytics 노즐을 배포한 다음 Azure Monitor logs 콘솔에서 데이터에 액세스 하는 방법을 알아보세요. 
> [!NOTE]
> PCF 2.0에서 Vm에 대한 BOSH 상태 메트릭은 기본적으로 Loggregator Firehose에 전달 되 고 Azure Monitor logs 콘솔에 통합 됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. 비용 절감
### <a name="cost-saving-for-devtest-environments"></a>개발/테스트 환경에 대한 비용 절감
#### <a name="b-series-"></a>B 시리즈: *
일반적으로 F 및 D VM 시리즈를 Pivotal Cloud Foundry 프로덕션 환경에 일반적으로 권장하는 반면 새롭게 "높은 성능을 발휘할 수 있는" [B 시리즈](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/)는 새로운 옵션을 제공합니다. B 시리즈 간의 안정적인 Vm은 웹 서버, 소규모 데이터베이스 및 개발 및 테스트 환경과 같이 CPU의 전체 성능이 지속적으로 필요 하지 않은 작업에 적합 합니다. 이러한 작업에는 일반적으로 버스트 가능한 성능 요구 사항이 있습니다. $0.05/시간(F1)에 비한 $0.012/시간(B1)입니다. 자세한 내용은 [VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) 및 [가격](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 전체 목록을 참조하세요. 
#### <a name="managed-standard-disk"></a>관리되는 표준 디스크 
프리미엄 디스크는 프로덕션에서 신뢰할 수 있는 성능에 대해 권장했습니다.  [관리 디스크](https://azure.microsoft.com/services/managed-disks/)에서 표준 스토리지는 다른 성능과 유사한 안정성을 제공할 수도 있습니다. 개발/테스트 또는 중요 하지 않은 환경과 같이 성능이 중요 하지 않은 워크 로드의 경우 관리 되는 표준 디스크는 저렴 한 비용으로 대체 옵션을 제공 합니다.  
### <a name="cost-saving-in-general"></a>일반적인 비용 절감 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Azure 예약으로 VM 비용 대폭 절감: 
현재 환경이 일반적으로 무기한으로 가동되더라도 모든 CF VM은 "요청 시" 가격 책정을 사용하여 요금이 청구됩니다. 이제 1년 또는 3년의 VM 용량을 예약하고 45-65%를 할인받을 수 있습니다. 할인은 환경을 변경하지 않고 청구 시스템에 적용됩니다. 자세한 내용은 [Azure 예약의 작동 원리](https://azure.microsoft.com/pricing/reserved-vm-instances/)를 참조하세요. 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>더 작은 크기의 프리미엄 디스크 관리: 
관리 디스크가 더 작은 디스크 크기를 지원합니다(예: 프리미엄 및 표준 디스크 모두에 P4(32GB) 및 P6(64GB)). 워크로드가 작은 경우 표준 프리미엄 디스크에서 관리되는 프리미엄 디스크로 마이그레이션하면 비용을 절약할 수 있습니다.
#### <a name="use-azure-first-party-services"></a>Azure 자사 서비스 사용: 
Azure의 자사 서비스를 활용하면 위의 섹션에서 설명한 HA 및 안정성뿐만 아니라 장기 관리 비용을 낮출 수 있습니다. 

Pivotal에서는 PCF 고객에 대해 [Small Footprint ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html)를 발표했습니다. 구성 요소는 4개의 VM에 공동 배치되어 최대 2500개의 애플리케이션 인스턴스를 실행합니다. 평가판은 현재 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)를 통해 지원됩니다.

## <a name="next-steps"></a>다음 단계
Pivotal Cloud Foundry에서 사용할 수 있으려면 먼저 [오픈 소스 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)에서 Azure 통합 기능을 사용할 수 있습니다. *으로 표시된 기능은 PCF를 통해 지원되지 않습니다. Azure Stack와 Cloud Foundry 통합은이 문서에서 다루지 않습니다.
*로 표시된 기능에서 PCF 지원 또는 Azure Stack과 Cloud Foundry 통합의 경우 최신 상태에 대해 Pivotal 및 Microsoft 계정 관리자에게 문의합니다. 

