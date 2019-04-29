---
title: Linux 가상 머신에서 SAP 사용 | Microsoft Docs
description: Microsoft Azure의 Linux VM(가상 머신)에서 SAP를 사용하는 방법을 알아봅니다.
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: e4c4afefe7a4746f2d8138bde7fea152f228a8bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648885"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Azure의 Linux 가상 머신에서 SAP 사용
클라우드 컴퓨팅은 중소 기업에서 대기업 및 다국적 기업까지 IT 업계 내에서 점점 더 중요해지는 널리 사용되는 용어입니다. Microsoft Azure는 다양한 새로운 가능성을 제공하는 Microsoft의 Cloud Services 플랫폼입니다. 이제 고객은 클라우드 서비스로 애플리케이션을 신속하게 프로비전 및 프로비전 해제할 수 있으므로 기술 또는 예산 제한에 제한되지 않습니다. 하드웨어 인프라에 시간과 예산을 투자하는 대신 기업은 고객 및 사용자를 위한 애플리케이션, 비즈니스 프로세스 및 그 이점에 집중할 수 있습니다.

Microsoft는 Microsoft Azure 가상 머신과 함께 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. SAP NetWeaver 기반 애플리케이션은 Azure Virtual Machines(IaaS)에서 지원됩니다. 아래 백서는 Azure의 Windows 가상 머신에서 SAP NetWeaver 기반 애플리케이션을 계획하고 구현하는 방법에 대해 설명합니다. [Windows 가상 머신](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)에서 SAP NetWeaver 기반 애플리케이션을 구현할 수도 있습니다.

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>Azure SUSE Linux Virtual Machines에서 SAP NetWeaver
제목: Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트

요약: 이 시점에서 Azure Linux Vm에서 SAP NetWeaver 실행에 대 한 공식 SAP 지원이 없는 경우 그렇기는 하지만 고객이 SAP 지원에 문의할 필요가 없다면 Azure Linux VM에서 일부 테스트를 수행하거나 SAP 데모 또는 교육 시스템 실행을 고려할 수 있습니다. 이 문서는 SAP를 실행할 수 있도록 Azure SUSE Linux VM을 설정하는 데 도움을 주며 일반적으로 일어날 수 있는 위험을 방지하기 위한 몇 가지 기본적인 힌트를 제공합니다.

업데이트: 2015년 12월

[이 문서는 여기서 확인할 수 있습니다.](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

