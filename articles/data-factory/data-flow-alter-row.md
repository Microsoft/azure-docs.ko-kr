---
title: Azure Data Factory 데이터 흐름 Alter 행 변환을 매핑
description: 데이터베이스 대상 변환을 사용 하 여 Azure 데이터 팩터리 매핑 데이터 흐름 변경 행을 업데이트 하는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627119"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter 행 변환

Alter 행 변환을 사용 하 여 행 삽입, 삭제, 업데이트 및 upsert 정책을 설정 합니다. 식으로-일대다 조건을 추가할 수 있습니다. 행 (또는 행) 될 수 있습니다 이러한 각 조건 삽입, 업데이트, 삭제 또는 upsert 합니다. Alter 행 데이터베이스에 대해 DDL 및 DML 작업을 생성할 수 있습니다.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![행 설정을 변경할](media/data-flow/alter-row1.png "행 Alter Settings")

> [!NOTE]
> Alter 행 변환을 데이터 흐름에서 싱크 데이터베이스 에서만 작동 합니다. 작업 (insert, update, delete, upsert) 행에 할당 하는 디버그 세션 중 발생 하지 않습니다. 파이프라인 실행 데이터 흐름 태스크를 추가 하 고 데이터베이스 테이블에서 alter 행 정책을 시행할 수 파이프라인 디버그 또는 트리거를 사용 해야 합니다.

## <a name="view-policies"></a>정책 보기

데이터 흐름 디버깅 모드를 전환 하 고 데이터 미리 보기 창에서 alter 행 정책의 결과 확인 합니다. 변경 행을 데이터 흐름 디버그 모드에서 실행 하는 대상에 대해 DDL 또는 DML 작업 생성 하지 않습니다. 해당 작업을 수행 하려면 파이프라인 내부에서 실행 데이터 흐름 작업 내에서 데이터 흐름을 실행 합니다.

![행 정책을 변경할](media/data-flow/alter-row3.png "행 정책 변경")

이렇게 하면 확인 하 고 해당 조건에 따라 각 행의 상태를 볼 수 있습니다. 작업을 나타내는 데이터 흐름에서 발생 하는 업데이트, 삭제 및 upsert 작업 수행 됩니다 파이프라인 내부에서 데이터 흐름을 실행 하는 경우, 각 삽입 아이콘 나타냅니다 있습니다.

## <a name="sink-settings"></a>싱크 설정

싱크 유형 작동 하려면 Alter 행에 대 한 데이터베이스가 있어야 합니다. 싱크로 설정에서 각 작업 수를 설정 해야 합니다.

![Alter 행 싱크](media/data-flow/alter-row2.png "Alter 행 싱크")

데이터베이스 싱크를 사용 하 여 ADF 데이터 흐름의 기본 동작 행을 삽입 하는 것입니다. 업데이트, upsert, 및도 삭제를 허용 하려는 경우 이러한 동작을 허용 하도록 싱크이 상자도 확인 해야 합니다.

> [!NOTE]
> 에 삽입, 업데이트 또는 upsert 싱크에 대상 테이블의 스키마를 수정 하면 데이터 흐름이 실패 합니다. 데이터베이스에서 대상 스키마를 수정 하려면 싱크에 "테이블 다시 만들기" 옵션을 선택 해야 합니다. 이 삭제 하 고 새 스키마 정의 사용 하 여 테이블을 다시 만듭니다.

## <a name="next-steps"></a>다음 단계

Alter 행 변환 후 하려는 [대상 데이터 저장소로 데이터를 싱크](data-flow-sink.md)합니다.
