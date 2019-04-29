---
title: Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원
description: 이 문서에서는 Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원의 작동 방식에 대해 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930251"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원

Azure Cosmos DB는 자동으로 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 스토리지 서비스에서 개별적으로 저장되고 이러한 백업은 지역 재해에 대비한 복원을 위해 전역적으로 복제됩니다. 자동 백업은 Azure Cosmos 계정, 데이터베이스 또는 컨테이너를 실수로 삭제하거나 업데이트하고 나중에 데이터를 복구해야 하는 시나리오에서 유용합니다.

## <a name="automatic-and-online-backups"></a>자동 온라인 백업

Azure Cosmos DB를 사용하면 데이터뿐만 아니라 데이터의 백업도 지역 재해에 대해 중복성 및 복원력이 뛰어납니다. 다음 단계는 Azure Cosmos DB 데이터 백업을 수행 하는 방법을 보여줍니다.

* Azure Cosmos DB는 자동으로 데이터베이스의 백업을 4 시간 마다 하 고 언제 든 지 시간의 최근 2 개의 백업이 저장 됩니다. 단, 컨테이너 또는 데이터베이스가 삭제된 경우 Azure Cosmos DB는 지정된 컨테이너 또는 데이터베이스의 기존 스냅숏을 30일 동안 유지합니다.

* Azure Cosmos DB는 실제 데이터를 Azure Cosmos DB 내에 로컬로 상주 하는 반면 이러한 백업은 Azure Blob 저장소에 저장 합니다.

*  짧은 대기 시간을 보장 하기 위해 백업의 스냅숏이 현재 쓰기 지역과 동일한 지역의 Azure Blob storage에 저장 됩니다 (쓰기 지역 중 하나 또는 다중 마스터 구성이 있는 경우) 데이터베이스 계정에 Azure Cosmos입니다. 지역적 재해에 대비한 복구를 위해 Azure Blob Storage에 있는 백업 데이터의 각 스냅숏은 GRS(지역 중복 스토리지)를 통해 다른 지역으로 다시 복제됩니다. 백업이 복제되는 지역은 원본 지역 및 원본 지역에 연결된 지역 쌍을 기반으로 합니다. 자세한 내용은 [Azure 지역의 지역 중복 쌍 목록](../best-practices-availability-paired-regions.md) 문서를 참조하세요. 이 백업에 직접 액세스할 수 없습니다. Azure Cosmos DB는 백업 복원이 시작된 경우에만 이 백업을 사용하게 됩니다.

* 백업은 애플리케이션의 성능이나 가용성에 영향을 주지 않고 수행됩니다. Azure Cosmos DB는 프로비전된 추가 처리량(RU)을 사용하지 않으며 데이터베이스의 성능 및 가용성에 영향을 주지 않고 백그라운드에서 데이터 백업을 수행합니다.

