---
title: Azure Vm에서 SAP 시작 | Microsoft Docs
description: Microsoft Azure의 가상 컴퓨터 (Vm)에서 실행 되는 SAP 솔루션에 알아봅니다
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835328"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용 하 여 호스트 하 고 SAP 워크 로드 시나리오를 실행 합니다.

Microsoft Azure를 사용 하면 확장성, 준수 및 기업에서 입증 된 플랫폼에서 중요 업무용 SAP 워크 로드 및 시나리오를 실행할 안전 하 게 있습니다. 확장성, 유연성을 얻고 Azure 절감 합니다. Microsoft와 SAP 사이의 확장 된 파트너 관계를 사용 하 여 Azure에서 개발, 테스트 및 프로덕션 시나리오 전체에서 SAP 응용 프로그램을 실행할 수 있으며 완벽 한 지원을 받으세요. SAP NetWeaver에서 SAP S/4HANA, Windows, Linux 및 SAP HANA에서 SQL, SAP BI로 답변이 거둘 수 있습니다.

Azure에서 다양 한 DBMS 사용 하 여 SAP NetWeaver 시나리오 호스팅 외에도 Azure에서 SAP BI와 같은 다른 SAP 워크 로드 시나리오를 호스트할 수 있습니다. 

SAP HANA에 대 한 Azure의 고유성 떨어져 있는 Azure를 설정 하는 제품입니다. 더 많은 메모리와 CPU 리소스 수요가 많은 SAP 시나리오 SAP HANA를 포함 하는 호스트를 사용 하려면 Azure 고객 전용 베어 메탈 하드웨어 사용을 제공 합니다. S/4HANA 또는 다른 SAP HANA 워크 로드에 대 한 최대 24 TB (120 TB 스케일 아웃)의 메모리를 필요로 하는 SAP HANA 배포를 실행 하려면이 솔루션을 사용 합니다. 

또한 Azure에서 SAP 워크 로드 시나리오 호스팅 id 통합 및 single sign-on의 요구 사항을 만들 수 있습니다. Azure Active Directory (Azure AD)를 사용 하 여 다양 한 SAP 구성 요소를 연결 하 여 SAP 소프트웨어-as a service (SaaS) 또는 플랫폼-as a service (PaaS) 제품을이 상황이 발생할 수 있습니다. 이러한 통합 및 Azure ad single sign-on 시나리오의 목록을 SAP 엔터티 설명 되 고 "AAD SAP id 통합 및 single sign on" 섹션에 설명

## <a name="latest-changes"></a>최신 변경 내용

- 릴리스의 [Azure HANA Large Instances는 Azure portal을 통해 제어](hana-li-portal.md)

- 릴리스의 [SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](high-availability-guide-suse-netapp-files.md)

- 하는 바를 확실히 **Linux OS 매개 변수 net.ipv4.tcp_timestamps** 부하 분산 장치를 Azure와 함께에서 설정

- 릴리스의 [Azure 가용성 영역을 사용 하 여 SAP 워크 로드 구성](sap-ha-availability-zones.md)

- [SAP 워크로드 계획 및 배포 검사 목록](sap-deployment-checklist.md)의 릴리스




## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 문서 안내 SAP HANA on Azure (대규모 인스턴스) 또는 short, HANA 큰 인스턴스에 대 한 합니다. HANA 큰 인스턴스는 다음 영역에 정보를 참조 하세요.

- [Azure의 SAP HANA(대규모 인스턴스) 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [인프라 및 SAP HANA on Azure (큰 인스턴스)에 대 한 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Azure (큰 인스턴스)에서 SAP HANA를 설치 합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP hana on Azure (큰 인스턴스) 고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [문제 해결 및 SAP HANA on Azure (큰 인스턴스)를 모니터링 합니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

다음 단계:

- 읽기 [SAP HANA on Azure (큰 인스턴스)의 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure virtual machines에서 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 필수 요소로, Azure IaaS의 기본 서비스를 제공 하는 Azure의 서비스 주체를 사용 하 여 친숙 해야 합니다. 따라서 Azure 계산, 저장소 및 네트워킹에 대 한 지식이 필요합니다. SAP NetWeaver 관련에서 처리 되는 대부분의 이러한 주제 [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)합니다. 

Azure의 HANA에 대 한 내용은 다음 문서 및 해당 subarticles를 참조 하세요.

- [빠른 시작: Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure virtual machines에서 SAP HANA backup 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [저장소 스냅숏에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure virtual machines에서 배포 된 SAP NetWeaver
이 섹션에서는 SAP NetWeaver 및 Azure의 Business One에 대 한 계획 및 배포 설명서를 나열합니다. 설명서는 Azure에서 SAP 워크 로드를 사용 하 여 기본 사항 및 비 HANA 데이터베이스의 사용에 집중합니다. 문서 및 고가용성에 대 한 문서도 Azure에서 HANA의 고가용성을 위해 foundation 같은:

- [Azure virtual machines에서 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux Vm에서 SAP NetWeaver 실행](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery를 사용 하 여 다중 계층 SAP NetWeaver 응용 프로그램 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure에서 SAP 워크 로드를 HANA 이외의 데이터베이스에 대 한 내용은 다음을 참조 하세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm에서 SAP MaxDB, Live 캐시 및 콘텐츠 서버 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure에서 SAP HANA 데이터베이스에 대 한 자세한 내용은 "Azure virtual machines에서 SAP HANA." 섹션을 참조 하세요.

Azure에서 SAP 워크 로드를의 고가용성에 대 한 정보를 보려면

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

이 문서는 다른 다양 한 아키텍처 및 시나리오 문서를 가리킵니다. 뒷부분에 나오는 시나리오 문서를 배포 및 구성을 다른 고가용성 메서드를 설명 하는 자세한 기술 문서에 대 한 링크가 제공 됩니다. 설정 하 고 SAP NetWeaver 워크 로드의 고가용성을 구성 하는 방법을 보여 주는 다른 문서는 Linux 및 Windows 운영 체제를 설명 합니다.


Azure Active Directory (Azure AD) 및 SAP 서비스 및 single sign on 간의 통합에 대 한 내용은 다음을 참조 하세요.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 환경: Fiori Launchpad SAML single sign-on을 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Azure 서비스의 SAP 구성 요소 통합에 대 한 내용은 다음을 참조 하세요.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




