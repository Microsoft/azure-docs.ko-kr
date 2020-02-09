---
title: SQL 관리 되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 Azure SQL Database 관리 되는 인스턴스 마이그레이션의 원본 및 대상 구성에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9a313ea798519273ce57961544ec5b37c4d9c5ca
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749259"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Azure Database Migration Service를 사용 하 여 Azure SQL DB Managed Instance 마이그레이션에 대한 네트워크 토폴로지

이 문서에서는 온-프레미스 SQL Server에서 Azure SQL Database Managed Instance으로 포괄적인 마이그레이션 환경을 제공 하기 위해에서 사용할 수 Azure Database Migration Service 있는 다양 한 네트워크 토폴로지를 설명 합니다.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>하이브리드 워크로드에 대해 구성된 Azure SQL Database Managed Instance 

Azure SQL Database Managed Instance가 온-프레미스 네트워크에 연결되어 있는 경우 이 토폴로지를 사용합니다. 이 방법은 가장 간소화된 네트워크 라우팅을 제공하고 마이그레이션 동안 최대 데이터 처리량을 제공합니다.

![하이브리드 워크로드에 대한 네트워크 토폴로지](media/resource-network-topologies/hybrid-workloads.png)

**요구 사항**

- 이 시나리오에서는 Azure SQL Database 관리 되는 인스턴스와 Azure Database Migration Service 인스턴스가 동일한 Microsoft Azure Virtual Network에서 만들어지지만 다른 서브넷을 사용 합니다.  
- 이 시나리오에서 사용 되는 가상 네트워크는 [express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 네트워크에도 연결 됩니다.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>온-프레미스 네트워크에서 격리된 Azure SQL Database Managed Instance

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- Azure SQL Database 관리 되는 인스턴스는 온-프레미스 연결에서 격리 되지만 Azure Database Migration Service 인스턴스는 온-프레미스 네트워크에 연결 됩니다.
- RBAC (역할 기반 Access Control) 정책이 적용 되는 경우 Azure SQL Database 관리 되는 인스턴스를 호스트 하는 동일한 구독에 액세스 하도록 사용자를 제한 해야 합니다.
- Azure SQL Database Managed Instance 및 Azure Database Migration Service에 사용 되는 가상 네트워크가 서로 다른 구독에 있습니다.

![온-프레미스 네트워크에서 격리된 Managed Instance의 네트워크 토폴로지](media/resource-network-topologies/mi-isolated-workload.png)

**요구 사항**

- 이 시나리오에 사용 Azure Database Migration Service 하는 가상 네트워크는 (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 네트워크에도 연결 되어야 합니다.
- Azure SQL Database 관리 되는 인스턴스 및 Azure Database Migration Service에 사용 되는 가상 네트워크 간에 [VNet 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 을 설정 합니다.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>클라우드-클라우드 마이그레이션: 공유 가상 네트워크

원본 SQL Server Azure VM에서 호스트 되 고 Azure SQL Database 관리 되는 인스턴스 및 Azure Database Migration Service와 동일한 가상 네트워크를 공유 하는 경우이 토폴로지를 사용 합니다.

![공유 VNet을 사용 하 여 클라우드-클라우드 마이그레이션에 대한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud.png)

**요구 사항**

- 추가 요구 사항 없음

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>클라우드-클라우드 마이그레이션: 격리 된 가상 네트워크

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- Azure SQL Database 관리 되는 인스턴스는 격리 된 가상 네트워크에서 프로 비전 됩니다.
- RBAC (역할 기반 Access Control) 정책이 적용 되는 경우 Azure SQL Database 관리 되는 인스턴스를 호스트 하는 동일한 구독에 액세스 하도록 사용자를 제한 해야 합니다.
- Azure SQL Database Managed Instance 및 Azure Database Migration Service에 사용 되는 가상 네트워크가 서로 다른 구독에 있습니다.

![Isolated VNet을 사용 하 여 클라우드-클라우드 마이그레이션에 대한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**요구 사항**

- Azure SQL Database 관리 되는 인스턴스 및 Azure Database Migration Service에 사용 되는 가상 네트워크 간에 [VNet 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 을 설정 합니다.

## <a name="inbound-security-rules"></a>인바운드 보안 규칙

| **이름**   | **포트** | **프로토콜** | **원본** | **대상** | **작업** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 모두      | 모두          | DMS SUBNET | 모두             | 허용      |

## <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| **이름**                  | **포트**                                              | **프로토콜** | **원본** | **대상**           | **작업** | **규칙이 필요한 이유**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 관리                | 443,9354                                              | TCP          | 모두        | 모두                       | 허용      | Service Bus 및 Azure blob storage를 통한 관리 평면 통신. <br/>(Microsoft 피어링을 사용하도록 설정한 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                             |
| 진단               | 12000                                                 | TCP          | 모두        | 모두                       | 허용      | DMS는 이 규칙을 사용하여 문제 해결을 위한 진단 정보를 수집합니다.                                                                                                                      |
| SQL 원본 서버         | 1433(또는 SQL Server가 수신 대기 중인 TCP IP 포트) | TCP          | 모두        | 온-프레미스 주소 공간 | 허용      | DMS의 SQL Server 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                                       |
| SQL Server 명명된 인스턴스 | 1434                                                  | UDP          | 모두        | 온-프레미스 주소 공간 | 허용      | DMS의 SQL Server 명명된 인스턴스 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                        |
| SMB 공유                 | 445                                                   | TCP          | 모두        | 온-프레미스 주소 공간 | 허용      | Azure SQL Database MI 및 Azure VM의 SQL Server로 마이그레이션할 데이터베이스 백업 파일을 저장하는 DMS용 SMB 네트워크 공유 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.) |
| DMS_subnet                | 모두                                                   | 모두          | 모두        | DMS_Subnet                | 허용      |                                                                                                                                                                                                  |

## <a name="see-also"></a>참고 항목

- [Azure SQL Database Managed Instance로 SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Azure Portal을 사용하여 가상 네트워크 만들기](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>다음 단계

- Azure Database Migration Service에 대한 개요는 [Azure Database Migration Service 이란?](dms-overview.md)문서를 참조 하세요.
- Azure Database Migration Service의 지역별 가용성에 대한 최신 정보는 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) 페이지를 참조 하세요.
