---
title: Azure(대규모 인스턴스)에서 SAP HANA의 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80ef63cdd9de8cb2340fe15d761402bb9f00fae9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795945"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스) 아키텍처

SAP HANA on Azure(대규모 인스턴스) 솔루션에는 높은 수준에서 VM에 상주하는 SAP 애플리케이션 계층이 있습니다. 데이터베이스 계층은 Azure IaaS에 연결된 동일한 Azure 지역의 대규모 인스턴스 스탬프에 있는 SAP TDI 구성 하드웨어에 상주합니다.

> [!NOTE]
> SAP 애플리케이션 계층은 SAP DBMS 계층과 동일한 Azure 지역에 배포합니다. 이 규칙은 Azure의 SAP 워크로드에 대해 게시된 정보에 잘 설명되어 있습니다. 

SAP HANA on Azure(대규모 인스턴스)의 전체 아키텍처는 SAP TDI 인증 하드웨어(가상화되지 않은 SAP HANA 데이터베이스용 운영 체제 미설치 고성능 서버) 구성을 제공합니다. 또한 사용자 요구 사항에 맞게 SAP 애플리케이션 계층에 대한 리소스 크기를 조정할 수 있는 Azure 기능 및 유연성도 제공합니다.

![Azure(큰 인스턴스)에서 SAP HANA의 아키텍처 개요](./media/hana-overview-architecture/image1-architecture.png)

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.

- **오른쪽**: 최종 사용자가 액세스할 수 있도록 데이터의 서로 다른 응용 프로그램을 실행 하는 온-프레미스 인프라 센터에서는 LOB SAP와 같은 응용 프로그램입니다. 이상적으로 이 온-프레미스 인프라는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 Azure에 연결됩니다.

- **Center**: Azure IaaS를 보여 줍니다.,이 경우를 사용 하 여 Vm의 SAP 또는 DBMS 시스템으로 SAP HANA를 사용 하는 다른 응용 프로그램을 호스트 합니다. VM에서 제공하는 메모리에서 작동하는 더 작은 HANA 인스턴스는 애플리케이션 계층과 함께 VM에 배포됩니다. 가상 머신에 대한 자세한 내용은 [가상 머신](https://azure.microsoft.com/services/virtual-machines/)을 참조하세요.

   Azure 네트워크 서비스는 다른 애플리케이션과 함께 SAP 시스템을 가상 네트워크로 그룹화하는 데 사용됩니다. 이러한 가상 네트워크는 온-프레미스 시스템 및 SAP HANA on Azure(대규모 인스턴스)에 연결됩니다.

   SAP NetWeaver 응용 프로그램 및 Azure에서 실행 되도록 지원 되는 데이터베이스에 대 한 참조 [SAP Support Note #1928533 – Azure의 SAP 응용 프로그램: 지원 되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)합니다. Azure에 SAP 솔루션을 배포하는 방법에 대한 설명서는 다음을 참조하세요.

  -  [Windows 가상 머신에서 SAP 사용](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Azure 가상 머신에서 SAP 솔루션 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **왼쪽**: Azure 큰 인스턴스 스탬프에서 SAP HANA TDI 인증 하드웨어를 표시합니다. HANA 대규모 인스턴스 장치는 온-프레미스에서 Azure로 연결할 때와 동일한 기술을 사용하여 구독의 가상 네트워크에 연결됩니다.

Azure 큰 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅**: 필요한 컴퓨팅 기능을 제공 하는 SAP HANA 인증 된 Intel Xeon E7 8890v3 또는 Intel Xeon E7-8890v4 프로세서를 기반으로 하는 서버입니다.
- **네트워크**: 컴퓨팅, 저장소 및 LAN 구성 요소를 상호 연결 하는 통합 된 고속 네트워크 패브릭입니다.
- **저장소**: 통합된 된 네트워크 패브릭을 통해 액세스 되는 저장소 인프라입니다. 제공되는 특정 저장소 용량은 배포되는 특정 SAP HANA on Azure(대규모 인스턴스) 구성에 따라 다릅니다. 추가 저장소 용량은 추가 월별 비용으로 사용할 수 있습니다.

큰 인스턴스 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트로 배포됩니다. 테넌트 배포 시 Azure 등록 내에서 Azure 구독 이름을 지정합니다. Azure 구독은 HANA 대규모 인스턴스가 청구되는 구독입니다. 이러한 테넌트는 Azure 구독과 1:1 관계를 포함합니다. 네트워크의 경우 서로 다른 Azure 구독에 속한 다른 가상 네트워크에서 한 Azure 지역의 한 테넌트에 배포된 HANA 대규모 인스턴스 장치에 액세스할 수 있습니다. 이러한 Azure 구독은 동일한 Azure 등록에 속해야 합니다. 

VM과 마찬가지로, SAP HANA on Azure(대규모 인스턴스)가 여러 Azure 지역에 제공됩니다. 재해 복구 기능을 제공하려면 옵트인하도록 선택할 수 있습니다. 하나의 지정학적 지역의 서로 다른 큰 인스턴스 스탬프가 서로 연결됩니다. 예를 들어 미국 서부 및 미국 동부에 있는 HANA 대규모 인스턴스 스탬프가 재해 복구 복제를 위한 전용 네트워크 링크를 통해 연결됩니다. 

Azure Virtual Machines를 사용하여 다양한 VM 유형 중에서 선택할 수 있는 것처럼 SAP HANA의 다양한 워크로드 유형에 맞게 조정된 HANA 대규모 인스턴스의 다양한 SKU 중에서 선택할 수 있습니다. SAP는 Intel 프로세서 세대에 따라 다양한 워크로드에 대한 메모리 대 프로세서 소켓 비율을 적용합니다. 다음 표에서는 제공된 SKU 형식을 보여줍니다.

[HLI에 사용 가능한 SKU](hana-available-skus.md)에서 사용 가능한 SKU를 찾을 수 있습니다.

**다음 단계**
- [SAP HANA(대규모 인스턴스) 네트워크 아키텍처](hana-network-architecture.md) 참조