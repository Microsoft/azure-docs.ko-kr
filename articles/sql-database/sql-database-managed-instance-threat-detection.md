---
title: 위협 탐지 구성 - Azure SQL Database 관리형 인스턴스 | Microsoft Docs
description: 위협 탐지는 관리되는 인스턴스의 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61313910"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Azure SQL Database 관리형 인스턴스에서 위협 탐지(미리 보기) 구성

[관리되는 인스턴스](sql-database-managed-instance-index.yml)에 대한 [위협 탐지](sql-database-threat-detection-overview.md)는 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 나타내는 비정상적인 활동을 검색합니다. 위협 탐지는 **잠재적인 SQL 삽입**, **비정상적인 위치 또는 데이터 센터에서 액세스**, **생소한 보안 주체 또는 잠재적으로 위험한 애플리케이션의 액세스** 및 **무차별 SQL 자격 증명 대입 공격**을 식별할 수 있습니다. 자세한 내용은 [위협 탐지 경고](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)를 참조하세요.

[이메일 알림](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) 또는 [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)을 통해 탐지된 위협에 대한 알림을 받을 수 있습니다.

[위협 탐지](sql-database-threat-detection-overview.md)는 고급 SQL 보안 기능용 통합 패키지인 [ADS](sql-database-advanced-data-security.md)(Advanced Data Security) 제품에 포함되어 있습니다. 중앙 SQL ADS 포털을 통해 위협 탐지에 액세스하고 관리할 수 있습니다. 위협 탐지 서비스는 처음 30일간 무료이며 관리되는 인스턴스당 매월 15달러가 부과됩니다.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Azure Portal에서 관리되는 인스턴스에 대한 위협 탐지 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 관리되는 인스턴스의 구성 페이지로 이동합니다. **설정** 페이지에서 **위협 검색**을 선택합니다.
3. 위협 검색 구성 페이지에서
   - 위협 감지를 **켭니다**
   - 비정상적인 데이터베이스 활동이 감지되는 경우 보안 경고를 수신할 **메일 목록**을 구성합니다.
   - 비정상적인 위협 감사 레코드가 저장되는 **Azure Storage 계정**을 선택합니다.
4. **저장**을 클릭하여 새롭거나 업데이트된 위협 검색 정책을 저장합니다.

   ![위협 검색](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>다음 단계

- [위협 탐지](sql-database-threat-detection-overview.md)에 대해 자세히 알아봅니다.
- [관리되는 인스턴스란?](sql-database-managed-instance.md)에서 관리되는 인스턴스에 대해 자세히 알아봅니다.
- [단일 데이터베이스 위협 탐지](sql-database-threat-detection.md)에 대해 자세히 알아봅니다.
- [관리되는 인스턴스 감사](https://go.microsoft.com/fwlink/?linkid=869430)에 대해 자세히 알아봅니다.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아봅니다.
