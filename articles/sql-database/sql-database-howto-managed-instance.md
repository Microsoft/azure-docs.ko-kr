---
title: 관리 되는 인스턴스를 구성 하는 방법
description: Azure SQL Database Managed Instance를 구성 및 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811192"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database에서 Managed Instance를 사용하는 방법

이 문서에서는 Managed Instance를 관리 및 구성하는 데 도움이 될 수 있는 다양한 지침, 스크립트 및 설명을 확인할 수 있습니다.

## <a name="migration"></a>마이그레이션

- [Managed Instance로 마이그레이션](sql-database-managed-instance-migrate.md) – Managed Instance로 마이그레이션하기 위한 권장되는 마이그레이션 프로세스 및 도구에 대해 알아봅니다.

- [Managed Instance로 TDE 인증서 마이그레이션](sql-database-managed-instance-migrate-tde-certificate.md) - SQL Server Database가 TDE(투명한 데이터 암호화)로 보호되는 경우 Azure에서 복원하려는 백업의 암호를 해독하는 데 Managed Instance가 사용할 수 있는 인증서를 마이그레이션해야 합니다.

## <a name="network-configuration"></a>네트워크 구성

- [Managed Instance 서브넷의 크기 결정](sql-database-managed-instance-determine-size-vnet-subnet.md) - 내부에 리소스를 추가하고 나면 Managed Instance는 크기를 조정할 수 없는 전용 서브넷에 배치됩니다. 따라서 서브넷에 배포하려는 인스턴스의 수 및 유형에 따라 서브넷에 필요한 주소의 IP 범위를 계산해야 합니다.
- [Managed Instance용 신규 VNet 및 서브넷 만들기](sql-database-managed-instance-create-vnet-subnet.md) - [여기에 설명된 네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 따라 Managed Instance를 배포하려는 Azure VNet 및 서브넷을 구성해야 합니다. 이 가이드에서는 Managed Instance용으로 적절하게 구성된 새 VNet 및 서브넷을 만드는 가장 쉬운 방법을 확인할 수 있습니다.
- [Managed Instance용 기존 VNet 및 서브넷 구성](sql-database-managed-instance-configure-vnet-subnet.md) - 내부에 Managed Instance를 배포하도록 기존 VNet 및 서브넷을 구성하려는 경우 이 문서에서 [네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)을 확인하고 요구 사항에 따라 서브넷을 구성하는 스크립트를 확인할 수 있습니다.
- [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md) - db 메일 프로필의 연결된 서버를 통해 Managed Instance에서 사용자 지정 도메인의 외부 리소스에 액세스하려는 경우 사용자 지정 DNS를 구성해야 합니다.
- [네트워크 구성 동기화](sql-database-managed-instance-sync-network-configuration.md) - [Azure Virtual Network와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)했는데 Managed Instance로의 연결을 설정할 수 없는 상황이 발생할 수 있습니다. 시도할 수 있는 방법은 서비스 계획에 대한 네트워킹 구성을 새로 고치는 것입니다.
- 관리 [엔드포인트 IP 주소 찾기](sql-database-managed-instance-find-management-endpoint-ip-address.md) – 관리 되는 인스턴스는 관리 목적으로 공용 엔드포인트을 사용 합니다. 여기에 설명된 스크립트를 사용하여 관리 엔드포인트의 IP 주소를 확인할 수 있습니다.
- [기본 제공 방화벽 보호 확인](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) - Managed Instance는 필요한 포트에서만 트래픽을 허용하는 기본 제공 방화벽으로 보호됩니다. 이 가이드에 설명된 스크립트를 사용하여 기본 제공 방화벽 규칙을 확인할 수 있습니다.
- [애플리케이션 연결](sql-database-managed-instance-connect-app.md) - Managed Instance가 개인 IP 주소를 사용하여 고유한 프라이빗 Azure VNet에 배치됩니다. 애플리케이션을 Managed Instance에 연결하는 데 사용할 수 있는 다양한 패턴에 대해 알아보세요.

## <a name="feature-configuration"></a>기능 구성

- [트랜잭션 복제](replication-with-sql-database-managed-instance.md)를 사용하면 Managed Instance 간에, 또는 온-프레미스 SQL Server와 Managed Instance 간에 데이터를 복제할 수 있습니다. 이 가이드에서 트랜잭션 복제를 사용 및 구성하는 방법에 대한 자세한 내용을 확인하세요.
- [위협 검색 구성](sql-database-managed-instance-threat-detection.md) - [위협 검색](sql-database-threat-detection-overview.md)은 SQL 삽입 또는 의심스러운 위치에서의 액세스와 같은 다양한 잠재적 공격을 검색하는 기본 제공 Azure SQL Database 기능입니다. 이 가이드에서 Managed Instance에 대해 [위협 검색](sql-database-threat-detection-overview.md)을 사용하도록 설정하고 구성하는 방법을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [단일 데이터베이스용 방법 가이드](sql-database-howto-single-database.md)에 대해 자세히 알아봅니다.