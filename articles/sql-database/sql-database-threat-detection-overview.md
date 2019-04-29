---
title: Advanced Threat Protection - Azure SQL Database | Microsoft Docs
description: Advanced Threat Protection Azure SQL Database에서 잠재적 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614555"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL Database의 Advanced Threat Protection

Advanced Threat Protection에 대 한 [Azure SQL Database](sql-database-technical-overview.md) 하 고 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 데이터베이스를 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 해로운 시도 나타내는 비정상적인 활동을 감지 합니다.

Advanced Threat Protection의 일부인 합니다 [고급 데이터 보안](sql-database-advanced-data-security.md) ADS ()는 SQL의 고급 보안 기능에 대 한 통합된 패키지는 제공 합니다. Advanced Threat Protection 액세스 및 중앙 SQL 광고 포털을 통해 관리할 수 있습니다.

> [!NOTE]
> 이 항목은 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection 이란

 Advanced Threat Protection 고객이를 감지 하 여 비정상적인 활동에 대 한 보안 경고를 제공 하 여 발생할 수 있는 잠재적 위협에 대응할 수 있도록 보안의 새로운 계층을 제공 합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. Advanced Threat Protection 경고를 통합 하며 [Azure Security Center](https://azure.microsoft.com/services/security-center/), 의심 스러운 활동의 세부 정보를 포함 하 고 조사 하 고 위협을 완화 하는 방법에 대 한 조치를 권장 합니다. Advanced Threat Protection 간단 하 게는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 잠재적 위협을 해결 합니다.

완전한 조사 환경을 위해, Azure 저장소 계정의 감사 로그에 데이터베이스 이벤트를 쓰는 [SQL Database 감사](sql-database-auditing.md)를 사용하도록 설정하는 것이 좋습니다.  

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection 경고

Azure SQL Database에 대 한 advanced Threat Protection은 데이터베이스를 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 해로운 시도 나타내는 비정상적인 활동을 검색 하 고 다음 경고를 트리거할 수 있습니다.

- **SQL 삽입에 대한 취약성**: 애플리케이션에서 데이터베이스에 잘못된 SQL 문을 생성하면 이 경고가 트리거됩니다. 이 경고는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 잘못된 문 생성에 대한 두 가지 가능한 이유가 있습니다.

  - 잘못된 SQL문을 구성하는 애플리케이션 코드의 결함
  - 애플리케이션 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
- **잠재적 SQL 삽입**: SQL 삽입에 대해 식별된 애플리케이션 취약성으로 인해 활성 악용이 발생하면 이 경고가 트리거됩니다. 즉 공격자가 취약한 애플리케이션 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.
- **비정상적인 위치에서 액세스**: 비정상적인 지리적 위치에서 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **비정상적인 Azure 데이터 센터에서 액세스**: 최근 기간 동안 이 서버에 표시된 비정상적인 Azure 데이터 센터에서 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(Azure, Power BI, Azure SQL Query Editor의 새 애플리케이션)을 검색합니다. 다른 경우에서 경고는 Azure 리소스/서비스의 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **알 수 없는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션, 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잠재적으로 위험한 애플리케이션에서 액세스**: 잠재적으로 위험한 애플리케이션이 데이터베이스에 액세스하는 데 사용되면 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하여 공격을 검색합니다.
- **무차별 암호 대입 SQL 자격 증명**: 자격 증명이 다른 로그인의 실패 횟수가 비정상적으로 많으면 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>의심스러운 이벤트 감지 시 비정상적인 데이터베이스 활동 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.

![비정상적인 활동 보고서](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. 이메일에서 **최근 SQL 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고, SQL 데이터베이스에서 검색된 활성 SQL 위협을 대략적으로 보여주는 Azure Security Center 경고 페이지를 표시합니다.

   ![활동 위협](./media/sql-database-threat-detection/active_threats.png)

2. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.

   예를 들어 SQL 삽입은 데이터 기반 애플리케이션 공격에 사용되는 인터넷 상의 가장 대표적인 웹 애플리케이션 보안 문제 중 하나입니다. 공격자는 애플리케이션의 취약성을 이용하여 악의적인 SQL 문을 애플리케이션 항목 필드에 삽입하고 데이터베이스의 데이터를 침범하거나 수정합니다. SQL 삽입 경고의 경우, 경고 세부 정보에 악용된 취약한 SQL 문이 포함되어 있습니다.

   ![특정 경고](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Azure portal에서 데이터베이스에 대 한 Advanced Threat Protection 경고 탐색

Advanced Threat Protection에는 해당 경고를 통합 하며 [Azure 보안 센터](https://azure.microsoft.com/services/security-center/)합니다. 데이터베이스 및 Azure portal에서 SQL 광고 블레이드 내에서 라이브 SQL Advanced Threat Protection 타일 활성 위협의 상태를 추적 합니다.

클릭 **Advanced Threat Protection 경고** Azure Security Center를 시작 하려면 경고 페이지 및 활성 SQL 위협 검색 데이터베이스 또는 데이터 웨어하우스에 대 한 개요를 가져옵니다.

   ![고급 위협 보호 경고](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection 알림 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [단일 및 풀링된 데이터베이스에서 Advanced Threat Protection](sql-database-threat-detection.md)합니다.
- 에 대해 자세히 알아보세요 [Advanced Threat Protection에서 관리 되는 인스턴스](sql-database-managed-instance-threat-detection.md)합니다.
- 에 대해 자세히 알아보세요 [고급 데이터 보안](sql-database-advanced-data-security.md)합니다.
- [Azure SQL Database 감사](sql-database-auditing.md)에 대한 자세한 정보
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아봅니다.
- 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.  
