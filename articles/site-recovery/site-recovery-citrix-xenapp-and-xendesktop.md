---
title: Azure Site Recovery를 사용하여 다중 계층 Citrix XenDesktop 및 XenApp 배포에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Citrix XenDesktop 및 XenApp 배포에 대한 재해 복구를 설정하는 방법을 설명합니다.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038237"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>다중 계층 Citrix XenApp 및 XenDesktop 배포에 대해 재해 복구 설정



Citrix XenDesktop은 어디서든 모든 사용자에게 데스크톱 및 애플리케이션을 주문형으로 전달하는 데스크톱 가상화 솔루션입니다. FlexCast 전달 기술을 사용하여 XenDesktop은 애플리케이션 및 데스크톱을 사용자에게 빠르고 안전하게 전달할 수 있습니다.
현재 Citrix XenApp은 재해 복구 기능을 제공하지 않습니다.

훌륭한 재해 복구 솔루션에서는 위의 복잡한 애플리케이션 아키텍처에 기반하여 복구 계획을 모델링할 수 있어야 하며, 다양한 계층 간의 애플리케이션 매핑을 처리하기 위한 사용자 지정 단계를 추가할 수 있어야 합니다. 이에 따라 재해 발생시 한 번의 클릭으로 RTO(복구 시간 목표)를 낮추는 확실한 솔루션을 제공할 수 있습니다.

이 문서에서는 Hyper-V 및 VMware vSphere 플랫폼에서 온-프레미스 Citrix XenApp 배포용 재해 복구 솔루션을 빌드하기 위한 단계별 지침을 제공합니다. 또한 복구 계획, 지원되는 구성 및 필수 구성 요소를 사용하여 Azure로의 테스트 장애 조치(재해 복구 드릴) 및 계획되지 않은 장애 조치를 수행하는 방법을 설명합니다.


## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목을 이해해야 합니다.

1. [Azure에 가상 머신 복제](site-recovery-vmware-to-azure.md)
1. 방법: [복구 네트워크 디자인](site-recovery-network-design.md)
1. [Azure로 테스트 장애 조치 수행](site-recovery-test-failover-to-azure.md)
1. [Azure로 장애 조치 수행](site-recovery-failover.md)
1. 방법: [도메인 컨트롤러 복제](site-recovery-active-directory.md)
1. 방법: [SQL Server 복제](site-recovery-sql.md)

## <a name="deployment-patterns"></a>배포 패턴

Citrix XenApp 및 XenDesktop 팜에는 일반적으로 다음 배포 패턴이 있습니다.

**배포 패턴**

AD DNS 서버, SQL Database 서버, Citrix Delivery Controller, StoreFront 서버, XenApp Master(VDA), Citrix XenApp License Server 등을 사용하여 Citrix XenApp 및 XenDesktop 배포

![배포 패턴 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery 지원

이 문서의 목적상, vSphere 6.0/System Center VMM 2012 R2에서 관리되는 VMware 가상 머신의 Citrix 배포를 사용하여 DR을 설정했습니다.

### <a name="source-and-target"></a>원본 및 대상

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 이 문서에서 다루지 않는 내용 | 예
**VMware** | 이 문서에서 다루지 않는 내용 | 예
**물리적 서버** | 이 문서에서 다루지 않는 내용 | 예

### <a name="versions"></a>버전
고객은 Hyper-V 또는 VMware에서 실행되는 Virtual Machines 또는 물리적 서버로 XenApp 구성 요소를 배포할 수 있습니다. Azure Site Recovery는 Azure에 대한 실제 배포와 가상 배포를 모두 보호할 수 있습니다.
Azure에서는 XenApp 7.7 이상이 지원되므로 이러한 버전을 사용하는 배포만 재해 복구 또는 마이그레이션을 위해 Azure로 장애 조치할 수 있습니다.

### <a name="things-to-keep-in-mind"></a>주의할 사항

1. 서버 OS 컴퓨터를 사용하여 XenApp 게시 앱 및 XenApp 게시 데스크톱을 전달하는 온-프레미스 배포의 보호 및 복구는 지원됩니다.

2. 데스크톱 OS 컴퓨터를 사용하여 클라이언트 가상 데스크톱용 Desktop VDI(Windows 10 포함)를 전달하는 온-프레미스 배포의 보호 및 복구는 지원되지 않습니다. 이는 Site Recovery가 데스크톱 OS를 사용하여 머신의 복구를 지원하지 않기 때문입니다.  또한 일부 클라이언트 가상 데스크톱 운영 체제(예: Windows 7)는 Azure에서 아직 라이선스가 지원되지 않습니다. Azure의 클라이언트/서버 데스크톱용 라이선스에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/licensing-faq/).

