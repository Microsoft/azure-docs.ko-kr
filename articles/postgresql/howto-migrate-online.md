---
title: Azure Database for PostgreSQL - 단일 서버로 최소 가동 중지 시간 마이그레이션
description: 이 문서에서는 Azure Database Migration Service를 사용하여 Azure Database for PostgreSQL - 단일 서버로 PostgreSQL 데이터베이스의 최소 가동 중지 시간 마이그레이션을 수행하는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489798"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버로 최소 가동 중지 시간 마이그레이션
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

새로 도입된 [Azure Database Migration Service](https://aka.ms/get-dms)(DMS)에 대한 **지속적인 동기화 기능** 을 사용하여 최소 가동 중지 시간으로 Azure Database for PostgreSQL로 PostgreSQL 마이그레이션을 수행할 수 있습니다. 이 기능은 애플리케이션에서 발생하는 가동 중지 시간을 제한합니다.

## <a name="overview"></a>개요
Azure DMS는 Azure Database for PostgreSQL에 대한 온-프레미스 초기 로드를 수행한 다음, 애플리케이션이 실행되는 동안 계속해서 모든 새 트랜잭션을 Azure와 동기화합니다. 데이터가 Azure 쪽 대상을 처리한 후 잠시(최소 가동 중지 시간) 동안 애플리케이션을 중지하고, 대상을 처리하기 위해 데이터의 마지막 일괄 처리(애플리케이션을 중지한 때부터 애플리케이션이 모든 새 트래픽을 효과적으로 사용할 수 없을 때까지)를 기다린 다음, Azure를 가리키도록 연결 문자열을 업데이트합니다. 완료하면 애플리케이션이 Azure에 게시됩니다!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Azure Database Migration Service와 지속적인 동기화":::

## <a name="next-steps"></a>다음 단계
- PostgreSQL 앱을 Azure Database for PostgreSQL로 마이그레이션하는 방법을 보여주는 데모 버전이 포함된 [Microsoft Azure를 사용한 앱 현대화](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102) 비디오를 봅니다.
- [DMS를 사용하여 PostgreSQL을 Azure Database for PostgreSQL로 온라인 마이그레이션](../dms/tutorial-postgresql-azure-postgresql-online.md) 자습서를 참조하세요.