* 실수로 삭제 되거나 데이터 손상 있어야 하는 경우 문의 [Azure 지원](https://azure.microsoft.com/support/options/) 8 시간 이내 Azure Cosmos DB 팀 도움을 줄 수 있도록 하는 데이터 백업에서 복원 합니다.

다음 이미지는 미국 서부의 기본 실제 파티션 3개를 모두 포함하는 Azure Cosmos 컨테이너가 미국 서부의 원격 Azure Blob Storage 계정에 백업된 다음, 미국 동부에 복제되는 과정을 보여줍니다.

![GRS Azure Storage에 있는 모든 Cosmos DB 엔터티의 정기적인 전체 백업](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>사용자 고유의 백업을 관리하기 위한 옵션

Azure Cosmos DB SQL API 계정을 통해 다음 방법 중 하나를 사용하면 백업을 직접 유지할 수도 있습니다.

* [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용하여 데이터를 주기적으로 선택한 스토리지로 이동합니다.

* Azure Cosmos DB [변경 피드](change-feed.md)를 사용하여 증분 변경에 대한 데이터와 함께 전체 백업에 대한 데이터를 주기적으로 읽고, 사용자 고유의 스토리지에 저장합니다.

## <a name="backup-retention-period"></a>Backup 보존 기간

Azure Cosmos DB는 데이터의 스냅숏을 4시간 간격으로 만듭니다. 지정된 시간에는 마지막 두 스냅숏만 유지됩니다. 단, 컨테이너 또는 데이터베이스가 삭제된 경우 Azure Cosmos DB는 지정된 컨테이너 또는 데이터베이스의 기존 스냅숏을 30일 동안 유지합니다.

## <a name="restoring-data-from-online-backups"></a>온라인 백업에서 데이터 복원

실수로 데이터를 삭제하거나 수정하는 경우는 다음 시나리오 중 하나에서 발생할 수 있습니다.  

* 전체 Azure Cosmos 계정이 삭제됨

* 둘 이상의 Azure Cosmos 데이터베이스가 삭제됨

* 둘 이상의 Azure Cosmos 컨테이너가 삭제됨

* 컨테이너 내 Azure Cosmos 항목(예: 문서)이 삭제되거나 수정됨. 이 특정 사례를 일반적으로 “데이터 손상”이라고 합니다.

* 공유 제품 데이터베이스나 공유 제품 데이터베이스 내 컨테이너가 삭제되거나 손상됨

Azure Cosmos DB는 위의 모든 시나리오에서 데이터를 복원할 수 있습니다. 복원 프로세스에서는 항상 복원된 데이터를 저장할 새 Azure Cosmos 계정이 생성됩니다. 새 계정의 이름은 지정하지 않는 경우 `<Azure_Cosmos_account_original_name>-restored1` 형식이 됩니다. 복원이 여러 번 시도되는 경우 마지막 숫자가 증분됩니다. 미리 생성된 Azure Cosmos 계정으로는 데이터를 복원할 수 없습니다.

Azure Cosmos 계정이 삭제된 경우 계정 이름이 사용 중이 아니라면 동일한 이름의 계정으로 데이터를 복원할 수 있습니다. 이러한 경우 삭제 후 계정을 다시 만들지 않는 것이 좋습니다. 복원된 데이터가 동일한 이름을 사용하지 않도록 방지할 뿐 아니라 복원할 올바른 계정을 확인하는 데도 수월하기 때문입니다. 

Azure Cosmos 데이터베이스가 삭제되는 경우 전체 데이터베이스 또는 해당 데이터베이스 내 컨테이너의 하위 세트를 복원할 가능성이 있습니다. 데이터베이스에서 컨테이너를 선택하여 복원하면 복원된 모든 데이터는 새 Azure Cosmos 계정에 배치될 수 있습니다.

컨테이너 내 둘 이상의 항목이 실수로 삭제되거나 변경된 경우(데이터 손상 사례) 복원할 시간을 지정해야 합니다. 이 경우 시간이 핵심입니다. 컨테이너는 실시간이기 때문에 백업은 여전히 실행 중이므로 보존 기간(기본값은 8시간) 이상 대기하는 경우 백업은 덮어쓰기됩니다. 삭제의 경우 데이터는 백업 주기에 의해 덮어쓰기되지 않으므로 더 이상 저장되지 않습니다. 삭제된 데이터베이스 또는 컨테이너에 대한 백업은 30일 동안 저장됩니다.

데이터베이스 수준에서 처리량을 프로비전하는 경우(즉, 여기서 컨테이너의 세트는 프로비전된 처리량을 공유) 이 경우의 백업 및 복원 프로세스는 개별 컨테이너 수준이 아닌 전체 데이터베이스 수준에서 발생합니다. 이러한 경우 복원할 컨테이너의 하위 세트를 선택하는 것을 옵션이 아닙니다.

## <a name="migrating-data-to-the-original-account"></a>원래 계정으로 데이터 마이그레이션

데이터 복원의 기본적인 목표는 실수로 삭제 또는 수정한 모든 데이터를 복구하는 방법을 제공하는 것입니다. 따라서 예상하는 것을 포함하도록 하려면 복구된 데이터의 콘텐츠를 먼저 검사하는 것이 좋습니다. 그런 다음, 기본 계정으로 데이터를 마이그레이션하는 작업을 수행합니다. 복원된 계정을 실시간 계정으로 사용할 수 있지만, 프로덕션 워크로드가 있는 경우에는 권장되지 않습니다.  

다음은 원래 Azure Cosmos 계정으로 다시 데이터를 마이그레이션하는 다양한 방법입니다.

* [Cosmos DB 데이터 마이그레이션 도구](import-data.md) 사용
* [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md) 사용
* Azure Cosmos DB의 [변경 피드](change-feed.md) 사용 
* 사용자 지정 코드 쓰기

복원된 계정은 계속 요금이 부과되므로 마이그레이션이 완료되는 즉시 삭제합니다.

## <a name="next-steps"></a>다음 단계

다음으로 Azure Cosmos 계정의 데이터를 복원하는 방법 또는 Azure Cosmos 계정으로 데이터를 마이그레이션하는 방법을 알아봅니다

* 복원 요청을 수행하려면 Azure 지원에 문의하여 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
* [Azure Cosmos 계정에서 데이터를 복원하는 방법](how-to-backup-and-restore.md)
* [Cosmos DB 변경 피드를 사용](change-feed.md)하여 Azure Cosmos DB로 데이터를 이동합니다.
* [Azure Data Factory를 사용](../data-factory/connector-azure-cosmos-db.md)하여 데이터를 Azure Cosmos DB로 이동합니다.

