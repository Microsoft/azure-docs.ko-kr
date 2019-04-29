---
title: 고급 데이터 보안 - Azure SQL Database | Microsoft Docs
description: 중요한 데이터 검색 및 분류, 데이터베이스 취약성 관리 및 Azure SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: monhaber
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: a078ac38cef5b395a19481188c474c7f908160d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419564"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database에 대한 고급 데이터 보안

고급 데이터 보안은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="overview"></a>개요

고급 데이터 보안 (ADS)의 데이터 검색 및 분류, 취약성 평가, 고급 위협 방지를 비롯 한 고급 SQL 보안 기능을 제공 합니다.

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)(현재 미리 보기)는 데이터베이스에 있는 중요한 데이터의 검색, 분류, 레이블 지정 및 보호를 위해 Azure SQL Database에 기본 제공된 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다.
- [취약성 평가](sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) 를 액세스 하거나 데이터베이스를 악용 하려는 비정상적이 고 잠재적으로 해로운 시도 나타내는 비정상적인 활동을 감지 합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. Advanced Threat Protection 경고는 의심 스러운 활동의 세부 정보를 제공 하 고 조사 하 고 위협을 완화 하는 방법에 대 한 조치를 권장 합니다.

한 번 SQL ADS를 활성화하면 포함된 기능을 모두 사용할 수 있습니다. 클릭 한 번으로 SQL Database 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 대해 ADS를 사용하도록 설정할 수 있습니다. ADS 설정을 사용하거나 관리하려면 [SQL 보안 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) 역할, SQL Database 관리자 역할 또는 SQL Server 관리자 역할에 속해야 합니다. 

ADS 가격 책정은 Azure Security Center 표준 계층에 부합합니다. 여기서 보호되는 각 SQL Database 서버 또는 관리되는 인스턴스는 하나의 노드로 계산됩니다. 새로 보호되는 리소스는 Security Center 표준 계층의 평가판 자격을 획득합니다. 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="getting-started-with-ads"></a>ADS 시작

다음 단계에서는 ADS를 시작합니다.

## <a name="1-enable-ads"></a>1. ADS 사용

SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목에서 **Advanced Data Security**로 이동하여 ADS를 사용하도록 설정합니다. 데이터베이스 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 ADS를 사용하도록 설정하려면 **서버에서 Advanced Data Security 사용**을 클릭합니다.

> [!NOTE]
> 저장소 계정을 자동으로 만들어지고 저장 하도록 구성 하 **취약성 평가** 결과 검색 합니다. 동일한 리소스 그룹 및 지역에 다른 서버에 대 한 광고를 이미 설정한 경우 기존 저장소 계정을 사용 됩니다.

![ADS 사용](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS의 비용은 노드당 Azure Security Center 표준 계층 가격 책정에 부합합니다. 여기서 노드는 전체 SQL Database 서버 또는 관리되는 인스턴스입니다. 따라서 ADS를 사용하여 데이터베이스 서버 또는 관리되는 인스턴스의 모든 데이터베이스를 보호하기 위해 한 번만 결제합니다. ADS를 처음에 평가판으로 사용해볼 수 있습니다.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 데이터를 분류하고, 취약점을 추적하고, 위협 경고를 조사하기 시작

**데이터 검색 및 분류** 카드를 클릭하여 분류하도록 추천받은 중요한 열을 확인하고 영구 민감도 레이블을 포함한 데이터를 분류합니다. **취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고를 받은 경우 클릭 합니다 **Advanced Threat Protection** 경고 및 Azure Security Center 보안 경고 페이지를 통해 Azure 구독에서 모든 경고에 대해 통합 된 보고서를 보려면 보려는 카드 세부 정보 .

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. SQL Database 서버 또는 관리되는 인스턴스에서 ADS 설정 관리

ADS 설정을 보고 관리하려면 SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목에서 **Advanced Data Security**로 이동합니다. 이 페이지를 사용 하도록 설정 또는 광고를 사용 하지 않도록 설정 하 고 수정할 수 취약점 평가 및 전체 SQL Database 서버 또는 관리 되는 인스턴스에 대 한 Advanced Threat Protection 설정 합니다.

![서버 설정](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. SQL 데이터베이스의 ADS 설정 관리

특정 데이터베이스의 ADS 설정을 재정의하려면 **데이터베이스 수준에서 Advanced Data Security 사용** 확인란을 선택합니다. 별도 Advanced Threat Protection 경고나 대신 또는 경고 및 결과에 모든 데이터베이스에 대해 수신 하는 것 외에도 개별 데이터베이스에 대 한 취약성 평가 결과 수신 하는 특정 요구 사항이 있는 경우에이 옵션을 사용 합니다 데이터베이스 서버 또는 관리 되는 인스턴스입니다.

확인란을 선택 하면이 데이터베이스에 대 한 관련 설정을 다음 구성할 수 있습니다.
 
![데이터베이스 및 고급 위협 방지 설정](./media/sql-advanced-protection/database_threat_detection_settings.png) 

데이터베이스 서버 또는 관리되는 인스턴스의 고급 데이터 보안 설정은 ADS 데이터베이스 창에서도 액세스할 수 있습니다. 기본 ADS 창에서 **설정**을 클릭한 다음, **Advanced Data Security 서버 설정 보기**를 클릭합니다. 

![데이터베이스 설정](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>다음 단계 

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)에 대해 자세히 알아보기 
- [취약성 평가](sql-vulnerability-assessment.md)에 대해 자세히 알아보기 
- 자세한 내용은 [Advanced Threat Protection](sql-database-threat-detection.md)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아봅니다.
