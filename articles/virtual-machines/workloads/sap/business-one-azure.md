---
title: Azure Virtual Machines의 SAP Business One | Microsoft Docs
description: Azure의 SAP Business One입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 101710b5a57faa37be77ff4b059fa0d494f4e617
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835654"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP Business One
이 문서에서는 Azure Virtual Machines에 SAP Business One을 배포하는 지침을 제공합니다. 이 문서는 SAP용 Business One 설치 문서를 대체하지 않습니다. 이 문서에서는 Business One 애플리케이션을 실행할 Azure 인프라에 대한 기본 계획 및 배포 지침을 다루어야 합니다.

Business One은 다음 두 가지 데이터베이스를 지원합니다.
- SQL Server - [SAP Note #928839 - Release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)(SAP 노트 #928839 - Microsoft SQL Server에 대한 릴리스 계획)을 참조하세요.
- SAP HANA - SAP HANA에 대한 정확한 SAP Business One 지원 매트릭스를 보려면 [SAP Product Availability Matrix](https://support.sap.com/pam)(SAP 제품 가용성 매트릭스)를 확인하세요.

SQL Server의 경우, [SAP NetWeaver에 대한 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide)에 설명된 기본 배포 고려 사항이 적용됩니다. SAP HANA의 경우, 이 문서에 고려 사항이 언급되어 있습니다.

## <a name="prerequisites"></a>필수 조건
이 가이드를 사용하려면 다음 Azure 구성 요소에 대한 기본 지식이 필요합니다.

- [Windows의 Azure 가상 머신](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Linux의 Azure 가상 머신](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [PowerShell을 사용하는 Azure 네트워킹 및 가상 네트워크 관리](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Azure 네트워킹 및 CLI 사용 하 여 가상 네트워크](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure CLI를 사용하여 Azure 디스크 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Business One에만 관심이 있는 경우에도 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 문서를 통해 유용한 정보를 얻을 수 있습니다.

SAP Business One을 배포하는 인스턴스는 다음 조건을 충족한다고 가정합니다.

- VM 등의 지정된 인프라에 SAP HANA를 설치하는 데 익숙함
- Azure VM 등의 인프라에 SAP Business One 애플리케이션을 설치하는 데 익숙함
- SAP Business One 및 선택한 DBMS 시스템 운영에 익숙함
- Azure에 인프라를 배포하는 데 익숙함

이러한 모든 영역은 이 문서에서 다루지 않습니다.

Azure 문서 외에도 Business One을 참조하거나 Business One에 대한 SAP의 중앙 노트인 주요 SAP 노트에 대해 알고 있어야 합니다.

- [528296 - General Overview Note for SAP Business One Releases and Related Products](https://launchpad.support.sap.com/#/notes/528296)(528296 - SAP Business One 릴리스 및 관련 제품에 대한 일반 개요 노트)
- [2216195 - Release Updates Note for SAP Business One 9.2, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)(2216195 - SAP Business One 9.2에 대한 릴리스 업데이트 노트, SAP HANA용 버전)
- [2483583 - Central Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)(2483583 - SAP Business One 9.3에 대한 중앙 노트)
- [2483615 - Release Updates Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)(2483615 - SAP Business One 9.3에 대한 릴리스 업데이트 노트)
- [2483595 - Collective Note for SAP Business One 9.3 General Issues](https://launchpad.support.sap.com/#/notes/2483595)(2483595 - SAP Business One 9.3 일반 문제에 대한 총체적 노트)
- [2027458 - Collective Consulting Note for SAP HANA-Related Topics of SAP Business One, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)(2027458 - SAP Business One의 SAP HANA 관련 항목에 대한 총체적 컨설팅 노트, SAP HANA용 버전)


## <a name="business-one-architecture"></a>Business One 아키텍처
Business One은 다음 두 개의 계층이 있는 애플리케이션입니다.

- ‘fat’ 클라이언트가 있는 클라이언트 계층
- 테넌트에 대한 데이터베이스 스키마가 포함된 데이터베이스 계층

[SAP Business One Administrator's Guide](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf)(SAP Business One 관리자 가이드)에는 클라이언트 파트에서 실행 중인 구성 요소와 서버 파트에서 실행 중인 파트에 대한 더 유용한 개요가 나와 있습니다. 

클라이언트 계층과 DBMS 계층 간에는 대기 시간이 중요한 상호 작용이 많기 때문에 Azure에 배포할 때 두 계층이 모두 Azure에 있어야 합니다. 그런 다음, 사용자가 Business One 클라이언트 구성 요소를 위해 RDS 서비스를 실행하는 하나 이상의 VM에 RDS를 수행하는 것이 일반적입니다.

### <a name="sizing-vms-for-sap-business-one"></a>SAP Business One의 VM 크기 조정

클라이언트 VM의 크기 조정과 관련된 리소스 요구 사항은 SAP가 [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)(SAP Business One 하드웨어 요구 사항 가이드) 문서에서 설명합니다. Azure의 경우, 문서의 2.4 장에 명시된 요구 사항을 사용하여 계산하고 집중해야 합니다.

SAP NetWeaver에서 지원하는 VM만 Business One 클라이언트 구성 요소 및 DBMS 호스트를 호스트하는 Azure 가상 머신으로 사용할 수 있습니다. SAP NetWeaver에서 지원하는 Azure VM 목록은 [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)(SAP 노트 #1928533)을 참조하세요.

SAP HANA를 Business One의 DBMS 백 엔드로 실행하는 경우, [HANA certifeid IaaS platform list](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One)(HANA 인증 IaaS 플랫폼 목록)에서 HANA의 Business에 대해 나열된 VM만 HANA에 사용할 수 있습니다. Business One 클라이언트 구성 요소는 DBMS 시스템으로 사용되는 SAP HANA에 대한 보다 강력한 이 제한 사항의 영향을 받지 않습니다.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>SAP Business One에 사용할 운영 체제 릴리스

원칙적으로, 항상 최신 운영 체제 릴리스를 사용하는 것이 가장 좋습니다. 특히 Linux 공간에서는 Suse 및 Red Hat의 최신 부 릴리스마다 새로운 Azure 기능이 도입되었습니다. Windows 쪽에서는 Windows Server 2016을 사용하는 것이 좋습니다.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Azure에 SAP Business One용 인프라 배포
다음 몇 개의 장에서는 SAP를 배포하는 데 중요한 인프라 부분에 대해 설명합니다.

### <a name="azure-network-infrastructure"></a>Azure 네트워크 인프라
Azure에 배포하는 데 필요한 네트워크 인프라는 단일 Business One 시스템을 직접 배포하는지, 또는 고객을 위해 수십 개의 Business One 시스템을 호스트하는 호스터인지에 따라 다릅니다. Azure에 연결하는 방법에 따라 디자인이 약간 변경될 수도 있습니다. Azure에 대한 VPN 연결이 있고, [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 통해 Active Directory를 Azure로 확장하는 디자인은 다양한 가능성 중 하나입니다.

![Business One을 포함하는 간단한 네트워크 구성](./media/business-one-azure/simple-network-with-VPN.PNG)

제시된 간소화된 구성에는 라우팅을 제어 및 제한할 수 있는 몇 가지 보안 인스턴스가 도입되었습니다. 이 구성은 다음으로 시작됩니다. 

- 고객의 온-프레미스 쪽에 있는 라우터/방화벽.
- 다음 인스턴스는 SAP Business One 구성을 실행하는 Azure VNet에 대한 라우팅 및 보안 규칙을 도입하는 데 사용할 수 있는 [Azure 네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)입니다.
- Business One 클라이언트의 사용자가 데이터베이스를 실행하는 Business One 서버를 실행하는 서버를 볼 수 없도록 하려면 Business One 클라이언트를 호스트하는 VM과 Business One 서버를 VNet에 있는 두 개의 다른 서브넷에 구분해야 합니다.
- Business One 서버에 대한 액세스를 제한하기 위해 다시 두 개의 다른 서브넷에 할당된 Azure NSG를 사용합니다.

보다 정교한 버전의 Azure 네트워크 구성은 문서화된 Azure [허브 및 스포크 아키텍처 모범 사례](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)를 기반으로 합니다. 허브 및 스포크 아키텍처 패턴은 간소화된 첫 번째 구성을 다음과 같은 구성으로 변경합니다.


![Business One을 포함하는 허브 및 스포크 구성](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

사용자가 Azure에 대한 개인 연결 없이 인터넷을 통해 연결하는 경우, Azure의 네트워크 디자인은 [Azure 및 인터넷 간의 DMZ](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)에 대한 Azure 참조 아키텍처에 문서화된 원칙을 따라야 합니다.

### <a name="business-one-database-server"></a>Business One 데이터베이스 서버
데이터베이스 유형으로 SQL Server 및 SAP HANA를 사용할 수 있습니다. DBMS에 관계없이, [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)(SAP 워크로드에 대한 Azure Virtual Machines DBMS 배포의 고려 사항) 문서를 읽고 Azure VM의 DBMS 배포와 관련 네트워킹 및 저장소 항목을 전반적으로 이해해야 합니다.

특정 및 일반 데이터베이스 문서에서 이미 강조되었지만, 다음을 숙지해야 합니다.

- [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 및 [Azure에서 Linux 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

이러한 문서는 저장소 유형 및 고가용성 구성을 선택하는 데 도움이 됩니다.

원칙적으로, 다음 지침을 따라야 합니다.

- 표준 HDD보다 프리미엄 SSD를 사용합니다. 사용 가능한 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](../../windows/disks-types.md) 문서를 참조하세요.
- 비관리형 디스크보다 Azure Managed Disks 사용
- 디스크 구성을 사용하여 충분한 IOPS 및 I/O 처리량이 구성되었는지 확인
- 비용 효율적인 저장소 구성을 위해 /hana/data 및 /hana/log 볼륨 결합


#### <a name="sql-server-as-dbms"></a>SQL Server를 DBMS로 사용
SQL Server를 Business One의 DBMS로 배포하려면 [SQL Server Azure Virtual Machines DBMS deployment for SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)(SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포) 문서를 참조하세요. 

SQL Server의 DBMS 쪽에 대한 대략적인 크기 조정 예상은 다음과 같습니다.

| 사용자 수 | vCPU | 메모리 | 예제 VM 유형 |
| --- | --- | --- | --- |
| 최대 20명 | 4 | 16GB | D4s_v3, E4s_v3 |
| 최대 40명 | 8 | 32GB | D8s_v3, E8s_v3 |
| 최대 80명 | 16 | 64GB | D16s_v3, E16s_v3 |
| 최대 150명 | 32 | 128GB | D32s_v3, E32s_v3 |

위에 나열된 크기 조정은 시작할 위치에 대한 아이디어를 제공합니다. 필요한 리소스가 더 많거나 적을 수 있으며, 이 경우 Azure에서 쉽게 조정할 수 있습니다. VM을 다시 시작하기만 하면 VM 유형 간에 변경할 수 있습니다.

#### <a name="sap-hana-as-dbms"></a>SAP HANA를 DBMS로 사용
SAP HANA를 DBMS로 사용하는 다음 섹션에서는 [Azure의 SAP HANA 운영 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) 문서의 고려 사항을 따라야 합니다.

Azure에서 Business One의 데이터베이스로 사용되는 SAP HANA에 대한 고가용성 및 재해 복구 구성은 [Azure 가상 머신의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) 문서를 참조하세요.

SAP HANA 백업 및 복원 전략은 [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) 및 해당 문서에서 가리키는 문서를 참조하세요.

 
### <a name="business-one-client-server"></a>Business One 클라이언트 서버
이러한 구성 요소에서 저장소 고려 사항은 중요하지 않습니다. 그래도 신뢰할 수 있는 플랫폼을 구현하는 것이 좋습니다. 따라서 기본 VHD의 경우에도 이 VM에 Azure Premium Storage를 사용해야 합니다. [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)(SAP Business One 하드웨어 요구 사항 가이드)에 제공된 데이터를 사용하여 VM 크기를 조정합니다. Azure의 경우, 문서의 2.4 장에 명시된 요구 사항을 사용하여 계산하고 집중해야 합니다. 요구 사항을 계산할 때 다음 문서와 비교하여 이상적인 VM을 찾아야 합니다.

- [Azure에서 Windows 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533)(SAP 노트 #1928533)

필요한 CPU 수와 메모리를 Microsoft에서 문서화된 내용과 비교합니다. VM을 선택할 때 네트워크 처리량도 고려하세요.








