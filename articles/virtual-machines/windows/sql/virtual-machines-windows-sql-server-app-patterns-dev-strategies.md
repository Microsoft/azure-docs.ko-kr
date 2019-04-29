---
title: VM에서 SQL Server 애플리케이션 패턴 | Microsoft Docs
description: 이 문서에서는 Azure VM에서 SQL Server에 대한 애플리케이션 패턴을 설명 합니다. 설계자와 개발자들에게 좋은 애플리케이션 아키텍처 및 설계를 위한 기초를 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 988acec8d7044afe87523637e46c9a4deb92b55e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477739"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>요약:
Azure 환경에서 SQL Server에 사용할 하나 이상의 애플리케이션 패턴을 정하는 것은 중요한 설계 의사 결정입니다. 이를 위해서는 SQL Server와 Azure의 각 인프라 구성 요소가 어떻게 연동되는지를 잘 이해하고 있어야 합니다. Azure 인프라 서비스에서 SQL을 사용하면 Windows Server를 바탕으로 구축된 기존 SQL Server 애플리케이션을 Azure의 가상 머신으로 간편하게 마이그레이션하고 그에 대한 유지 관리 및 모니터링을 수행할 수 있습니다.

이 글의 목표는 설계자 및 개발자들이 기존 애플리케이션을 Azure로 마이그레이션하고 Azure에서 새로운 애플리케이션을 개발할 때 활용할 수 있도록 좋은 애플리케이션 아키텍처 및 설계에 대한 기초 지식을 제공하는 것입니다.

각 애플리케이션 패턴에서는 온-프레미스 시나리오와, 해당하는 클라우드 지원 솔루션 및 관련 기술 권장 사항을 확인할 수 있습니다. 또한 애플리케이션의 올바른 개발에 도움이 되는 Azure 특정 개발 전략에 대해서도 논의합니다. 가능한 애플리케이션 패턴이 다양하기 때문에 설계자와 개발자들은 자신의 애플리케이션 및 사용자에게 가장 적합한 패턴을 선택하는 것이 좋습니다.

**기술 관련 기여자:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**기술 검토자:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>소개
서로 다른 시스템과 여러 구성 요소에 다양한 애플리케이션 계층의 구성 요소를 구분하여 여러 형태의 n계층 애플리케이션을 개발할 수 있습니다. 예를 들어, 클라이언트 애플리케이션과 비즈니스 규칙을 한 컴퓨터에 배치하고, 프런트엔드 웹 계층과 데이터 액세스 계층 구성 요소를 다른 컴퓨터에 배치하며, 백엔드 데이터베이스 계층을 또 다른 컴퓨터에 배치할 수 있습니다. 이러한 종류의 구조화는 계층을 서로 격리하는 데 도움이 됩니다. 데이터 원본 위치를 변경할 경우 클라이언트나 애플리케이션이 아닌 데이터 액세스 계층 구성 요소만 변경하면 갑니다.

일반적인 *n계층* 애플리케이션에는 프레젠테이션 계층, 비즈니스 계층 및 데이터 계층 등이 포함됩니다.

| 계층 | 설명 |
| --- | --- |
| **프레젠테이션** |*프레젠테이션 계층* (웹 계층, 프런트엔드 계층)은 사용자가 애플리케이션과 상호 작용하는 계층입니다. |
| **비즈니스** |*비즈니스 계층* (중간 계층)은 프레젠테이션 계층과 계층 및 데이터 계층이 서로 간의 통신을 위해 사용하는 계층으로, 시스템의 핵심 기능이 여기에 포함됩니다. |
| **데이터** |*데이터 계층* 은 기본적으로 애플리케이션의 데이터를 저장하는 서버입니다(예: SQL Server를 실행하는 서버). |

