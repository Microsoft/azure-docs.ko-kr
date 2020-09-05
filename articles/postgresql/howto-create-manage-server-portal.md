---
title: Azure Database for PostgreSQL 관리-Azure Portal
description: Azure Portal에서 Azure Database for PostgreSQL 서버를 관리 하는 방법을 알아봅니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 908a61a00f0e33016074a6f985271ac94157fdf4
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854989"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL 서버 관리

이 문서에서는 Azure Database for PostgreSQL 서버를 관리 하는 방법을 보여 줍니다. 관리 작업에는 계산 및 저장소 크기 조정, 관리자 암호 재설정 및 서버 세부 정보 보기가 포함 됩니다.

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-server"></a>서버 만들기

Azure Database for PostgreSQL 서버를 만들고 시작 하는 방법을 알아보려면 [빠른](quickstart-create-server-database-portal.md) 시작을 참조 하세요.

## <a name="scale-compute-and-storage"></a>계산 및 저장소 크기 조정

서버를 만든 후에는 요구 사항이 변경 됨에 따라 범용 및 메모리 액세스에 최적화 된 계층 사이를 확장할 수 있습니다. VCores를 늘리거나 줄여서 계산 및 메모리를 확장할 수도 있습니다. 저장소를 확장할 수 있습니다 (그러나 저장소 크기를 줄일 수 없음).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>범용 및 메모리 액세스에 최적화 된 계층 간의 크기 조정

범용에서 메모리 액세스에 최적화 된 메모리 또는 그 반대로 확장할 수 있습니다. 서버를 만든 후 기본 계층에서로의 변경은 지원 되지 않습니다.

1. Azure Portal에서 서버를 선택 합니다. **설정** 섹션에 있는 **가격 책정 계층**을 선택 합니다.

2. 크기를 조정 하는 대상에 따라 **범용** 또는 **메모리 최적화**를 선택 합니다.

   ![Azure Database for PostgreSQL에서 기본, 범용 또는 메모리 액세스에 최적화 된 계층을 선택 하는 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/change-pricing-tier.png)

   > [!NOTE]
   > 계층을 변경 하면 서버가 다시 시작 됩니다.

3. **확인** 을 선택 하 여 변경 내용을 저장 합니다.

### <a name="scale-vcores-up-or-down"></a>VCores 확장 또는 축소

1. Azure Portal에서 서버를 선택 합니다. **설정** 섹션에 있는 **가격 책정 계층**을 선택 합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

   ![Azure Database for PostgreSQL에서 vCore 옵션을 선택할 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/scaling-compute.png)

   > [!NOTE]
   > VCores 크기를 조정 하면 서버를 다시 시작 합니다.

3. **확인** 을 선택 하 여 변경 내용을 저장 합니다.

### <a name="scale-storage-up"></a>저장소 크기 조정

1. Azure Portal에서 서버를 선택 합니다. **설정** 섹션에 있는 **가격 책정 계층**을 선택 합니다.

2. 슬라이더를 원하는 값으로 이동 하 여 **저장소** 설정을 변경 합니다.

   ![Azure Database for PostgreSQL에서 저장소 크기 조정을 선택 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/scaling-storage.png)

   > [!NOTE]
   > 저장소 크기를 축소할 수 없습니다.

3. **확인** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="update-admin-password"></a>관리자 암호 업데이트

Azure Portal를 사용 하 여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택 합니다. **개요** 창에서 **암호 재설정**을 선택 합니다.

   ![Azure Database for PostgreSQL에서 암호를 다시 설정 하 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 대 한 메시지가 표시 됩니다.

   ![암호를 다시 설정 하 고 Azure Database for PostgreSQL 저장 하는 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/reset-password.png)

3. **확인** 을 선택 하 여 새 암호를 저장 합니다.

## <a name="delete-a-server"></a>서버 삭제

서버를 더 이상 필요 하지 않은 경우 삭제할 수 있습니다. 

1. Azure Portal에서 서버를 선택 합니다. **개요** 창에서 **삭제**를 선택 합니다.

   ![Azure Database for PostgreSQL에서 서버를 삭제 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/overview-delete.png)

2. 입력 상자에 서버 이름을 입력 하 여 삭제 하려는 서버 인지 확인 합니다.

   ![Azure Database for PostgreSQL에서 서버 삭제를 확인 하는 Azure Portal의 스크린샷](./media/howto-create-manage-server-portal/confirm-delete.png)

   > [!NOTE]
   > 서버 삭제는 취소할 수 없습니다.

3. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [백업 및 서버 복원](howto-restore-server-portal.md) 에 대 한 자세한 정보
- [Azure Database for PostgreSQL의 튜닝 및 모니터링 옵션](concepts-monitoring.md) 에 대해 알아봅니다.