3.  Azure Site Recovery는 기존 온-프레미스 MCS 또는 PVS 클론을 복제 및 보호할 수 없습니다.
Azure RM 프로비전을 사용하여 Delivery Controller에서 이러한 클론을 다시 만들어야 합니다.

4. NetScaler는 FreeBSD를 기반으로 하며 Azure Site Recovery는 FreeBSD OS 보호를 지원하지 않으므로 Azure Site Recovery를 사용하여 보호할 수 없습니다. Azure로 장애 조치한 후 Azure Marketplace에서 새 NetScaler 어플라이언스를 배포 및 구성해야 합니다.


## <a name="replicating-virtual-machines"></a>가상 머신 복제

복제 및 복구를 사용하려면 Citrix XenApp 배포의 다음 구성 요소를 보호해야 합니다.

* AD DNS 서버 보호
* SQL Database 서버 보호
* Citrix Delivery Controller 보호
* StoreFront 서버 보호
* XenApp Master(VDA) 보호
* Citrix XenApp License Server 보호


**AD DNS 서버 복제**

Azure에서 도메인 컨트롤러를 복제 및 구성하기 위한 지침은 [Azure Site Recovery로 Active Directory 및 DNS 보호](site-recovery-active-directory.md)를 참조하세요.

**SQL Database 서버 복제**

SQL Server 보호를 위한 권장 옵션에 대한 자세한 기술 지침은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](site-recovery-sql.md)를 참조하세요.

나머지 구성 요소 가상 머신을 Azure로 복제하기 시작하려면 [이 지침](site-recovery-vmware-to-azure.md)을 따릅니다.

