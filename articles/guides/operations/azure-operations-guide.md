---
title: Azure IT 운영자를 위한 시작 가이드 | Microsoft Docs
description: Azure IT 운영자를 위한 시작 가이드
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 0a383e8e2c38dbd00e0a55f9debb9b4daf40fa9c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832759"
---
# <a name="get-started-for-azure-it-operators"></a>Azure IT 운영자를 위한 시작하기

이 가이드에서는 Microsoft Azure 인프라의 배포 및 관리와 관련된 핵심 개념을 소개합니다. 클라우드 컴퓨팅 또는 Azure 자체를 처음 사용하는 경우 이 가이드를 통해 개념, 배포 및 관리 세부 정보를 빠르게 익힐 수 있습니다. 이 가이드의 여러 섹션에서는 가상 머신 배포 등의 작업을 설명한 후 심층 기술 정보에 대한 링크를 제공합니다.

## <a name="cloud-computing-overview"></a>클라우드 컴퓨팅 개요

클라우드 컴퓨팅은 기존의 온-프레미스 데이터 센터에 대한 최신 대안을 제공합니다. 퍼블릭 클라우드 공급업체는 모든 컴퓨팅 인프라와 기본 관리 소프트웨어를 제공 및 관리합니다. 이러한 공급업체는 다양한 클라우드 서비스를 제공합니다. 이 경우 클라우드 서비스에는 가상 머신, 웹 서버 또는 클라우드에 호스트된 데이터베이스 엔진이 있습니다. 클라우드 공급자 고객은 필요에 따라 이러한 클라우드 서비스를 임대합니다. 이를 통해 하드웨어 유지 관리 비용을 줄이고 운영 비용에 더 투자할 수 있습니다. 클라우드 서비스는 다음과 같은 이점도 제공합니다.

- 대규모 컴퓨팅 환경의 신속한 배포

- 더 이상 필요하지 않은 시스템의 신속한 할당 해제

- 부하 분산 장치와 같은 일반적으로 복잡한 시스템의 손쉬운 배포

- 유연한 컴퓨팅 용량 또는 필요에 따른 확장 기능

- 비용 효율성이 높은 컴퓨팅 환경

- 웹 기반 포털 또는 프로그래밍 방식 자동화를 통해 어디서나 액세스 가능

- 대부분의 컴퓨팅 및 애플리케이션 요구 사항을 충족시키는 클라우드 기반 서비스

온-프레미스 인프라를 사용하면 배포된 하드웨어 및 소프트웨어를 완벽하게 제어할 수 있습니다. 지금까지는 하드웨어 구입 결정을 할 때 주로 하드웨어 확장에 중점을 두었습니다. 예를 들어 최대 성능 요구 사항을 충족하기 위해 더 많은 코어가 있는 서버를 구입했습니다. 그러나 이러한 인프라는 해당 수요 기간 외에는 충분히 활용되지 않을 수 있습니다. Azure를 사용하면 필요한 인프라만 배포할 수 있으며 언제든지 이를 조정할 수 있습니다. 따라서 성능 요구 사항을 충족시키기 위해 추가 컴퓨팅 노드 배포를 통한 확장에 주력할 수 있습니다. 비싼 하드웨어를 통해 확장하는 것보다 클라우드 서비스를 확장하는 것이 비용 면에서 더 효율적입니다.

Microsoft는 전 세계에 수많은 Azure 데이터 센터를 배포했으며 추가 배포를 계획하고 있습니다. 또한 Microsoft는 중국 및 독일과 같은 지역에 Sovereign 클라우드를 늘리고 있습니다. 이러한 방식의 데이터 센터 배포는 대규모 글로벌 기업만 가능하므로 Azure를 사용하면 모든 규모의 기업이 고객에게 가까운 서비스를 쉽게 배포할 수 있습니다.

Azure는 컴퓨팅 요구 사항의 증가에 따라 신속하게 확장할 수 있는 기능을 제공하므로 소규모 기업의 경우 저렴한 비용으로 진입할 수 있습니다. 이를 통해 인프라에 대한 막대한 자본 투자를 방지하고 필요에 따라 시스템을 설계 및 재설계할 수 있는 유연성을 제공합니다. 클라우드 컴퓨팅 사용은 스케일 패스트(scale-fast)와 페일 패스트(Fail-fast)로 이루어지는 신생 기업 성장 모델에 적합합니다.

사용 가능한 Azure 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

### <a name="cloud-computing-model"></a>클라우드 컴퓨팅 모델

