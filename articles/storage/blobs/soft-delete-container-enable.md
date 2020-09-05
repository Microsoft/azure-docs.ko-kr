---
title: 컨테이너에 대해 일시 삭제 사용 및 관리 (미리 보기)
titleSuffix: Azure Storage
description: 컨테이너 일시 삭제 (미리 보기)를 사용 하 여 데이터가 잘못 수정 되거나 삭제 될 때 더 쉽게 데이터를 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c2ce40f7d8a8d08e191fce11c98bd780e0c2fda1
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230078"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>컨테이너에 대해 일시 삭제 사용 및 관리 (미리 보기)

컨테이너 일시 삭제 (미리 보기)는 데이터를 실수로 또는 실수로 수정 또는 삭제 하지 않도록 보호 합니다. 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 하면 컨테이너와 해당 콘텐츠가 삭제 된 후 지정 된 보존 기간 내에 복구 될 수 있습니다.

응용 프로그램 또는 다른 저장소 계정 사용자가 실수로 데이터를 수정 하거나 삭제할 수 있는 경우 컨테이너 일시 삭제를 설정 하는 것이 좋습니다. 이 문서에서는 컨테이너에 대해 일시 삭제를 사용 하도록 설정 하는 방법을 보여 줍니다. 미리 보기에 등록 하는 방법을 비롯 하 여 컨테이너 일시 삭제에 대 한 자세한 내용은 [컨테이너의 일시 삭제 (미리 보기)](soft-delete-container-overview.md)를 참조 하세요.

종단 간 데이터 보호를 위해 blob 및 Blob 버전 관리에 대해서도 일시 삭제를 사용 하도록 설정 하는 것이 좋습니다. Blob에 대해 일시 삭제를 사용 하도록 설정 하는 방법을 알아보려면 [blob에 대 한 일시 삭제 설정 및 관리](soft-delete-blob-enable.md)를 참조 하세요. Blob 버전 관리를 사용 하도록 설정 하는 방법을 알아보려면 [blob 버전 관리](versioning-overview.md)를 참조 하세요.

## <a name="enable-container-soft-delete"></a>컨테이너 일시 삭제 사용

언제 든 지 Azure Portal 또는 Azure Resource Manager 템플릿을 사용 하 여 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.
1. **Blob service**에서 **데이터 보호** 설정을 찾습니다.
1. **컨테이너 일시 삭제** 속성을 *사용*으로 설정 합니다.
1. **보존 정책**에서 소프트 삭제 된 컨테이너를 Azure Storage 유지 하는 기간을 지정 합니다.
1. 변경 내용을 저장합니다.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Azure Portal에서 컨테이너 일시 삭제를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용 하 여 컨테이너 일시 삭제를 사용 하도록 설정 하려면 **containerDeleteRetentionPolicy** 속성을 설정 하는 템플릿을 만듭니다. 다음 단계에서는 Azure Portal에서 템플릿을 만드는 방법을 설명 합니다.

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
1.  **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.
1.  **템플릿 배포**를 선택 하 고 **만들기**를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드**를 선택 합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣습니다.  `<account-name>`   자리 표시자를 사용자의 저장소 계정 이름으로 바꿉니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. 보존 기간을 지정 합니다. 기본값은 7입니다.
1. 템플릿을 저장하는 경우
1. 계정에 대 한 리소스 그룹을 지정한 다음 **검토 + 만들기**   단추를 선택 하 여 템플릿을 배포 하 고 컨테이너 일시 삭제를 사용 하도록 설정 합니다.

## <a name="view-soft-deleted-containers"></a>일시 삭제 된 컨테이너 보기

일시 삭제를 사용 하도록 설정 하면 Azure Portal에서 일시 삭제 된 컨테이너를 볼 수 있습니다. 일시 삭제 된 컨테이너는 지정 된 보존 기간 동안 표시 됩니다. 보존 기간이 만료 된 후에는 일시 삭제 된 컨테이너가 영구적으로 삭제 되 고 더 이상 표시 되지 않습니다.

Azure Portal에서 일시 삭제 된 컨테이너를 보려면 다음 단계를 수행 합니다.

1. Azure Portal의 저장소 계정으로 이동 하 여 컨테이너의 목록을 확인 합니다.
1. 삭제 된 컨테이너 표시 스위치를 설정/해제 하 여 목록에 삭제 된 컨테이너를 포함 합니다.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Azure Portal에서 일시 삭제 된 컨테이너를 보는 방법을 보여 주는 스크린샷":::

## <a name="restore-a-soft-deleted-container"></a>일시 삭제 된 컨테이너 복원

보존 기간 내에 일시 삭제 된 컨테이너 및 해당 콘텐츠를 복원할 수 있습니다. Azure Portal에서 일시 삭제 된 컨테이너를 복원 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 저장소 계정으로 이동 하 여 컨테이너의 목록을 확인 합니다.
1. 삭제 하려는 컨테이너의 상황에 맞는 메뉴를 표시 하 고 메뉴에서 삭제 **취소** 를 선택 합니다.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Azure Portal에서 일시 삭제 된 컨테이너를 복원 하는 방법을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [컨테이너에 대 한 일시 삭제 (미리 보기)](soft-delete-container-overview.md)
- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [Blob 버전 관리](versioning-overview.md)