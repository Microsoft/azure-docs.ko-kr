---
title: Azure SQL Database Managed Instance의 가상 네트워크 만들기 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database Managed Instance를 배포할 수 있는 가상 네트워크를 만드는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: bonova, carlrab
manager: craigg
origin.date: 01/15/2019
ms.date: 02/25/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700505"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance의 가상 네트워크 만들기

이 문서에서는 Azure SQL Database Managed Instance를 배포할 수 있는 유효한 가상 네트워크 및 서브넷을 만드는 방법을 설명합니다.

Azure SQL Database Managed Instance는 Azure [가상 네트워크](../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다.

- 개인 IP 주소 보호
- 온-프레미스 네트워크에서 직접 Managed Instance에 연결
- 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 Managed Instance 연결
- Azure 리소스에 Managed Instance 연결  

> [!Note]
> 첫 번째 인스턴스를 배포하기 전에 [Managed Instance의 서브넷 크기를 결정](sql-database-managed-instance-determine-size-vnet-subnet.md)해야 합니다. 리소스를 추가한 후에는 서브넷 크기를 조정할 수 없습니다.
>
> 기존 가상 네트워크를 사용하려는 경우 Managed Instance를 수용하도록 해당 네트워크 구성을 수정해야 합니다. 자세한 내용은 [Managed Instance의 기존 가상 네트워크 수정](sql-database-managed-instance-configure-vnet-subnet.md)을 참조하세요.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들고 구성하는 가장 쉬운 방법은 Azure Resource Manager 배포 템플릿을 사용하는 것입니다.

1. Azure 포털에 로그인합니다.

2. **Azure에 배포** 단추를 선택합니다.

   <a target="_blank" href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   이 단추를 사용하면 네트워크 환경을 구성하는 데 사용할 수 있는 양식이 열리는데, 여기서 Managed Instance를 배포할 수 있습니다.

   > [!Note]
   > 이 Azure Resource Manager 템플릿은 두 개의 서브넷이 있는 가상 네트워크를 배포합니다. **ManagedInstances**라는 하나의 서브넷이 Managed Instance용으로 예약되고 미리 구성된 경로 테이블을 포함합니다. **Default**라는 다른 서브넷은 Managed Instance에 액세스해야 하는 다른 리소스(예: Azure Virtual Machines)에 사용됩니다.

3. 네트워크 환경을 구성합니다. 다음 양식에서 네트워크 환경의 매개 변수를 구성할 수 있습니다.

   ![Azure 네트워크를 구성하기 위한 Resource Manager 템플릿](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   가상 네트워크 및 서브넷의 이름을 변경하고, 네트워킹 리소스에 연결된 IP 범위를 조정할 수 있습니다. **구매** 단추를 선택하면 이 양식이 생성되어 환경을 구성합니다. 두 개의 서브넷 필요하지 않은 경우에는 기본 서브넷을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 알아봅니다.
- [Managed Instance의 기존 가상 네트워크를 수정](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
