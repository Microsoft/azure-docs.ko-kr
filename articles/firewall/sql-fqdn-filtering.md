---
title: SQL FQDN을 사용하여 Azure Firewall 애플리케이션 규칙 구성
description: 이 문서에서는 Azure Firewall 애플리케이션 규칙에서 SQL FQDN을 구성하는 방법에 대해 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791445"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDN을 사용하여 Azure Firewall 애플리케이션 규칙 구성

이제 SQL FQDN을 사용하여 Azure Firewall 애플리케이션 규칙을 구성할 수 있습니다. 이렇게 하면 가상 네트워크에서 지정된 SQL 서버 인스턴스로만 액세스를 제한할 수 있습니다.

SQL FQDN을 사용하여 트래픽을

- VNet에서 Azure SQL Database 또는 Azure Synapse Analytics로 예를 들면 다음과 같습니다. *sql-server1.database.windows.net* 에 대한 액세스만 허용합니다.
- 온-프레미스에서 Azure SQL Managed Instances 또는 VNet에서 실행되는 SQL IaaS로
- 스포크-스포크에서 Azure SQL Managed Instances 또는 Vnet에서 실행되는 SQL IaaS로

SQL FQDN 필터링은 [프록시 모드](../azure-sql/database/connectivity-architecture.md#connection-policy)에서만 지원됩니다(포트 1433). 기본 리디렉션 모드에서 SQL을 사용하는 경우 [네트워크 규칙](features.md#network-traffic-filtering-rules)의 일부로 SQL 서비스 태그를 사용하여 액세스를 필터링할 수 있습니다.
SQL IaaS 트래픽에 기본 포트가 아닌 포트를 사용하는 경우 방화벽 애플리케이션 규칙에서 해당 포트를 구성할 수 있습니다.

## <a name="configure-using-azure-cli"></a>Azure CLI를 사용하여 구성

1. [Azure CLI를 사용하여 Azure Firewall](deploy-cli.md)을 배포합니다.
2. 트래픽을 Azure SQL Database, Azure Synapse Analytics 또는 SQL Managed Instance로 필터링하는 경우 SQL 연결 모드가 **프록시** 로 설정되어 있는지 확인합니다. SQL 연결 모드를 전환하는 방법에 대해 알아보려면 [Azure SQL 연결 설정](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)을 참조하세요.

   > [!NOTE]
   > SQL *프록시* 모드는 *리디렉션* 에 비해 더 많은 대기 시간이 발생할 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)의 [SQL 서비스](service-tags.md) 태그를 사용하여 액세스를 필터링하면 됩니다.

3. SQL FQDN을 사용하여 SQL Server에 대한 액세스를 허용하는 애플리케이션 규칙을 통해 새 규칙 컬렉션을 만듭니다.

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Azure PowerShell을 사용하여 구성

1. [Azure PowerShell을 사용하여 Azure Firewall](deploy-ps.md)을 배포합니다.
2. 트래픽을 Azure SQL Database, Azure Synapse Analytics 또는 SQL Managed Instance로 필터링하는 경우 SQL 연결 모드가 **프록시** 로 설정되어 있는지 확인합니다. SQL 연결 모드를 전환하는 방법에 대해 알아보려면 [Azure SQL 연결 설정](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)을 참조하세요.

   > [!NOTE]
   > SQL *프록시* 모드는 *리디렉션* 에 비해 더 많은 대기 시간이 발생할 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)의 [SQL 서비스](service-tags.md) 태그를 사용하여 액세스를 필터링하면 됩니다.

3. SQL FQDN을 사용하여 SQL Server에 대한 액세스를 허용하는 애플리케이션 규칙을 통해 새 규칙 컬렉션을 만듭니다.

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Azure Portal을 사용하여 구성
1. [Azure CLI를 사용하여 Azure Firewall](deploy-cli.md)을 배포합니다.
2. 트래픽을 Azure SQL Database, Azure Synapse Analytics 또는 SQL Managed Instance로 필터링하는 경우 SQL 연결 모드가 **프록시** 로 설정되어 있는지 확인합니다. SQL 연결 모드를 전환하는 방법에 대해 알아보려면 [Azure SQL 연결 설정](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)을 참조하세요.  

   > [!NOTE]
   > SQL *프록시* 모드는 *리디렉션* 에 비해 더 많은 대기 시간이 발생할 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)의 [SQL 서비스](service-tags.md) 태그를 사용하여 액세스를 필터링하면 됩니다.
3. 적절한 프로토콜, 포트 및 SQL FQDN을 사용하여 애플리케이션 규칙을 추가한 다음, **저장** 을 선택합니다.
   ![SQL FQDN을 사용하는 애플리케이션 규칙](media/sql-fqdn-filtering/application-rule-sql.png)
4. 방화벽을 통해 트래픽을 필터링하는 VNet의 가상 머신에서 SQL에 액세스합니다. 
5. [Azure Firewall 로그](./firewall-workbook.md)에서 트래픽이 허용되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

SQL 프록시 및 리디렉션 모드에 대해 알아보려면 [Azure SQL 데이터베이스 연결 아키텍처](../azure-sql/database/connectivity-architecture.md)를 참조하세요.