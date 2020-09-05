---
title: '자습서: Azure FXT Edge Filer에 케이블 연결'
description: 네트워크 포트를 케이블로 연결하고 Azure FXT Edge Filer 하드웨어의 전원을 연결하는 방법
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84692974"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>자습서: Azure FXT Edge Filer 노드에 네트워크 및 전원 공급 장치를 연결하는 방법

이 자습서에서는 케이블을 사용하여 Azure FXT Edge Filer 하드웨어 노드를 네트워크에 연결하는 방법을 설명합니다.

이 자습서에서는 다음에 대해 알아봅니다. 

> [!div class="checklist"]
> * 환경에 맞는 네트워크 케이블 종류를 선택하는 방법
> * Azure FXT Edge Filer 노드를 데이터 센터 네트워크에 연결하는 방법
> * CMA(케이블 관리 암)를 통해 케이블을 정리하는 방법
> * 랙 탑재 디바이스에 전원을 연결하고 켜는 방법

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에, Azure FXT Edge Filer가 표준 장비 랙에 설치되어야 합니다. Filer 노드에 CMA가 설치되어 있어야 합니다. 

## <a name="identify-ports"></a>포트 확인

Azure FXT Edge Filer 뒷면의 다양한 포트를 확인합니다. 
 
![케이블로 연결된 디바이스의 뒷면](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>디바이스에 케이블 연결

* [네트워크 포트](#network-ports)에 설명된 대로 RJ-45 포트를 데이터 센터의 네트워크 원본에 연결합니다.  
* 보안 DHCP 서버를 사용하여 [iDRAC 포트](#idrac-port)를 별도의 네트워크에 안전하게 연결합니다. 
* USB 포트와 VGA 포트를 사용하여 키보드와 모니터를 초기 설치 노드에 연결합니다. 노드를 부팅하고 [초기 암호를 설정](fxt-node-password.md)하여 노드의 다른 포트를 활성화해야 합니다. 자세한 내용은 [초기 암호 설정](fxt-node-password.md)을 참조하세요. 

이 문서에서는 노드에 [AC 전원을 연결](#connect-power-cables)하는 방법도 설명합니다. 

특수한 문제 해결에 필요한 경우 노드의 [직렬 포트](#serial-port-only-when-necessary)에 연결하는 방법도 설명합니다. 

### <a name="network-ports"></a>네트워크 포트 

각 Azure FXT Edge Filer 노드에는 다음과 같은 네트워크 포트가 있습니다. 

* 고속 25GbE/10GbE 이중 속도 데이터 포트 6개: 

  * 두 이중 포트 플러그 인 네트워크 어댑터가 제공하는 포트 4개
  * 마더보드 mezzanine 네트워크 어댑터가 제공하는 포트 2개 

* 마더보드 mezzanine 네트워크 어댑터가 제공하는 1GbE 포트 2개 

고속 25GbE/10GbE 데이터 포트에는 표준 SFP28 호환 케이지가 있습니다. 광학 케이블을 사용하려면 SFP28 광학 트랜시버 모듈(제공되지 않음)을 설치해야 합니다.

1GbE 포트에는 표준 RJ-45 커넥터가 있습니다.

지원되는 케이블, 스위치 및 트랜시버의 전체 목록은 [Cavium FastlinQ 41000 시리즈 상호 운용성 행렬](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)을 참조하세요.

시스템에 사용하는 연결 유형은 데이터 센터 환경에 따라 달라집니다.

* 25GbE 네트워크에 연결하는 경우 각 고속 데이터 포트를 다음 케이블 형식 중 하나로 연결합니다.

  * 25GbE 또는 이중 속도 25GbE/10GbE 기능을 제공하는 광학 케이블과 SFP28 광학 트랜시버
  * SFP28 형식 25GbE 지원 직접 연결 twinaxial 케이블 연결

* 10GbE 네트워크에 연결하는 경우 각 고속 데이터 포트를 다음 중 하나로 연결합니다. 

  * 10GbE 또는 이중 속도 25GbE/10GbE 기능을 제공하는 광학 케이블과 SFP28 광학 트랜시버
  * SFP28 형식 25GbE 지원 직접 연결 twinaxial 케이블 연결
  * SFP28 형식 10GbE 지원 직접 연결 twinaxial 케이블

* 1GbE 네트워크 포트는 클러스터 관리 트래픽에 사용됩니다. 클러스터 구성에 사용할 물리적으로 격리된 네트워크를 만들려면 클러스터를 만들 때 **1Gb 관리 네트워크 사용** 옵션을 확인하세요([관리 네트워크 구성](fxt-cluster-create.md#configure-the-management-network)에 설명되어 있음). 지원되는 케이블 목록의 설명에 따라 케이블로 포트를 표준 Cat5에 연결하거나 더 좋은 케이블로 연결합니다.

  모든 트래픽에 고속 포트를 사용하려는 경우 1GbE 포트를 연결하지 않아도 됩니다. 기본적으로 더 빠른 데이터 포트가 있으면 1GbE 네트워크 포트가 사용되지 않습니다.  

### <a name="idrac-port"></a>iDRAC 포트  

iDRAC이라고 표시된 포트는 하드웨어 관리와 모니터링에 사용되는 원격 액세스 컨트롤러와 통신할 수 있는 1Gb 연결입니다. FXT 소프트웨어는 이 컨트롤러에 IPMI(Platform Management Interface)를 사용하여 문제를 해결하고 복구합니다. 기본 제공 [iDRAC 인터페이스](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/)를 사용하여 이 포트를 통해 하드웨어를 모니터링할 수 있습니다. iDRAC 및 IPMI 액세스는 기본적으로 활성화됩니다. 

> [!Note]
> iDRAC 포트는 운영 체제를 무시하고 노드의 하드웨어와 직접 상호 작용할 수 있습니다. 

iDRAC 포트에 연결하고 구성할 때 다음과 같은 보안 전략을 사용합니다.

* 클러스터 액세스에 사용되는 데이터 네트워크와 물리적으로 분리된 네트워크에만 iDRAC 포트를 연결합니다.
* 각 노드에서 안전한 iDRAC 관리자 암호를 설정합니다. 하드웨어를 활성화하려면 이 암호를 설정해야 합니다. [하드웨어 암호 설정](fxt-node-password.md)의 지침을 따르세요.
* 기본 iDRAC 포트 구성은 IP 주소 할당에 DHCP 및 IPv4를 사용합니다. DHCP 환경이 잘 보호되고 DHCP 클라이언트와 DHCP 서버 간에 연결이 제한되는지 확인합니다. (클러스터 제어판에는 클러스터를 만든 후 노드의 주소 구성 방법을 변경하기 위한 설정이 있습니다.)
* iDRAC 포트를 "전용 모드"(기본값)로 설정된 채로 둡니다. 이 설정은 iDRAC/IPMI 네트워크 트래픽을 전용 RJ-45 포트로 제한합니다.

iDRAC 포트는 고속 네트워크 연결이 필요 없습니다.
  
### <a name="serial-port-only-when-necessary"></a>직렬 포트(필요한 경우만)

상황에 따라 Microsoft 서비스 및 지원 팀에서 문제를 진단하기 위해 노드의 직렬 포트에 터미널을 연결하라고 요청할 수 있습니다.  

콘솔을 연결하려면 다음을 수행합니다.

1. FXT Edge Filer 노드의 뒷면에서 직렬(COM1) 포트를 찾습니다.
1. Null 모뎀 케이블을 사용하여 직렬 포트를 ANSI-115200-8N1에 대해 구성된 터미널에 연결합니다.
1. 콘솔에 로그인하고, 지원 담당자의 지시에 따라 다른 단계를 수행합니다.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>CMA(케이블 관리 암)에서 케이블 정리

각 Azure FXT Edge Filer 노드는 케이블 관리 암이 옵션으로 제공됩니다. CMA를 사용하면 매우 간편하게 케이블을 정리할 수 있으며 케이블을 분리하지 않고도 섀시의 뒷면에 쉽게 액세스할 수 있습니다. 

다음 지침에 따라 CMA를 통해 케이블을 정리합니다. 

1. 주변 시스템을 방해하지 않도록, 바스켓으로 들어오고 나오는 케이블을 제공된 케이블 타이로 묶습니다(1).
1. CMA를 작동 위치에 둔 상태에서, 케이블을 내부 및 외부 바스켓에 감습니다(2).
1. 바스켓의 양쪽 끝에 미리 설치되어 있는 벨크로 스트랩을 사용하여 케이블을 고정합니다(3).
1. CMA를 돌려서 트레이의 제자리에 놓습니다(4).
1. 시스템 뒷면에 상태 표시기 케이블을 설치하고 케이블로 CMA를 감아 단단히 고정합니다. 케이블의 반대쪽 끝을 외부 CMA 바스켓 모서리에 연결합니다(5). 

   > [!CAUTION]
   > 돌출된 케이블 때문에 시스템이 손상되는 일이 없도록, 케이블을 CMA에 감은 후 상태 표시기 케이블의 느슨한 부분을 고정합니다. 

![케이블이 설치된 CMA 일러스트레이션](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  CMA를 설치하지 않은 경우 레일 키트에 제공된 두 개의 벨크로 스트랩을 사용하여 시스템 뒷면에서 케이블을 연결합니다.
> 
>  1. 양쪽 랙 플랜지의 내부 측면에 외부 CMA 브라켓을 찾습니다.
>  2. 케이블을 시스템 커넥터에서 왼쪽 및 오른쪽으로 당겨 빼내서 조심스럽게 모읍니다.
>  3. 벨크로 스트랩을 시스템의 각 측면에 있는 외부 CMA 브라켓의 툴링 슬롯에 끼워서 케이블 묶음을 고정시킵니다.
> 
>     ![CMA 없이 케이블 정리](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>IP 주소 요구 사항 정보

Azure FXT Edge Filer 하이브리드 스토리지 캐시의 하드웨어 노드에서는 IP 주소가 클러스터 소프트웨어를 통해 관리됩니다.

노드마다 하나 이상의 IP 주소가 필요하지만, 노드 주소는 클러스터에 노드가 추가되거나 제거될 때 할당됩니다. 

필요한 총 IP 주소 수는 캐시를 구성하는 노드의 수에 따라 달라집니다. 

노드를 설치한 후 제어판 소프트웨어를 사용하여 IP 주소 범위를 구성합니다. 자세한 내용은 [클러스터 정보 수집](fxt-cluster-create.md#gather-information-for-the-cluster)을 참조하세요.  

## <a name="connect-power-cables"></a>전원 케이블 연결

각 Azure FXT Edge Filer 노드는 두 개의 PSU(전원 공급 장치)를 사용합니다. 

> [!TIP] 
> 두 개의 중복 PSU를 활용하려면 각 AC 전원 케이블을 독립 분기 회로의 PDU(전원 분배 장치)에 연결합니다.  
> 
> PDU에 전원을 공급할 때 UPS를 사용하면 안전을 강화할 수 있습니다. 

1. 제공된 전원 코드를 섀시의 PSU에 연결합니다. 코드와 PSU가 완전히 연결되었는지 확인합니다. 
1. 전원 코드를 장비 랙의 전원 분배 장치에 연결합니다. 가능하다면 두 코드에 각각 별도의 전원을 사용합니다. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Azure FXT Edge Filer 노드 전원 켜기

노드 전원을 켜려면 앞면 시스템 전면의 전원 단추를 누릅니다. 단추는 오른쪽 제어판에 있습니다. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Azure FXT Edge Filer 노드 전원 끄기

시스템을 테스트하는 동안 그리고 클러스터에 시스템을 추가하기 전에 전원 단추를 사용하여 시스템을 종료할 수 있습니다. 그러나 Azure FXT Edge Filer 노드가 클러스터의 일부로 사용 중이면 클러스터 제어판 소프트웨어를 사용하여 하드웨어를 종료해야 합니다. 자세한 내용은 [안전하게 Azure FXT Edge Filer 하드웨어 전원을 끄는 방법](fxt-power-off.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

하드웨어 연결을 마친 후에는 각 노드를 켜고 루트 암호를 설정하여 초기화합니다. 
> [!div class="nextstepaction"]
> [초기 암호 설정](fxt-node-password.md)
