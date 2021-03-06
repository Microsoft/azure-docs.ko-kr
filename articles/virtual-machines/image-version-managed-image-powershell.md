---
title: 공유 이미지 갤러리에 관리 되는 이미지 복제
description: Azure PowerShell를 사용 하 여 공유 이미지 갤러리의 이미지 버전에 관리 되는 이미지를 복제 하는 방법에 대해 알아봅니다.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556983"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>공유 이미지 갤러리 이미지에 관리 되는 이미지 복제

복제 하 고 공유 이미지 갤러리로 이동 하려는 기존 관리 이미지가 있는 경우 관리 되는 이미지에서 직접 공유 이미지 갤러리 이미지를 만들 수 있습니다. 새 이미지를 테스트 한 후에는 원본 관리 이미지를 삭제할 수 있습니다. [Azure CLI](image-version-managed-image-cli.md)를 사용 하 여 관리 되는 이미지에서 공유 이미지 갤러리로 마이그레이션할 수도 있습니다.

이미지 갤러리의 이미지에는 다음 예제에서 만들 두 가지 구성 요소가 있습니다.
- 이미지 **정의** 는 이미지 및 사용에 대 한 요구 사항에 대 한 정보를 전달 합니다. 여기에는 이미지가 Windows 또는 Linux 인지, 특수 하거나 일반화 되었는지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함 됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 공유 이미지 갤러리를 사용할 때 VM을 만드는 데 사용 됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 만들 때 이미지 버전은 VM에 대 한 새 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 관리 되는 기존 이미지가 있어야 합니다. 관리 되는 이미지에 데이터 디스크가 포함 되어 있는 경우 데이터 디스크 크기는 1TB를 넘을 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="get-the-gallery"></a>갤러리 가져오기

모든 갤러리 및 이미지 정의를 이름으로 나열할 수 있습니다. 결과는 형식 `gallery\image definition\image version` 입니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

오른쪽 갤러리를 찾았으면 나중에 사용할 변수를 만듭니다. 이 예제에서는 *Mygallery* 리소스 그룹에서 *mygallery* 라는 갤러리를 가져옵니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이미지에 대 한 정보를 관리 하는 데 사용 됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때에 올바른 정보가 모두 있는지 확인 합니다. 관리 이미지는 항상 일반화 되므로를 설정 해야 `-OsState generalized` 합니다. 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 이 예제에서 이미지 정의 이름은 *Myimagedefinition* 이며 일반화 된 Windows OS를 위한 것입니다. Linux OS를 사용 하 여 이미지에 대 한 정의를 만들려면를 사용 `-OsType Linux` 합니다. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>관리되는 이미지 가져오기

[Get-AzImage](/powershell/module/az.compute/get-azimage)를 사용하여 리소스 그룹에서 사용할 수 있는 이미지 목록을 볼 수 있습니다. 이미지 이름과 해당 이미지가 어떤 리소스 그룹에 들어 있는지 알 수 있으면 `Get-AzImage`를 다시 사용하여 이미지 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *myImage* 라는 이미지를 가져온 후 변수 *$managedImage* 에 할당합니다. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>이미지 버전 만들기

[AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)를 사용 하 여 관리 되는 이미지에서 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위한 대상 영역을 선택할 때 *원본* 지역을 복제 대상으로 포함 해야 합니다. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 진행률을 보려면를 입력 `$job.State` 합니다.

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다. 
>
> `-StorageAccountType Premium_LRS`이미지 버전을 만들 때를 추가 하 여, 또는 [영역 중복 저장소](../storage/common/storage-redundancy.md) 를 추가 하 여 Premium storage에 이미지를 저장할 수도 있습니다 `-StorageAccountType Standard_ZRS` .
>

## <a name="delete-the-managed-image"></a>관리 되는 이미지 삭제

새 이미지 버전이 제대로 작동 하는지 확인 한 후 관리 되는 이미지를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

복제가 완료 되었는지 확인 한 후 [일반화 된 이미지](vm-generalized-image-version-powershell.md)에서 VM을 만들 수 있습니다.

구매 계획 정보를 제공 하는 방법에 대 한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조 하세요.