---
title: '알려진 문제: Oracle에서 Azure Database for PostgreSQL로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 Oracle에서 Azure Database for PostgreSQL 단일 서버로의 온라인 마이그레이션과의 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 0a1f46698ddb966c315d08a794dd710a74295f97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437808"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Oracle의 온라인 마이그레이션과 PostgreSQL 용 Azure DB-단일 서버에 대한 알려진 문제/마이그레이션 제한 사항

Oracle에서 Azure Database for PostgreSQL 단일 서버로의 온라인 마이그레이션과 관련 된 알려진 문제 및 제한 사항은 다음 섹션에 설명 되어 있습니다.

## <a name="oracle-versions-supported-as-a-source-database"></a>원본 데이터베이스로 지원 되는 Oracle 버전

Azure Database Migration Service에 대한 연결을 지원 합니다.

- Oracle 버전 10g, 11g 및 12c.
- Oracle Enterprise, Standard, Express 및 Personal Edition이 있습니다.

Azure Database Migration Service는 CDBs (다중 테넌트 컨테이너 데이터베이스) 연결을 지원 하지 않습니다.

## <a name="postgresql-versions-supported-as-a-target-database"></a>대상 데이터베이스로 지원 되는 PostgreSQL 버전

Azure Database Migration Service는 Azure Database for PostgreSQL 단일 서버 버전 9.5, 9.6, 10 및 11로의 마이그레이션을 지원 합니다. Azure Database for PostgreSQL-단일 서버에서 버전 지원에 대한 현재 정보는 [지원 되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조 하세요.

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

다음 데이터 형식은 마이그레이션되지 **않습니다** .

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 중첩 테이블
- 사용자 정의 데이터 형식
- 메모
- 가상 열
- ROWID 열을 기반으로 구체화 된 뷰

또한 빈 BLOB/CLOB 열은 대상에서 NULL로 매핑됩니다.

## <a name="lob-limitations"></a>LOB 제한 사항

- 제한 된 크기의 LOB 모드가 설정 된 경우 Oracle 원본의 빈 Lob는 NULL 값으로 복제 됩니다.
- Long 개체 이름 (30 바이트 이상)은 지원 되지 않습니다.
- LONG 및 LONG 원시 열의 데이터는 64k를 초과할 수 없습니다. 64k가 넘는 데이터는 잘립니다.
- Oracle 12 에서만 LOB 열에 대한 변경 내용은 지원 되지 않습니다 (마이그레이션).
- XMLTYPE 및 LOB 열에 대한 업데이트는 지원 되지 않습니다 (마이그레이션).

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

- 고객은 SYSDBA를 사용 하 여 Oracle에 연결 해야 합니다.
- 파티션/하위 파티션 작업 (ADD, DROP, EXCHANGE 및 TRUNCATE)으로 인해 발생 하는 데이터 변경 내용은 마이그레이션되지 않으며 다음과 같은 오류가 발생할 수 있습니다.
  - 추가 작업의 경우 추가 된 데이터에 대한 업데이트 및 삭제는 "적용 된 행 0 개" 경고를 반환할 수 있습니다.
  - DROP 및 TRUNCATE 작업의 경우 새 삽입으로 인해 "중복" 오류가 발생할 수 있습니다.
  - EXCHANGE 작업의 경우 "0 개의 행이 영향을 받음" 경고 및 "중복" 오류가 발생할 수 있습니다.
- 이름이 아포스트로피를 포함 하는 테이블은 복제할 수 없습니다.
