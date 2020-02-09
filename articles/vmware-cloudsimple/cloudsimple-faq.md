---
title: FAQ-Azure VMware 솔루션 (AVS)
description: Azure VMware 솔루션에 대 한 질문과 대답 (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025064"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>AVS의 VMware 솔루션에 대 한 질문과 대답

## <a name="avs-service"></a>AVS 서비스

**Azure VMware 솔루션 (AVS) 이란?**

Azure VMware 솔루션 (AVS)은 VMware 워크 로드를 변환 하 고 Azure에서 전용 사설 클라우드 (몇 분)로 확장 합니다. AVS는 프로 비전, 인프라 관리 및 온-프레미스와 Azure 간의 오케스트레이션 워크 로드를 처리 합니다. 앱이 온-프레미스와 Azure에서 동일 하 게 실행 되기 때문에 앱을 다시 설계 하는 복잡성 없이 클라우드의 탄력성 및 서비스를 활용 하는 것이 좋습니다. AVS를 사용 하면 주문형 프로 비전, 종 량 제 및 용량 최적화를 제공 하는 클라우드 소비 모델을 통해 총 소유 비용을 줄일 수 있습니다. 기능, 이점 및 시나리오는 [Azure에서 제공 하는 VMware 솔루션인 AVS](cloudsimple-vmware-solutions-overview.md) 를 참조 하세요.

**AVS 사설 클라우드 란?**

AVS 사설 클라우드는 Azure 위치의 Microsoft Azure 인프라 (하드웨어 및 데이터 센터 공간)에 배포 된 고성능 계산, 저장소 및 네트워킹 환경으로 구성 된 사설 전용 클라우드입니다. AVS 사설 클라우드는 네이티브 VMware ' platform as a service '를 제공 합니다. VMware 용어로 각 AVS 사설 클라우드는 정확히 하나의 vCenter Server 인스턴스를 포함 합니다. VCenter Server는 해당 하는 Vsphere (가상 SAN) 저장소와 함께 하나 이상의 vSphere 클러스터에 포함 된 여러 ESXi 노드를 관리 합니다. AVS 서비스는 Azure 구독에 여러 개의 AVS 사설 클라우드를 포함할 수 있습니다. 자세한 내용은 [AVS 사설 클라우드 개요](cloudsimple-private-cloud.md)를 참조 하세요.

**AVS 서비스는 어디에서 사용할 수 있나요?**

AVS는 미국 동부, 미국 서 부 및 추가 지역이 출시 될 유럽 서부 지역에서 제공 됩니다.

**AVS에 대해 내 구독을 사용 하도록 설정 어떻게 할까요??**

[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) 에서 Microsoft 계정 담당자에 게 연락 하 여 AVS 서비스에 대 한 구독을 사용 하도록 설정할 수 있습니다. AVS 서비스를 사용 하도록 설정할 전자 메일에 구독 ID를 제공 합니다. 

**AVS 포털에 액세스할 어떻게 할까요? 있나요?**

Azure Portal에서 AVS 포털에 액세스 합니다. 자세한 내용은 [Azure Portal에서 VMware 솔루션 (AVS) 포털 액세스](access-cloudsimple-portal.md)를 참조 하세요.

**AVS 사설 클라우드의 용량을 늘릴 어떻게 할까요? 있나요?**

용량을 늘리려면 Azure Portal에서 추가 노드를 구매한 후 노드를 사용 하 여 AVS 포털에서 AVS 사설 클라우드를 확장 합니다. 기존 vSphere 클러스터에 노드를 더 추가 하거나 새 vSphere 클러스터에 추가할 수 있습니다. 자세한 내용은 [AVS 사설 클라우드 확장](expand-private-cloud.md)을 참조 하세요.

**유지 관리 중에 내 AVS 사설 클라우드는 어떻게 되나요?**

AVS는 예약 된 유지 관리 간격 전에 며칠 후에 알림을 제공 합니다. 유지 관리는 AVS 사설 클라우드의 가용성을 보장 하는 중단 없는 방법으로 수행 됩니다. 유지 관리의 유형은 다음 중 하나일 수 있습니다.

* **AVS 인프라**. AVS 인프라는 항상 사용 가능 하도록 설계 되었습니다. 이 유형의 유지 관리 간격 동안 중복 구성 요소는 서비스 중단을 방지 하기 위해 한 번에 하나씩 업데이트 됩니다. AVS 사설 클라우드 vCenter, 모든 가상 머신, AVS 사설 클라우드의 인터넷 연결, 온-프레미스 또는 Azure에 대 한 연결에 대 한 액세스를 유지 합니다.
* **AVS 포털**. 이 유형의 유지 관리 간격 중에는 일부 기능을 사용 하지 않도록 설정 하거나 액세스할 수 없습니다. 유지 관리 간격 이전의 알림에는 유지 관리를 수행 하는 동안 기능 제한에 대 한 세부 정보가 포함 됩니다.

## <a name="connectivity"></a>연결

**AVS 지역 네트워크에 대 한 연결 옵션은 무엇 인가요?**

AVS는 AVS 지역 네트워크에 연결 하기 위한 다음과 같은 연결 옵션을 제공 합니다. 여러 옵션을 동시에 사용할 수 있습니다.

* **온-프레미스 데이터 센터에서 AVS 지역 네트워크로의 express 경로 연결**입니다. 이는 Global Reach을 사용 하 여 온-프레미스 Express 경로 회로를 AVS Express 경로 회로에 연결 하는 대기 시간이 짧고, 보안 개인 연결입니다. 연결 설정에 대 한 지침은 Express 경로를 [사용 하 여 온-프레미스에서 AVS로 연결](on-premises-connection.md)을 참조 하세요.
* **Azure 가상 네트워크에서 AVS 지역 네트워크로의 express 경로 연결**입니다. 이는 가상 네트워크 게이트웨이를 사용 하 여 Azure의 가상 네트워크를 AVS Express 경로 회로에 연결 하는 고속 대기 시간이 짧은 보안 개인 연결입니다. 연결 설정에 대 한 지침은 [express 경로를 사용 하 여 Azure 가상 네트워크에 AVS 사설 클라우드 환경 연결](azure-expressroute-connection.md)을 참조 하세요.
* **온-프레미스 데이터 센터에서 AVS 지역 네트워크로의 사이트 간 VPN 연결**. 온-프레미스 VPN 장치에서 AVS 사설 클라우드 지역으로의 보안 가상 개인 네트워크입니다. 자세한 내용은 [AVS 네트워크에서 VPN 게이트웨이 설정](vpn-gateway.md)을 참조 하세요.

**AVS 사설 클라우드에 연결할 어떻게 할까요? 있나요?**

Avs 포털에서 AVS 사설 클라우드의 세부 정보를 볼 수 있습니다. AVS 사설 클라우드에 해당 하는 vCenter에 연결 하려면 먼저 사이트 간 VPN, 지점 및 사이트 간 VPN 또는 Express 경로를 사용 하 여 네트워크 연결이 설정 되어 있는지 확인 합니다. 그런 다음 Azure Portal에서 AVS 포털을 시작 하 고 홈 페이지 또는 AVS 사설 클라우드 세부 정보 페이지에서 **vSphere 클라이언트 시작** 을 클릭 합니다.

**Express 경로 회로의 이점은 무엇 인가요?**

Azure Express 경로 회로는 고속, 짧은 대기 시간, 보안 연결입니다. AVS는 고객 당 지역 당 전용 Express 경로 회로를 제공 합니다. 이 회로를 사용 하 여 온-프레미스 또는 Azure 구독에서 보안 연결을 설정할 수 있습니다.

**AVS에 연결 하는 네트워크 비용은 무엇 인가요? 모든 송신 요금은 AVS와 Azure 간에 또는 지역 간에 적용 되나요?**

네트워크 송신에는 AVS 요금이 부과 되지 않습니다. Azure 표준 요금은 가상 네트워크 또는 온-프레미스 Express 경로 회로에서 송신 트래픽에 적용 됩니다.

## <a name="networking"></a>네트워킹

**내 AVS 사설 클라우드에 사용할 수 있는 네트워킹 기능은 무엇 인가요?**

Vlan (및 서브넷) 및 방화벽 테이블을 프로 비전 하 고, AVS 사설 클라우드에서 실행 되는 가상 머신에 매핑되는 공용 IP 주소를 할당할 수 있습니다. 네트워킹 기능에 대 한 자세한 내용은 [vlan 및 서브넷 개요](cloudsimple-vlans-subnets.md), [방화벽 테이블 개요](cloudsimple-firewall-tables.md)및 [공용 IP 주소 개요](cloudsimple-public-ip-address.md)를 참조 하세요.

**내 응용 프로그램에 대해 내 응용 프로그램에 대해 다른 서브넷을 설정 어떻게 할까요?**

Avs 포털에서 AVS 사설 클라우드에 Vlan을 만듭니다. VLAN을 만든 후에는 VLAN을 사용 하 여 AVS 사설 클라우드 vCenter에서 분산 포트 그룹을 만들고 분산 포트 그룹에 연결 된 가상 컴퓨터를 만들 수 있습니다. VLAN/서브넷에 대해 방화벽 테이블을 사용 하도록 설정 하 고 네트워크 트래픽을 보호 하는 방화벽 규칙을 정의할 수 있습니다.

**내 AVS 사설 클라우드에 사용할 수 있는 방화벽 설정은 무엇 인가요?**

북-남 및 동-서 트래픽에 대 한 규칙을 구성할 수 있습니다. 규칙은 방화벽 테이블에 정의 됩니다. 방화벽 테이블은 AVS 사설 클라우드의 Vlan에 연결할 수 있습니다. 자세한 내용은 [AVS 사설 클라우드에 대 한 방화벽 테이블 및 규칙 설정](firewall.md)을 참조 하세요.

**내 AVS 사설 클라우드 환경에서 Vm에 공용 IP 주소를 할당할 수 있나요?**

AVS 포털에서 새 공용 IP 주소를 할당 하 고 가상 머신 또는 어플라이언스의 개인 IP 주소와 연결할 수 있습니다. 새 방화벽 규칙을 만들거나 기존 방화벽 규칙을 적용 하 여 포털에서 특정 포트 및 IP 주소의 트래픽을 허용할 수도 있습니다. 자세한 내용은 [AVS 사설 클라우드 환경에 대 한 공용 IP 주소 할당](public-ips.md)을 참조 하세요.

## <a name="security"></a>보안

**AVS에서 내 보안 옵션은 무엇 인가요?**

AVS는 AVS 사설 클라우드 환경을 보호 하기 위해 다음과 같은 보안 기능을 제공 합니다.

* **미사용 데이터 암호화** AVS 사설 클라우드의 vSAN 저장소에 상주 하는 미사용 데이터를 암호화할 수 있습니다. vSAN은 Azure vNet 또는 온-프레미스 환경에 배포할 수 있는 외부 키 관리 서버를 지원 합니다. 자세한 내용은 [AVS 사설 클라우드에 대 한 vSAN 암호화 구성](vsan-encryption.md)을 참조 하세요.
* **네트워크 보안**. Avs 사설 클라우드와 인터넷, AVS 사설 클라우드 및 온-프레미스 환경 또는 AVS 사설 클라우드의 서브넷 내에서 적용 되는 방화벽 규칙을 사용 하 여 네트워크 트래픽 흐름을 제어 합니다.
* **보안, 개인 연결**. 온-프레미스 네트워크와 Azure 구독 간에 안전한 개인 연결이 설정 됩니다.

## <a name="compute"></a>컴퓨팅

**어떤 종류의 호스트를 사용할 수 있나요?**

AVS는 다음과 같은 호스트 유형을 제공 합니다.

* **CS28 노드:** CPU: 2, 2.2 g h z, 총 28 코어, 48 HT.  RAM: 256 G B.  저장소: 1600 GB NVMe 캐시, 5760 GB 데이터 (모두-플래시). 네트워크: 4x25Gbe NIC
* **CS36 노드:** CPU 2x 2.3 g h z, 총 36 코어, 72 HT.  RAM: 512 G B.  저장소: 3200 GB NVMe cache 11520 GB 데이터 (모두-플래시).  네트워크: 4x25Gbe NIC
* **CS36m 노드:** CPU 2x 2.3 g h z, 총 36 코어, 72 HT.  RAM: 576 G B.  저장소: 3200 GB NVMe cache 13360 GB 데이터 (모두-플래시).  네트워크: 4x25Gbe NIC

**하드웨어 오류는 어떻게 처리 되나요?**

모든 AVS 인프라는 AVS 플랫폼과 서비스 운영 팀에서 지속적으로 모니터링 합니다. 하드웨어 오류가 검색 되 면 새 노드가 AVS 사설 클라우드에 추가 되 고 실패 한 노드가 제거 됩니다.

## <a name="storage"></a>Storage

**AVS 사설 클라우드에서 지원 되는 저장소 유형은 무엇 인가요?**

AVS는 모든 AVS 사설 클라우드를 사용 하 여 모든 플래시 VMware vSAN 저장소를 제공 합니다. 각 vSphere 자체 Vsphere 데이터 저장소를 사용 하 여 생성 됩니다. 자세한 내용은 [AVS 사설 클라우드 VMware 구성 요소-vSAN storage](vmware-components.md#vsan-storage)를 참조 하세요.

**데이터 암호화가 지원 되나요?**
예. 온-프레미스 또는 Azure에 배포 된 KMS (키 관리 서버)를 사용 하 여 vSAN에 저장 된 데이터를 암호화 하도록 AVS 사설 클라우드에서 vSAN 저장소를 설정할 수 있습니다.

**실패 한 디스크는 어떻게 처리 되나요?**

AVS는 AVS 사설 클라우드의 모든 하드웨어 구성 요소를 지속적으로 모니터링 합니다. 디스크 오류가 감지 되었거나 디스크가 실패 한 것으로 확인 되 면 (추론 기반) 새 노드가 자동으로 AVS 사설 클라우드에 추가 됩니다. 오류가 발생 하거나 실패 한 디스크가 포함 된 노드가 AVS 사설 클라우드에서 제거 됩니다.

## <a name="vmware"></a>VMware

**온-프레미스에서 응용 프로그램 및 데이터의 대규모 업로드 또는 마이그레이션을 수행할 어떻게 할까요? 있나요?**

AVS는 네이티브 VMware vSphere 솔루션을 제공 합니다. 모든 대량 데이터 마이그레이션을 위한 VMware 도구는 모두 AVS 사설 클라우드에서 사용할 수 있습니다. 옵션은 다음과 같습니다.

* 데이터를 대량으로 마이그레이션하기 위한 VMware HCX
* 온-프레미스에서 AVS로 저장소 vMotion를 사용 하 여 데이터의 콜드 마이그레이션.

**VMware 도구를 설치할 수 있나요?**

AVS는 네이티브 VMware vSphere 솔루션을 제공 합니다. 온-프레미스 vSphere 환경을 관리 하는 데 사용 되는 모든 VMware 도구를 AVS에서 사용할 수 있습니다. AVS는 VMware 도구를 설치 하기 위한 BYOL (사용자 라이선스 가져오기) 모델을 지원 합니다.

**업데이트 및 업그레이드는 어떻게 관리 되나요?**

AVS는 원활한 중단 없는 방식으로 AVS 사설 클라우드의 모든 인프라 구성 요소를 관리 하 고 업데이트 합니다. VMware 또는 인프라 공급 업체에서 릴리스된 모든 업데이트 및 보안 패치는 AVS로 한정 되는 즉시 업데이트 되도록 예약 됩니다.

AVS는 AVS 사설 클라우드에 설치 된 응용 프로그램의 업그레이드 또는 업데이트를 수행 하지 않습니다.

## <a name="azure-integration"></a>Azure 통합

**지원 되는 Azure 서비스는 무엇 인가요?**

AVS는 azure에서 구독에 대 한 Azure Express 경로 연결을 제공 합니다. 구독에서 실행 중인 모든 서비스는 사용자의 AVS 사설 클라우드에 연결할 수 있습니다. 예를 들면 다음과 같습니다.

* 을 (를) AVS vCenter의 id 원본으로 **Azure Active Directory** 합니다.
* **Azure storage** 를 통해 AVS 사설 클라우드의 백업, 이미지 및 기타 데이터를 저장 합니다.
* 공용 및 AVS 사설 클라우드에 걸쳐 있는 응용 프로그램 아키텍처를 사용 하는 **하이브리드 응용 프로그램** 예를 들어 Azure에서 AVS 사설 클라우드의 응용 프로그램 및 데이터베이스 서버에 액세스 하는 webservers를 만들 수 있습니다.
* VMware 지원 로깅, 성능 메트릭 및 보안 관리에서 실행 되는 워크 로드에 대 한 **azure monitor** 및 **azure security center**

**VMware 테넌트를 Azure에 매핑할 어떻게 할까요? 있나요?**

AVS는 Azure Portal에서 AVS 사설 클라우드의 VMware Vm을 관리 하는 고유한 기능을 제공 합니다. 원하는 리소스 제약 조건으로 구성 된 vCenter 리소스 풀을 전역 관리자가 구독에 매핑할 수 있습니다. 

**Azure에서 제공 하는 라이선싱 혜택은 무엇 인가요?**

AVS를 사용 하면 Azure 하이브리드 사용 혜택을 활용 하 여 라이선스에 대 한 최대 90%를 절감할 수 있습니다. 이 혜택은 Microsoft 라이선스에 대 한 투자를 보존 하 고 다른 클라우드 솔루션을 기준으로 TCO를 절감 합니다. Windows Server 2008 및 Microsoft SQL Server 2008에 대 한 확장 된 보안 업데이트도 제공 됩니다. BYOL (사용자 라이선스 보유) 모델을 사용 하면 Veeam 및 Zerto와 같은 일반적인 앱에 대 한 비용을 적게 유지할 수 있습니다. 
