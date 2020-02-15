---
title: Azure Vm에서 SAP 시작 | Microsoft Docs
description: Microsoft Azure의 Vm (가상 머신)에서 실행 되는 SAP 솔루션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83144738124b223e305b6c0d3ad1065f5dae408e
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212604"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용 하 여 SAP 워크 로드 시나리오 호스트 및 실행

Microsoft Azure를 사용 하는 경우 확장 가능 하 고 규정을 준수 하는 엔터프라이즈 입증 플랫폼에서 중요 업무용 SAP 워크 로드 및 시나리오를 안정적으로 실행할 수 있습니다. Azure의 확장성, 유연성 및 비용 절감 효과를 얻을 수 있습니다. Microsoft와 SAP 간의 확장 된 파트너 관계를 사용 하 여 Azure에서 개발 및 테스트 및 프로덕션 시나리오에 걸쳐 SAP 응용 프로그램을 실행 하 고 완벽 하 게 지원할 수 있습니다. SAP NetWeaver에서 SAP S/4HANA, Linux의 SAP BI에서 Windows로, SAP HANA SQL에 대해 설명 했습니다.

Azure에서 다른 DBMS를 사용 하 여 SAP NetWeaver 시나리오를 호스트 하는 것 외에도 Azure에서 SAP BI와 같은 다른 SAP 워크 로드 시나리오를 호스트할 수 있습니다. 

Azure for SAP HANA의 고유성은 Azure를 따로 설정 하는 제품입니다. SAP HANA와 관련 된 추가 메모리 및 CPU 리소스를 많이 사용 하는 SAP 시나리오 호스팅을 지원 하기 위해 Azure는 고객 전용 운영 체제 미 설치 하드웨어를 제공 합니다. S/4HANA 또는 기타 SAP HANA 워크 로드에 대해 최대 24tb (120-TB)의 메모리를 필요로 하는 SAP HANA 배포를 실행 하려면이 솔루션을 사용 합니다. 

Azure에서 SAP 워크 로드 시나리오를 호스트 하는 것도 id 통합 및 Single Sign-On 요구 사항을 만들 수 있습니다. 이 상황은 Azure Active Directory (Azure AD)를 사용 하 여 다양 한 SAP 구성 요소와 SAP SaaS (software as a service) 또는 PaaS (platform as a service) 제품을 연결 하는 경우에 발생할 수 있습니다. Azure AD 및 SAP 엔터티와 이러한 통합 및 Single Sign-On 시나리오의 목록은 "AAD SAP identity integration and Single Sign-On" 섹션에서 설명 하 고 설명 합니다.

## <a name="changes-to-the-sap-workload-section"></a>SAP 워크 로드 섹션의 변경 내용
Azure의 SAP 워크 로드 섹션에서 문서에 대 한 변경 내용은이 문서의 끝에 나열 되어 있습니다.


## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 문서는 Azure (Large Instances) 또는 간단한 HANA Large Instances에 대 한 SAP HANA를 안내 합니다. HANA Large Instances의 다음 영역에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure의 SAP HANA(대규모 인스턴스) 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure (Large Instances)의 SAP HANA에 대 한 인프라 및 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Azure (Large Instances)에 SAP HANA 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Azure (큰 인스턴스)의 SAP HANA에 대 한 고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Azure (Large Instances)에서 SAP HANA 문제 해결 및 모니터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

다음 단계:

