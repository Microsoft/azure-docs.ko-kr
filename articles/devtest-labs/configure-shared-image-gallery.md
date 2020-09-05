---
title: Azure DevTest Labs |에서 공유 이미지 갤러리 구성 Microsoft Docs
description: 사용자가 랩 리소스를 만드는 동안 공유 위치에서 이미지에 액세스할 수 있도록 하는 Azure DevTest Labs에서 공유 이미지 갤러리를 구성 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7ec08fa741c1b52d3dd1d1e2b4247d3689190020
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271042"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 이미지 갤러리 구성
DevTest Labs는 이제 [공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md) 기능을 지원 합니다. 랩 사용자는 랩 리소스를 만드는 동안 공유 위치에서 이미지에 액세스할 수 있습니다. 또한 사용자 지정 관리 VM 이미지를 중심으로 구조와 조직을 구축할 수 있습니다. 공유 이미지 갤러리 기능은 다음을 지원 합니다.

- 이미지의 관리 되는 전역 복제
- 보다 쉽게 관리할 수 있도록 이미지의 버전 관리 및 그룹화
- 가용성 영역을 지 원하는 지역에서 ZRS (영역 중복 저장소) 계정을 사용 하 여 이미지를 항상 사용할 수 있도록 합니다. ZRS는 영역 장애 발생 시 보다 나은 복원력을 제공합니다.
- RBAC (역할 기반 액세스 제어)를 사용 하 여 구독 간 및 테 넌 트 간에도 공유 합니다.

자세한 내용은 [공유 이미지 갤러리 설명서](../virtual-machines/windows/shared-image-galleries.md)를 참조 하세요. 
 
유지 관리해야 하는 관리되는 이미지가 많고, 회사 전체에서 사용 가능하도록 하려면 공유 이미지 갤러리를 이미지를 쉽게 업데이트하고 공유할 수 있게 해주는 리포지토리로 사용할 수 있습니다. 랩 소유자는 기존 공유 이미지 갤러리를 랩에 연결할 수 있습니다. 이 갤러리가 연결 되 면 랩 사용자는 이러한 최신 이미지에서 컴퓨터를 만들 수 있습니다. 이 기능의 주요 이점은 DevTest Labs에서 랩, 구독 및 지역에 걸쳐 이미지를 공유 하는 이점을 얻을 수 있다는 것입니다. 

> [!NOTE]
> 공유 이미지 갤러리 서비스와 관련 된 비용에 대해 알아보려면 [공유 이미지 갤러리에 대 한 요금 청구](../virtual-machines/windows/shared-image-galleries.md#billing)를 참조 하세요.

## <a name="considerations"></a>고려 사항
- 한 번에 하나의 공유 이미지 갤러리만 랩에 연결할 수 있습니다. 다른 갤러리를 연결 하려면 기존 항목을 분리 하 고 다른 갤러리를 연결 해야 합니다. 
- DevTest Labs는 현재 랩을 통해 갤러리에 이미지를 업로드 하는 것을 지원 하지 않습니다. 
- 공유 이미지 갤러리 이미지를 사용 하 여 가상 컴퓨터를 만드는 동안 DevTest Labs는 항상이 이미지의 최신 게시 된 버전을 사용 합니다. 그러나 이미지에 여러 버전이 있는 경우 사용자는 가상 컴퓨터를 만드는 동안 고급 설정 탭으로 이동 하 여 이전 버전에서 컴퓨터를 만들도록 선택할 수 있습니다.  
- DevTest Labs는 공유 이미지 갤러리에서 랩을 존재 하는 영역에 이미지를 복제 하도록 자동으로 시도 하지만 항상 가능한 것은 아닙니다. 이러한 이미지에서 Vm을 만드는 데 문제가 있는 사용자를 방지 하려면 이미지가 랩의 지역에 이미 복제 되어 있는지 확인 합니다. "

## <a name="use-azure-portal"></a>Azure Portal 사용
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 합니다.
1. 목록에서 **DevTest Labs** 를 선택 합니다.
1. 랩 목록에서 **랩을**선택 합니다.
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택 합니다.
1. 왼쪽 메뉴의 **가상 컴퓨터 기반** 에서 **공유 이미지 갤러리** 를 선택 합니다.

    ![공유 이미지 갤러리 메뉴](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **연결** 단추를 클릭 하 고 드롭다운에서 갤러리를 선택 하 여 기존 공유 이미지 갤러리를 랩에 연결 합니다.

    ![연결](./media/configure-shared-image-gallery/attach-options.png)
1. 이미지 갤러리가 연결 된 후에는이를 선택 하 여 연결 된 갤러리로 이동 합니다. VM 생성을 위해 공유 이미지를 **사용 하거나 사용 하지 않도록** 갤러리를 구성 합니다. 목록에서 이미지 갤러리를 선택 하 여 구성 합니다. 

    기본적으로 **모든 이미지를 가상 컴퓨터 기본으로 사용 하도록 허용** 이 **예**로 설정 됩니다. 즉, 연결 된 공유 이미지 갤러리에서 사용할 수 있는 모든 이미지를 랩 사용자가 새 랩 VM을 만들 때 사용할 수 있습니다. 특정 이미지에 대 한 액세스를 제한 해야 하 **는 경우 모든 이미지를 가상 컴퓨터 기반으로 사용 하도록 허용** 을 **아니요**로 변경 하 고 vm을 만들 때 허용 하려는 이미지를 선택한 후 **저장** 단추를 선택 합니다.

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="이미지 사용 또는 사용 안 함":::

    > [!NOTE]
    > 공유 이미지 갤러리에서 일반화 된 이미지와 특수 이미지를 모두 지원 합니다. 
1. 그러면 랩 사용자는 **기본 선택** 페이지에서 **+ 추가** 를 클릭 하 고 이미지를 찾아 사용 이미지를 사용 하 여 가상 컴퓨터를 만들 수 있습니다.

    ![랩 사용자](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>공유 이미지 갤러리를 랩에 연결
Azure Resource Manager 템플릿을 사용 하 여 공유 이미지 갤러리를 랩에 연결 하는 경우 다음 예제와 같이 리소스 관리자 템플릿의 리소스 섹션 아래에 추가 해야 합니다.

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

전체 리소스 관리자 템플릿 예제를 보려면 공개 GitHub 리포지토리에서 다음 리소스 관리자 템플릿 샘플을 참조 하세요. [랩을 만드는 동안 공유 이미지 갤러리를 구성](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)합니다.

## <a name="use-rest-api"></a>REST API 사용

### <a name="get-a-list-of-labs"></a>랩 목록 가져오기 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>랩에 연결 된 공유 이미지 갤러리 목록 가져오기

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>공유 이미지 갤러리 만들기 또는 업데이트

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>공유 이미지 갤러리의 이미지 나열

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>다음 단계
연결 된 공유 이미지 갤러리의 이미지를 사용 하 여 VM을 만드는 방법에 대 한 다음 문서를 참조 하세요. [갤러리에서 공유 이미지를 사용 하 여 Vm 만들기](add-vm-use-shared-image.md)