![XenApp 구성 요소 보호](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Compute 및 네트워크 설정**

머신을 보호(복제된 항목 아래에 상태가 "보호됨"으로 표시됨)한 후에는 Compute 및 네트워크 설정을 구성해야 합니다.
Compute 및 네트워크 > Compute 속성에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다.
필요한 경우 Azure 요구 사항을 준수하도록 이름을 수정합니다. 또한 대상 네트워크, 서브넷 및 Azure VM에 할당될 IP 주소에 대한 정보를 보고 추가할 수 있습니다.

다음 사항에 유의하세요.

* 대상 IP 주소를 설정할 수 있습니다. 주소를 입력하지 않으면 장애 조치(failover)된 컴퓨터가 DHCP를 사용합니다. 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다. 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(failover)에 동일한 대상 IP 주소를 사용해도 됩니다.

* AD/DNS 서버의 경우 온-프레미스 주소를 유지하면 Azure 가상 네트워크에 대한 DNS 서버와 동일한 주소를 지정할 수 있습니다.

네트워크 어댑터 수는 다음과 같이 대상 가상 머신에 대해 지정하는 크기에 따라 결정됩니다.

*   원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일해야 합니다.
*   원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
* 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는 1개의 어댑터만 있어야 합니다.
*   가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.
*   가상 머신에 네트워크 어댑터가 여러 개 있으면 목록에서 첫 번째 어댑터가 Azure Virtual Machines에서 기본 네트워크 어댑터가 됩니다.


## <a name="creating-a-recovery-plan"></a>복구 계획 만들기

XenApp 구성 요소 VM에 대한 복제를 활성화한 후에는 복구 계획을 만들어야 합니다.
복구 계획은 장애 조치 및 복구에 대한 요구 사항이 유사한 가상 머신을 그룹화합니다.  

**복구 계획을 만드는 단계**

1. 복구 계획에서 XenApp 구성 요소 가상 머신을 추가합니다.
2. 복구 계획 -> + 복구 계획을 클릭합니다. 복구 계획에 대한 직관적인 이름을 제공합니다.
3. VMware 가상 머신의 경우: 원본 및 대상을 각각 VMware 프로세스 서버와 Microsoft Azure로 선택하고 배포 모델을 Resource Manager로 선택한 후 항목 선택을 클릭합니다.
4. Hyper-V 가상 머신의 경우: 원본 및 대상을 각각 VMM 서버와 Microsoft Azure로 선택하고 배포 모델을 Resource Manager로 선택한 후 항목 선택을 클릭하고 XenApp 배포 VM을 선택합니다.

### <a name="adding-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가

복구 계획을 사용자 지정하여 특정 시작 순서, 스크립트 또는 수동 작업에 대한 장애 조치 그룹을 추가할 수 있습니다. 다음 그룹을 복구 계획에 추가해야 합니다.

1. 장애 조치(failover) 그룹 1: AD DNS
2. 장애 조치(failover) 그룹 2: SQL Server VM
2. 장애 조치(failover) 그룹 3: VDA 마스터 이미지 VM
3. 장애 조치(failover) 그룹 4: Delivery Controller 및 StoreFront 서버 VM


### <a name="adding-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가

복구 계획의 특정 그룹 이전 또는 이후에 스크립트를 실행할 수 있습니다. 또한 수동 작업이 포함되고 장애 조치(failover) 중에 수행될 수도 있습니다.

사용자 지정 복구 계획은 다음과 유사합니다.:

1. 장애 조치(failover) 그룹 1: AD DNS
2. 장애 조치(failover) 그룹 2: SQL Server VM
3. 장애 조치(failover) 그룹 3: VDA 마스터 이미지 VM

   >[!NOTE]     
   >수동 또는 스크립트 작업을 포함하는 4, 6 및 7단계는 MCS/PVS 카탈로그가 있는 온-프레미스 XenApp 환경에만 적용됩니다.

4. 그룹 3 수동 또는 스크립트 작업: 마스터 VDA VM을 종료합니다.
Azure로 장애 조치된 경우 마스터 VDA VM은 실행 상태가 됩니다. Azure 호스팅을 사용하여 새 MCS 카탈로그를 만들려면 마스터 VDA VM이 중지됨(할당 취소됨) 상태에 있어야 합니다. Azure Portal에서 VM을 종료합니다.

5. 장애 조치(failover) 그룹 4: Delivery Controller 및 StoreFront 서버 VM
6. 그룹 3 수동 또는 스크립트 작업 1:

    ***Azure RM 호스트 연결 추가***

    Azure에서 새 MCS 카탈로그를 프로비전하려면 Delivery Controller 머신에서 Azure 호스트 연결을 만듭니다. 이 [문서](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)에 설명된 단계를 따릅니다.

7. 그룹 3 수동 또는 스크립트 작업 2:

    ***Azure에서 MCS 카탈로그 다시 만들기***

    기본 사이트의 기존 MCS 또는 PVS 클론은 Azure에 복제되지 않습니다. 복제된 마스터 VDA 및 Azure 프로비전을 사용하여 Delivery Controller에서 이러한 클론을 다시 만들어야 합니다. 이 [문서](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/)에 설명된 대로 단계를 따라 Azure에서 MCS 카탈로그를 만듭니다.

![XenApp 구성 요소에 대한 복구 계획](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >[위치](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts)에서 스크립트를 사용하여 장애 조치된 가상 컴퓨터의 새 IP로 DNS를 업데이트하거나, 필요한 경우 장애 조치된 가상 컴퓨터에 부하 분산 장치를 연결할 수 있습니다.


## <a name="doing-a-test-failover"></a>테스트 장애 조치 수행

[이 지침](site-recovery-test-failover-to-azure.md)에 따라 테스트 장애 조치를 수행합니다.

![복구 계획](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>장애 조치 수행

장애 조치를 수행할 때 [이 지침](site-recovery-failover.md)을 따릅니다.

## <a name="next-steps"></a>다음 단계

이 백서에서 XenApp 및 XenDesktop 배포를 복제하는 방법에 대해 [자세히](https://aka.ms/citrix-xenapp-xendesktop-with-asr) 알아볼 수 있습니다. Site Recovery를 사용하여 [다른 애플리케이션을 복제하는 방법](site-recovery-workload.md)에 대한 지침을 확인하세요.