- [Azure (Large Instances)에서 SAP HANA의 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 읽기



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 가상 컴퓨터의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 필수 구성 요소로 서 azure IaaS의 기본 서비스를 제공 하는 Azure의 주요 서비스에 대해 잘 알고 있어야 합니다. 따라서 Azure 계산, 저장소 및 네트워킹에 대 한 지식이 필요 합니다. 이러한 주제 중 상당수는 SAP NetWeaver 관련 [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에서 처리 됩니다. 

Azure의 HANA에 대 한 자세한 내용은 다음 문서 및 해당 하위 문서를 참조 하세요.

- [빠른 시작: Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure virtual machines의 SAP HANA에 대 한 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [스토리지 스냅샷에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure virtual machines에 배포 된 SAP NetWeaver
이 섹션에서는 Azure의 SAP NetWeaver 및 Business One에 대 한 계획 및 배포 설명서를 제공 합니다. 이 설명서에서는 Azure의 SAP 워크 로드에서 HANA가 아닌 데이터베이스를 사용 하는 기본 사항 및 사용에 중점을 설명 합니다. 고가용성에 대 한 문서와 문서는 다음과 같이 Azure의 HANA 고가용성을 위한 기초 이기도 합니다.

- [Azure virtual machines의 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux Vm에서 SAP NetWeaver 실행](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery를 사용 하 여 다중 계층 SAP NetWeaver 응용 프로그램 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure의 SAP 워크 로드에서 HANA가 아닌 데이터베이스에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm에서 SAP MaxDB, 라이브 캐시 및 콘텐츠 서버 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure의 SAP HANA 데이터베이스에 대 한 자세한 내용은 "Azure virtual machines에서 SAP HANA" 섹션을 참조 하세요.

Azure에서 SAP 워크 로드의 고가용성에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

이 문서에서는 다양 한 기타 아키텍처 및 시나리오 문서를 가리킵니다. 이후 시나리오 문서에서는 다양 한 고가용성 방법의 배포 및 구성을 설명 하는 자세한 기술 문서에 대 한 링크가 제공 됩니다. SAP NetWeaver 워크 로드에 대 한 고가용성을 설정 하 고 구성 하는 방법을 보여 주는 다른 문서는 Linux 및 Windows 운영 체제입니다.


Azure Active Directory (Azure AD)와 SAP 서비스 및 Single Sign-On 간 통합에 대 한 자세한 내용은 다음을 참조 하세요.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 환경: Azure AD를 사용 하 여 Fiori 실행 패드 SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

SAP 구성 요소에 Azure 서비스를 통합 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>변경 로그

- 02/13/2020: [Azure vm의 Sap NetWeaver에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 고가용성 및 RHEL의 Sap Azure NetApp Files NetWeaver에 대 한 고가용성 및 [azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 의 경우 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞게 조정 합니다.
- 02/11/2020: azure [Virtual Machines에 대 한 Azure Large Instance 마이그레이션의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration) 릴리스
- 02/07/2020: [SAP HA 시나리오에서 Azure 표준 ILB를 사용 하 여 샘플 NSG 스크린샷 업데이트를 사용 하는 vm에 대 한 공용 끝점 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) 을 변경 합니다.
- 02/03/2020: sap 응용 프로그램용 [SLES의 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) 에서 sap nw에 대 한 고가용성을 변경 하 고, [SLES의 anf를 사용 하는 AZURE VM의 sap nw 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 사용 하 여 SLES에서 클러스터 노드의 호스트 이름에 대시 사용에 대 한 경고를 제거 합니다.
- 01/28/2020: RHEL의 [Azure vm에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 을 변경 하 여 SAP HANA 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞춥니다.
- 01/17/2020: [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 을 변경 하 여 기존 vm을 근접 배치 그룹으로 이동 하는 섹션을 변경 합니다.
- 01/17/2020: Azure 가용성 영역을 [사용 하 여 SAP 워크 로드 구성을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 변경 하 여 가용성 영역 간의 대기 시간 측정을 자동화 하는 절차를 가리키도록 합니다.
- 01/16/2020: [Azure에서 SAP HANA (Large Instances)를 설치 및 구성 하는 방법에 따라](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) OS 릴리스를 HANA IaaS 하드웨어 디렉터리에 맞게 조정 합니다.
- 01/16/2020: [SLES 다중 SID 가이드에서 Azure vm의 Sap NetWeaver에 대 한 고가용성 변경 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) : 큐에 대기 중인 서버 2 아키텍처 (ENSA2)를 사용 하 여 sap 시스템에 대 한 지침 추가
- 01/10/2020: SLES에 [Azure NetApp Files 있는 Azure vm의 대기 노드를 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 하 여 SAP HANA를 확장 하 고 [SAP HANA, RHEL의 Azure NetApp Files를 사용 하 여 azure vm의 대기 노드를 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 하 여 `nfs4_disable_idmapping` 변경 내용을 영구적으로 변경 하는 방법에 대 한 지침을 추가 합니다.
- 01/10/2020: sap 응용 프로그램 및 [Azure NetApp Files Virtual Machines azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 에서 sap [NetWeaver의 sap에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 고가용성의 변경 사항은 sap 응용 프로그램에 대해 Azure NetApp Files SLES를 사용 하 여 sap 응용 프로그램에서 Azure NetApp Files NFSv4 볼륨을 탑재 하는 방법에 대 한 지침을 추가할 수 있습니다.
- 12/23/2019: [SLES 다중 SID 가이드의 Azure vm에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 릴리스
- 12/18/2019: RHEL의 [Azure NetApp Files를 사용 하 여 Azure vm에서 대기 노드를 사용 하 여 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 릴리스
- 11/21/2019: SAP HANA의 변경 내용은 [Azure vm에서 SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure vm의 대기 노드로 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 되어 NFS ID 매핑의 구성을 간소화 하 고 권장 되는 기본 네트워크 인터페이스를 변경 하 여 라우팅을 간소화 합니다.
- 11/15/2019: sap 응용 프로그램에 대 한 [Azure NetApp Files와 sap SUSE Linux Enterprise Server NetWeaver](high-availability-guide-suse-netapp-files.md) 의 고가용성에 대 한 고가용성의 사소한 변경 내용으로, 용량 풀 크기 제한을 명확 하 게 설명 하 고 NFSv3 버전만 지원 되는 NetWeaver [Azure NetApp Files Red Hat Enterprise Linux에](high-availability-guide-rhel-netapp-files.md) 대 한 sap 응용 프로그램의 경우 고가용성을 제공 합니다.
- 11/12/2019: [Windows에서 SMB (Azure NetApp Files)를 사용 하는 SAP NetWeaver의 고가용성](high-availability-guide-windows-netapp-files-smb.md) 릴리스
- 11/08/2019: [SUSE Linux Enterprise Server의 Azure vm에서 SAP HANA의 고가용성](sap-hana-high-availability.md)을 변경 하 고, [azure vm (가상 머신)에서 SAP HANA 시스템 복제](sap-hana-high-availability-rhel.md)Virtual Machines를 설정 하 고, sap [응용 프로그램 SUSE Linux Enterprise Server](high-availability-guide-suse.md)에서 sap NetWeaver에 대 한 고가용성을 설정 하 고, VIRTUAL MACHINES을 사용 하 SUSE Linux Enterprise Server [에서 sap NetWeaver에 대](high-availability-guide-suse-netapp-files.md)한 고가용성을 Azure NetApp Files, [sap NetWeaver에 대 한 고가용성 azure Virtual Machines ](high-availability-guide-rhel.md), [Azure Virtual Machines Azure NetApp Files Red Hat Enterprise Linux 고가용성](high-availability-guide-rhel-netapp-files.md), [SUSE Linux Enterprise Server의 azure vm에서 NFS에 대 한](high-availability-guide-suse-nfs.md)고가용성, sap NetWeaver에 대 한 [GlusterFS의 azure Red Hat Enterprise Linux vm](high-availability-guide-rhel-glusterfs.md) 에서 azure 표준 부하 분산 장치 권장  
- 11/08/2019: 암호화 권장 사항을 명시 하기 위해 [SAP 워크 로드 계획 및 배포 검사 목록의](sap-deployment-checklist.md) 변경 내용  
- 11/04/2019: 유니캐스트 구성을 사용 하 여 클러스터를 직접 만들 수 있도록 [Azure에서 SUSE Linux Enterprise Server Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 의 변경 내용  
- 10/29/2019: [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 끝점 연결](high-availability-guide-standard-load-balancer-outbound-connections.md) 릴리스
- 10/25/2019:/hana/shared 볼륨에 대 한 NFS 프로토콜을 명확 하 게 설명 하 [는 SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure vm에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) azure [가상 머신 저장소 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 및 SAP HANA의 대기 노드를 확장 하는 SAP HANA의 변경
- 10/22/2019: sap [응용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)프로그램에 대 한 SUSE Linux Enterprise Server의 sap NetWeaver에 대 한 고가용성, sap 응용 프로그램에 대 한 Azure NetApp Files를 사용 하는 [SUSE Linux Enterprise Server의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)azure vm에 대 한 고가용성, [SUSE LINUX ENTERPRISE SERVER의 azure vm에서 NFS에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)고가용성, [azure에서 SUSE Linux Enterprise Server 설정, azure에서 SUSE Linux Enterprise Server 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), azure [vm에서 IBM Db2 LUW의 고가용성, Pacemaker 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) azure 부하 분산 장치 검색 강화를 위해 [SUSE Linux Enterprise Server에서 Azure vm의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 에 대 한 고가용성
- [Azure 가상 컴퓨터 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 의 anf 섹션과 헤더 섹션을 변경 합니다.
- 10/21/2019: SLES의 [Azure NetApp Files를 사용 하 여 Azure vm에서 대기 노드를 사용 하 여 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 릴리스
- 10/16/2019: [백업 및 복원](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore) 에서 끊어진 링크를 수정 하십시오.
- 10/16/2019: [Pacemaker를 사용 하는 SUSE Linux Enterprise Server의 Azure vm에서 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) 을 SLES 12 s p 3에서 SLES 12 SP4로 변경 합니다.
- 10/11/2019: [Azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 에서 Ultra disk 저장소 구성 및 anf 소개의 변경 내용
- 10/01/2019: 보다 명확한 정보를 얻기 위해 [SAP 응용 프로그램의 네트워크 대기 시간을 최적화 하기 위해 Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 의 그래픽을 변경 합니다.
- 10/01/2019:/hana/shared.에 대 한 항상 사용 가능한 NFS 공유에 대 한 문을 수정 하기 위해 [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) 을 변경 합니다. 
- 09/28/2019: SBD를 RHEL 클러스터에서 지원 하지 않는다는 것을 명시 하기 위해 [Azure의 Red Hat Enterprise Linux Pacemaker On 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) 변경  
- 09/17/2019: SAP 용 VM 확장 관련 용어를 통합 하기 위한 NetWeaver 계획 및 배포 가이드 변경  
- 08/22/2019: [Azure에서 SUSE Linux Enterprise Server On Pacemaker on을 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 하 여 사용자 지정 역할 만들기에 대 한 url을 업데이트 합니다.  
- 08/16/2019: azure의 새 버전으로 업데이트 하는 경우 [azure에서 Red Hat Enterprise Linux On Pacemaker on을 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) 하 여 사용자 지정 역할의 작업을 업데이트 하도록 고객에 게 알립니다.  
- 08/15/2019: Ultra disk의 일반 공급 (이전의 울트라 SSD)을 반영 하도록 [Azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 변경 되었습니다.
- 08/01/2019: [Azure에서 SUSE Linux Enterprise Server On Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 으로 변경 하 여 SLES 15 용 변경 사항을 통합 합니다. 
- 07/23/2019: [Azure의 파일 공유를 사용 하 여 Windows 장애 조치 (failover) 클러스터의 SAP ASCS/SCS 인스턴스를 클러스터](sap-high-availability-guide-wsfc-file-share.md) 에서 변경 하 여 Azure Site Recovery 서비스에서 제공 하는 저장소 공간 다이렉트 지원을 반영 합니다.
- 07/14/2019: [SAP 응용 프로그램의 네트워크 대기 시간을 최적화 하기 위해 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md) 릴리스
- 07/11/2019: hana large Instances를 포함 하는 다양 한 문서의 변경 내용이 HANA Large Instances의 수정 버전 4를 포함 합니다.
- 07/09/2019: [Red Hat Enterprise Server에서 IBM DB2 HADR](high-availability-guide-rhel-ibm-db2-luw.md) 에 대 한 새 가이드 릴리스
- 06/13/2019: [sap 응용 프로그램용 Azure NetApp Files를 사용 하 Red Hat Enterprise Linux의 Sap NetWeaver에 대 한 고가용성](high-availability-guide-rhel-netapp-files.md) 릴리스


