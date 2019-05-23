---
title: Azure Migrate를 사용하여 평가 후 컴퓨터 마이그레이션 | Microsoft Docs
description: Azure Migrate 서비스를 통해 평가를 실행한 후 컴퓨터를 마이그레이션하기 위한 권장 사항을 가져오는 방법을 알아봅니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 068b55e40afc96dbcfae26c8bf7da8a1b9ea349a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598370"
---
# <a name="migrate-machines-after-assessment"></a>평가 후 컴퓨터 마이그레이션


[Azure Migrate](migrate-overview.md)는 온-프레미스 컴퓨터를 평가하여 Azure로의 마이그레이션에 적합한지, Azure에서 컴퓨터를 실행하기 위해 크기 조정 및 비용 예상안을 제공하는지 여부를 확인합니다. 현재 Azure Migrate은 마이그레이션에 대해 컴퓨터를 평가하기만 합니다. 현재 마이그레이션 자체는 타 Azure 서비스를 통해 수행됩니다.

이 문서에서는 마이그레이션 평가 실행 후 마이그레이션 도구에 대한 제안을 가져오는 방법을 설명합니다.

> [!NOTE]
> Azure Government에서는 마이그레이션 도구 제안을 사용할 수 없습니다.

## <a name="migration-tool-suggestion"></a>마이그레이션 도구 제안

마이그레이션 도구와 관련한 제안을 가져오려면 온-프레미스 환경에 대한 심층 검색을 수행해야 합니다. 심층 검색은 온-프레미스 컴퓨터에 에이전트를 설치하여 수행합니다.  

1. Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 탐색한 다음 마이그레이션 평가를 만듭니다. [자세히 알아보기](tutorial-assessment-vmware.md).
2. 권장되는 마이그레이션 방법을 보려는 온-프레미스 컴퓨터 각각에 Azure Migrate 에이전트를 다운로드하여 설치합니다. [이 절차에 따라](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) 에이전트를 설치합니다.
2. 리프트 앤 시프트 마이그레이션에 적합한 온-프레미스 컴퓨터를 확인합니다. 실행되는 앱에 대한 변경이 필요하지 않아 그대로 마이그레이션 가능한 VM입니다.
3. 리프트 앤 시프트 마이그레이션에는 Azure Site Recovery를 사용하는 것이 좋습니다. 또는 Azure로의 마이그레이션을 지원하는 타사 도구를 사용할 수 있습니다. [자세히 알아보기](../site-recovery/tutorial-migrate-on-premises-to-azure.md). 
4. 리프트 앤 시프트 마이그레이션에 적합하지 않은 온-프레미스 컴퓨터, 즉 전체 VM이 아니라 특정 앱을 마이그레이션하려는 경우 다른 마이그레이션 도구를 사용할 수 있습니다. 예를 들어 SQL Server, MySQL 또는 Oracle 등의 온-프레미스 데이터베이스를 Azure로 마이그레이션하려는 경우 [Azure Database Migration 서비스](https://azure.microsoft.com/campaigns/database-migration/)를 권장합니다. 


## <a name="review-suggested-migration-methods"></a>제안된 마이그레이션 방법 검토

1. 제안된 마이그레이션 방법을 가져오기 전에 Azure Migrate 프로젝트를 만들고 온-프레미스 컴퓨터를 검색한 다음 마이그레이션 평가를 실행해야 합니다. [자세히 알아보기](tutorial-assessment-vmware.md).
2. 평가를 만든 후 프로젝트 > **개요** > **대시보드**에서 봅니다. **평가 준비 상태**를 클릭합니다.

    ![평가 준비 상태](./media/tutorial-assessment-vmware/assessment-report.png)  

3. **제안된 도구**에서 마이그레이션에 사용할 수 있는 도구 제안을 검토합니다.

    ![제안된 도구](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
