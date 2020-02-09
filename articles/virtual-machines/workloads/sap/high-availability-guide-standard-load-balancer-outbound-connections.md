---
title: SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 엔드포인트 연결
description: SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 엔드포인트 연결
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 52179f15829981d59ff060784a49eccef89bb186
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083732"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 엔드포인트 연결

이 문서의 범위는 공용 엔드포인트에 대 한 아웃 바운드 연결을 설정 하는 구성을 설명 하는 것입니다. 구성은 주로 SUSE/RHEL에 대 한 Pacemaker를 사용 하는 고가용성의 컨텍스트입니다.  

고가용성 솔루션에서 Azure 펜스 에이전트와 함께 Pacemaker를 사용 하는 경우 Vm은 Azure 관리 API에 대 한 아웃 바운드 연결을 사용 해야 합니다.  
이 문서에서는 시나리오에 가장 적합 한 옵션을 선택할 수 있는 몇 가지 옵션을 제공 합니다.  

## <a name="overview"></a>개요

클러스터링을 통해 SAP 솔루션에 대 한 고가용성을 구현 하는 경우 필요한 구성 요소 중 하나를 [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)합니다. Azure는 두 가지 부하 분산 장치 Sku (standard 및 basic)를 제공 합니다.

표준 Azure 부하 분산 장치는 기본 부하 분산 장치에 비해 몇 가지 이점을 제공 합니다. 예를 들어 Azure 가용성 영역에서 작동 하는 경우 보다 쉽게 문제를 해결 하 고 대기 시간을 줄일 수 있도록 더 나은 모니터링 및 로깅 기능을 제공 합니다. "HA 포트" 기능은 모든 포트를 포함 합니다. 즉, 더 이상 모든 개별 포트를 나열할 필요가 없습니다.  

Azure 부하 분산 장치의 기본 SKU와 표준 SKU 간에는 몇 가지 중요 한 차이점이 있습니다. 그 중 하나는 공용 엔드포인트에 대 한 아웃 바운드 트래픽을 처리 하는 것입니다. 전체 기본 및 표준 SKU 부하 분산 장치 비교는 [LOAD BALANCER SKU 비교](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)를 참조 하세요.  
 
