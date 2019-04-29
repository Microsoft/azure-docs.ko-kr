---
title: Azure SQL Database Managed Instance의 기존 가상 네트워크 구성 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database Managed Instance를 배포할 수 있는 기존 가상 네트워크 및 서브넷을 구성하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, bonova, carlrab
manager: digimobile
origin.date: 01/15/2019
ms.date: 04/29/2019
ms.openlocfilehash: c4ff12f0c9adcb9943a6e2426eaf2740ba171e39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700482"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance의 기존 가상 네트워크 구성

Azure SQL Database Managed Instance는 Azure [가상 네트워크](../virtual-network/virtual-networks-overview.md) 및 Managed Instance 전용 서브넷에만 배포되어야 합니다. [Managed Instance 가상 네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 따라 구성된 경우 기존 가상 네트워크 및 서브넷을 사용할 수 있습니다.

다음 경우 중 하나에 해당하면 이 문서에 설명된 스크립트를 사용하여 네트워크가 유효한지 검사하고 수정할 수 있습니다.

- 아직 구성하지 않은 새 서브넷이 있습니다.
- 서브넷에 [요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 맞는지 잘 모릅니다.
- 변경한 후에 서브넷이 여전히 [네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)을 준수하는지 확인하려고 합니다.

> [!Note]
> Azure Resource Manager 배포 모델을 통해 만든 가상 네트워크에서만 Managed Instance를 만들 수 있습니다. 클래식 배포 모델을 통해 만든 Azure Virtual Network는 지원되지 않습니다. [Managed Instance에 대한 서브넷 크기 확인](sql-database-managed-instance-determine-size-vnet-subnet.md) 문서의 지침에 따라 서브넷 크기를 계산합니다. 리소스를 배포한 후에는 서브넷 크기를 조정할 수 없습니다.

## <a name="validate-and-modify-an-existing-virtual-network"></a>기존 가상 네트워크 유효성 검사 및 수정

기존 서브넷 내에서 Managed Instance를 만들려는 경우 서브넷을 준비하는 데 다음 PowerShell 스크립트가 권장됩니다.

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

스크립트는 다음 3단계에 따라 서브넷을 준비합니다.

1. 유효성 검사: 선택한 가상 네트워크와 서브넷이 Managed Instance 네트워킹 요구 사항을 준수하는지 검사합니다.
2. 확인: 사용자에게 Managed Instance 배포를 위해 서브넷을 준비할 때 변경해야 하는 사항이 표시됩니다. 동의도 요청됩니다.
3. 준비: 가상 네트워크 및 서브넷이 제대로 구성됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
