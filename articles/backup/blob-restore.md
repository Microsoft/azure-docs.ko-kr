---
title: Azure Blob 복원
description: Azure Blob (미리 보기)을 복원 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745517"
---
# <a name="restore-azure-blobs-in-preview"></a>Azure Blob 복원 (미리 보기)

운영 백업이 구성 된 저장소 계정의 블록 blob은 보존 범위 내의 특정 시점으로 복원할 수 있습니다. 또한 복원 범위를 저장소 계정의 모든 블록 blob 또는 blob의 하위 집합으로 지정할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

- Blob이 같은 저장소 계정으로 복원 됩니다. 따라서 복원 하는 시간 이후에 변경 된 blob을 덮어씁니다.
- 표준 범용 v2 저장소 계정의 블록 blob만 복원 작업의 일부로 복원할 수 있습니다. 추가 blob, 페이지 blob 및 premium 블록 blob은 복원 되지 않습니다.
- 복원 작업이 진행 중인 동안에는 저장소의 blob을 읽거나 쓸 수 없습니다.
- 활성 임대가 있는 blob은 복원할 수 없습니다. 활성 임대가 있는 blob이 복원할 blob 범위에 포함 된 경우 복원 작업이 자동으로 실패 합니다. 복원 작업을 시작 하기 전에 활성 임대를 중단 합니다.
- 스냅숏은 복원 작업의 일부로 생성 되거나 삭제 되지 않습니다. 기본 blob만 이전 상태로 복원 됩니다.
- 컨테이너 **삭제** 작업을 호출 하 여 저장소 계정에서 컨테이너를 삭제 하는 경우 해당 컨테이너는 복원 작업을 통해 복원할 수 없습니다. 전체 컨테이너를 삭제 하는 대신 나중에 복원할 수 있는 경우 개별 blob을 삭제 합니다. 또한 컨테이너를 실수로 삭제 하지 않도록 보호 하기 위해 운영 백업 뿐만 아니라 컨테이너에 대해 일시 삭제를 사용 하도록 설정 하는 것이 좋습니다.
- 모든 제한 사항 및 지원 되는 시나리오는 [지원 매트릭스](blob-backup-support-matrix.md) 를 참조 하세요.

## <a name="restore-blobs"></a>Blob 복원

백업 센터를 통해 복원을 시작할 수 있습니다.

1. 백업 센터에서 위쪽 표시줄의 **복원** 으로 이동 합니다.

    ![백업 센터에서 복원](./media/blob-restore/backup-center-restore.png)

1. **복원 시작** 탭에서 데이터 원본 유형으로 **Azure blob (Azure Storage)** 을 선택 하 고 복원 하려는 **백업 인스턴스** 를 선택 합니다. 여기에 백업 인스턴스는 복원 하려는 blob이 포함 된 저장소 계정입니다.

     ![백업 인스턴스 선택](./media/blob-restore/select-backup-instance.png)

1. **복구 지점 선택** 탭에서 데이터를 복원할 날짜 및 시간을 선택 합니다. 슬라이더를 사용 하 여 복원할 특정 시점을 선택할 수도 있습니다. 날짜 옆의 정보 거품형은 데이터를 복원할 수 있는 유효한 기간을 보여 줍니다. Blob에 대 한 운영 백업은 연속 백업을 사용 하 여 데이터를 복구할 수 있는 요소를 세부적으로 제어할 수 있습니다.

    >[!NOTE]
    > 여기에 표시 된 시간은 현지 시간입니다.

    ![복원에 대 한 날짜 및 시간](./media/blob-restore/date-and-time.png)