공용 IP 주소가 없는 Vm이 내부 (공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치 되는 경우, 추가 구성을 수행 하지 않으면 공용 엔드포인트에 대 한 아웃 바운드 연결이 없습니다.  

VM에 공용 IP 주소가 할당 되었거나 VM이 공용 IP 주소를 사용 하는 부하 분산 장치의 백 엔드 풀에 있는 경우 공용 엔드포인트에 대 한 아웃 바운드 연결을 사용 합니다.  

SAP 시스템은 종종 중요 한 비즈니스 데이터를 포함 합니다. SAP 시스템을 호스트 하는 Vm에는 공용 IP 주소를 포함 하는 것이 거의 허용 되지 않습니다. 동시에 VM에서 공용 엔드포인트으로의 아웃 바운드 연결을 요구 하는 시나리오가 있습니다.  

Azure 공용 엔드포인트에 대 한 액세스를 요구 하는 시나리오의 예는 다음과 같습니다.  
- Pacemaker 클러스터에서 Azure Fence 에이전트를 fence 메커니즘으로 사용
- Azure Backup
- Azure Site Recovery  
- 공용 리포지토리를 사용 하 여 운영 체제 패치
- SAP 응용 프로그램 데이터 흐름에는 공용 엔드포인트에 대 한 아웃 바운드 연결이 필요할 수 있습니다.

SAP 배포에 공용 엔드포인트에 대 한 아웃 바운드 연결이 필요 하지 않은 경우에는 추가 구성을 구현할 필요가 없습니다. 공용 엔드포인트에서 인바운드 연결이 필요 하지 않은 경우 고가용성 시나리오에 대 한 내부 표준 SKU Azure Load Balancer를 만들 수 있습니다.  

> [!Note]
> 공용 IP 주소가 없는 Vm이 내부 (공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치 되는 경우 공용 엔드포인트으로의 라우팅을 허용 하기 위해 추가 구성을 수행 하지 않는 한 아웃 바운드 인터넷 연결이 없습니다.  
>Vm이 공용 ip 주소를 사용 하거나 공용 IP 주소를 사용 하는 Azure 부하 분산 장치의 백 엔드 풀에 이미 있는 경우 VM은 이미 공용 엔드포인트에 대 한 아웃 바운드 연결을 가집니다.


다음 백서를 먼저 읽어 보세요.

* Azure 표준 Load Balancer
  * [Azure 표준 Load Balancer 개요](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) -Azure 표준 부하 분산 장치, 중요 한 원칙, 개념 및 자습서의 포괄적인 개요 
  * [Azure의 아웃 바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) -azure에서 아웃 바운드 연결을 구현 하는 방법에 대 한 시나리오
  * [부하 분산 장치 아웃 바운드 규칙](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)-부하 분산 장치 아웃 바운드 규칙의 개념 및 아웃 바운드 규칙을 만드는 방법을 설명 합니다.
* Azure Firewall
  * [Azure 방화벽 개요](https://docs.microsoft.com/azure/firewall/overview)-azure 방화벽 개요
  * [자습서: Azure 방화벽 배포 및 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) -Azure Portal를 통해 azure 방화벽을 구성 하는 방법에 대 한 지침
* [가상 네트워크-사용자 정의 규칙](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) -Azure 라우팅 개념 및 규칙  
* [보안 그룹 서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) -서비스 태그를 사용 하 여 네트워크 보안 그룹 및 방화벽 구성을 간소화 하는 방법

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>인터넷에 대 한 아웃 바운드 연결에 대 한 추가 외부 Azure 표준 Load Balancer

공용 엔드포인트에서 VM에 대 한 인바운드 연결을 허용 하지 않고 공용 엔드포인트에 대 한 아웃 바운드 연결을 구현 하는 한 가지 옵션은 공용 IP 주소를 사용 하 여 두 번째 부하 분산 장치를 만들고, 두 번째 부하 분산 장치의 백 엔드 풀에 Vm을 추가 하 고, [아웃 바운드 규칙만](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)정의 하는 것입니다.  
[네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview) 을 사용 하 여 VM의 아웃 바운드 호출에 액세스할 수 있는 공용 엔드포인트을 제어 합니다.  
자세한 내용은 문서 [아웃 바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)의 시나리오 2를 참조 하세요.  
구성은 다음과 같습니다.  

![네트워크 보안 그룹을 사용 하 여 공용 엔드포인트에 대 한 연결 제어](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>중요 고려 사항

- 공용 엔드포인트에 대 한 아웃 바운드 연결을 구현 하 고 비용을 최적화 하기 위해 동일한 서브넷의 여러 Vm에 대해 하나의 추가 공용 Load Balancer를 사용할 수 있습니다.  
- [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview) 을 사용 하 여 vm에서 액세스할 수 있는 공용 엔드포인트을 제어 합니다. 네트워크 보안 그룹을 서브넷 또는 각 VM에 할당할 수 있습니다. 가능 하면 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 를 사용 하 여 보안 규칙의 복잡성을 줄입니다.  
- 공용 IP 주소 및 아웃 바운드 규칙을 포함 하는 Azure 표준 부하 분산 장치는 공용 엔드포인트에 직접 액세스할 수 있습니다. 감사 및 로깅을 위해 중앙 집중식 회사 솔루션을 통해 모든 아웃 바운드 트래픽이 전달 되도록 회사 보안 요구 사항이 있는 경우이 시나리오에서 요구 사항을 충족 하지 못할 수 있습니다.  

>[!TIP]
>가능 하면 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 를 사용 하 여 네트워크 보안 그룹의 복잡성을 줄입니다. 

### <a name="deployment-steps"></a>배포 단계

1. 부하 분산 장치 만들기  
   1. [Azure Portal](https://portal.azure.com) 에서 모든 리소스를 클릭 하 고 추가를 클릭 한 다음 **Load Balancer** 를 검색 합니다.  
   1. **만들기** 
   1. Load Balancer 이름 **MyPublicILB**  
   1. **공용** 을 유형으로 선택 하 고 **Standard** 를 SKU로 선택 합니다.  
   1. **공용 IP 주소 만들기** 를 선택 하 고 이름을 **MyPublicILBFrondEndIP** 로 지정 합니다.  
   1. 가용성 영역으로 **영역 중복** 을 선택 합니다.  
   1. 검토 및 만들기를 클릭 한 다음 만들기를 클릭 합니다.  
2. 백 엔드 풀 **MyBackendPoolOfPublicILB** 을 만들고 vm을 추가 합니다.  
   1. 가상 네트워크 선택  
   1. Vm 및 해당 IP 주소를 선택 하 여 백 엔드 풀에 추가 합니다.  
3. [아웃 바운드 규칙을 만듭니다](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). 현재 Azure Portal에서 아웃 바운드 규칙을 만들 수 없습니다. [Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)를 사용 하 여 아웃 바운드 규칙을 만들 수 있습니다.  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 특정 공용 엔드포인트에 대 한 액세스를 제한 하는 네트워크 보안 그룹 규칙을 만듭니다. 기존 네트워크 보안 그룹이 있는 경우 조정할 수 있습니다. 아래 예제에서는 Azure 관리 API에 대 한 액세스를 사용 하도록 설정 하는 방법을 보여 줍니다. 
   1. 네트워크 보안 그룹으로 이동 합니다.
   1. 아웃 바운드 보안 규칙을 클릭 합니다.
   1. **인터넷**에 대 한 모든 아웃 바운드 액세스를 **거부** 하는 규칙을 추가 합니다.
   1. 모든 인터넷 액세스를 거부 하는 규칙의 우선 순위 보다 우선 순위가 낮은 **Azurecloud**에 대 한 액세스를 **허용** 하는 규칙을 추가 합니다.


   아웃 바운드 보안 규칙은 다음과 같습니다. 

   ![공용 IP를 사용 하는 두 번째 Load Balancer의 아웃 바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Azure 네트워크 보안 그룹에 대 한 자세한 내용은 [보안 그룹 ](https://docs.microsoft.com/azure/virtual-network/security-overview)을 참조 하세요. 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>인터넷에 대 한 아웃 바운드 연결을 위한 Azure 방화벽

공용 엔드포인트에서 VM에 대 한 인바운드 연결을 허용 하지 않고 공용 엔드포인트에 대 한 아웃 바운드 연결을 구현 하는 또 다른 옵션은 Azure 방화벽을 사용 하는 것입니다. Azure 방화벽은 기본 제공 되는 고가용성을 포함 하는 관리 되는 서비스 이며 여러 가용성 영역 확장할 수 있습니다.  
또한 azure 방화벽을 통해 트래픽을 라우팅하는 azure 방화벽을 가리키는 Vm 및 Azure 부하 분산 장치를 배포 하는 서브넷과 연결 된 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)를 배포 해야 합니다.  
Azure 방화벽을 배포 하는 방법에 대 한 자세한 내용은 [Azure 방화벽 배포 및 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)을 참조 하세요.  

아키텍처는 다음과 같습니다.

![Azure 방화벽과의 아웃 바운드 연결](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>중요 고려 사항

- Azure 방화벽은 기본 제공 되는 고가용성을 갖춘 클라우드 네이티브 서비스 이며 영역 배포를 지원 합니다.
- AzureFirewallSubnet 이라는 추가 서브넷이 필요 합니다. 
- SAP Vm이 있는 가상 네트워크 또는 다른 가상 네트워크의 VM에 대 한 아웃 바운드 데이터 집합을 전송 하는 경우에는 비용 효율적인 솔루션이 아닐 수 있습니다. 이러한 예 중 하나는 가상 네트워크에서 대량 백업을 복사 하는 것입니다. 자세한 내용은 Azure 방화벽 가격 책정을 참조 하세요.  
- 회사 방화벽 솔루션이 Azure 방화벽이 아니고 중앙 집중화 된 회사 솔루션을 통해 모든 아웃 바운드 트래픽이 전달 되도록 하는 보안 요구 사항이 있는 경우이 솔루션은 실용적이 지 않을 수 있습니다.  

>[!TIP]
>가능 하면 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 를 사용 하 여 Azure 방화벽 규칙의 복잡성을 줄입니다.  

### <a name="deployment-steps"></a>배포 단계

1. 배포 단계에서는 Vm에 대 한 가상 네트워크 및 서브넷이 이미 정의 되어 있다고 가정 합니다.  
2. VM 및 표준 Load Balancer 배포 되는 동일한 Virtual Network에서 서브넷 **AzureFirewallSubnet** 를 만듭니다.  
   1. Azure Portal에서 모든 리소스를 클릭 하 고, Virtual Network를 검색 하 고, Virtual Network를 클릭 하 고, 서브넷을 선택 Virtual Network 이동 합니다.  
   1. 서브넷 추가를 클릭 합니다. **AzureFirewallSubnet** 를 이름으로 입력 합니다. 적절 한 주소 범위를 입력 합니다. 저장합니다.  
3. Azure 방화벽을 만듭니다.  
   1. Azure Portal 모든 리소스를 선택 하 고 추가, 방화벽, 만들기를 차례로 클릭 합니다. 리소스 그룹을 선택 합니다 (Virtual Network 인 동일한 리소스 그룹을 선택).  
   1. Azure 방화벽 리소스의 이름을 입력 합니다. 예를 들어 **Myazurefirewall**이 있습니다.  
   1. 지역을 선택 하 고 Vm이 배포 되는 가용성 영역에 따라 2 개 이상의 가용성 영역을 선택 합니다.  
   1. SAP Vm 및 Azure 표준 부하 분산 장치를 배포 하는 Virtual Network를 선택 합니다.  
   1. 공용 IP 주소: 만들기를 클릭 하 고 이름을 입력 합니다. 예를 들면 **MyFirewallPublicIP**입니다.  
4. 지정 된 공용 엔드포인트에 대 한 아웃 바운드 연결을 허용 하는 Azure 방화벽 규칙을 만듭니다. 이 예제에서는 Azure 관리 API 공용 엔드포인트에 대 한 액세스를 허용 하는 방법을 보여 줍니다.  
   1. 규칙, 네트워크 규칙 컬렉션을 차례로 선택한 다음 네트워크 규칙 컬렉션 추가를 클릭 합니다.  
   1. 이름: **MyOutboundRule**, 우선 순위를 입력 하 고 작업 **허용**을 선택 합니다.  
   1. 서비스: 이름 **Toazureapi**.  프로토콜: **Any**를 선택 합니다. 원본 주소: Vm 및 표준 Load Balancer 배포 되는 서브넷의 범위 (예: **11.97.0.0/24**)를 입력 합니다. 대상 포트: <b>*</b>을 입력 합니다.  
   1. 저장
   1. Azure 방화벽에 배치 된 상태에서 개요를 선택 합니다. Azure 방화벽의 개인 IP 주소를 적어둡니다.  
5. Azure 방화벽에 대 한 경로 만들기  
   1. Azure Portal 모든 리소스를 선택 하 고 추가, 경로 테이블, 만들기를 차례로 클릭 합니다.  
   1. 이름 MyRouteTable을 입력 하 고 구독, 리소스 그룹 및 위치를 선택 합니다 (가상 네트워크 및 방화벽의 위치와 일치).  
   1. 저장  

   방화벽 규칙은 Azure 방화벽과의 ![아웃 바운드 연결과 같습니다](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Vm의 서브넷에서 **Myazurefirewall**의 개인 IP에 대 한 사용자 정의 경로를 만듭니다.
   1. 경로 테이블에 배치 되 면 경로를 클릭 합니다. 추가를 선택합니다. 
   1. 경로 이름: ToMyAzureFirewall, 주소 접두사: **0.0.0.0/0**. 다음 홉 유형: 가상 어플라이언스를 선택 합니다. 다음 홉 주소: 구성 된 방화벽의 개인 IP 주소를 입력 합니다. **11.97.1.4**.  
   1. 저장

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Azure 관리 API에 대 한 Pacemaker 호출에 Proxy 사용

프록시를 사용 하 여 Azure 관리 API 공용 엔드포인트에 대 한 Pacemaker 호출을 허용할 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

  - 이미 회사 프록시가 있는 경우 아웃 바운드 호출을 통해 공용 엔드포인트으로 라우팅할 수 있습니다. 공용 엔드포인트에 대 한 아웃 바운드 호출은 회사 제어 지점을 통해 전달 됩니다.  
  - 프록시 구성에서 Azure 관리 API에 대 한 아웃 바운드 연결을 허용 하는지 확인 합니다. https://management.azure.com  
  - Vm에서 프록시로의 경로가 있는지 확인 합니다.  
  - 프록시는 HTTP/HTTPS 호출만 처리 합니다. 다른 프로토콜 (예: RFC)을 통해 공용 엔드포인트에 대 한 아웃 바운드 호출을 추가로 수행 해야 하는 경우 대체 솔루션이 필요 합니다.  
  - Pacemaker 클러스터의 불안정성을 방지 하려면 프록시 솔루션을 항상 사용 가능 해야 합니다.  
  - 프록시 위치에 따라 azure 펜스 에이전트에서 Azure 관리 API로의 호출에 추가 대기 시간이 발생할 수 있습니다. 회사 프록시가 아직 온-프레미스에 있는 동안 Pacemaker 클러스터가 Azure에 있으면 대기 시간을 측정 하 고이 솔루션이 적합 한 경우를 고려 합니다.  
  - 아직 사용 가능한 회사 프록시가 아직 없는 경우 고객이 추가 비용 및 복잡성을 발생 시킬 수 있으므로이 옵션을 사용 하지 않는 것이 좋습니다. 그럼에도 불구 하 고 추가 프록시 솔루션을 배포 하기로 결정 한 경우 Pacemaker에서 Azure Management public API로 아웃 바운드 연결을 허용 하려면 프록시가 항상 사용 가능 하 고 Vm에서 프록시로의 대기 시간이 낮은 지 확인 합니다.  

### <a name="pacemaker-configuration-with-proxy"></a>프록시를 사용 하는 Pacemaker 구성 

업계에서 사용할 수 있는 다양 한 프록시 옵션이 있습니다. 프록시 배포에 대 한 단계별 지침은이 문서의 범위를 벗어납니다. 아래 예제에서는 프록시가 **Myproxyservice** 에 응답 하 고 포트 **MyProxyPort**을 수신 하는 것으로 가정 합니다.  
Pacemaker가 Azure 관리 API와 통신할 수 있도록 하려면 모든 클러스터 노드에서 다음 단계를 수행 합니다.  

1. Pacemaker 구성 파일/etc/sysconfig/pacemaker를 편집 하 고 다음 줄 (모든 클러스터 노드)을 추가 합니다.  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. **모든** 클러스터 노드에서 pacemaker 서비스를 다시 시작 합니다.  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>다음 단계

* [Azure에서 SUSE의 Pacemaker를 구성 하는 방법 알아보기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Azure에서 Red Hat에 Pacemaker를 구성 하는 방법 알아보기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
