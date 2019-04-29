---
title: SQL Database 재해 복구 훈련 | Microsoft Docs
description: Azure SQL Database를 사용하여 재해 복구 훈련을 수행하는 방법에 대한 지침 및 모범 사례를 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 5d754ae558d485036a9a55f573a3f40162ed9f84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725434"
---
# <a name="performing-disaster-recovery-drill"></a>재해 복구 훈련 수행

복구 워크플로에 대한 애플리케이션 준비의 유효성 검사를 정기적으로 수행하는 것이 좋습니다. 애플리케이션 동작과 데이터 손실의 영향 및/또는 장애 조치(failover)와 관련된 중단을 검사하는 것은 적절한 엔지니어링 실무입니다. 또한 대부분의 업계 표준에서 비즈니스 연속성 인증의 일부로 요구하는 사항이기도 합니다.

재해 복구 훈련의 수행은 다음으로 구성됩니다.

* 데이터 계층 중단 시뮬레이션
* 복구
* 복구 후 애플리케이션 무결성 검사

[비즈니스 연속성을 위한 애플리케이션 설계](sql-database-business-continuity.md)방법에 따라 연습을 실행하는 워크플로가 달라질 수 있습니다. 이 문서에서는 Azure SQL Database와 관련하여 재해 복구 훈련을 수행하기 위한 모범 사례를 설명합니다.

## <a name="geo-restore"></a>지역 복원

재해 복구 훈련을 수행할 때 잠재적인 데이터 손실을 방지하려면, 프로덕션 환경의 복사본을 만들고 이를 애플리케이션의 장애 조치(failover) 워크플로를 검사하는 데 사용하는 방법으로 훈련을 수행합니다.

### <a name="outage-simulation"></a>중단 시뮬레이션

중단을 시뮬레이트하기 위해 원본 데이터베이스의 이름을 바꿀 수 있습니다. 이와 같이 이름을 변경하면 애플리케이션 연결이 실패합니다.

### <a name="recovery"></a>복구

* [여기](sql-database-disaster-recovery.md)에 설명된 대로 서로 다른 서버에 데이터베이스의 지역 복원을 수행합니다.
* 복구된 데이터베이스에 연결하도록 애플리케이션 구성을 변경하고 [복구 후 데이터베이스 구성](sql-database-disaster-recovery.md) 가이드에 따라 복구를 완료합니다.

### <a name="validation"></a>유효성 검사

복구 후 애플리케이션 무결성 검사(연결 문자열, 로그인, 기본 기능 테스트 또는 기타 표준 애플리케이션 로그아웃 절차의 유효성 검사 부분 포함)로 훈련을 완료합니다.

## <a name="failover-groups"></a>장애 조치(failover) 그룹

장애 조치(failover) 그룹을 사용하여 보호되는 데이터베이스의 경우에는 보조 서버로의 계획된 장애 조치(failover)가 연습에 포함됩니다. 계획된 장애 조치(failover)는 역할이 전환될 때 장애 조치(failover) 그룹의 주 데이터베이스와 보조 데이터베이스가 동기화 상태로 유지되도록 합니다. 계획되지 않은 장애 조치와 달리 이 작업에서는 데이터가 손실되지 않으므로 프로덕션 환경에서 연습을 수행할 수 있습니다.

### <a name="outage-simulation"></a>중단 시뮬레이션

중단을 시뮬레이션하기 위해 데이터베이스에 연결된 웹 애플리케이션 또는 가상 머신을 비활성화할 수 있습니다. 이와 같은 중단 시뮬레이션이 진행되면 웹 클라이언트에 대한 연결이 실패합니다.

### <a name="recovery"></a>복구

* DR 지역의 애플리케이션 구성이 완전히 액세스 가능한 새로운 주 데이터베이스가 될 이전의 보조 데이터베이스를 가리키는지 확인합니다.
* 보조 서버에서 장애 조치(failover) 그룹의 [계획된 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)를 시작합니다.
* [복구 후 데이터베이스 구성](sql-database-disaster-recovery.md) 가이드에 따라 복구를 완료합니다.

### <a name="validation"></a>유효성 검사

복구 후 애플리케이션 무결성 검사(연결, 기본 기능 테스트 또는 기타 훈련 로그아웃에 필요한 유효성 검사 포함)로 훈련을 완료합니다.

## <a name="next-steps"></a>다음 단계

* 비즈니스 연속성 시나리오에 대해 알아보려면 [연속성 시나리오](sql-database-business-continuity.md)를 참조하세요.
* Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-active-geo-replication.md)와 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 참조하세요.