1. **매개 변수 복원** 탭에서 접두사 일치를 사용 하 여 저장소 계정, 특정 컨테이너 또는 blob의 하위 집합에 있는 모든 blob을 복원할지 여부를 선택 합니다. 접두사 일치를 사용 하는 경우 접두사 또는 filepaths 범위를 10 개까지 지정할 수 있습니다. 접두사 일치 사용에 대 한 자세한 내용은 [여기](#use-prefix-match-for-restoring-blobs)를 참조 하세요.

    ![복원 매개 변수](./media/blob-restore/restore-parameters.png)

    다음 옵션 중 하나를 선택합니다.

    - **저장소 계정의 모든 Blob 복원**:이 옵션을 사용 하면 선택한 시점으로 다시 롤백하여 저장소 계정의 모든 블록 blob을 복원 합니다. 많은 양의 데이터를 포함 하는 저장소 계정 또는 살펴보기 높은 변동의 경우 복원 하는 데 시간이 더 오래 걸릴 수 있습니다.

    - **선택한 컨테이너 찾아보기 및 복원**:이 옵션을 사용 하면 복원할 컨테이너를 최대 10 개까지 찾아보고 선택할 수 있습니다. 저장소 계정의 컨테이너를 볼 수 있는 권한이 있어야 합니다. 그렇지 않으면 저장소 계정의 콘텐츠를 볼 수 없습니다.

    - **접두사 일치를 사용 하 여 복원할 Blob 선택**:이 옵션을 사용 하면 접두사 일치를 사용 하 여 blob의 하위 집합을 복원할 수 있습니다. 지정 된 시간에 해당 blob을 이전 상태로 되돌리기 위해 단일 컨테이너 내에서 또는 여러 컨테이너에서 사전순으로 최대 10 개의 blob 범위를 지정할 수 있습니다. 유의 해야 할 몇 가지 사항은 다음과 같습니다.

        - 슬래시 (/)를 사용 하 여 blob 접두사에서 컨테이너 이름을 구분할 수 있습니다.
        - 지정 된 범위의 시작이 포함 되어 있지만 지정 된 범위는 배타적입니다.

    접두사를 사용 하 여 blob 범위를 복원 하는 방법에 대 한 자세한 내용은 [이 섹션](#use-prefix-match-for-restoring-blobs)을 참조 하세요.

1. 복원할 blob의 지정을 완료 한 후 **검토 + 복원** 탭으로 이동 하 고 **복원** 을 선택 하 여 복원을 시작 합니다.

1. **복원 추적**: **백업 작업** 보기를 사용 하 여 복원의 세부 정보 및 상태를 추적할 수 있습니다. 이렇게 하려면 **backup Center**  >  **backup 작업** 으로 이동 합니다. 복원이 수행 되는 동안 상태가 **진행 중으로** 표시 됩니다.

    ![백업 작업 탭](./media/blob-restore/backup-jobs.png)

    복원 작업이 성공적으로 완료 되 면 상태가 **완료** 로 변경 됩니다. 복원이 성공적으로 완료 되 면 저장소 계정에서 blob을 다시 읽고 쓸 수 있습니다.

## <a name="additional-topics"></a>추가 항목

### <a name="use-prefix-match-for-restoring-blobs"></a>Blob 복원에 접두사 일치 사용

다음 예제를 참조하세요.

![접두사 일치를 사용 하 여 복원](./media/blob-restore/prefix-match.png)

이미지에 표시 된 복원 작업은 다음 작업을 수행 합니다.

- *Container1* 의 전체 콘텐츠를 복원 합니다.
- *Container2* 에서 *blob5* 를 통해 사전순으로 *blob1.txt* 된 범위에 있는 blob을 복원 합니다. 이 범위는 *blob1.txt*, *blob11*, *blob100*, *blob2* 등의 이름으로 blob을 복원 합니다. 범위 끝은 배타적 이므로 이름이 *blob4* 로 시작 하는 blob을 복원 하지만 이름이 *blob5* 로 시작 하는 blob은 복원 하지 않습니다.
- *Container3* 및 *container4* 의 모든 blob을 복원 합니다. 범위 끝은 배타적 이므로이 범위는 *container5* 을 복원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Blob에 대 한 운영 백업 개요 (미리 보기)](blob-backup-overview.md)
