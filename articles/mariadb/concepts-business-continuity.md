---
title: Azure Database for MariaDB의 비즈니스 연속성 개요
description: Azure Database for MariaDB의 비즈니스 연속성 개요입니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043019"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Azure Database for MariaDB의 비즈니스 연속성 개요

이 개요에서는 Azure Database for MariaDB에서 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 유발하거나 데이터베이스 및 애플리케이션을 사용할 수 없게 될 수 있는 중단 이벤트로부터 복구하는 옵션에 대해 알아봅니다. 사용자 또는 애플리케이션 오류가 데이터 무결성에 영향을 주거나, Azure 지역에 가동 중단이 발생하거나, 애플리케이션에 유지 관리가 필요할 때 수행할 작업을 알아봅니다.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>비즈니스 연속성을 제공하는 데 사용할 수 있는 기능

Azure Database for MariaDB에는 자동화된 백업 및 사용자가 지역 복원을 시작할 수 있는 기능을 포함하는 비즈니스 연속성 기능이 제공됩니다. 이러한 기능은 ERT(예상 복구 시간) 및 잠재적 데이터 손실에 대해 각기 다른 특성이 있습니다. 이러한 옵션을 이해하면 적절한 옵션을 선택하여 다양한 시나리오에 함께 사용할 수 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트 후 애플리케이션이 완벽하게 복구되기까지 허용되는 최대 시간 즉, RTO(복구 시간 목표)를 이해해야 합니다. 중단 이벤트 후 복구될 때 애플리케이션이 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 크기(시간 간격)인 RPO(복구 지점 목표)도 이해해야 합니다.

다음 표에서는 ERT와 RPO에서 사용 가능한 기능을 비교합니다.

| **기능** | **Basic** | **범용** | **메모리에 최적화** |
| :------------: | :-------: | :-----------------: | :------------------: |
| 백업에서 특정 시점 복원 | 보존 기간 내 모든 복원 지점 | 보존 기간 내 모든 복원 지점 | 보존 기간 내 모든 복원 지점 |
| 지리적으로 복제된 백업에서 지역 복원 | 지원되지 않음 | ERT < 12시간<br/>RPO < 1시간 | ERT < 12시간<br/>RPO < 1시간 |

> [!IMPORTANT]
> 서버를 삭제하면 해당 서버에 포함된 모든 데이터베이스도 삭제되고 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.

## <a name="recover-a-server-after-a-user-or-application-error"></a>사용자 또는 애플리케이션 오류가 발생한 후 서버 복구

서비스의 백업을 사용하여 다양한 중단 이벤트에서 서버를 복구할 수 있습니다. 사용자가 실수로 데이터를 삭제할 수도 있고, 의도치 않게 중요한 테이블을 삭제할 수도 있고, 전체 데이터베이스를 삭제할 수도 있습니다. 애플리케이션에서 결함으로 인해 양호한 데이터를 잘못된 데이터로 덮어쓸 수도 있습니다.

특정 시점 복원을 수행하면 알려진 특정 시점에서 서버의 복사본을 만들 수 있습니다. 이 특정 시점은 서버에 대해 구성한 백업 보존 기간 내에 있어야 합니다. 데이터가 새 서버로 복원된 후에는 원래 서버를 새로 복원된 서버로 바꾸거나 복원된 서버에서 원래 서버로 필요한 데이터를 복사할 수 있습니다.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Azure 지역 데이터 센터 중단에서 복구

드문 경우지만 Azure 데이터 센터에서 가동 중단이 발생할 수 있습니다. 중단이 발생하면 비즈니스 중단이 몇 분만 지속될 수도 있지만 몇 시간 동안 지속될 수도 있습니다.

한 가지 옵션은 데이터 센터 중단이 끝날 때 서버가 온라인 상태가 되기까지 기다리는 것입니다. 이것은 개발 환경처럼 일정 기간 동안 서버를 오프라인 상태로 유지할 수 있는 애플리케이션에 적합합니다. 데이터 센터가 중단되면 중단이 얼마나 지속될지 알 수 없으므로, 이 옵션은 한동안 서버가 필요 없는 경우에만 적합합니다.

다른 옵션은 지역 중복 백업을 사용하여 서버를 복원하는 Azure Database for MariaDB의 지역 복원 기능을 사용하는 것입니다. 이러한 백업은 서버를 호스트하는 지역이 오프라인인 경우에도 액세스가 가능합니다. 이러한 백업에서 다른 지역으로 복원하여 서버를 다시 온라인 상태로 만들 수 있습니다.

> [!IMPORTANT]
> 지역 복원은 지역 중복 백업 저장소로 서버를 프로비전한 경우에만 가능합니다.

## <a name="next-steps"></a>다음 단계

- 자동화된 백업에 대해 자세히 알아보려면 [Azure Database for MariaDB의 백업](concepts-backup.md)을 참조하세요.
- Azure Portal을 사용하여 특정 시점으로 복원하려면  [Azure Portal을 사용하여 특정 시점으로 데이터베이스 복원](howto-restore-server-portal.md)을 참조하세요.

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
