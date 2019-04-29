---
title: Azure Database for MySQL의 읽기 복제본
description: 이 문서에서는 Azure Database for MySQL의 읽기 복제본에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093219"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL의 읽기 복제본

읽기 복제본 기능을 사용하면 동일한 Azure 지역 내에서 Azure Database for MySQL 서버(마스터)의 데이터를 최대 5개의 읽기 전용 서버(복제본)로 복제할 수 있습니다. 읽기 전용 복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

Azure Database for MySQL 서비스에서 만든 복제본은 일반/독립형 MySQL 서버와 같은 방식으로 관리할 수 있는 새로운 서버입니다. 각 읽기 복제본의 경우 vCore에서 프로비전된 컴퓨팅 및 프로비전된 스토리지에 대한 요금이 GB/월 단위로 청구됩니다.

MySQL 복제 기능 및 문제에 대한 자세한 내용은 [MySQL 복제 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)를 참조하세요.

## <a name="when-to-use-read-replicas"></a>읽기 복제본을 사용하면 적절한 경우

읽기 집약적인 애플리케이션 및 워크로드는 읽기 전용 복제본으로 처리할 수 있습니다. 읽기 복제본은 읽기 및 쓰기 둘 다에 단일 서버만 사용하는 경우와 비교할 때 사용 가능한 읽기 용량을 높이는 데 도움이 됩니다. 읽기 워크로드는 복제본으로 분리될 수 있으며 쓰기 워크로드를 마스터로 보내질 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

### <a name="pricing-tiers"></a>가격 책정 계층

읽기 복제본은 범용 및 메모리 최적화 가격 책정 계층에서만 사용할 수 있습니다.

### <a name="master-server-restart"></a>마스터 서버 다시 시작

기존 복제본이 없는 있는 마스터에 대 한 복제본을 만들 때 마스터 복제를 위한 준비를 먼저 다시 시작 됩니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

### <a name="stopping-replication"></a>복제 중지

마스터 및 복제본 서버 간의 복제를 중지하도록 선택할 수 있습니다. 복제를 중지하면 마스터 및 복제본 서버 간의 복제 관계가 제거됩니다.

복제가 중지되면 복제본 서버는 독립형 서버가 됩니다. 독립형 서버의 데이터는 "복제 중지" 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립형 서버는 마스터 서버를 따르지 않습니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

### <a name="replicas-are-new-servers"></a>복제본은 새로운 서버임

복제본은 새로운 Azure Database for MySQL 서버로 생성됩니다. 기존 서버는 복제본으로 만들 수 없습니다.

### <a name="replica-server-configuration"></a>복제본 서버 구성

복제본 서버는 다음 구성을 포함하여 마스터와 동일한 서버 구성으로 생성됩니다.

- 가격 책정 계층 
- 계산 세대
- vCore 수
- Storage
- Backup 보존 기간
- 백업 중복 옵션
- MySQL 엔진 버전
- 방화벽 규칙

복제본을 만든 후에는 마스터 서버와는 별도로 가격 책정 계층(기본 가격 책정 계층에서 이 계층으로 또는 그 반대로 변경하는 경우는 제외), 계산 생성, vCores, 스토리지 및 백업 보존을 변경할 수 있습니다.

### <a name="master-server-configuration"></a>마스터 서버 구성

마스터의 서버 구성(예: vCores 또는 저장소)이 업데이트되면 복제본 구성은 같거나 더 큰 값으로 업데이트되어야 합니다. 이렇게 하지 않으면 복제본 서버가 마스터에 대한 변경을 따라가지 못하여 결과적으로 충돌할 수 있습니다.

복제본 서버를 만든 후에 마스터 서버에 추가된 새 방화벽 규칙은 복제본에 복제되지 않습니다. 복제본은 새 방화벽 규칙에서도 업데이트되어야 합니다.

### <a name="deleting-the-master-server"></a>마스터 서버 삭제

마스터 서버가 삭제되면 모든 읽기 복제본에 대한 복제가 중지됩니다. 이러한 복제본은 독립형 서버가 됩니다. 마스터 서버 자체는 삭제됩니다.

### <a name="user-accounts"></a>사용자 계정

마스터 서버의 사용자는 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용하여 읽기 복제본에만 연결할 수 있습니다.

### <a name="other"></a>기타

- GTID(전역 트랜잭션 식별자)는 지원되지 않습니다.
- 복제본의 복제본 만들기는 지원되지 않습니다.
- 메모리 내 테이블이 있으면 복제본이 동기화되지 않을 수 있기 때문입니다. 이것은 MySQL 복제 기술의 제한입니다. 자세한 내용은 [MySQL 참조 문서](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)를 참조하세요.
- 복제본 서버를 만든 후 마스터 서버에서 [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 매개 변수를 조정하면 복제본이 동기화되지 않을 수 있습니다. 복제본 서버는 다른 테이블스페이스를 인식하지 못합니다.
- 마스터 서버 테이블에 기본 키가 있는지 확인합니다. 기본 키가 없는 경우 마스터 및 복제본 간의 복제 대기 시간이 발생할 수 있습니다.
- [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)에서 MySQL 복제 제한 사항의 전체 목록을 검토하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-portal.md) 방법 알아보기
- [Azure CLI를 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-cli.md) 방법 알아보기
