---
title: Azure 전용 호스트의 SQL Server VM
description: Azure 전용 호스트에서 SQL Server VM를 실행 하기 위한 세부 정보에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834342"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure 전용 호스트의 SQL Server VM 

이 문서에서는 [Azure 전용 호스트](/azure/virtual-machines/windows/dedicated-hosts)와 SQL Server VM를 사용 하는 구체적인 방법을 자세히 설명 합니다. Azure 전용 호스트에 대 한 추가 정보는 [Azure 전용 호스트 소개](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)블로그 게시물에서 찾을 수 있습니다. 

## <a name="overview"></a>개요
[Azure 전용 호스트](/azure/virtual-machines/windows/dedicated-hosts) 는 단일 azure 구독 전용 가상 머신을 하나 이상 호스트할 수 있는 물리적 서버를 제공 하는 서비스입니다. 전용 호스트는 Microsoft의 데이터 센터에서 사용 되는 것과 동일한 물리적 서버 이며 리소스로 제공 됩니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로 비전 할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합 한 구성으로 Vm을 프로 비전 된 호스트에 직접 넣을 수 있습니다.

## <a name="limitations"></a>제한 사항

- 가상 머신 확장 집합은 현재 전용 호스트에서 지원 되지 않습니다.
- 지원 되는 VM 시리즈: DSv3 및 ESv3. 

## <a name="licensing"></a>라이선스

Azure 전용 호스트에 SQL Server VM를 추가할 때 두 가지 다른 라이선스 옵션 중에서 선택할 수 있습니다. 

  - **SQL VM 라이선싱**: 각 SQL Server VM 라이선스에 대해 개별적으로 지불 하는 기존 라이선스 옵션입니다. 
  - **전용 호스트 라이선스**: Azure 전용 호스트에 사용할 수 있는 새 라이선스 모델 SQL Server 라이선스는 호스트 수준에서 번들로 묶여 지불 됩니다. 


기존 SQL Server 라이선스를 사용 하기 위한 호스트 수준 옵션: 
  - SQL Server Enterprise 버전 Azure 하이브리드 혜택
    - SA 또는 구독이 있는 고객에 게 제공 됩니다.
    - 사용 가능한 모든 실제 코어를 사용 하 고 무제한 가상화를 이용 합니다 (호스트에서 지 원하는 최대 vCPUs까지).
        - Azure 전용 호스트에 Azure 하이브리드 혜택를 적용 하는 방법에 대 한 자세한 내용은 [AZURE 하이브리드 혜택 FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)를 참조 하세요. 
  - 10 월 1 일 전에 획득 한 라이선스 SQL Server
      - SQL Server Enterprise edition에는 호스트 수준 및 VM 별 라이선스 옵션이 모두 있습니다. 
      - SQL Server Standard edition에는 VM 별 라이선스 옵션만 사용할 수 있습니다. 
          - 자세한 내용은 [Microsoft 제품 용어](https://www.microsoft.com/licensing/product-licensing/products)를 참조 하세요. 
  - SQL Server 전용 호스트 수준 옵션을 선택 하지 않으면 다중 테넌트 Vm과 마찬가지로 개별 Vm 수준에서 SQL Server AHB를 선택할 수 있습니다.



## <a name="provisioning"></a>프로비저닝  
전용 호스트에 SQL Server VM를 프로 비전 하는 것은 다른 Azure 가상 머신과는 다릅니다. [Azure PowerShell](../dedicated-hosts-powershell.md), [Azure Portal](../dedicated-hosts-portal.md)및 [Azure CLI](../../linux/dedicated-hosts-cli.md)를 사용 하 여이 작업을 수행할 수 있습니다.

전용 호스트에 기존 SQL Server VM을 추가 하는 프로세스에는 가동 중지 시간이 필요 하지만 데이터에는 영향을 주지 않으며 데이터 손실이 발생 하지 않습니다. 그럼에도 불구 하 고 시스템 데이터베이스를 비롯 한 모든 데이터베이스를 이동 하기 전에 백업 해야 합니다.

## <a name="virtualization"></a>가상화 

전용 호스트의 이점 중 하나는 무제한 가상화입니다. 예를 들어 64 vCores에 대 한 라이선스를 가질 수 있지만, 호스트에서 128 vCores를 가져오도록 구성할 수 있습니다. 따라서 vCores를 2 개 SQL Server 라이선스의 절반에 대해서만 지불 하면 됩니다. 

호스트 이기 때문에 1:2 비율로 가상화를 설정할 수 있습니다. 

## <a name="faq"></a>FAQ

**Q: Azure 전용 호스트에서 Windows Server/SQL Server 라이선스에 Azure 하이브리드 혜택 어떻게 작동 하나요?**

A: 고객은 기존 Windows Server의 가치를 사용 하 고 소프트웨어 보증이 있는 SQL Server 라이선스를 사용 하거나 구독 라이선스를 한정 하 여 Azure 하이브리드 혜택를 사용 하 여 Azure 전용 호스트에서 요금을 절감할 수 있습니다. Windows Server Datacenter 및 SQL Server Enterprise Edition 고객은 전체 호스트의 라이선스를 받을 때 무제한 가상화를 제공 합니다 (호스트에서 가능한 한 많은 Windows Server 가상 머신을 배포 하 여 기본 서버의 실제 용량을 따름). Azure 하이브리드 혜택를 사용 합니다.  Azure 전용 호스트의 모든 Windows Server 및 SQL Server 작업은 Windows Server 및 SQL Server 2008/r 2에 대 한 추가 비용 없이 확장 된 보안 업데이트에도 적합 합니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