애플리케이션 계층은 애플리케이션의 기능과 구성 요소의 논리적 그룹화를 설명하는 반면, 계층은 별도의 물리적 서버, 컴퓨터, 네트워크 또는 원격 위치에서 기능과 구성 요소의 물리적 배포를 설명합니다. 애플리케이션 계층은 동일한 물리적 컴퓨터 안에 상주하거나(동일 계층) 여러 컴퓨터에 배포될 수 있으며(n계층) 각 계층의 구성 요소는 잘 정의된 인터페이스를 통해 타 계층의 구성 요소와 통신합니다. 계층이라는 용어는 2계층, 3계층 및 n계층 등, 물리적 배포 패턴으로 생각하면 됩니다. **2계층 애플리케이션 패턴** 에는 애플리케이션 서버와 데이터베이스 서버 등, 2개의 애플리케이션 계층이 포함됩니다. 직접 통신은 애플리케이션 서버와 데이터베이스 서버 간에 발생합니다. 애플리케이션 서버에는 웹 계층과 비즈니스 계층 구성 요소가 모두 포함되어 있습니다. **3계층 애플리케이션 패턴**에는 웹 서버, 비즈니스 논리 계층 및/또는 비즈니스 계층 데이터 액세스 구성 요소를 포함하는 애플리케이션 서버, 데이터베이스 서버 등, 3개의 애플리케이션 계층이 있습니다. 웹 서버와 데이터베이스 서버 간 통신은 애플리케이션 서버를 통해 발생합니다. 애플리케이션 계층 및 계층에 대한 자세한 내용은 [Microsoft 애플리케이션 아키텍처 가이드](https://msdn.microsoft.com/library/ff650706.aspx)를 참조하세요.

이 글을 읽기 전에 SQL Server 및 Azure에 대한 기본 개념을 알고 있어야 합니다. 관련 정보는 [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) 및 [Azure.com](https://azure.microsoft.com/)을 참조하세요.

이 문서에서는 간단한 애플리케이션과 매우 복잡한 엔터프라이즈급 애플리케이션 모두에 맞게 조절 가능한 몇 가지 애플리케이션 패턴을 설명합니다. 각 패턴을 자세히 설명하기 전에 [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) 및 [Azure Virtual Machine의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) 등 Azure에서 사용 가능한 데이터 스토리지 서비스에 대해 잘 알고 있어야 합니다. 애플리케이션에 대한 최적의 설계 의사 결정을 위해서는 어떤 데이터 스토리지를 언제 사용할지에 대해 분명히 이해하고 있어야 합니다.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Azure Virtual Machine에서의 SQL Server 선택 조건:
* SQL Server 및 Windows에서 제어를 해야 하는 경우. 예를 들어, SQL Server 버전, 특수 핫픽스, 성능 구성 등이 여기에 포함될 수 있습니다.
* SQL Server 온-프레미스와의 완벽한 호환성이 필요하고 기존 애플리케이션을 그대로 Azure로 옮겨가고자 할 경우.
* Azure 환경의 기능을 활용하고자 하나 Azure SQL Database에서 애플리케이션에 필요한 일부 기능을 지원하지 않는 경우. 여기에는 다음과 같은 부분이 해당할 수 있습니다.
  
  * **데이터베이스 크기**: 이 문서 업데이트 시점 현재, SQL Database에서 최대 1TB의 데이터를 지원합니다. 애플리케이션에 1TB 이상의 데이터가 필요하고 사용자 지정 분할 솔루션을 구현하지 않으려는 경우, Azure Virtual Machine에서 SQL Server를 사용하는 것이 좋습니다. 최신 정보는 [Azure SQL Database 확장](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU 기반 구매 모델](../../../sql-database/sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](../../../sql-database/sql-database-service-tiers-vcore.md)(미리 보기)을 참조하세요.
  * **HIPAA 규정 준수**: Azure Virtual Machines의 SQL Server는 HIPAA BAA(Business Associate Agreement)가 적용되므로, 의료 부문 고객 및 ISV(독립 소프트웨어 공급업체)는 [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) 대신 [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)를 선택할 수 있습니다. 규정 준수에 대한 자세한 내용은 [Microsoft Azure 보안 센터: 규정 준수](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.
  * **인스턴스 수준 기능**: 현재 SQL Database에서는 데이터베이스 외부에 상주하는 기능을 지원하지 않습니다(예: 연결 서버, 에이전트 작업, 파일 스트림, 서비스 broker 등). 자세한 내용은 [Azure SQL Database 지침 및 제한 사항](https://msdn.microsoft.com/library/azure/ff394102.aspx)을 참조하세요.

## <a name="1-tier-simple-single-virtual-machine"></a>1계층(단순): 단일 가상 머신
이 애플리케이션 패턴에서는 SQL Server 애플리케이션 및 데이터베이스를 Azure의 독립 실행형 가상 머신에 배포합니다. 동일한 가상 컴퓨터가 클라이언트/웹 애플리케이션, 비즈니스 구성 요소, 데이터 액세스 계층 및 데이터베이스 서버를 포함합니다. 프레젠테이션, 비즈니스 및 데이터 액세스 코드는 논리적으로 구분되어 있으나 물리적으로는 단일 서버 컴퓨터에 위치합니다. 대다수의 고객이 이 애플리케이션 패턴에서 시작하여 웹 역할이나 가상 머신을 시스템에 추가하는 방식으로 확장해 나갑니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* 플랫폼이 애플리케이션의 요구 사항에 부응하는지를 평가하기 위해 Azure 플랫폼에 대한 간단한 마이그레이션을 수행하려는 경우
* 모든 애플리케이션 계층을 동일한 Azure 데이터 센터 안의 동일한 가상 컴퓨터에서 호스팅하여 계층 간 대기 시간을 줄이려는 경우
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우

다음 표에서는 간단한 온-프레미스 시나리오와, Azure에서 단일 가상 머신에 클라우드 지원 솔루션을 배포하는 방법을 보여줍니다.

![1계층 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

확장성이나 보안 우려로 별도의 계층을 사용해야 하는 경우가 아니라면, 비즈니스 계층(비즈니스 논리 및 데이터 액세스 구성 요소)을 프레젠테이션 계층과 동일한 물리적 계층에 배포하면 애플리케이션 성능을 극대화할 수 있습니다.

이 패턴은 매우 일반적인 패턴이기 때문에 데이터를 SQL Server VM으로 이동하는 데 유용한 마이그레이션에 대한 다음 문서를 참조하세요. [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)

## <a name="3-tier-simple-multiple-virtual-machines"></a>3계층(단순): 여러 가상 머신
이 애플리케이션 패턴에서는 서로 다른 가상 머신에 각각의 애플리케이션 계층을 배치하여 Azure에서 3계층 애플리케이션을 배포합니다. 이 패턴은 간편한 수직 확장 및 수평 확장 시나리오를 위한 유연한 환경을 제공합니다. 한 가상 컴퓨터가 클라이언트/웹 애플리케이션을 포함할 때 다른 가상 컴퓨터가 각각 비즈니스 구성 요소와 데이터베이스 서버를 호스팅합니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* Azure Virtual Machines에 복잡한 데이터베이스 애플리케이션 마이그레이션을 수행하려는 경우
* 서로 다른 지역에서 서로 다른 애플리케이션 계층을 호스팅하려는 경우. 예를 들어, 보고용으로 여러 지역에 배포된 공유 데이터베이스가 있는 경우입니다.
* 온-프레미스 가상화 플랫폼의 엔터프라이즈 애플리케이션을 Azure Virtual Machines로 이동하려는 경우. 엔터프라이즈 애플리케이션에 대한 자세한 내용은 [엔터프라이즈 애플리케이션이란?](https://msdn.microsoft.com/library/aa267045.aspx)을 참조하세요.
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우

다음 그림에서는 서로 다른 가상 컴퓨터에 각각의 애플리케이션을 배치하는 방식으로 Azure에서 간단한 3계층 애플리케이션을 배치하는 방법을 보여줍니다.

![3계층 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

이 애플리케이션 패턴에서는 각 계층에 단일 가상 컴퓨터(VM)가 있습니다. Azure에 여러 VM이 있는 경우 가상 네트워크를 설정하는 것이 좋습니다. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 는 신뢰할 수 있는 보안 경계를 만들고 VM이 개별 IP 주소를 통해 서로 통신할 수 있도록 합니다. 또한 항상 모든 인터넷 연결이 프레젠테이션 계층으로만 이동하도록 합니다. 이 애플리케이션 패턴을 따를 경우 네트워크 보안 그룹 규칙을 관리하여 액세스를 제어합니다. 자세한 내용은 [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

이 표에서 인터넷 프로토콜은 TCP, UDP, HTTP 또는 HTTPS가 될 수 있습니다.

> [!NOTE]
> Azure에서의 가상 네트워크 설정은 무료입니다. 그러나 온-프레미스에 연결하는 VPN 게이트웨이에는 비용이 부과됩니다. 이 요금은 연결이 프로비전되어 사용 가능한 상태인 시간을 기준으로 부과됩니다.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>프레젠테이션 계층 확장이 있는 2계층 및 3계층 
이 애플리케이션 패턴에서는 서로 다른 Virtual Machines에 각각의 애플리케이션 계층을 배치하여 Azure Virtual Machines에서 2계층 또는 3계층 데이터베이스 애플리케이션을 배포합니다. 또한 들어오는 클라이언트 요청 규모 증대에 따라 프레젠테이션 계층을 확장할 수 있습니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* 온-프레미스 가상화 플랫폼의 엔터프라이즈 애플리케이션을 Azure Virtual Machines로 이동하려는 경우.
* 들어오는 클라이언트 요청 규모 증대에 따라 프레젠테이션 계층을 확장하려는 경우
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우
* 필요에 따라 확장 및 축소할 수 있는 인프라 환경을 보유하고자 하는 경우

다음 그림에서는 들어오는 클라이언트 요청의 규모 증대에 따라 프레젠테이션 계층을 확장하여 Azure의 여러 가상 머신에 애플리케이션 계층을 배치하는 방법을 보여줍니다. 이 그림에 표시된 것처럼 Azure 부하 분산 장치는 여러 가상 머신 간의 트래픽 분산과 연결 대상 웹 서버 결정을 담당합니다. 부하 분산 장치 뒤에 여러 웹 서버 인스턴스가 있으면 프레젠테이션 계층의 고가용성이 보장됩니다.

![애플리케이션 패턴 - 프레젠테이션 계층 수평 확장](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>한 계층에 여러 VM이 있는 2계층, 3계층 또는 n계층 패턴 모범 사례
동일한 클라우드 서비스와 동일한 가용성 집합의 동일한 계층에 속하는 가상 머신을 배치하는 것이 좋습니다. 예를 들어, 웹 서버 집합을 **CloudService1** 및 **AvailabilitySet1**에, 데이터베이스 서버 집합을 **CloudService2** 및 **AvailabilitySet2**에 배치합니다. Azure의 가용성 집합을 사용하면 고가용성 노드를 별도의 오류 도메인과 업그레이드 도메인에 배치할 수 있습니다.

계층의 여러 VM 인스턴스를 활용하려면 애플리케이션 계층 간에 Azure 부하 분산 장치를 구성해야 합니다. 각 계층에서 부하 분산 장치를 구성하려면 각 계층의 VM에 개별적으로 부하 분산 엔드포인트를 만듭니다. 특정 계층의 경우 먼저 동일한 클라우드 서비스에서 VM을 만듭니다. 이를 통해 모두 동일한 공용 가상 IP 주소를 갖게 됩니다. 다음으로 해당 계층에서 가상 머신 중 하나에 엔드포인트를 만듭니다. 그런 다음 부하 분산을 위해 해당 계층의 다른 가상 머신에 동일한 엔드포인트를 할당합니다. 부하 분산된 집합을 만들면 여러 가상 머신에 트래픽을 배분하고, 부하 분산 장치가 백엔드 VM 노드 실패 시 연결할 노드를 결정하게 할 수 있습니다. 예를 들어, 부하 분산 장치 뒤에 여러 웹 서버 인스턴스가 있으면 프레젠테이션 계층의 고가용성이 보장됩니다.

또한 항상 모든 인터넷 연결이 프레젠테이션 계층으로 먼저 이동하도록 하는 것이 바람직합니다. 프레젠테이션 계층이 비즈니스 계층에 액세스한 다음 비즈니스 계층이 데이터 계층에 액세스합니다. 프레젠테이션 계층에 대한 액세스를 허용하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

Azure의 부하 분산 장치는 온-프레미스 환경의 부하 분산 장치와 유사하게 작동합니다. 자세한 내용은 [Azure 인프라 서비스를 위한 부하 분산](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

또한 Azure Virtual Network를 사용하여 가상 머신에 대한 개인용 네트워크를 설정하는 것이 좋습니다. 이렇게 하면 개인 IP 주소를 통해 가상 컴퓨터 간에 통신할 수 있습니다. 자세한 내용은 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)를 참조하세요.

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>비즈니스 계층 확장이 있는 2계층 및 3계층 
이 애플리케이션 패턴에서는 서로 다른 Virtual Machines에 각각의 애플리케이션 계층을 배치하여 Azure Virtual Machines에서 2계층 또는 3계층 데이터베이스 애플리케이션을 배포합니다. 또한 애플리케이션의 복잡성 때문에 여러 가상 머신에 애플리케이션 서버 구성 요소를 배포하고자 할 수 있습니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* 온-프레미스 가상화 플랫폼의 엔터프라이즈 애플리케이션을 Azure Virtual Machines로 이동하려는 경우.
* 애플리케이션의 복잡성 때문에 여러 가상 머신에 애플리케이션 서버 구성 요소를 배포하고자 합니다.
* 비즈니스 논리 집중적 온-프레미스 LOB(기간 업무) 애플리케이션을 Azure Virtual Machines로 이동하려고 합니다. LOB(기간 업무) 애플리케이션은 회계, 인사(HR), 급여, 공급망 관리 및 리소스 계획 애플리케이션 등과 같은 기업 운영에 중요한 컴퓨터 애플리케이션의 집합입니다.
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우
* 필요에 따라 확장 및 축소할 수 있는 인프라 환경을 보유하고자 하는 경우

다음 그림은 온-프레미스 시나리오와 해당 클라우드 사용 솔루션을 보여줍니다. 이 시나리오에서는 비즈니스 논리 계층 및 데이터 액세스 구성 요소를 포함하는 비즈니스 계층을 확장하여 Azure의 여러 가상 머신에 애플리케이션 계층을 배치합니다. 이 그림에 표시된 것처럼 Azure 부하 분산 장치는 여러 가상 머신 간의 트래픽 분산과 연결 대상 웹 서버 결정을 담당합니다. 부하 분산 장치 뒤에 여러 애플리케이션 서버 인스턴스가 있으면 비즈니스 계층의 고가용성이 보장됩니다. 자세한 내용은 [한 계층에 여러 가상 머신이 있는 2계층, 3계층 또는 n계층 애플리케이션 패턴 모범 사례](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)를 참조하세요.

![비즈니스 계층 수평 확장이 있는 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>프레젠테이션 비즈니스 계층 확장 및 HADR이 있는 2계층 및 3계층
이 애플리케이션 패턴에서는 프레젠테이션 계층(웹 서버) 및 비즈니스 계층(애플리케이션 서버) 구성 요소를 여러 Virtual Machines에 배포하여 2계층 또는 3계층 데이터베이스 애플리케이션을 Azure Virtual Machines에 배포합니다. 또한 Azure 가상 머신에서 데이터베이스를 위한 고가용성 및 재해 복구 솔루션을 구현합니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* SQL Server 고가용성 및 재해 복구 기능을 구현하여 가상화된 플랫폼 온-프레미스에서 Azure로 엔터프라이즈 애플리케이션을 이전하려는 경우
* 들어오는 클라이언트 요청 규모 증대 및 애플리케이션 복잡성 증대에 따라 프레젠테이션 계층과 비즈니스 계층을 확장하려는 경우
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우
* 필요에 따라 확장 및 축소할 수 있는 인프라 환경을 보유하고자 하는 경우

다음 그림은 온-프레미스 시나리오와 해당 클라우드 사용 솔루션을 보여줍니다. 이 시나리오에서는 Azure의 여러 가상 머신에서 프레젠테이션 계층 및 비즈니스 계층 구성 요소를 확장합니다. 또한 Azure에서 SQL Server 데이터베이스에 대한 고가용성 및 재해 복구(HADR) 기술을 구현합니다.

서로 다른 VM에서 여러 애플리케이션 사본을 실행하면 해당 VM에서 요청의 부하를 분산할 수 있습니다. 여러 가상 머신이 있을 때는 모든 VM이 어느 시점에 액세스 가능하고 실행 중이어야 합니다. 부하 분산을 구성할 경우 Azure Load Balancer가 VM 상태를 추적하고 들어오는 호출을 정상 작동하는 VM 노드로 전달합니다. 가상 머신의 부하 분산을 설정하는 방법에 대한 정보는 [Azure 인프라 서비스를 위한 부하 분산](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 부하 분산 장치 뒤에 여러 웹 및 애플리케이션 서버 인스턴스가 있으면 프레젠테이션 및 비즈니스 계층의 고가용성이 보장됩니다.

![수평 확장 및 고가용성](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>SQL HADR가 필요한 애플리케이션 패턴 모범 사례
Azure Virtual Machines에서 SQL Server 고가용성 및 재해 복구 솔루션을 설정할 때는 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)를 사용하는 Virtual Machines에 대해 가상 네트워크를 설정하는 것이 필수입니다.  Virtual Network 내의 가상 머신은 서비스 중단 시간 후에도 안정적인 개인 IP 주소를 갖게 되므로 DNS 이름 확인에 필요한 업데이트 시간이 필요하지 않습니다. 또한 가상 네트워크에서는 온-프레미스 네트워크를 Azure로 확대하고 신뢰할 수 있는 보안 경계를 만들 수 있습니다. 예를 들어, 애플리케이션에 회사 도메인 제한 사항(예: Windows 인증, Active Directory)이 있는 경우 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 설정이 필요합니다.

Azure에서 프러덕션 코드를 실행하는 대부분의 고객은 Azure에 주 및 보조 복제본을 유지합니다.

고가용성 및 재해 복구 기술에 대한 종합 정보 및 자습서는 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>Azure VM 및 Cloud Services를 사용하는 2계층 및 3계층 
이 애플리케이션 패턴에서는 [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md)(웹 및 작업자 역할 - PaaS, 서비스 형태의 플랫폼)와 [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(IaaS, 인프라 형태의 플랫폼)을 모두 사용하여 Azure에 2계층 또는 3계층 애플리케이션을 배포합니다. 프레젠테이션 계층/비즈니스 계층에 [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/)를, 데이터 계층에 [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 SQL Server를 사용하면 Azure에서 실행되는 대부분의 애플리케이션에 유용합니다. Cloud Services에서 실행되는 컴퓨팅 인스턴스가 있으면 관리, 배포, 모니터링 및 확장이 용이하기 때문입니다.

Cloud Services에서는 Azure가 인프라를 자동으로 유지 관리합니다. 즉, 정기 유지 관리를 수행하고 운영 체제를 패치하고 서비스 및 하드웨어 오류의 복구를 시도합니다. 애플리케이션 확장이 필요할 경우 애플리케이션에서 사용하는 가상 머신이나 인스턴스의 수를 늘이거나 줄여 클라우드 서비스 프로젝트에 대해 자동 및 수동 확장 옵션을 사용할 수 있습니다. 또한 온-프레미스 Visual Studio를 사용하여 Azure의 클라우드 서비스 프로젝트에 애플리케이션을 배포할 수 있습니다.

즉 프레젠테이션/비즈니스 계층에 대한 광범위 관리 작업을 자체적으로 보유하지 않고자 하며 애플리케이션에 복잡한 소프트웨어 또는 운영 체제 구성이 필요하지 않은 경우 Azure Cloud Services를 사용합니다. Azure SQL Database가 원하는 기능을 일부 지원하지 않는다면 Azure Virtual Machine에서 데이터 계층에 SQL Server를 사용합니다. 두 서비스의 장점을 결합하여 Azure Cloud Services에서 애플리케이션을 실행하고 Azure Virtual Machines에 데이터를 저장합니다. 자세한 비교는 [Azure에서의 개발 전략 비교](#comparing-web-development-strategies-in-azure)에서 해당 항목을 참조하세요.

이 애플리케이션 패턴에서는 프레젠테이션 계층이 Azure 실행 환경에서 실행되는 Cloud Services 구성 요소인 웹 역할을 포함하며, IIS 및 ASP.NET의 지원에 따라 웹 애플리케이션 프로그래밍용으로 사용자 지정됩니다. 비즈니스 또는 백엔드 계층은 Azure 실행 환경에서 실행되는 Cloud Services 구성 요소인 작업자 역할을 포함합니다. 일반적인 개발에 유용하며 웹 역할에 대한 백그라운드 처리를 수행할 수 있습니다. 데이터베이스 계층은 Azure에서 SQL Server 가상 머신에 상주합니다. 프레젠테이션 계층과 데이터베이스 계층 간의 통신은 직접 또는 비즈니스 계층 - 작업자 역할 구성 요소를 통해 수행됩니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* SQL Server 고가용성 및 재해 복구 기능을 구현하여 가상화된 플랫폼 온-프레미스에서 Azure로 엔터프라이즈 애플리케이션을 이전하려는 경우
* 필요에 따라 확장 및 축소할 수 있는 인프라 환경을 보유하고자 하는 경우
* Azure SQL Database가 애플리케이션이나 데이터베이스에 필요한 기능을 모두 다 지원하는 것은 아닙니다.
* 다양한 워크로드에 대한 부하 테스트를 수행하면서도 여러 물리적 컴퓨터의 항시 보유와 유지 관리는 원하지 않을 경우

다음 그림은 온-프레미스 시나리오와 해당 클라우드 사용 솔루션을 보여줍니다. 이 시나리오에서는 웹 역할에 프레젠테이션 계층, 작업자 역할에 비즈니스 계층을 배치하지만 데이터 계층은 Azure의 가상 머신에 배치합니다. 서로 다른 웹 역할에서 프레젠테이션 계층의 여러 사본을 실행하면 요청의 부하를 분산할 수 있습니다. Azure Virtual Machines에 Azure Cloud Services를 결합할 경우 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 도 설정하는 것이 좋습니다. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)를 통해 클라우드의 동일한 클라우드 서비스 안에서 안정적인 영구 개인 IP 주소가 있게 됩니다. 가상 머신과 클라우드 서비스에 대해 가상 네트워크를 정의한 후에는 개인 IP 주소를 통해 서로 간에 통신을 시작할 수 있습니다. 또한 가상 머신과 Azure 웹/작업자 역할이 동일한 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 에 있으면 대기 시간이 짧아지고 더 안전한 연결이 가능합니다. 자세한 내용은 [클라우드 서비스란?](../../../cloud-services/cloud-services-choose-me.md)을 참조하세요.

이 그림에 표시된 것처럼 Azure 부하 분산 장치는 여러 가상 머신 간의 트래픽 분산과, 연결 대상 웹 서버 또는 애플리케이션 서버 결정을 담당합니다. 부하 분산 장치 뒤에 여러 웹 및 애플리케이션 서버 인스턴스가 있으면 프레젠테이션 계층 및 비즈니스 계층의 고가용성이 보장됩니다. 자세한 내용은 [SQL HADR가 필요한 애플리케이션 패턴 모범 사례](#best-practices-for-application-patterns-requiring-sql-hadr)를 참조하세요.

![Cloud Services가 있는 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

이 애플리케이션 패턴을 구현하기 위한 또 다른 방법은 다음 그림처럼 프레젠테이션 계층과 비즈니스 계층 구성 요소를 모두 포함하는 통합 웹 역할을 사용하는 것입니다. 이 애플리케이션 패턴은 안정적인 설계가 필요한 애플리케이션에 유용합니다. Azure는 웹 및 작업자 역할에서 상태 비저장 컴퓨팅 노드를 제공하므로 다중 기술 중 하나를 사용하여 세션 상태를 저장하는 논리를 구현하는 것이 좋습니다. [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) 또는 [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![Cloud Services가 있는 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Azure VM, Azure SQL Database 및 Azure App Service(Web Apps)가 있는 패턴
이 애플리케이션 패턴의 주 목표는 솔루션에서 Azure IaaS(서비스 방식의 인프라) 구성 요소를 Azure PaaS(서비스 방식의 플랫폼) 구성 요소와 결합하는 방법을 보여주기 위한 것입니다. 이 패턴은 관계형 데이터 스토리지를 위한 Azure SQL Database에 초점을 맞춥니다. 여기에는 Azure IaaS(서비스 방식의 인프라)에 속하는 Azure 가상 머신의 SQL Server가 포함되지 않습니다.

이 애플리케이션 패턴에서는 프레젠테이션과 비즈니스 계층을 동일한 가상 머신에 배치하고 Azure SQL Database(SQL Database) 서버에 액세스하여 데이터베이스 애플리케이션을 배포합니다. 기존 IIS 기반 웹 솔루션을 사용하여 프레젠테이션 계층을 구현할 수 있습니다. 또는 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)를 사용하여 결합된 프레젠테이션 및 비즈니스 계층을 구현할 수 있습니다.

이 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* 이미 Azure에서 기존 SQL Database 서버를 구성했고 애플리케이션을 신속히 테스트하려는 경우
* Azure 환경의 기능을 테스트하려는 경우
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 비즈니스 논리 및 데이터 액세스 구성 요소는 웹 애플리케이션 안에 자체 포함될 수 있습니다.

다음 그림은 온-프레미스 시나리오와 해당 클라우드 사용 솔루션을 보여줍니다. 이 시나리오에서는 Azure SQL Database의 데이터에 액세스 하 Azure에서 단일 가상 머신에 애플리케이션 계층을 배치합니다.

![혼합 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Azure Web Apps를 사용하여 결합된 웹 및 애플리케이션 계층을 구현하려면 웹 애플리케이션의 컨텍스트에서 중간 계층 또는 애플리케이션 계층에 동적 연결 라이브러리(DLL)로 유지하는 것이 좋습니다.

또한 이 글의 [Azure에서의 웹 개발 전략 비교](#comparing-web-development-strategies-in-azure) 부분에서 제공하는 권장 사항을 통해 프로그래밍 방법에 대해 자세히 확인합니다.

## <a name="n-tier-hybrid-application-pattern"></a>N계층 하이브리드 애플리케이션 패턴
N계층 하이브리드 애플리케이션 패턴에서는 온-프레미스와 Azure 간에 분산된 여러 계층에서 애플리케이션을 구현합니다. 따라서 타 계층을 변경하지 않고도 특정 계층을 수정하거나 추가할 수 있는 유연하고 재사용 가능한 하이브리드 시스템을 만들게 됩니다. 회사 네트워크를 클라우드로 확대하려면 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 서비스를 사용합니다.

이 하이브리드 애플리케이션 패턴은 다음과 같은 경우에 유용합니다.

* 애플리케이션을 일부는 클라우드에서, 일부는 온-프레미스에서 실행할 애플리케이션을 구축하려는 경우
* 기존 온-프레미스 애플리케이션의 일부 또는 전체를 클라우드로 마이그레이션하려는 경우
* 온-프레미스 가상화 플랫폼의 엔터프라이즈 애플리케이션을 Azure로 이동하려는 경우
* 필요에 따라 확장 및 축소할 수 있는 인프라 환경을 보유하고자 하는 경우
* 신속하게 개발을 프로비전하고 단기간에 환경을 테스트하려는 경우
* 엔터프라이즈 데이터베이스 애플리케이션에 대한 비용 효율적인 백업 방법이 필요한 경우

다음 그림에서는 온-프레미스와 Azure에 걸쳐 있는 n계층 하이브리드 애플리케이션 패턴을 보여줍니다. 그림에서 보는 것처럼 온-프레미스 인프라에는 사용자 인증과 권한 부여를 지원하는 [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) 도메인 컨트롤러가 포함됩니다. 이 그림은 데이터 계층의 일부는 온-프레미스 데이터 센터에, 다른 데이터 계층 부분은 Azure에 있는 시나리오를 설명합니다. 사용자 애플리케이션의 요구에 따라 여러 다른 하이브리드 시나리오를 구현할 수 있습니다. 예를 들어, 프레젠테이션 계층과 비즈니스 계층은 온-프레미스 환경에 두고 데이터 계층은 Azure에 배치할 수 있습니다.

![n계층 애플리케이션 패턴](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Azure에서는 Active Directory를 조직의 독립 실행형 클라우드 디렉터리로 사용할 수 있지만 기존 온-프레미스 Active Directory와 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)를 통합할 수도 있습니다. 그림에서처럼 비즈니스 계층 구성 요소는 개인 내부 IP 주소를 통해 [Azure의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md), [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)를 통해 온-프레미스 SQL Server, 또는 .NET Framework 데이터 공급자 기술을 통해 [SQL Database](../../../sql-database/sql-database-technical-overview.md) 등 여러 데이터 소스에 액세스할 수 있습니다. 이 그림에서 Azure SQL Database는 선택적 데이터 스토리지 서비스입니다.

n계층 하이브리드 애플리케이션 패턴에서는 다음 워크플로를 지정된 순서대로 구현할 수 있습니다.

1. [MAP(Microsoft Assessment and Planning) Toolkit](https://microsoft.com/map)를 사용하여 클라우드로 이동해야 하는 엔터프라이즈 데이터베이스 애플리케이션을 파악합니다. MAP Toolkit는 가상화를 고려 중인 컴퓨터에서 인벤토리 및 성능 데이터를 수집하여 용량 및 평가 계획에 대한 권장 사항을 제공합니다.
2. 리소스 및 저장소 계정과 가상 머신 등과 같이, Azure 플랫폼에 필요한 리소스와 구성을 계획합니다.
3. 온-프레미스의 회사 네트워크와 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)간의 네트워크 연결을 설정합니다. 온-프레미스 회사 네트워크와 Azure의 가상 머신 간에 연결을 설정하려면 다음 두 방법 중 하나를 사용합니다.
   
   1. Azure의 가상 머신에 있는 공용 끝점을 통해 온-프레미스와 Azure 간의 연결을 설정합니다. 이 방법은 설정이 간단하며 SQL Server 인증을 가상 머신에서 사용할 수 있습니다. 또한 VM에 대한 공용 트래픽을 제어하도록 네트워크 보안 그룹 규칙을 설정합니다. 자세한 내용은 [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
   2. Azure 가상 사설망(VPN) 터널을 통해 온-프레미스와 Azure 간의 연결을 설정합니다. 이 방법을 사용하면 도메인 정책을 Azure의 가상 머신까지 확대할 수 있습니다. 또한 방화벽 규칙을 설정하고 가상 컴퓨터에서 Windows 인증을 사용할 수 있습니다. 현재 Azure는 사이트 간 및 지점-사이트 간 보안 VPN 연결을 지원합니다.
      
      * 사이트 간 보안 연결을 통해 온-프레미스 네트워크와 Azure의 가상 네트워크 간에 네트워크 연결을 설정할 수 있습니다. 온-프레미스 데이터 센터 환경을 Azure에 연결하는 것이 좋습니다.
      * 안전한 지점-사이트 간 연결을 통해 Azure의 가상 네트워크와, 다른 어딘가에서 실행되는 개별 컴퓨터 간에 네트워크 연결을 설정할 수 있습니다. 주로 개발 및 테스트용으로 사용하는 것이 좋습니다.
      
      Azure의 SQL Server 연결 방법에 대한 정보는 [Azure의 SQL Server Virtual Machine에 연결](virtual-machines-windows-sql-connect.md)을 참조하세요.
4. Azure의 가상 머신 디스크에 온-프레미스 데이터를 백업하는 예약 작업 및 알림을 설정합니다. 자세한 내용은 [Azure Blob Storage 서비스로 SQL Server Backup 및 복원](https://msdn.microsoft.com/library/jj919148.aspx)과 [Azure Virtual Machines에서 SQL Server의 Backup 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요.
5. 애플리케이션의 필요에 따라 다음과 같은 세 가지 일반적인 시나리오 중 하나를 구현할 수 있습니다.
   
   1. 웹 서버, 애플리케이션 서버, 중요하지 않은 데이터를 Azure의 데이터베이스 서버에 두고 중요한 데이터는 온-프레미스에 보관할 수 있습니다.
   2. 웹 서버와 애플리케이션 서버를 온-프레미스에 두고 데이터베이스 서버를 Azure의 가상 머신에 둘 수 있습니다.
   3. 데이터베이스 서버, 웹 서버, 애플리케이션 서버를 온-프레미스에 두고 데이터베이스 복제본을 Azure의 가상 머신에 둘 수 있습니다. 이 설정에서는 온-프레미스 웹 서버 또는 보고 애플리케이션이 Azure의 데이터베이스 복제본에 액세스할 수 있습니다. 따라서 온-프레미스 데이터베이스의 워크로드를 줄이는 효과가 있습니다. 이 시나리오는 읽기 워크로드가 매우 많은 개발 용도로 구현하는 것이 좋습니다. Azure에서 데이터베이스 복제본을 만드는 방법은 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)의 AlwaysOn 가용성 그룹을 참조하세요.

## <a name="comparing-web-development-strategies-in-azure"></a>Azure에서의 웹 개발 전략 비교
Azure에서 다계층 SQL Server 기반 애플리케이션을 구현하려면 다음 두 가지 프로그래밍 방법 중 하나를 사용할 수 있습니다.

* Azure Virtual Machines의 SQL Server에서 Azure 및 액세스 데이터베이스에서 기존 웹 서버(IIS,인터넷 정보 서비스)를 설정합니다.
* 클라우드 서비스를 구현하여 Azure에 배포합니다. 그런 다음 이 클라우드 서비스가 Azure 가상 머신의 SQL Server에 액세스할 수 있는지 확인합니다. 클라우드 서비스는 여러 웹 및 작업자 역할을 포함할 수 있습니다.

다음 표에서는 Azure Virtual Machines의 SQL Server와 관련하여 Azure Cloud Services 및 Azure Web Apps를 기존 웹 개발과 비교합니다. Azure Web Apps는 공용 가상 IP 주소나 DNS 이름을 통해 Azure VM의 SQL Server를 Azure Web Apps용 데이터 소스로 사용할 수 있기 때문에 이 표에 포함되어 있습니다.

|  | Azure Virtual Machines의 기존 웹 개발 | Azure의 Cloud Services | Azure Web Apps에서 웹 호스팅 |
| --- | --- | --- | --- |
| **온-프레미스에서 애플리케이션 마이그레이션** |기존 애플리케이션 있는 그대로 사용합니다. |애플리케이션에 웹 및 작업자 역할이 필요합니다. |기존 애플리케이션 그대로이지만 빠른 확장성이 필요한 자체 포함된 웹 애플리케이션 및 웹 서비스에 적합합니다. |
| **개발 및 배포** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. 각 클라우드 서비스에는 스테이징 및 프로덕션 등, 서비스 패키지와 구성을 배포할 수 있는 두 가지 환경이 있습니다. 먼저 클라우드 서비스를 스테이징 환경에 배포하여 테스트한 후 프로덕션으로 수준을 올릴 수 있습니다. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **관리 및 설치** |애플리케이션, 데이터, 방화벽 규칙, 가상 네트워크 및 운영 체제의 관리 작업을 담당합니다. |애플리케이션, 데이터, 방화벽 규칙, 가상 네트워크에 대한 관리 작업을 담당합니다. |애플리케이션 및 데이터에 대해서만 관리 작업을 담당합니다. |
| **HADR(고가용성 및 재해 복구)** |동일한 가용성 집합 및 동일한 클라우드 서비스에 가상 머신을 배치하는 것이 좋습니다. VM을 동일한 가용성 집합에 배치하면 Azure가 고가용성 노드를 별도의 장애 도메인 및 업그레이드 도메인에 배치할 수 있습니다. 마찬가지로, 동일한 클라우드 서비스에 VM을 유지하면 부하 분산이 가능하고 VM이 Azure 데이터 센터 안에서 로컬 네트워크를 통해 다른 VM과 직접 통신할 수 있습니다.<br/><br/>가동 중지 시간을 방지하기 위해 Azure Virtual Machines에서 SQL Server에 대한 고가용성 및 재해 복구 솔루션 구현을 담당하고 있습니다. 지원되는 HADR 기술은 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.<br/><br/>사용자 고유의 데이터와 애플리케이션 백업을 담당해야 합니다.<br/><br/>데이터 센터의 호스트 시스템에 하드웨어 문제로 장애가 발생하면 Azure가 가상 머신을 이동할 수 있습니다. 또한 보안 또는 소프트웨어 업데이트를 위해 호스트 컴퓨터를 업데이트할 때 계획에 따른 VM 중단 시간이 있을 수 있습니다. 따라서 지속적 가용성을 보장하려면 각 애플리케이션 계층에 둘 이상의 VM을 유지하는 것이 좋습니다. Azure는 단일 가상 머신에 대한 SLA를 제공하지 않습니다. |Azure는 기본 하드웨어 또는 운영 체제 소프트웨어로 인한 오류를 관리합니다. 애플리케이션 고가용성을 보장하기 위해 웹 또는 작업자 역할의 여러 인스턴스를 구현하는 것이 좋습니다. 자세한 내용은 [Cloud Services, Virtual Machines 및 Virtual Network 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)을 참조하세요.<br/><br/>사용자 고유의 데이터와 애플리케이션 백업을 담당해야 합니다.<br/><br/>Azure VM에서 SQL Server 데이터베이스에 상주하는 데이터베이스에 대해 가동 중지 시간을 방지하기 위한 고가용성 및 재해 복구 솔루션의 구현을 담당하고 있습니다. 지원되는 HDAR 기술은 Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구를 참조하세요.<br/><br/>**SQL Server 데이터베이스 미러링**: Azure Cloud Services와 함께 사용합니다(웹/작업자 역할). SQL Server VM 및 클라우드 서비스 프로젝트를 동일한 Azure Virtual Network에 둘 수 있습니다. SQL Server VM이 동일한 Virtual Network에 없는 경우, 통신을 SQL Server 인스턴스에 라우팅하는 SQL Server 별칭을 만들어야 합니다. 또한 별칭 이름은 SQL Server 이름과 일치해야 합니다. |고가용성은 Azure 작업자 역할, Azure Blob Storage 및 Azure SQL Database에서 상속됩니다. 예를 들어, Azure Storage는 모든 BLOB, 테이블 및 큐 데이터의 복제본 3개를 유지 관리합니다. 어느 시점에나 Azure SQL Database는 실행 중인 데이터에 대해 주 복제본 1개와 보조 복제본 2개 등, 3개의 복제본을 보유하고 있습니다. 자세한 내용은 [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) 및 [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md)를 참조하세요.<br/><br/>Azure Web Apps용 데이터 소스로 Azure VM의 SQL Server를 사용할 때는 Azure Web Apps가 Azure Virtual Network를 지원하지 않는다는 점에 유의합니다. 즉, Azure에서 Azure Web Apps로부터의 모든 SQL Server VM 연결은 가상 머신의 공용 끝점을 통해 이루어져야 합니다. 이로 인해 고가용성 및 재해 복구 시나리오에 몇 가지 제한이 발생할 수 있습니다. 예를 들어, 데이터베이스 미러링이 있는 SQL Server VM에 연결하는 Azure Web Apps의 클라이언트 애플리케이션은 새로운 주 서버에 연결하지 못할 수 있습니다. 데이터베이스 미러링에서는 Azure의 SQL Server 호스트 VM 간에 Azure Virtual Network 설정이 필요하기 때문입니다. 따라서 Azure Web Apps로 **SQL Server Database 미러링**은 현재 지원되지 않습니다.<br/><br/>**SQL Server AlwaysOn 가용성 그룹**: Azure에서 SQL Server VM으로 Azure Web Apps를 사용하는 경우 AlwaysOn 가용성 그룹을 설정할 수 있습니다. 하지만 AlwaysOn 가용성 그룹 수신기의 통신이 부하가 분산된 공용 엔드포인트를 통해 주 복제본으로 라우팅되도록 구성해야 합니다. |
| **크로스 프레미스 연결** |Azure Virtual Network를 사용하여 온-프레미스에 연결할 수 있습니다. |Azure Virtual Network를 사용하여 온-프레미스에 연결할 수 있습니다. |Azure Virtual Network가 지원됩니다. 자세한 내용은 [Web Apps Virtual Network 통합](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)을 참조하세요. |
| **확장성** |가상 머신 크기를 늘리거나 디스크를 더 추가하면 확장이 가능합니다. 가상 컴퓨터 크기에 대한 자세한 내용은 [Azure에 대한 Virtual Machine 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.<br/><br/>**데이터베이스 서버의 경우**: 데이터베이스 분할 기법 및 SQL Server AlwaysOn 가용성 그룹을 통한 스케일 아웃이 가능합니다.<br/><br/>많은 읽기 작업의 경우 여러 보조 노드 뿐만 아니라 SQL Server 복제에서 [AlwaysOn 가용성 그룹](https://msdn.microsoft.com/library/hh510230.aspx)을 사용할 수 있습니다.<br/><br/>많은 쓰기 작업의 경우 애플리케이션 확장을 제공하는 여러 물리적 서버에서 수평 분할 데이터를 구현할 수 있습니다.<br/><br/>또한 [데이터 종속 라우팅으로 SQL Server](https://technet.microsoft.com/library/cc966448.aspx)를 사용하여 확장을 구현할 수 있습니다. 데이터 종속적 라우팅(DDR)에서는 데이터베이스 요청을 여러 SQL Server 노드로 라우팅하기 위해 일반적으로 비즈니스 계층의 클라이언트 애플리케이션에서 분할 메커니즘을 구현해야 합니다. 비즈니스 계층에는 데이터의 분할 방법과 데이터를 포함하는 노드에 대한 매핑이 포함됩니다.<br/><br/>가상 머신을 실행 중인 애플리케이션을 확장할 수 있습니다. 자세한 내용은 [애플리케이션 크기를 조정하는 방법](../../../cloud-services/cloud-services-how-to-scale-portal.md)을 참조하세요.<br/><br/>**중요 참고**: Azure의 **자동 크기 조정** 기능을 사용하면 애플리케이션에서 사용하는 Virtual Machines를 자동으로 늘리거나 줄일 수 있습니다. 이 기능은 사용량이 많은 기간 동안 최종 사용자 환경에 부정적인 영향이 발생하지 않도록 하며 수요가 낮을 때는 VM이 종료됩니다. SQL Server VM이 포함된 경우에는 클라우드 서비스에 자동 크기 조정 옵션을 설정하지 않는 것이 좋습니다. 자동 크기 조정 기능을 설정하면 Azure가 가상 머신의 CPU 사용량이 일부 임계값보다 높으면 해당 VM을 켜고 CPU 사용량이 그보다 낮아지면 가상 머신을 끌 수 있기 때문입니다. 자동 크기 조정 기능은 웹 서버처럼 VM이 이전 상태를 참조하지 않고 워크로드를 관리할 수 있는 비저장 애플리케이션에 유용합니다. 그러나 SQL Server처럼 한 인스턴스만 데이터베이스 쓰기를 허용하는 상태 저장 애플리케이션에는 자동 크기 조정 기능이 유용하지 못합니다. |여러 웹 및 작업자 역할을 사용하면 확장이 가능합니다. 웹 역할 및 작업자 역할의 가상 머신 크기에 대한 자세한 내용은 [Cloud Services에 대한 크기 구성](../../../cloud-services/cloud-services-sizes-specs.md)을 참조하세요.<br/><br/>**Cloud Services**를 사용할 때는 처리 분산과 유연한 애플리케이션 확장을 위해 여러 역할을 정의할 수 있습니다. 각각의 클라우드 서비스는 각기 고유한 애플리케이션 파일 및 구성을 포함하는 하나 이상의 웹 역할 및/또는 작업자 역할을 포함합니다. 역할에 대해 배포한 역할 인스턴스(가상 머신)의 수를 늘려 클라우드 서비스를 수직 확장하고 역할 인스턴스의 수를 줄여 클라우드 서비스를 수평 확장할 수 있습니다. 자세한 내용은 [Azure 실행 모드](../../../cloud-services/cloud-services-choose-me.md)를 참조하세요.<br/><br/>확장은 [Cloud Services, Virtual Machines 및 Virtual Network Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 및 부하 분산 장치를 통한 기본 제공 Azure 고가용성 지원을 통해 사용 가능합니다.<br/><br/>다중 계층 애플리케이션의 경우 Azure Virtual Network를 통해 웹/작업자 역할 애플리케이션을 데이터베이스 서버에 연결하는 것이 좋습니다. 또한 Azure는 동일한 클라우드 서비스에서 여러 VM의 부하를 분산하여 사용자 요청을 VM 간에 분배합니다. 이러한 방식으로 연결된 가상 머신은 Azure 데이터 센터 내의 로컬 네트워크를 통해 서로 직접 통신할 수 있습니다.<br/><br/>Azure Portal과 예약 시간에 **자동 크기 조정**을 설정할 수 있습니다. 자세한 내용은 [포털에서 클라우드 서비스 크기 자동 조정을 구성하는 방법](../../../cloud-services/cloud-services-how-to-scale-portal.md)을 참조하세요. |**강화 및 축소**: 웹 사이트에 예약된 인스턴스(VM)의 크기를 증가/감소시킬 수 있습니다.<br/><br/>확장: 웹 사이트에 예약된 인스턴스(VM)를 더 많이 추가할 수 있습니다.<br/><br/>Azure 클래식 포털과 예약 시간에 **자동 크기 조정** 을 설정할 수 있습니다. 자세한 내용은 [Web Apps 크기를 조정하는 방법](../../../app-service/web-sites-scale.md)을 참조하세요. |

프로그래밍 방법 선택과 관련한 자세한 내용은 [Azure Web Apps, Cloud Services 및 VM: 사용하는 경우](../../../app-service/overview-compare.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

