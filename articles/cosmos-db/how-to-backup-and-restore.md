---
title: 백업에서 Azure Cosmos DB 데이터를 복원하는 방법
description: 이 문서에서는 백업에서 Azure Cosmos DB 데이터를 복원하는 방법, 데이터 복원을 위해 Azure 지원에 문의하는 방법, 데이터가 복원된 후 수행해야 하는 단계를 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1d886e146e9e18eb735e6f88d2cb2c1a4a472924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059641"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Azure Cosmos DB의 백업에서 데이터 복원 

데이터베이스 또는 컨테이너를 실수로 삭제한 경우, [지원 티켓을 제출]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하거나 [Azure 지원에 문의]( https://azure.microsoft.com/support/options/)하여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원만 제공 됩니다 선택한 계획에 대 한 같은 **표준**, **개발자**, 및 해당 이상 계획 합니다. Azure 지원은 **Basic** 플랜에는 사용할 수 없습니다. 여러 지원 플랜에 대해 자세히 알아보려면 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 페이지를 참조하세요. 

백업의 특정 스냅숏을 복원하려면 Azure Cosmos DB에서 해당 스냅숏의 백업 주기 동안 데이터를 사용할 수 있어야 합니다.

## <a name="request-a-restore"></a>복원 요청

복원을 요청하려면 다음 세부 정보가 필요합니다.

* 구독 ID를 준비합니다.

* 데이터가 어떻게 실수로 삭제되거나 수정되었는지에 따라 추가 정보를 준비해야 합니다. 일부 시간에 구애 받는 경우 불리할 수 있는 전후 과정을 최소화하기 위해 사전에 사용 가능한 정보를 확보하는 것이 좋습니다.

* 전체 Azure Cosmos DB 계정이 삭제된 경우 삭제된 계정의 이름을 제공해야 합니다. 삭제된 계정과 동일한 이름을 가진 다른 계정을 만들 경우 올바른 계정을 선택하는 데 도움이 되므로 이를 지원팀과 공유하세요. 복원 상태의 혼란을 최소화하기 위해 삭제된 각 계정에 대한 다양한 지원 티켓을 제출하는 것이 좋습니다.

* 하나 이상의 데이터베이스가 삭제된 경우 Azure Cosmos 계정뿐 아니라 Azure Cosmos 데이터베이스 이름을 제공하고 동일한 이름의 새 데이터베이스가 있는지 여부를 지정해야 합니다.

* 하나 이상의 컨테이너가 삭제된 경우 Azure Cosmos 계정 이름, 데이터베이스 이름 및 컨테이너 이름을 제공해야 합니다. 또한 같은 이름의 컨테이너가 존재하는지 여부를 지정합니다.

* 실수로 삭제 되거나 데이터 손상 있어야 하는 경우 문의 [Azure 지원](https://azure.microsoft.com/support/options/) 8 시간 이내 Azure Cosmos DB 팀 도움을 줄 수 있도록 하는 데이터 백업에서 복원 합니다.
  
  * 데이터베이스 또는 컨테이너를 실수로 삭제 한 경우에 Sev B 또는 심각도 C Azure 지원 사례를 엽니다. 
  * 실수로 삭제 되거나 손상 된 컨테이너 내에서 일부 문서 있을 경우 심각도 A 지원 사례를 엽니다. 

데이터 손상이 발생하는 경우 및 컨테이너 내 문서가 수정되거나 삭제되면 **가능한 한 빨리 컨테이너를 삭제**합니다. 컨테이너를 삭제하면 Azure Cosmos DB가 백업을 덮어쓰는 것을 방지할 수 있습니다. 어떠한 이유로 삭제할 수 없는 경우에는 가능한 한 빨리 티켓을 제출해야 합니다. Azure Cosmos 계정 이름, 데이터베이스 이름, 컬렉션 이름 외에도 데이터를 복원할 수 있는 시점을 지정해야 합니다. 가능한 한 정확하게 해야 해당 시점에 가장 적합한 백업을 결정할 수 있습니다. 또한 시간을 UTC로 지정하는 것도 중요합니다. 

다음 스크린샷은 Azure Portal을 사용하여 데이터를 복원하기 위해 컨테이너(컬렉션/그래프/테이블)에 대한 지원 요청을 만드는 방법을 보여줍니다. 요청의 우선 순위를 지정하는 데 도움이 되도록 데이터의 유형, 복원의 목적, 데이터가 삭제된 시간과 같은 추가 정보를 제공합니다.

![Azure Portal을 사용하여 백업 지원 요청 만들기](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>복원 후 작업

데이터를 복원한 후 새 계정 이름(일반적으로 `<original-name>-restored1` 형식임) 및 계정이 복구된 시간에 대한 알림을 받습니다. 복원된 계정은 프로비저닝된 처리량과 인덱싱 정책이 동일하며 원래 계정과 동일한 영역에 있습니다. 구독 관리자 또는 공동 관리자인 사용자는 복원된 계정을 볼 수 있습니다.

데이터를 복원한 후 복원된 계정의 데이터를 검사하고 유효성을 확인하고, 예상되는 버전을 포함하는지 확인해야 합니다. 모든 것이 정상인 경우 [Azure Cosmos DB 변경 피드](change-feed.md) 또는 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용하여 데이터를 원래 계정으로 다시 마이그레이션해야 합니다.

데이터를 마이그레이션하는 즉시 컨테이너 또는 데이터베이스를 삭제하는 것이 좋습니다. 복원된 데이터베이스 또는 컨테이너를 삭제하지 않으면 요청 단위, 스토리지 및 송신 관련 비용이 발생합니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 데이터를 원래 계정으로 다시 마이그레이션하는 방법에 대해 알아볼 수 있습니다.

* 복원 요청을 수행하려면 Azure 지원에 문의하여 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
* [Cosmos DB 변경 피드를 사용](change-feed.md)하여 Azure Cosmos DB로 데이터를 이동합니다.

* [Azure Data Factory를 사용](../data-factory/connector-azure-cosmos-db.md)하여 데이터를 Azure Cosmos DB로 이동합니다.