Azure는 고객에게 제공되는 서비스 범주를 기준으로 클라우드 컴퓨팅 모델을 사용합니다. 세 가지 서비스 범주에는 IaaS(Infrastructure as a Service), PaaS(Platform as a Service) 및 SaaS(Software as a Service)가 포함됩니다. 공급업체는 이러한 각 범주의 컴퓨팅 스택 구성 요소에 대한 책임 일부 또는 전체를 공유합니다. 클라우드 컴퓨팅에 대한 각 범주를 살펴보겠습니다.
![클라우드 컴퓨팅 스택 비교](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: 서비스 제공 인프라

IaaS 클라우드 공급업체는 모든 물리적 컴퓨팅 리소스와 컴퓨터 가상화를 구현하는 데 필요한 소프트웨어를 실행 및 관리합니다. 이 서비스의 고객은 이러한 호스트된 데이터 센터에 가상 머신을 배포합니다. 가상 머신은 오프사이트 데이터 센터에 있지만 IaaS 고객이 운영 체제의 구성 및 관리를 제어하고, 기본 인프라 작업은 클라우드 공급업체에 맡길 수 있습니다.

Azure에는 가상 머신, 가상 머신 확장 집합 및 관련 네트워킹 인프라를 포함한 여러 가지 IaaS 솔루션이 포함되어 있습니다. Virtual Machines는 "리프트 앤 시프트(lift and shift)" 마이그레이션 모델을 사용할 수 있기 때문에 서비스를 Azure로 처음 마이그레이션할 때 많이 사용하는 방법입니다. 데이터 센터에서 현재 서비스를 실행 중인 인프라 등의 VM을 구성한 다음 소프트웨어를 새 VM으로 마이그레이션할 수 있습니다. 다른 서비스나 스토리지에 대한 URL과 같은 구성 업데이트가 필요할 수도 있지만 이러한 방식으로 많은 애플리케이션을 마이그레이션할 수 있습니다.

가상 머신 확장 집합은 Azure Virtual Machines 위에 빌드되어 동일한 VM의 클러스터를 손쉽게 배포할 수 있습니다. 또한 가상 머신 확장 집합은 자동 확장을 지원하므로 필요할 때 새 VM을 자동으로 배포할 수 있습니다. 따라서 가상 머신 확장 집합은 Azure Service Fabric 및 Azure Container Service와 같은 상위 수준의 마이크로 서비스 컴퓨팅 클러스터를 호스트하는 이상적인 플랫폼입니다.

#### <a name="paas-platform-as-a-service"></a>PaaS: 서비스 제공 플랫폼

PaaS를 사용하면 클라우드 서비스 공급업체가 제공하는 환경에 애플리케이션을 배포할 수 있습니다. 모든 인프라 관리는 공급업체에서 수행하므로 애플리케이션 개발 및 데이터 관리에 집중할 수 있습니다.

Azure는 Azure App Service 및 Azure Cloud Services(웹 및 작업자 역할)의 Web Apps 기능을 포함한 여러 가지 PaaS 컴퓨팅 제안 사항을 제공합니다. 두 경우 모두 개발자는 지원에 대한 세부적인 지식이 없어도 여러 가지 방법으로 애플리케이션을 배포할 수 있습니다. 개발자는 VM(가상 머신)을 만들거나 RDP(원격 데스크톱 프로토콜)를 사용하여 각 애플리케이션에 로그인하거나 애플리케이션을 설치할 필요가 없습니다. 단추 하나만 누르면(또는 가까이 가면) Microsoft에서 제공하는 도구가 VM을 프로비저닝한 다음 VM에 애플리케이션을 배포 및 설치합니다.

#### <a name="saas-software-as-a-service"></a>SaaS: 서비스 제공 소프트웨어

SaaS는 중앙에서 호스트되고 관리되는 소프트웨어입니다. 일반적으로 다중 테넌트 아키텍처를 기반으로 하고 단일 버전의 애플리케이션이 모든 고객에게 사용됩니다. 모든 위치에서 최상의 성능을 보장하기 위해 여러 인스턴스로 확장할 수 있습니다. SaaS 소프트웨어는 일반적으로 월간 또는 연간 구독을 통해 사용이 허가됩니다. SaaS 소프트웨어 공급업체가 소프트웨어 스택의 모든 구성 요소를 담당하므로 사용자는 제공되는 서비스만 관리하면 됩니다.

Microsoft Office 365는 SaaS 제공 사항의 좋은 예입니다. 구독자는 월간 또는 연간 구독료를 지불하고 Microsoft Exchange, Microsoft OneDrive 및 나머지 Microsoft Office 제품군을 서비스로 받습니다. 구독자는 항상 최신 버전을 사용할 수 있으며 Exchange Server는 자동으로 관리됩니다. 이렇게 하면 매년 Office를 설치 및 업그레이드하는 것에 비해 필요한 비용과 노력이 줄어듭니다.

## <a name="azure-services"></a>Azure 서비스

Azure는 클라우드 컴퓨팅 플랫폼에서 많은 서비스를 제공합니다. 다음과 같은 서비스가 있습니다.

### <a name="compute-services"></a>컴퓨팅 서비스

애플리케이션 워크로드 호스팅 및 실행을 위한 서비스:

- Azure Virtual Machines - Linux 및 Windows

- App Services(Web Apps, Mobile Apps, Logic Apps, API Apps 및 함수 앱)

- Azure Batch(대규모 병렬 및 배치 컴퓨팅 작업의 경우)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Data services

데이터 저장 및 관리를 위한 서비스:

- Azure Storage(Azure Blob, Queue, Table 및 File 서비스 구성)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>애플리케이션 서비스

애플리케이션 빌드 및 운영을 위한 서비스:

- Azure AD(Azure Active Directory)

- 분산된 시스템을 연결하기 위한 Azure Service Bus

- 빅 데이터 처리를 위한 Azure HDInsight

- 통합 및 오케스트레이션 워크플로를 위한 Azure Logic Apps

- Azure Media Services

### <a name="network-services"></a>Network Services

Azure와 온-프레미스 데이터 센터 간 및 Azure 내에서의 네트워킹을 위한 서비스:

- Azure Virtual Network

- Azure ExpressRoute

- Azure 제공 DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Azure 서비스에 대한 자세한 내용은 [Azure 서비스 설명서](https://docs.microsoft.com/azure)를 참조하세요.

## <a name="azure-key-concepts"></a>Azure 주요 개념

### <a name="datacenters-and-regions"></a>데이터 센터 및 지역

Azure는 전 세계 여러 지역에서 일반적으로 사용 가능한 글로벌 클라우드 플랫폼입니다. Azure에서 서비스, 애플리케이션 또는 VM을 프로비저닝할 때 지역을 선택하라는 메시지가 나타납니다. 선택한 지역은 애플리케이션이 실행되는 특정 데이터 센터를 나타냅니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

Azure를 사용할 때 얻을 수 있는 장점 중 하나는 전 세계의 다양한 데이터 센터에 애플리케이션을 배포할 수 있다는 것입니다. 선택한 지역은 애플리케이션의 성능에 영향을 줄 수 있습니다. 네트워크 요청 대기 시간을 줄이기 위해 고객에게 가장 가까운 지역을 선택하는 것이 좋습니다. 특정 국가/지역에서 앱을 배포하기 위한 법적 요건을 충족하는 지역을 선택할 수도 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal은 Azure 리소스 및 서비스를 만들고 관리하고 제거하는 데 사용할 수 있는 웹 기반 애플리케이션입니다. Azure Portal의 주소는 [portal.azure.com](https://portal.azure.com)입니다. 여기에는 Azure 리소스 관리를 위해 사용자 지정 가능한 대시보드 및 도구가 포함되어 있습니다. 또한 청구 및 구독 정보를 제공합니다. 자세한 내용은 [Microsoft Azure Portal 개요](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) 및 [포털을 통해 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-portal/resource-group-portal)를 참조하세요.

### <a name="resources"></a>리소스

Azure 리소스는 Azure 구독에 배포된 개별 컴퓨팅, 네트워킹, 데이터 또는 앱 호스팅 서비스입니다. 일반적인 리소스에는 가상 머신, 스토리지 계정 또는 SQL Database 등이 있습니다. Azure 서비스는 종종 관련 Azure 리소스로 구성됩니다. 예를 들어 Azure 가상 머신에는 VM, 스토리지 계정, 네트워크 어댑터 및 공용 IP 주소가 포함될 수 있습니다. 이러한 리소스는 개별적으로 또는 그룹으로 만들고 관리하고 삭제할 수 있습니다. Azure 리소스에 대해서는 이 가이드의 뒷부분에서 자세히 설명합니다.

### <a name="resource-groups"></a>리소스 그룹

Azure 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 리소스만 포함될 수 있습니다. Azure 리소스 그룹에 대해서는 이 가이드의 뒷부분에서 자세히 설명합니다.

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿

Azure Resource Manager 템플릿은 리소스 그룹에 배포할 하나 이상의 리소스를 정의하는 JSON(JavaScript Object Notation) 파일입니다. 또한 배포된 리소스 간의 종속성을 정의합니다. Resource Manager 템플릿은 이 가이드의 뒷부분에서 자세히 설명합니다.

### <a name="automation"></a>Automation

Azure Portal을 사용하여 리소스를 만들고 관리하고 삭제하는 작업 외에도 PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 이러한 작업을 자동화할 수 있습니다.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 Azure 관리 cmdlet을 제공하는 모듈 집합입니다. 이 cmdlet을 사용하여 Azure 서비스를 만들고 관리하고 제거할 수 있습니다. cmdlet을 사용하면 일관되고 반복 가능하며 자동화된 방식으로 배포할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)을 참조하세요.

#### <a name="azure-command-line-interface"></a>Azure 명령줄 인터페이스

Azure 명령줄 인터페이스는 명령줄에서 Azure 리소스를 만들고 관리하고 제거하는 데 사용할 수 있는 도구입니다. Azure CLI는 Linux, Mac OS X 및 Windows에서 사용할 수 있습니다. 자세한 내용 및 기술 세부 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

#### <a name="rest-apis"></a>REST API

Azure는 Azure Portal UI를 지원하는 REST API 집합을 기반으로 합니다. 또한 대부분의 REST API는 인터넷 사용 디바이스에서 Azure 리소스와 앱을 프로그래밍 방식으로 프로비저닝하고 관리할 수 있도록 지원됩니다. 자세한 내용은 [Azure REST SDK 참조](https://docs.microsoft.com/rest/api/index)를 참조하세요.

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

관리자는 Azure Cloud Shell이라는 브라우저에서 액세스할 수 있는 환경을 통해 Azure PowerShell 및 Azure CLI에 액세스할 수 있습니다. 이 대화형 인터페이스는 Linux 및 Windows 관리자가 Bash 또는 PowerShell 중에서 선택한 명령줄 인터페이스를 사용할 수 있도록 하는 유연한 도구를 제공합니다. Azure Cloud Shell은 포털을 통해, [shell.azure.com](https://shell.azure.com)에서 독립 실행형 웹 인터페이스로서 또는 여러 다른 액세스 지점을 통해 액세스할 수 있습니다. 자세한 내용은 [Azure Cloud Shell 개요](https://docs.microsoft.com/azure/cloud-shell/overview)를 참조하세요.

## <a name="azure-subscriptions"></a>Azure 구독

구독은 Azure 계정에 연결된 Azure 서비스의 논리적 그룹입니다. 단일 Azure 계정에 여러 구독이 포함될 수 있습니다. Azure 서비스에 대한 청구는 구독 단위로 이루어집니다. Azure 구독에는 구독에 대한 모든 권한을 가진 계정 관리자와 구독의 모든 서비스를 제어할 수 있는 서비스 관리자가 있습니다. 클래식 구독 관리자에 대한 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](../../cost-management-billing/manage/add-change-subscription-administrator.md)을 참조하세요. 관리자 외에도 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 사용하여 Azure 리소스를 세부적으로 제어할 수 있는 권한을 개별 계정에 부여할 수 있습니다.

### <a name="select-and-enable-an-azure-subscription"></a>Azure 구독 선택 및 사용

Azure 서비스를 사용하려면 먼저 구독이 있어야 합니다. 여러 구독 형식을 사용할 수 있습니다.

**체험 계정**: [Azure 웹 사이트](https://azure.microsoft.com/)에 체험 계정을 등록할 수 있는 링크가 있습니다. 무료 계정을 등록하면 Azure의 리소스 조합을 사용해 볼 수 있는 30일 과정에 대한 크레딧이 부여됩니다. 크레딧 금액을 초과하면 계정이 일시 중지됩니다. 평가판이 끝나면 서비스가 해제되고 더 이상 사용할 수 없습니다. 언제든지 종량제 구독으로 업그레이드할 수 있습니다.

**MSDN 구독**: MSDN 구독이 있는 경우 매달 Azure 크레딧으로 일정 금액이 제공됩니다. 예를 들어 Microsoft Visual Studio Enterprise with MSDN 구독이 있는 경우 Azure 크레딧으로 매달 \$150를 받게 됩니다.

크레딧 금액을 초과하면 다음 달이 시작될 때까지 서비스를 사용할 수 없습니다. 지출 한도를 해제하고 추가 비용으로 사용할 신용 카드를 추가할 수 있습니다. MSDN 계정에 대해서는 이러한 비용 중 일부가 할인됩니다. 예를 들어 Windows Server를 실행하는 VM에 대한 Linux 요금을 지불하면 Microsoft SQL Server와 같은 Microsoft 서버에 대한 추가 요금이 부과되지 않습니다. 따라서 MSDN 계정은 개발 및 테스트 시나리오에 적합합니다.

**BizSpark 계정**: Microsoft BizSpark 프로그램은 스타트업에 많은 이점을 제공합니다. 이러한 이점 중 하나는 최대 5개 MSDN 계정의 개발 및 테스트 환경을 위해 모든 Microsoft 소프트웨어에 액세스할 수 있다는 것입니다. 5개의 각 MSDN 계정에 대해 Azure 크레딧으로 $150를 받고, Virtual Machines와 같은 여러 Azure 서비스를 할인된 요금으로 이용할 수 있습니다.

**종량제**: 이 구독을 사용하면 신용 카드 또는 직불 카드를 계정에 연결하여 사용한 만큼 지불하게 됩니다. 조직인 경우 송장 승인을 받을 수도 있습니다.

**기업계약**: 기업계약을 체결하면 다음 해에 Azure에서 특정 수의 서비스를 사용한다는 약정을 하고, 해당 금액을 미리 지불합니다. 이 약정은 1년 동안 사용됩니다. 약정 금액을 초과하면 초과 비용을 후불로 지불할 수 있습니다. 약정 금액에 따라 Azure에서 서비스에 대한 할인을 받을 수 있습니다.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Azure 구독에 대한 관리 액세스 권한 부여

RBAC에는 권한을 할당하는 데 사용할 수 있는 몇 가지 기본 제공 역할이 있습니다. 특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할을 할당하면 다른 사용자에게 액세스를 위임할 수 있는 권한을 비롯한 구독의 리소스에 대한 모든 권한이 사용자에게 제공됩니다.

자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

### <a name="view-billing-information-in-the-azure-portal"></a>Azure Portal에서 청구 정보 보기

Azure의 중요한 구성 요소 중 하나가 청구 정보를 볼 수 있는 기능입니다. Azure Portal은 Azure 청구에 대한 상세한 정보를 제공합니다.

자세한 내용은 [Azure 청구 송장 및 일간 사용량 현황 데이터를 다운로드하는 방법](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)을 참조하세요.

### <a name="get-billing-information-from-billing-apis"></a>청구 API에서 청구 정보 가져오기

포털에서 청구서를 보는 것 외에도 Azure 청구 REST API를 통해 스크립트 또는 프로그램을 사용하여 청구 정보에 액세스할 수 있습니다.

- Azure 사용량 API를 사용하여 사용 현황 데이터를 검색할 수 있습니다. 관련 Azure 리소스에 태그를 지정하여 청구 사용량 정보를 세부적으로 조정할 수 있습니다. 예를 들어 리소스 그룹의 각 리소스에 부서 이름 또는 프로젝트 이름으로 태그를 지정한 다음 해당 태그에 대한 비용을 구체적으로 추적할 수 있습니다.

- Azure Rate Card API를 사용하여 사용 가능한 모든 리소스를 나열하고 각 리소스에 대한 메타데이터 및 가격 정보를 나열할 수 있습니다.

자세한 내용은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](../../cost-management-billing/manage/usage-rate-card-overview.md)를 참조하세요.

### <a name="forecast-cost-with-the-pricing-calculator"></a>가격 계산기로 비용 예측

Azure의 각 서비스 가격은 다릅니다. 많은 Azure 서비스는 기본, 표준 및 프리미엄 계층을 제공합니다. 일반적으로 각 계층에는 여러 가지 가격 및 성능 수준이 있습니다. [온라인 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 사용하여 가격을 예측할 수 있습니다. 계산기는 단일 리소스 또는 리소스 그룹에 대한 비용을 예측할 수 있는 유연성을 제공합니다.

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager는 Azure 리소스를 위한 배포, 관리 및 조직 메커니즘입니다. Resource Manager를 사용하여 많은 개별 리소스를 리소스 그룹에 함께 포함할 수 있습니다.

Resource Manager에는 관련 리소스의 사용자 지정 가능한 배포 및 구성을 허용하는 배포 기능도 포함되어 있습니다. 예를 들어 Resource Manager를 사용하면 여러 가상 머신, 부하 분산 장치 및 Azure SQL Database의 데이터베이스를 단일 단위로 구성하는 애플리케이션을 배포할 수 있습니다. Resource Manager 템플릿을 사용하여 이러한 배포를 개발합니다.

리소스 관리자는 다음과 같은 여러 이점이 있습니다.

- 이 리소스를 개별적으로 처리하는 것이 아니라 솔루션에 대한 모든 리소스를 그룹으로 배포, 관리 및 모니터링할 수 있습니다.

- 개발 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.

- 스크립트가 아닌 선언적 템플릿을 통해 인프라를 관리할 수 있습니다.

- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.

- RBAC가 기본적으로 관리 플랫폼으로 통합되기 때문에 리소스 그룹의 모든 서비스에 대해 액세스 제어를 적용할 수 있습니다.

- 리소스에 태그를 적용하여 구독에서 모든 리소스를 논리적으로 구성할 수 있습니다.

- 같은 태그를 공유하는 리소스 그룹에 대한 비용을 확인하여 조직의 청구를 명확히 할 수 있습니다.

### <a name="tips-for-creating-resource-groups"></a>리소스 그룹 만들기를 위한 팁

리소스 그룹에 대한 결정을 내릴 때는 다음 팁을 고려하세요.

- 리소스 그룹의 모든 리소스는 동일한 수명 주기를 가져야 합니다.

- 한 번에 하나의 그룹에만 리소스를 할당할 수 있습니다.

- 언제든지 리소스 그룹에 대해 리소스를 추가하거나 제거할 수 있습니다. 모든 리소스는 리소스 그룹에 속해야 합니다. 따라서 한 그룹에서 리소스를 제거하는 경우 다른 그룹에 리소스를 추가해야 합니다.

- 대부분의 리소스 유형은 언제든지 다른 리소스 그룹으로 이동할 수 있습니다.

- 리소스 그룹의 리소스가 다른 지역에 있을 수 있습니다.

- 리소스 그룹을 사용하여 포함된 리소스에 대한 액세스를 제어할 수 있습니다.

### <a name="building-resource-manager-templates"></a>Resource Manager 템플릿 빌드

Resource Manager 템플릿은 단일 리소스 그룹에 배포될 리소스 및 리소스 구성을 선언적으로 정의합니다. Resource Manager 템플릿을 사용하면 과도한 스크립팅 또는 수동 구성 없이 복잡한 배포를 조정할 수 있습니다. 템플릿을 개발한 후에는 동일한 결과가 나올 때마다 여러 번 배포할 수 있습니다.

Resource Manager 템플릿은 4개의 섹션으로 구성됩니다.

- **매개 변수**: 배포에 대한 입력입니다. 매개 변수 값은 사람 또는 자동화된 프로세스에 의해 제공될 수 있습니다. 매개 변수의 예로는 Windows VM에 대한 관리자의 사용자 이름과 암호를 들 수 있습니다. 매개 변수 값이 지정되면 배포 전체에서 사용됩니다.

- **변수**: 이 값은 배포 전체에서 사용되는 값을 포함하는 데 사용됩니다. 매개 변수와 달리 변수 값은 배포 시 제공되지 않습니다. 대신 하드 코딩되거나 동적으로 생성됩니다.

- **리소스:** 이 템플릿 섹션에서는 가상 머신, 스토리지 계정 및 가상 네트워크 등의 배포할 리소스를 정의합니다.

- **출력**: 배포가 완료되면 Resource Manager에서 동적으로 생성된 연결 문자열과 같은 데이터를 반환할 수 있습니다.

배포 자동화의 경우 다음과 같은 메커니즘을 사용할 수 있습니다.

- **함수**: Resource Manager 템플릿에서 여러 함수를 사용할 수 있습니다. 여기에는 문자열을 소문자로 변환, 정의된 리소스의 여러 인스턴스 배포 및 대상 리소스 그룹의 동적 반환 등의 작업이 포함됩니다. Resource Manager 함수를 사용하여 동적 배포를 빌드할 수 있습니다.

- **리소스 종속성**: 여러 리소스를 배포할 때 일부 리소스는 다른 리소스에 대한 종속성이 있습니다. 배포를 용이하게 하기 위해 종속성 선언을 사용하여 종속 리소스가 다른 리소스보다 먼저 배포되도록 할 수 있습니다.

- **템플릿 연결**: 하나의 Resource Manager 템플릿 내에서 다른 템플릿으로 연결할 수 있습니다. 이를 통해 대상으로 지정된 용도별 템플릿 집합으로 배포를 분해할 수 있습니다.

모든 텍스트 편집기에서 Resource Manager 템플릿을 빌드할 수 있습니다. 그러나 Visual Studio용 Azure SDK에는 유용한 도구가 포함되어 있습니다. Visual Studio를 사용하면 마법사를 통해 템플릿에 리소스를 추가한 다음 Visual Studio에서 직접 템플릿을 배포하고 디버깅할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)을 참조하세요.

마지막으로 Azure Portal에서 기존 리소스 그룹을 재사용 가능한 템플릿으로 변환할 수 있습니다. 이는 기존 리소스 그룹의 배포 가능한 템플릿을 만들려는 경우 또는 기본 JSON을 검사하려는 경우에 유용할 수 있습니다. 리소스 그룹을 내보내려면 리소스 그룹 설정에서 **자동화 스크립트** 단추를 선택합니다.

## <a name="security-of-azure-resources-rbac"></a>Azure 리소스 보안(RBAC)

지정된 범위(구독, 리소스 그룹 또는 개별 리소스)의 사용자 계정에 대한 운영 액세스 권한을 부여할 수 있습니다. 즉, 가상 머신 및 모든 관련 리소스와 같은 리소스 그룹에 리소스 집합을 배포하고 특정 사용자나 그룹에 사용 권한을 부여할 수 있습니다. 이 방법은 대상 리소스 그룹에 속한 리소스에 대한 액세스만 제한합니다. 가상 머신이나 가상 네트워크 등 단일 리소스에 대한 액세스 권한을 부여할 수도 있습니다.

액세스 권한을 부여하려면 사용자 또는 사용자 그룹에 역할을 할당합니다. 많은 역할이 미리 정의되어 있습니다. 자체 사용자 지정 역할을 직접 정의할 수도 있습니다.

아래에는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)의 몇 가지 예가 나와 있습니다.

- **소유자**: 이 역할의 사용자는 액세스를 비롯한 모든 사항을 관리할 수 있습니다.

- **읽기 권한자**: 이 역할의 사용자는 모든 유형의 리소스(비밀 제외)를 읽을 수는 있지만 변경할 수 없습니다.

- **가상 머신 기여자**: 이 역할의 사용자는 가상 머신을 관리할 수 있지만 가상 머신이 연결된 가상 네트워크나 VHD 파일이 있는 스토리지 계정은 관리할 수 없습니다.

- **SQL DB 기여자**: 이 역할의 사용자는 SQL Database를 관리할 수 있지만 보안 관련 정책은 관리할 수 없습니다.

- **SQL 보안 관리자**: 이 역할의 사용자는 SQL Server 및 Database의 보안 관련 정책을 관리할 수 있습니다.

- **스토리지 계정 기여자**: 이 역할의 사용자는 스토리지 계정을 관리할 수 있지만 스토리지 계정에 대한 액세스는 관리할 수 없습니다.

자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines는 Azure의 중앙 IaaS 서비스 중 하나입니다. Azure Virtual Machines는 Microsoft Azure 데이터 센터의 Windows 또는 Linux 가상 머신 배포를 지원합니다. Azure Virtual Machines를 사용하면 사용자가 VM 구성을 완전히 제어할 수 있으며 모든 소프트웨어 설치, 구성 및 유지 관리를 담당하게 됩니다.

Azure VM을 배포할 때 Azure Marketplace에서 이미지를 선택하거나 자체 일반 이미지를 제공할 수 있습니다. 이 이미지는 운영 체제 및 초기 구성을 적용하는 데 사용됩니다. 배포하는 동안 Resource Manager는 컴퓨터 이름, 관리자 자격 증명 및 네트워크 구성 할당과 같은 일부 구성 설정을 처리합니다. Azure 가상 머신 확장을 사용하여 소프트웨어 설치, 바이러스 백신 구성 및 모니터링 솔루션과 같은 구성을 더욱 자동화할 수 있습니다.

다양한 크기의 가상 머신을 만들 수 있습니다. 가상 머신의 크기는 처리, 메모리 및 스토리지 용량과 같은 리소스 할당을 나타냅니다. 경우에 따라 RDMA 지원 네트워크 어댑터 및 SSD 디스크와 같은 특정 기능은 특정 VM 크기에서만 사용할 수 있습니다. VM 크기와 기능의 전체 목록은 [Windows](../../virtual-machines/windows/sizes.md) 및 [Linux](../../virtual-machines/linux/sizes.md)에 대한 "Azure의 가상 머신 크기"를 참조하세요.

### <a name="use-cases"></a>사용 사례

Azure 가상 머신은 구성을 완벽하게 제어할 수 있기 때문에 PaaS 모델에 맞지 않는 다양한 서버 워크로드에 이상적입니다. 데이터베이스 서버(SQL Server, Oracle 또는 MongoDB), Windows Server Active Directory, Microsoft SharePoint 등과 같은 서버 워크로드는 Microsoft Azure Platform에서 실행할 수 있습니다. 원하는 경우 많이 재구성하지 않고 온-프레미스 데이터 센터에서 하나 이상의 Azure 지역으로 이러한 워크로드를 이동할 수 있습니다.

### <a name="deployment-of-virtual-machines"></a>가상 머신 배포

Azure Portal을 사용하거나, Azure PowerShell 모듈을 통한 자동화를 사용하거나, 플랫폼 간 CLI를 통한 자동화를 사용하여 Azure 가상 머신을 배포할 수 있습니다.

#### <a name="portal"></a>포털

Azure Portal을 사용하여 가상 머신을 배포하려는 경우 활성 Azure 구독 및 웹 브라우저에 대한 액세스만 필요합니다. 다양한 구성을 사용하는 다양한 운영 체제 이미지를 선택할 수 있습니다. 모든 스토리지 및 네트워킹 요구 사항은 배포 중에 구성됩니다. 자세한 내용은 [Windows](../../virtual-machines/windows/quick-create-portal.md) 및 [Linux](../../virtual-machines/linux/quick-create-portal.md)에 대한 "Azure Portal에서 가상 머신 만들기"를 참조하세요.

Azure Portal에서 가상 머신을 배포하는 것 외에도 포털에서 Azure Resource Manager 템플릿을 배포할 수 있습니다. 이렇게 하면 모든 리소스가 템플릿에 정의된 대로 배포되고 구성됩니다. 자세한 내용은 [Resource Manager 템플릿과 Azure Portal로 리소스 배포](../../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

#### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure 가상 컴퓨터를 배포하면 스토리지 및 네트워킹을 비롯한 모든 관련 가상 컴퓨터 리소스를 완벽하게 배포 자동화할 수 있습니다. 자세한 내용은 [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](../../virtual-machines/windows/quick-create-powershell.md)를 참조하세요.

Azure 컴퓨팅 리소스를 개별적으로 배포하는 것 외에도 Azure PowerShell 모듈을 사용하여 Azure Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

#### <a name="command-line-interface-cli"></a>CLI(명령줄 인터페이스)

PowerShell 모듈과 마찬가지로 Azure 명령줄 인터페이스는 배포 자동화를 제공하며 Windows, OS X 또는 Linux 시스템에서 사용할 수 있습니다. Azure CLI **vm quick-create** 명령을 사용하면 관련된 모든 가상 머신 리소스(스토리지 및 네트워킹 포함)와 가상 머신 자체가 배포됩니다. 자세한 내용은 [CLI를 사용하여 Azure에서 Linux VM 만들기](../../virtual-machines/linux/quick-create-cli.md)를 참조하세요.

마찬가지로 Azure CLI를 사용하여 Azure Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

### <a name="access-and-security-for-virtual-machines"></a>가상 머신의 액세스 및 보안

인터넷에서 가상 머신에 액세스하려면 관련 네트워크 인터페이스 또는 부하 분산 장치가 있어야 하며(해당하는 경우) 공용 IP 주소로 구성되어야 합니다. 공용 IP 주소에는 가상 머신 또는 부하 분산 장치로 확인되는 DNS 이름이 포함됩니다. 자세한 내용은 [Azure의 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)를 참조하세요.

NSG(네트워크 보안 그룹) 리소스를 사용하여 공용 IP 주소를 통해 가상 머신에 대한 액세스를 관리합니다. NSG는 방화벽과 같은 역할을 하며 정의된 포트 집합의 네트워크 인터페이스 또는 서브넷에서 트래픽을 허용하거나 거부합니다. 예를 들어 Azure VM을 사용하여 원격 데스크톱 세션을 만들려면 포트 3389에서 인바운드 트래픽을 허용하도록 NSG를 구성해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure에서 VM으로 포트 열기](../../virtual-machines/windows/nsg-quickstart-portal.md)를 참조하세요.

마지막으로 컴퓨터 시스템 관리와 마찬가지로 보안 자격 증명과 소프트웨어 방화벽을 사용하여 운영 체제에서 Azure 가상 컴퓨터에 대한 보안을 제공해야 합니다.

## <a name="azure-storage"></a>Azure Storage

Azure Storage는 내구성이 뛰어나고 확장 가능하며 중복 스토리지를 제공하는 Microsoft 관리 서비스입니다. 리소스 배포 방법을 사용하여 Azure storage 계정을 리소스 그룹에 리소스로 추가할 수 있습니다. Azure의 네 가지 스토리지 유형은: Blob Storage, File Storage, Table Storage 및 Queue Storage입니다. 스토리지 계정을 배포할 때 범용 및 Blob Storage라는 두 가지 계정 유형을 사용할 수 있습니다. 범용 스토리지 계정을 사용하면 네 가지 스토리지 유형 모두에 액세스할 수 있습니다. Blob Storage 계정은 범용 계정과 유사하지만 핫 액세스 계층 및 콜드 액세스 계층을 포함하는 특수한 Blob을 포함합니다. Blob Storage에 대한 자세한 내용은 [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md)를 참조하세요.

Azure storage 계정은 여러 수준의 중복성으로 구성할 수 있습니다.

- **로컬 중복 스토리지**는 모든 데이터의 3개 사본이 동기식으로 작성된 후에야 쓰기가 성공한 것으로 간주하여 가용성을 높입니다. 이러한 복사본은 단일 지역의 단일 시설에 저장됩니다. 복제본은 별도의 장애 도메인 및 업그레이드 도메인에 상주합니다. 즉, 데이터를 보유하고 있는 스토리지 노드에 장애가 발생하거나 업데이트를 위해 오프라인 상태가 된 경우에도 데이터를 사용할 수 있습니다.

- **지역 중복 스토리지**는 고가용성을 위해 주 지역에 있는 데이터의 동기식 복사본을 3개 만들고 재해 복구를 위해 한 쌍의 지역에 3개의 비동기식 복제본을 만듭니다.

- **읽기 액세스 지역 중복 스토리지**는 중복된 스토리지와 보조 지역의 데이터를 읽을 수 있는 기능입니다. 이 기능은 부분 재해 복구에 적합합니다. 주 지역에 문제가 있는 경우 쌍으로 된 지역에 대한 읽기 전용 액세스 권한을 가지도록 애플리케이션을 변경할 수 있습니다.

### <a name="use-cases"></a>사용 사례

각 스토리지 유형마다 사용 사례가 다릅니다.

#### <a name="blob-storage"></a>Blob Storage

*Blob*이라는 단어는 *Binary Large Object*의 머리글자어입니다. Blob은 컴퓨터에 저장한 파일과 같은 구조화되지 않은 파일입니다. Blob Storage는 문서, 미디어 파일 또는 애플리케이션 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob Storage를 개체 스토리지라고 합니다. Azure Blob Storage에는 Azure Virtual Machines 데이터 디스크도 저장됩니다.

Azure Storage는 세 종류의 Blob을 지원합니다.

- **블록 Blob**은 최대 195GB 크기(4MB×50,000 블록)의 일반 파일을 저장하는 데 사용됩니다. 블록 Blob에 대한 기본 사용 사례는 웹 사이트의 이미지 파일이나 미디어 파일과 같이 처음부터 끝까지 읽는 파일을 스토리지하는 것입니다. 64MB보다 큰 파일은 작은 블록으로 업로드해야 하기 때문에 이 파일을 블록 Blob이라고 합니다. 그런 다음 이러한 블록은 최종 Blob에 통합(또는 커밋)됩니다.

- **페이지 Blob**은 최대 1TB 크기의 임의 액세스 파일을 저장하는 데 사용됩니다. 페이지 Blob은 주로 Azure의 IaaS 컴퓨팅 서비스인 Azure Virtual Machines에 지속형 디스크를 제공하는 VHD의 백업 스토리지로 사용됩니다. 이들은 512바이트 페이지에 대한 임의의 읽기/쓰기 권한을 제공하기 때문에 페이지 Blob이라고 합니다.

- **추가 Blob**은 블록 Blob과 같이 블록으로 구성되지만 추가 작업에 최적화되어 있습니다. 이들은 하나 이상의 소스에서 동일한 Blob으로 정보를 기록하는 데 자주 사용됩니다. 예를 들어 여러 VM에서 실행 중인 애플리케이션에 대한 모든 추적 로깅을 동일한 추가 Blob에 기록할 수 있습니다. 하나의 추가 Blob은 최대 195GB까지 가능합니다.

자세한 내용은 [.NET을 사용하여 Azure Blob Storage 시작](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

#### <a name="file-storage"></a>File Storage

Azure File Storage는 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 클라우드에서 파일 공유를 제공하는 서비스입니다. 이 서비스는 SMB 2.1 및 SMB 3.0을 모두 지원합니다. Azure File Storage를 사용하여 파일 공유에 의존하는 애플리케이션을 비용이 많이 드는 다시 쓰기 작업 없이 빠르게 Azure로 마이그레이션할 수 있습니다. Azure 가상 머신, 클라우드 서비스 또는 온-프레미스 클라이언트에서 실행되는 애플리케이션은 클라우드에 파일 공유를 탑재할 수 있습니다. 이는 데스크톱 애플리케이션이 일반적인 SMB 공유를 탑재하는 방법과 비슷합니다. File Storage 공유를 동시에 탑재하고 액세스할 수 있는 애플리케이션 구성 요소 수에는 제한이 없습니다.

File Storage 공유는 표준 SMB 파일 공유이므로, Azure에서 실행되는 애플리케이션은 파일 시스템 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 따라서 개발자는 기존의 코드와 기술을 이용하여 기존 애플리케이션을 마이그레이션할 수 있습니다. IT 전문가는 PowerShell cmdlet을 사용하여 Azure 애플리케이션 관리의 일부로 File Storage 공유를 만들고 탑재하고 관리할 수 있습니다.

자세한 내용은 [Windows에서 Azure File Storage 시작](../../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux에서 Azure File Storage 사용 방법](../../storage/files/storage-how-to-use-files-linux.md)을 참조하세요.

#### <a name="table-storage"></a>Table Storage

Azure Table Storage는 클라우드에 구조화된 NoSQL 데이터를 저장하는 서비스입니다. Table Storage는 스키마 없이 디자인된 키/특성 저장소입니다. Table Storage는 스키마가 없기 때문에 애플리케이션의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. 모든 종류의 애플리케이션에서 빠르고 비용 효율적으로 데이터에 액세스할 수 있습니다. 비슷한 양의 데이터일 때 Table Storage는 일반적으로 전통적인 SQL에 비해 비용이 매우 낮습니다.

Table Storage를 사용하여 웹 애플리케이션의 사용자 데이터, 주소록, 디바이스 정보 및 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 세트을 저장할 수 있습니다. 엔터티를 원하는 수만큼 테이블에 저장할 수 있습니다. 스토리지 계정은 스토리지 계정의 최대 용량 한도 내에서 원하는 수의 테이블을 포함할 수 있습니다.

자세한 내용은 [Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)을 참조하세요.

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage는 애플리케이션 구성 요소 간에 클라우드 메시징을 제공합니다. 규모를 고려하여 애플리케이션을 디자인할 때는 애플리케이션 구성 요소를 개별적으로 확장할 수 있도록 각 구성 요소를 분리하는 경우가 많습니다. Queue Storage는 클라우드, 데스크톱, 온-프레미스 서버 또는 모바일 디바이스에서 실행 중인지와 관계 없이 애플리케이션 구성 요소 간에 통신을 위한 비동기 메시징을 제공합니다. Queue Storage는 또한 비동기 작업 관리와 프로세스 워크플로 작성을 지원합니다.

자세한 내용은 [Azure Queue Storage 시작](../../storage/queues/storage-dotnet-how-to-use-queues.md)을 참조하세요.

### <a name="deploying-a-storage-account"></a>스토리지 계정 배포

스토리지 계정을 배포하기 위한 옵션에는 몇 가지가 있습니다.

#### <a name="portal"></a>포털

Azure Portal을 사용하여 스토리지 계정을 배포하려는 경우 활성 Azure 구독 및 웹 브라우저에 대한 액세스만 필요합니다. 새 스토리지 계정을 새 리소스 그룹 또는 기존 리소스 그룹에 배포할 수 있습니다. 스토리지 계정을 만든 후에는 포털을 사용하여 Blob 컨테이너 또는 파일 공유를 만들 수 있습니다. 프로그래밍 방식으로 Table 및 Queue Storage 엔터티를 만들 수 있습니다. 자세한 내용은 [스토리지 계정 만들기](../../storage/common/storage-account-create.md) 를 참조하세요.

Azure Portal에서 스토리지 계정을 배포하는 것 외에도 포털에서 Azure Resource Manager 템플릿을 배포할 수 있습니다. 이렇게 하면 스토리지 계정을 포함한 모든 리소스가 템플릿에 정의된 대로 배포되고 구성됩니다. 자세한 내용은 [Resource Manager 템플릿과 Azure Portal로 리소스 배포](../../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

#### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure storage 계정을 배포하면 스토리지 계정을 완벽하게 배포 자동화할 수 있습니다. 자세한 내용은 [Azure Storage와 Azure PowerShelld 사용](../../storage/common/storage-powershell-guide-full.md)을 참조하세요.

Azure 리소스를 개별적으로 배포하는 것 외에도 Azure PowerShell 모듈을 사용하여 Azure Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

#### <a name="command-line-interface-cli"></a>CLI(명령줄 인터페이스)

PowerShell 모듈과 마찬가지로 Azure 명령줄 인터페이스는 배포 자동화를 제공하며 Windows, OS X 또는 Linux 시스템에서 사용할 수 있습니다. Azure CLI **storage account create** 명령을 사용하여 스토리지 계정을 만들 수 있습니다. 자세한 내용은 [Azure Storage에서 Azure CLI 사용](../../storage/common/storage-azure-cli.md)을 참조하세요.

마찬가지로 Azure CLI를 사용하여 Azure Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../resource-group-template-deploy-cli.md)를 참조하세요.

### <a name="access-and-security-for-azure-storage"></a>Azure Storage의 액세스 및 보안

Azure Storage는 Azure Portal을 통해, VM 생성 및 작동 중에, Storage 클라이언트 라이브러리에서 등 다양한 방법으로 액세스됩니다.

#### <a name="virtual-machine-disks"></a>가상 머신 디스크

가상 머신을 배포할 때는 가상 머신 운영 체제 디스크와 추가 데이터 디스크를 보관할 스토리지 계정을 만들어야 합니다. 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다. Blob의 최대 크기는 1,024GB이므로 단일 VM 디스크의 최대 크기는 1,023GB입니다. 대용량 데이터 디스크를 구성하려면 여러 데이터 디스크를 가상 컴퓨터에 사용하고 단일 논리 디스크로 함께 풀링하면 됩니다. 자세한 내용은 [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) 및 [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)용"Azure 디스크 관리"를 참조하세요.

#### <a name="storage-tools"></a>스토리지 도구

Azure storage 계정은 Visual Studio 클라우드 탐색기와 같은 다양한 스토리지 탐색기를 통해 액세스할 수 있습니다. 이러한 도구를 사용하면 스토리지 계정 및 데이터를 검색할 수 있습니다. 자세한 내용 및 사용 가능한 스토리지 탐색기 목록은 [Azure Storage 클라이언트 도구](../../storage/common/storage-explorers.md)를 참조하세요.

#### <a name="storage-api"></a>스토리지 API

스토리지 리소스는 HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 액세스할 수 있습니다. 또한 Azure Storage는 많이 사용되는 몇 가지 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 동기/비동기식 호출, 작업 일괄 처리, 예외 관리 및 자동 재시도와 같은 세부 사항을 처리하여 Azure Storage 작업을 간소화합니다. 자세한 내용은 [Azure Storage 서비스 REST API 참조](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)를 참조하세요.

#### <a name="storage-access-keys"></a>스토리지 액세스 키

각 스토리지 계정에는 기본 키와 보조 키라는 두 개의 인증 키가 있습니다. 스토리지 액세스 작업에 대해 두 키 중 하나를 사용할 수 있습니다. 이러한 스토리지 키는 스토리지 계정을 보호하는 데 사용되며 프로그래밍 방식으로 데이터에 액세스하는 데 필요합니다. 보안 강화를 위해 키 롤오버를 주기적으로 허용하는 두 가지 키가 있습니다. 키 소유권과 계정 이름이 있으면 스토리지 계정의 모든 데이터에 무제한 액세스할 수 있으므로 키를 안전하게 유지하는 것이 중요합니다.

#### <a name="shared-access-signatures"></a>공유 액세스 서명

스토리지 리소스에 대한 제어된 액세스를 사용자에게 허용할 필요가 없는 경우 공유 액세스 서명을 만들 수 있습니다. 공유 액세스 서명은 스토리지 리소스에 대해 위임된 액세스를 허용하는 URL에 추가할 수 있는 토큰입니다. 토큰을 소유한 사람은 토큰 유효 기간 동안 토큰이 가리키는 리소스에 토큰이 지정하는 권한으로 액세스할 수 있습니다. 자세한 내용은 [공유 액세스 서명 사용](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

## <a name="azure-virtual-network"></a>Azure Virtual Network

가상 네트워크는 가상 머신 간 통신을 지원하는 데 필요합니다. 서브넷, 사용자 지정 IP 주소, DNS 설정, 보안 필터링 및 부하 분산을 정의할 수 있습니다. Azure는 클라우드 전용 네트워크 또는 하이브리드 가상 네트워크와 같은 다양한 사용 사례를 지원합니다.

### <a name="cloud-only-virtual-networks"></a>클라우드 전용 가상 네트워크

Azure Virtual network는 기본적으로 Azure에 저장된 리소스에만 액세스할 수 있습니다. 동일한 가상 네트워크에 연결된 리소스는 서로 통신할 수 있습니다. 가상 머신 네트워크 인터페이스 및 부하 분산 장치를 공용 IP 주소와 연결하여 인터넷을 통해 가상 머신에 액세스할 수 있습니다. 네트워크 보안 그룹을 사용하여 공개적으로 노출된 리소스에 대한 액세스를 보호할 수 있습니다.

![2계층 웹 애플리케이션에 대한 Azure Virtual Network](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>하이브리드 가상 네트워크

ExpressRoute 또는 사이트 간 VPN 연결을 사용하여 온-프레미스 네트워크를 Azure virtual network에 연결할 수 있습니다. 이 구성에서 Azure virtual network는 기본적으로 온-프레미스 네트워크의 클라우드 기반 확장입니다.

Azure virtual network가 온-프레미스 네트워크에 연결되어 있으므로 프레미스 간 가상 네트워크는 조직에서 사용하는 고유한 주소 공간을 사용해야 합니다. 서로 다른 회사 위치에 특정 IP 서브넷이 할당되는 것과 같은 방식으로 Azure는 네트워크를 확장할 때 다른 위치가 됩니다.
가상 네트워크를 배포하기 위한 몇 가지 옵션이 있습니다.

- [포털](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [CLI(명령줄 인터페이스)](../../virtual-network/quick-create-cli.md)

- Azure 리소스 관리자 템플릿

> **사용 시기**: Azure에서 VM을 사용할 때면 언제든 가상 네트워크를 사용하게 됩니다. 이를 통해 VM을 온-프레미스 데이터 센터와 비슷한 공용 및 프라이빗 서브넷으로 분할할 수 있습니다.
>
> **시작**: Azure Portal을 사용하여 Azure virtual network를 배포하려는 경우 활성 Azure 구독 및 웹 브라우저에 대한 액세스만 필요합니다. 새 가상 네트워크를 새 리소스 그룹 또는 기존 리소스 그룹에 배포할 수 있습니다. 포털에서 새 가상 시스템을 만들 때 기존 가상 네트워크를 선택하거나 새 가상 네트워크를 만들 수 있습니다. 시작 및 [Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/quick-create-portal.md)를 참조하세요.

### <a name="access-and-security-for-virtual-networks"></a>가상 네트워크의 액세스 및 보안

네트워크 보안 그룹을 사용하여 Azure virtual network를 보호할 수 있습니다. NSG는 가상 네트워크의 VM 인스턴스에 대해 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. NSG를 서브넷 또는 서브넷 내의 개별 VM 인스턴스와 연결할 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다. 또한 해당 VM에 직접 NSG를 연결하여 개별 VM에 대한 트래픽을 추가로 제한할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../../virtual-network/security-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Windows VM 만들기](../../virtual-machines/windows/quick-create-portal.md)
- [Linux VM 만들기](../../virtual-machines/linux/quick-create-portal.md)
