---
title: Azure Automation을 사용하여 Azure SQL 데이터베이스 관리 | Microsoft Docs
description: Azure Automation 서비스를 사용하여 대규모 Azure SQL 데이터베이스를 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: e488e742fc49102f7c58d132a66bca2347ad575c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702098"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure Automation을 사용하여 Azure SQL 데이터베이스 관리

이 가이드에서는 Azure Automation 서비스 및 이 서비스를 사용하여 Azure SQL 데이터베이스 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure Automation 정의

[Azure Automation](https://azure.microsoft.com/services/automation/)은 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure Automation을 통해 장기 실행 작업, 수동 작업, 오류가 발생하기 쉬운 작업 및 빈번하게 반복되는 작업을 자동화하여 조직의 안정성, 효율성 및 가치 창출 시간을 향상시킬 수 있습니다.

Azure Automation은 조직이 성장함에 따라 요구를 충족하기 위해 크기가 조정되는 안정성과 가용성 높은 워크플로 실행 엔진을 제공합니다. Azure Automation에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure Automation에 의해 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT/DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Azure Automation을 통해 Azure SQL 데이터베이스 관리 향상

Azure SQL Database는 [Azure PowerShell 도구](/powershell/azure/overview)에서 사용할 수 있는 [Azure SQL Database PowerShell cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql)를 이용하여 Azure Automation에서 관리할 수 있습니다. Azure Automation에서는 이러한 Azure SQL Database PowerShell cmdlet을 기본적으로 사용할 수 있으므로 서비스 내에서 SQL DB 관리 작업을 모두 수행할 수 있습니다. Azure Automation에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

또한 Azure Automation에서 PowerShell을 통해 SQL 명령을 실행하여 SQL 서버와 직접 통신할 수 있습니다.

[Azure Automation Runbook 갤러리](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/)에는 Azure SQL 데이터베이스, 다른 Azure 서비스 및 타사 시스템의 관리 자동화를 시작할 수 있는 다양한 제품 팀 및 커뮤니티 Runbook이 포함되어 있습니다. Runbook 갤러리에는 다음이 포함됩니다.

- [SQL Server 데이터베이스에 대해 SQL 쿼리를 실행 합니다.](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [일정에 따라 Azure SQL Database를 세로로(위쪽 또는 아래쪽) 조정](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [해당 데이터베이스의 최대 크기에 도달한 경우 SQL 테이블을 줄이세요.](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Azure SQL Database가 심하게 조각화된 경우 테이블에 인덱스를 달아주세요.](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>다음 단계

Azure Automation의 기본 사항과 Azure Automation을 사용하여 Azure SQL 데이터베이스를 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure Automation에 대해 자세히 알아보세요.

- [Azure Automation 개요](../automation/automation-intro.md)
- [내 첫 번째 runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: 클라우드의 SQL 에이전트](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 