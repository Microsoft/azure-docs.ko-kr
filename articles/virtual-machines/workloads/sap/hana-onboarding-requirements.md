---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 온보딩 요구 사항 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA에 대한 온보딩 요구 사항입니다.
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
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 252c84bce2b70f6931593fe9410abe6cc146b5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679054"
---
# <a name="onboarding-requirements"></a>온보딩 요구 사항

이 목록은 Azure(더 큰 인스턴스)에서 SAP HANA를 실행하기 위한 요구 사항을 모읍니다.

**Microsoft Azure**

- Azure(큰 인스턴스)에서 SAP HANA로 연결할 수 있는 Azure 구독입니다.
- Microsoft 프리미어 지원 계약입니다. Azure에서 SAP를 실행하는 것과 관련된 특정 정보는 [SAP Support Note #2015553 – SAP on Microsoft Azure: 필수 구성 요소 지원](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요. CPU가 384개 이상인 HANA 대규모 인스턴스 장치를 사용하는 경우 Azure Rapid Response를 포함하도록 프리미어 지원 계약을 확장해야 합니다.
- SAP를 통해 크기 조정 연습을 수행한 후 필요한 HANA 대규모 인스턴스 SKU를 파악해야 합니다.

**네트워크 연결**

- Azure에 대한 온-프레미스 간 ExpressRoute: 온-프레미스 데이터 센터를 Azure에 연결하려면 ISP에서 최소한 1Gbps 연결을 주문해야 합니다. HANA 대규모 인스턴스 단위와 Azure 간 연결에도 ExpressRoute 기술을 사용합니다. HANA 대규모 인스턴스 장치와 Azure 간의 ExpressRoute 연결은 이 특정 ExpressRoute 회로의 모든 데이터 수신 및 송신 요금을 포함하여 HANA 대규모 인스턴스 장치의 가격에 포함됩니다. 따라서 고객으로 발생 하지 비용이 온-프레미스와 Azure 간에 ExpressRoute 링크 초과 합니다.

**운영 체제**

- SAP 애플리케이션용 SUSE Linux Enterprise Server 12에 대한 라이선스

   > [!NOTE] 
   > Microsoft에서 제공하는 운영 체제가 SUSE에 등록되어 있지 않습니다. 구독 관리 도구 인스턴스에 연결되어 있지 않습니다.

- Azure에서 VM에 배포된 SUSE Linux 구독 관리 도구. 이 도구는 SUSE에서 SAP HANA on Azure(대규모 인스턴스)를 등록하고 하나씩 업데이트할 수 있는 기능을 제공합니다. (HANA 대규모 인스턴스 데이터 센터 내에서는 인터넷에 액세스할 수 없습니다.) 
- SAP HANA용 Red Hat Enterprise Linux 6.7 또는 7.x에 대한 라이선스.

   > [!NOTE]
   > Microsoft에서 제공하는 운영 체제가 Red Hat에 등록되어 있지 않습니다. Red Hat 구독 관리자 인스턴스에 연결되어 있지 않습니다.

- Azure에서 VM에 배포된 Red Hat 구독 관리자. Red Hat 구독 관리자는 Red Hat에서 SAP HANA on Azure(대규모 인스턴스)를 등록하고 하나씩 업데이트할 수 있는 기능을 제공합니다. (Azure 대규모 인스턴스 스탬프에 배포된 테넌트 내에서는 인터넷에 직접 액세스할 수 없습니다.)
- SAP를 사용하려면 Linux 공급자와도 지원 계약을 체결해야 합니다. 이 요구 사항은 HANA 대규모 인스턴스 솔루션 또는 Azure에서 Linux를 실행한다는 사실로 인해 제거되지 않습니다. 일부 Linux Azure 갤러리 이미지와 달리, HANA 대규모 인스턴스의 솔루션 제품에는 서비스 요금이 *포함되지 않습니다*. Linux 배포자와의 지원 계약과 관련하여 SAP의 요구 사항을 충족시키는 것은 사용자의 책임입니다. 
   - SUSE Linux의 경우 [SAP Note #1984787 - SUSE Linux Enterprise Server 12: 설치 정보](https://launchpad.support.sap.com/#/notes/1984787) 및 [SAP Note #1056161 - SAP 애플리케이션에 대한 SUSE 우선 지원](https://launchpad.support.sap.com/#/notes/1056161)에서 지원 계약의 요구 사항을 살펴보세요.
   - Red Hat Linux의 경우 HANA 대규모 인스턴스의 운영 체제에 대한 지원 및 서비스 업데이트가 포함된 올바른 구독 수준을 있어야 합니다. Red Hat에서는 SAP 솔루션용 Red Hat Enterprise Linux 구독을 권장합니다. https://access.redhat.com/solutions/3082481을 참조하세요. 

Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.

운영 체제 및 HLI 펌웨어/드라이버 버전의 호환성 매트릭스는 [HLI에 대한 OS 업그레이드](os-upgrade-hana-large-instance.md)를 참조하세요.


> [!IMPORTANT] 
> 유형 II 단위의 경우 SLES 12 SP2 OS 버전만 지원됩니다. 


**데이터베이스**

- SAP HANA에 대한 라이선스 및 소프트웨어 설치 구성 요소(플랫폼 및 Enterprise 버전).

**애플리케이션**

- SAP HANA에 연결되는 모든 SAP 애플리케이션 및 관련 SAP 지원 계약에 대한 라이선스 및 소프트웨어 설치 구성 요소
- Azure의 SAP HANA(대규모 인스턴스) 환경 및 관련 지원 계약과 함께 사용되는 모든 비 SAP 애플리케이션에 대한 라이선스 및 소프트웨어 설치 구성 요소

**기술**

- Azure IaaS 및 해당 구성 요소에 대한 경험 및 지식
- Azure에서 SAP 워크로드를 배포하는 방법에 대한 경험 및 지식
- SAP HANA 설치 공인 전문가
- SAP HANA에 대한 고가용성 및 재해 복구를 설계하는 SAP 설계 기술

**SAP**

- SAP 고객으로 SAP와 지원 계약을 맺기를 기대합니다.
- 특히 HANA 대규모 인스턴스 SKU의 유형 II 클래스를 구현하는 경우, SAP HANA 버전 및 최종적인 대규모 강화 하드웨어 구성에 대해 SAP에 문의하세요.

**다음 단계**
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](hana-architecture.md) 참조