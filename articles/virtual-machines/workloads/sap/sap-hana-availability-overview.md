---
title: Azure VM의 SAP HANA 가용성 - 개요 | Microsoft Docs
description: Azure 네이티브 VM의 SAP HANA 작업을 설명합니다.
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
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1db56ad31991b85ffad415818c7c67f0ee30808d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708450"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA 고가용성

수많은 Azure 기능을 사용하여 Azure VM에서 SAP HANA와 같은 중요 업무용 데이터베이스를 배포 할 수 있습니다. 이 문서에서는 Azure VM에서 호스팅되는 SAP HANA 인스턴스의 가용성을 달성하는 방법에 대한 지침을 제공합니다. 그리고 Azure에서 SAP HANA 가용성을 높이기 위해 Azure 인프라를 사용하여 구현할 수 있는 몇 가지 시나리오에 대해 설명합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 다음을 포함하여 Azure의 IaaS(Infrastructure as a Service) 기본 사항에 대해 잘 알고 있다고 가정합니다. 

- Azure Portal 또는 PowerShell을 통해 가상 머신 또는 가상 네트워크를 배포하는 방법
- JSON(JavaScript Object Notification) 템플릿을 사용하는 옵션을 포함한 Azure 플랫폼 간 CLI(명령줄 인터페이스)

또한 이 문서에서는 SAP HANA 인스턴스를 설치, 관리 및 운영하는 데 익숙하다고 가정합니다. 특히 HANA 시스템 복제의 설정 및 작동에 대해 잘 알고 있어야 합니다. 여기에는 SAP HANA 데이터베이스에 대한 백업 및 복원과 같은 작업이 포함됩니다.

다음 문서에서는 Azure에서 SAP HANA를 사용하는 방법에 대해 간략히 설명합니다.

- [Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [Azure VM에서 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

또한 SAP HANA와 관련하여 다음 문서를 숙지하는 것도 좋습니다.

- [SAP HANA의 고가용성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: SAP HANA의 고가용성](https://archive.sap.com/documents/docs/DOC-66702)
- [SAP HANA에 대한 시스템 복제 수행](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01의 새로운 기능: 고가용성](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA 시스템 복제에 대한 네트워크 권장 사항](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA 시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA 서비스 자동 다시 시작](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA 시스템 복제 구성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Azure에서 VM을 배포하는 데 익숙하지 않은 경우, Azure에서 가용성 아키텍처를 정의하기 전에 먼저 [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)를 참조하는 것이 좋습니다.

## <a name="service-level-agreements-for-azure-components"></a>Azure 구성 요소에 대한 서비스 수준 계약

Azure는 네트워킹, 저장소 및 VM과 같은 다양한 구성 요소에 대해 서로 다른 가용성 SLA를 제공합니다. SLA는 모두 문서화되어 있습니다. 자세한 내용은 [Microsoft Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요. 

[Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)에서는 다음 두 가지 구성에 대한 별개의 두 가지 SLA에 대해 설명하고 있습니다.

- OS 디스크 및 모든 데이터 디스크에 [Azure 프리미엄 SSD](../../windows/disks-types.md)를 사용하는 단일 VM. 이 옵션은 매월 99.9%의 가동 시간을 제공합니다.
- [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)에 구성된 여러 개의 VM(2개 이상). 이 옵션은 매월 99.95%의 가동 시간을 제공합니다.

Azure 구성 요소에서 제공할 수 있는 SLA에 대한 가용성 요구 사항을 측정합니다. 그런 다음, SAP HANA에서 필요한 수준의 가용성을 달성할 수 있는 시나리오를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)에 대해 알아봅니다.
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)에 대해 알아봅니다. 















  


