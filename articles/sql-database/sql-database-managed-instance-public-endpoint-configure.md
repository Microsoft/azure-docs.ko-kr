---
title: 공용 엔드포인트 관리 인스턴스 구성
description: 관리 되는 인스턴스의 공용 엔드포인트을 구성 하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227996"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스에서 공용 엔드포인트 구성

[관리 되는 인스턴스의](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) 공용 엔드포인트은 [가상 네트워크](../virtual-network/virtual-networks-overview.md)외부에서 관리 되는 인스턴스에 대 한 데이터 액세스를 가능 하 게 합니다. Power BI, Azure App Service 또는 온-프레미스 네트워크와 같은 다중 테넌트 Azure 서비스에서 관리 되는 인스턴스에 액세스할 수 있습니다. 관리 되는 인스턴스의 공용 엔드포인트을 사용 하 여 vpn 처리량 문제를 방지 하는 데 도움이 될 수 있는 VPN을 사용할 필요가 없습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - Azure Portal에서 관리 되는 인스턴스에 대 한 공용 엔드포인트 사용
> - PowerShell을 사용 하 여 관리 되는 인스턴스에 대해 공용 엔드포인트 사용
> - 관리 되는 인스턴스 공용 엔드포인트에 대 한 트래픽을 허용 하도록 관리 되는 인스턴스 네트워크 보안 그룹 구성
> - 관리 되는 인스턴스 공용 엔드포인트 연결 문자열을 가져옵니다.

## <a name="permissions"></a>사용 권한

관리 되는 인스턴스에 있는 데이터의 민감도 때문에 관리 되는 인스턴스 공용 엔드포인트을 사용 하도록 구성 하려면 2 단계 프로세스가 필요 합니다. 이 보안 조치는 의무 (안)의 분리를 따릅니다.

- 관리 되는 인스턴스에서 공용 엔드포인트을 사용 하도록 설정 하는 것은 관리 되는 인스턴스 관리자가 수행 해야 합니다. 관리 되는 인스턴스 관리자는 SQL 관리 되는 인스턴스 리소스의 **개요** 페이지에서 찾을 수 있습니다.
- 네트워크 관리자가 수행 해야 하는 네트워크 보안 그룹을 사용 하 여 트래픽을 허용 합니다. 자세한 내용은 [네트워크 보안 그룹 권한](../virtual-network/manage-network-security-group.md#permissions)을 참조 하세요.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Azure Portal에서 관리 되는 인스턴스에 대 한 공용 엔드포인트 사용

1. <https://portal.azure.com/.>에서 Azure Portal를 시작 합니다.
1. 관리 되는 인스턴스를 사용 하 여 리소스 그룹을 열고 공용 엔드포인트을 구성 하려는 **SQL 관리 되는 인스턴스** 를 선택 합니다.
1. **보안** 설정에서 **가상 네트워크** 탭을 선택 합니다.
1. 가상 네트워크 구성 페이지에서 **사용** 을 선택 하 고 **저장** 아이콘을 선택 하 여 구성을 업데이트 합니다.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 인스턴스에 대해 공용 엔드포인트 사용

### <a name="enable-public-endpoint"></a>공용 엔드포인트 사용

다음 PowerShell 명령을 실행합니다. **구독 id** 를 구독 id로 바꿉니다. 또한 **rg 이름** 을 관리 되는 인스턴스의 리소스 그룹으로 바꾸고 **mi 이름** 을 관리 되는 인스턴스의 이름으로 바꿉니다.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>공용 엔드포인트 사용 안 함

PowerShell을 사용 하 여 공용 엔드포인트을 사용 하지 않도록 설정 하려면 다음 명령을 실행 합니다. 또한 구성 된 경우 인바운드 포트 3342에 대 한 NSG를 닫아야 합니다.

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>네트워크 보안 그룹에서 공용 엔드포인트 트래픽 허용

1. 관리 되는 인스턴스의 구성 페이지가 열려 있는 상태에서 **개요** 탭으로 이동 합니다. 그렇지 않은 경우에 **는 SQL 관리 되는 인스턴스** 리소스로 돌아갑니다. 가상 네트워크 **/서브넷** 링크를 선택 합니다. 그러면 가상 네트워크 구성 페이지로 이동 합니다.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 가상 네트워크의 왼쪽 구성 창에서 **서브넷** 탭을 선택 하 고 관리 되는 인스턴스의 **보안 그룹** 을 기록해 둡니다.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 관리 되는 인스턴스를 포함 하는 리소스 그룹으로 돌아갑니다. 위에서 언급 한 **네트워크 보안 그룹** 이름이 표시 됩니다. 네트워크 보안 그룹 구성 페이지로 이동 하는 이름을 선택 합니다.

1. **인바운드 보안 규칙** 탭을 선택 하 고 다음 설정을 사용 하 여 **deny_all_inbound** 규칙 보다 우선 순위가 높은 규칙을 **추가** 합니다. </br> </br>

    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    |**원본**     |모든 IP 주소 또는 서비스 태그         |<ul><li>Power BI와 같은 Azure 서비스의 경우 Azure 클라우드 서비스 태그를 선택 합니다.</li> <li>컴퓨터 또는 Azure VM의 경우 NAT IP 주소를 사용 합니다.</li></ul> |
    |**원본 포트 범위**     |*         |원본 포트가 일반적으로 동적으로 할당 되 고 예측할 수 없기 때문에 * (모두)로 그대로 둡니다. |
    |**대상이**     |모두         |관리 되는 인스턴스 서브넷으로의 트래픽을 허용 하는 대상으로 유지 |
    |**대상 포트 범위**     |3342         |대상 포트의 범위를 3342로,이는 관리 되는 인스턴스 공용 TDS 엔드포인트입니다. |
    |**프로토콜**     |TCP         |관리 되는 인스턴스가 TDS에 TCP 프로토콜을 사용 합니다. |
    |**작업**     |Allow         |공용 엔드포인트을 통해 관리 되는 인스턴스에 대 한 인바운드 트래픽 허용 |
    |**우선 순위**     |1300         |이 규칙이 **deny_all_inbound** 규칙 보다 높은 우선 순위 인지 확인 합니다. |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 포트 3342은 관리 되는 인스턴스에 대 한 공용 엔드포인트 연결에 사용 되며,이 시점에서 변경할 수 없습니다.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>관리 되는 인스턴스 공용 엔드포인트 연결 문자열 가져오기

1. 공용 엔드포인트에 대해 사용 하도록 설정 된 SQL 관리 되는 인스턴스 구성 페이지로 이동 합니다. **설정** 구성에서 **연결 문자열** 탭을 선택 합니다.
1. 공용 엔드포인트 호스트 이름은 < mi_name > 형식으로 제공 됩니다.< dns_zone >. net.tcp를 사용 하 고 연결에 사용 되는 포트는 3342입니다.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>다음 단계

- [공용 엔드포인트을 사용 하 여 관리 되는 인스턴스를 안전 하 게 사용 Azure SQL Database 하는](sql-database-managed-instance-public-endpoint-securely.md)방법을 알아봅니다.