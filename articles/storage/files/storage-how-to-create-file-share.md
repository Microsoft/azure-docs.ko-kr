---
title: Azure 파일 공유 만들기
titleSuffix: Azure Files
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 Azure 파일 공유를 만드는 방법입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 236134887728ebc3dd4d03fa4c9d9d450b39eac2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930675"
---
# <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure 파일 공유를 만들려면이를 사용 하는 방법에 대 한 세 가지 질문에 답변해 야 합니다.

- **Azure 파일 공유에 대 한 성능 요구 사항은 무엇 인가요?**  
    Azure Files는 하드 디스크 기반 (HDD 기반) 하드웨어에서 호스트 되는 표준 파일 공유 및 반도체 디스크 기반 (SSD 기반) 하드웨어에 호스트 되는 프리미엄 파일 공유를 제공 합니다.

- **어떤 크기의 파일 공유가 필요 한가요?**  
    표준 파일 공유는 최대 100 TiB에 걸쳐 있을 수 있지만이 기능은 기본적으로 사용 하도록 설정 되어 있지 않습니다. 5 TiB 보다 큰 파일 공유가 필요한 경우 저장소 계정에 대 한 큰 파일 공유 기능을 사용 하도록 설정 해야 합니다. 프리미엄 파일 공유는 특별 한 설정 없이 최대 100 TiB 확장 될 수 있지만, 표준 파일 공유와 같이 요금을 지불 하는 대신 프리미엄 파일 공유가 프로 비전 됩니다. 즉, 파일 공유를 훨씬 더 많이 프로 비전 하면 저장소의 총 비용이 증가 합니다.

- **Azure 파일 공유에 대 한 중복성 요구 사항은 무엇 인가요?**  
    표준 파일 공유는 LRS (로컬 중복), ZRS (영역 중복), GRS (지역 중복) 또는 GZRS (지역 중복) 저장소를 제공 하지만, large file share 기능은 로컬 중복 및 영역 중복 파일 공유 에서만 지원 됩니다. 프리미엄 파일 공유는 어떤 형태의 지역 중복도 지원 하지 않습니다.

    프리미엄 파일 공유는 저장소 계정을 제공 하 고 하위 하위 집합 영역에서 영역 중복성을 제공 하는 대부분의 지역에서 로컬 중복성으로 사용할 수 있습니다. 현재 지역에서 프리미엄 파일 공유를 사용할 수 있는지 확인 하려면 Azure에 대 한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 페이지를 참조 하세요. ZRS를 지 원하는 지역에 대 한 자세한 내용은 [중복성 Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조 하세요.

이러한 세 가지 옵션에 대 한 자세한 내용은 [Azure Files 배포 계획](storage-files-planning.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소
- 이 문서에서는 독자들이 이미 Azure 구독을 만들었다고 가정합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure PowerShell을 사용하려면 [최신 버전을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)하세요.
- Azure CLI를 사용하려면 [최신 버전을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)하세요.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
Azure 파일 공유는 저장소의 공유 풀을 나타내는 최상위 개체인 *저장소 계정*에 배포 됩니다. 이 저장소 풀은 여러 파일 공유를 배포 하는 데 사용할 수 있습니다. 

Azure는 고객이 가질 수 있는 다양 한 저장소 시나리오에 대해 여러 유형의 저장소 계정을 지원 하지만 Azure Files에는 두 가지 주요 유형의 저장소 계정이 있습니다. 만드는 데 필요한 저장소 계정 유형은 표준 파일 공유를 만들지, 아니면 프리미엄 파일 공유를 만들지에 따라 결정 됩니다. 

- **GPv2 (범용 버전 2) 저장소 계정**: GPv2 storage 계정을 사용 하면 표준/하드 디스크 기반 (HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. Azure 파일 공유를 저장 하는 것 외에도 GPv2 storage 계정은 blob 컨테이너, 큐 또는 테이블과 같은 다른 저장소 리소스를 저장할 수 있습니다. 

- **FileStorage storage 계정**: FileStorage storage 계정을 사용 하면 프리미엄/반도체 디스크 기반 (SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장 하는 데만 사용할 수 있습니다. FileStorage 계정에는 다른 저장소 리소스 (blob 컨테이너, 큐, 테이블 등)를 배포할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal를 통해 저장소 계정을 만들려면 대시보드에서 **+ 리소스 만들기** 를 선택 합니다. 결과 Azure Marketplace 검색 창에서 **저장소 계정** 을 검색 하 고 결과 검색 결과를 선택 합니다. 그러면 저장소 계정에 대 한 개요 페이지가 나타납니다. **만들기** 를 선택 하 여 저장소 계정 만들기 마법사를 진행 합니다.

![브라우저에서 저장소 계정 빨리 만들기 옵션의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>기본 사항 섹션
저장소 계정 만들기를 완료 하는 첫 번째 섹션에는 **기본**사항이 지정 되어 있습니다. 여기에는 저장소 계정을 만드는 데 필요한 모든 필드가 포함 됩니다. GPv2 저장소 계정을 만들려면 **성능** 라디오 단추가 *표준* 으로 설정 되어 있는지 확인 하 고 **계정 종류** 드롭다운 목록을 *StorageV2 (범용 v2)* 로 선택 합니다.

![StorageV2가 선택 된 표준 및 계정 종류가 선택 된 성능 라디오 단추의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage 저장소 계정을 만들려면 **성능** 라디오 단추가 *프리미엄* 으로 설정 되어 있는지 확인 하 고 **계정 종류** 드롭다운 목록을 *FileStorage*로 선택 합니다.

![프리미엄이 선택 된 성능 라디오 단추의 스크린샷 및 FileStorage가 선택 된 계정 종류](media/storage-how-to-create-file-share/create-storage-account-2.png)

다른 기본 필드는 저장소 계정에 대 한 선택과는 별개입니다.
- **구독**: 배포할 저장소 계정에 대 한 구독입니다. 
- **리소스 그룹**: 배포할 저장소 계정에 대 한 리소스 그룹입니다. 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다.
- **Storage 계정 이름**: 만들 저장소 계정 리소스의 이름입니다. 이 이름은 전역적으로 고유 해야 하지만 그렇지 않은 경우 원하는 이름을 지정할 수 있습니다. 저장소 계정 이름은 SMB를 통해 Azure 파일 공유를 탑재할 때 서버 이름으로 사용 됩니다.
- **Location**: 배포할 저장소 계정의 지역입니다. 리소스 그룹이 나 사용 가능한 다른 지역과 연결 된 지역이 될 수 있습니다.
- **복제**:이 필드는 복제 라고 하지만 실제로는 **중복성**을 의미 합니다. 이는 LRS (로컬 중복), ZRS (영역 중복), GRS (지역 중복) 및 지리적 영역 중복성의 중복성 수준입니다. 이 드롭다운 목록에는 Azure 파일 공유에 적용 되지 않는 읽기 액세스 지역 중복 (GRS) 및 읽기 액세스 지역 중복 (RA-GZRS)도 포함 되어 있습니다. 이를 선택 하 여 저장소 계정에 만들어진 모든 파일 공유는 실제로 지역 중복 또는 지역 중복이 됩니다. 사용자의 지역 또는 선택한 저장소 계정 유형에 따라 일부 중복성 옵션이 허용 되지 않을 수 있습니다.
- **액세스 계층**:이 필드는 Azure Files에는 적용 되지 않으므로 라디오 단추 중 하나를 선택할 수 있습니다.

#### <a name="the-networking-blade"></a>네트워킹 블레이드
네트워킹 섹션에서 네트워킹 옵션을 구성할 수 있습니다. 이러한 설정은 저장소 계정 만들기에 대 한 선택 사항이 며 원할 경우 나중에 구성할 수 있습니다. 이러한 옵션에 대 한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

#### <a name="the-advanced-blade"></a>고급 블레이드
고급 섹션에는 Azure 파일 공유에 대 한 몇 가지 중요 한 설정이 포함 되어 있습니다.

- **보안 전송 필요**:이 필드는 저장소 계정에 대 한 통신을 위해 저장소 계정에 암호화가 필요한 지 여부를 나타냅니다. 이를 사용 하도록 설정 하는 것이 좋습니다. 그러나 SMB 2.1 지원이 필요한 경우이를 사용 하지 않도록 설정 해야 합니다. 저장소 계정에 서비스 끝점 및/또는 개인 끝점을 사용 하 여 가상 네트워크에 대 한 액세스를 제한 하는 암호화를 사용 하지 않도록 설정 하는 것이 좋습니다.
- **대량 파일 공유**:이 필드는 최대 100 TiB을 확장 하는 파일 공유에 대 한 저장소 계정을 사용 하도록 설정 합니다. 이 기능을 사용 하도록 설정 하면 저장소 계정이 로컬 중복 및 영역 중복 저장소 옵션 으로만 제한 됩니다. GPv2 저장소 계정이 대량 파일 공유에 대해 사용 하도록 설정 된 후에는 대량 파일 공유 기능을 사용 하지 않도록 설정할 수 없습니다. 모든 프리미엄 파일 공유가 100 TiB까지 확장 될 수 있으므로 FileStorage storage 계정 (프리미엄 파일 공유용 저장소 계정)이이 옵션을 사용할 수 없습니다. 

![Azure Files에 적용 되는 중요 한 고급 설정의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-3.png)

고급 탭에서 사용할 수 있는 다른 설정 (blob 일시 삭제, Azure Data Lake 저장소의 계층 구조 네임 스페이스 및 blob 저장소에 대 한 NFSv3)은 Azure Files에 적용 되지 않습니다.

#### <a name="tags"></a>태그
태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다. 이러한 항목은 선택 사항이 며 저장소 계정을 만든 후에 적용할 수 있습니다.

#### <a name="review--create"></a>검토 + 만들기
저장소 계정을 만드는 마지막 단계는 **검토 + 만들기** 탭에서 **만들기** 단추를 선택 하는 것입니다. 저장소 계정에 대 한 필수 필드를 모두 채우지 않으면이 단추를 사용할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 저장소 계정을 만들려면 cmdlet을 사용 합니다 `New-AzStorageAccount` . 이 cmdlet에는 많은 옵션이 있습니다. 필요한 옵션만 표시 됩니다. 고급 옵션에 대 한 자세한 내용은 [ `New-AzStorageAccount` cmdlet 설명서](/powershell/module/az.storage/new-azstorageaccount)를 참조 하세요.

저장소 계정 및 후속 파일 공유 만들기를 간소화 하기 위해 변수에 여러 매개 변수를 저장 합니다. 변수 콘텐츠를 원하는 값으로 바꿀 수 있지만 저장소 계정 이름은 전역적으로 고유 해야 합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `-SkuName`매개 변수는 원하는 중복성 유형과 관련이 있습니다. 지역 중복 또는 지역 중복 저장소 계정을 원하는 경우에도 매개 변수를 제거 해야 합니다 `-EnableLargeFileShare` .

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `-SkuName`매개 변수는 `Premium` 와 원하는 중복성 수준 (로컬 중복)을 모두 포함 하도록 변경 되었습니다 `LRS` . `-Kind` `FileStorage` `StorageV2` GPv2 Storage 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 때문에 매개 변수는 대신입니다.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI 사용 하 여 저장소 계정을 만들려면 az storage account create 명령을 사용 합니다. 이 명령에는 많은 옵션이 있습니다. 필요한 옵션만 표시 됩니다. 고급 옵션에 대 한 자세한 내용은 [ `az storage account create` 명령 설명서](/cli/azure/storage/account)를 참조 하세요.

저장소 계정 및 후속 파일 공유 만들기를 간소화 하기 위해 변수에 여러 매개 변수를 저장 합니다. 변수 콘텐츠를 원하는 값으로 바꿀 수 있지만 저장소 계정 이름은 전역적으로 고유 해야 합니다.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `--sku`매개 변수는 원하는 중복성 유형과 관련이 있습니다. 지역 중복 또는 지역 중복 저장소 계정을 원하는 경우에도 매개 변수를 제거 해야 합니다 `--enable-large-file-share` .

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `--sku`매개 변수는 `Premium` 와 원하는 중복성 수준 (로컬 중복)을 모두 포함 하도록 변경 되었습니다 `LRS` . `--kind` `FileStorage` `StorageV2` GPv2 Storage 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 때문에 매개 변수는 대신입니다.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>파일 공유 만들기
저장소 계정을 만든 후에는 파일 공유를 만드는 것만 남았습니다. 이 프로세스는 프리미엄 파일 공유 또는 표준 파일 공유를 사용 하 고 있는지 여부에 관계 없이 거의 동일 합니다. 주요 차이점은 할당량 및 해당 **할당량이** 나타내는 것입니다.

표준 파일 공유의 경우에는 최종 사용자가 이동할 수 없는 Azure 파일 공유의 상한입니다. 표준 파일 공유에 대 한 할당량의 주 목적은 예산: "이 시점 이후에이 파일 공유를 확장 하지 않으려고 함"입니다. 할당량을 지정 하지 않으면 표준 파일 공유는 최대 100 TiB (또는 5 TiB, 저장소 계정에 대 한 대량 파일 공유 속성이 설정 되지 않은 경우)까지 확장 될 수 있습니다.

프리미엄 파일 공유의 경우 할당량은 **프로 비전 된 크기**의 평균으로 오버 로드 됩니다. 프로 비전 된 크기는 실제 사용량에 관계 없이 요금이 청구 되는 금액입니다. 프리미엄 파일 공유를 프로 비전 할 때 두 가지 요인을 고려해 야 합니다. 1) 공간 사용률 관점에서 공유의 향후 증가 및 2) 작업에 필요한 IOPS입니다. 프로 비전 된 모든 GiB는 추가 예약 및 버스트 IOPS를 되며 합니다. 프리미엄 파일 공유를 계획 하는 방법에 대 한 자세한 내용은 [premium 파일 공유 프로 비전](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)을 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)
방금 저장소 계정을 만든 경우 **리소스로 이동**을 선택 하 여 배포 화면에서 이동할 수 있습니다. 이전에 저장소 계정을 만든 경우 해당 계정을 포함 하는 리소스 그룹을 통해 이동할 수 있습니다. 저장소 계정에서 **파일 공유** 라는 타일을 선택 합니다. 저장소 계정에 대 한 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

![파일 공유 타일의 스크린샷](media/storage-how-to-create-file-share/create-file-share-1.png)

파일 공유 목록에 이전에이 저장소 계정에서 만든 파일 공유가 표시 되어야 합니다. 아직 파일 공유가 생성 되지 않은 경우 빈 테이블입니다. **+ 파일 공유** 를 선택 하 여 새 파일 공유를 만듭니다.

새 파일 공유 블레이드가 화면에 표시 됩니다. 새 파일 공유 블레이드의 필드를 완성 하 여 파일 공유를 만듭니다.

- **이름**: 만들 파일 공유의 이름입니다.
- **할당량**: 표준 파일 공유에 대 한 파일 공유의 할당량 프리미엄 파일 공유에 대 한 파일 공유의 프로 비전 된 크기입니다.

**만들기** 를 선택 하 여 새 공유 만들기를 완료 합니다. 저장소 계정이 가상 네트워크에 있는 경우에도 클라이언트가 가상 네트워크에 있는 경우를 제외 하 고는 Azure 파일 공유를 만들 수 없습니다. Azure PowerShell cmdlet을 사용 하 여이 특정 시점 제한을 해결할 수도 있습니다 `New-AzRmStorageShare` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Cmdlet을 사용 하 여 Azure 파일 공유를 만들 수 있습니다 [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) . 다음 PowerShell 명령은 `$resourceGroupName` `$storageAccountName` Azure PowerShell를 사용 하 여 저장소 계정 만들기 섹션에서 위에 정의 된 대로 변수를 설정 했다고 가정 합니다. 

> [!Important]  
> 프리미엄 파일 공유의 경우 `-QuotaGiB` 매개 변수는 프로 비전 된 파일 공유 크기를 참조 합니다. 프로 비전 된 파일 공유 크기는 사용량에 관계 없이 요금이 청구 되는 금액입니다. 표준 파일 공유는 프로 비전 된 크기가 아닌 사용량을 기준으로 요금이 청구 됩니다.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대 한 자세한 내용은 [공유, 디렉터리, 파일 및 메타 데이터 이름 지정 및](https://msdn.microsoft.com/library/azure/dn167011.aspx)참조를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용 하 여 Azure 파일 공유를 만들려면 먼저 저장소 계정 키를 가져와를 사용 하 여 파일 공유 만들기 작업에 권한을 부여 해야 합니다. 다음 명령을 사용 하 여이 작업을 수행할 수 있습니다 [`az storage account keys list`](/cli/azure/storage/account/keys) .

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

저장소 계정 키가 있으면 명령을 사용 하 여 Azure 파일 공유를 만들 수 있습니다 [`az storage share create`](/cli/azure/storage/share) . 

> [!Important]  
> 프리미엄 파일 공유의 경우 `--quota` 매개 변수는 프로 비전 된 파일 공유 크기를 참조 합니다. 프로 비전 된 파일 공유 크기는 사용량에 관계 없이 요금이 청구 되는 금액입니다. 표준 파일 공유는 프로 비전 된 크기가 아닌 사용량을 기준으로 요금이 청구 됩니다.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

저장소 계정이 가상 네트워크에 포함 되어 있고이 명령을 호출 하는 컴퓨터가 가상 네트워크의 일부가 아닌 경우에는이 명령이 실패 합니다. `New-AzRmStorageShare`위에서 설명한 대로 Azure PowerShell cmdlet을 사용 하거나 VPN 연결을 통해 가상 네트워크의 일부인 컴퓨터에서 Azure CLI를 실행 하 여이 시점 제한을 해결할 수 있습니다.

---

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대 한 자세한 내용은 [공유, 디렉터리, 파일 및 메타 데이터 이름 지정 및](https://msdn.microsoft.com/library/azure/dn167011.aspx)참조를 참조 하세요.

### <a name="create-a-hot-or-cool-file-share"></a>핫 또는 쿨 파일 공유 만들기
**범용 v2 (GPv2) 저장소 계정의** 파일 공유에는 트랜잭션 최적화, 핫 또는 쿨 파일 공유 (또는 혼합)가 포함 될 수 있습니다. 트랜잭션 최적화 공유는 모든 Azure 지역에서 사용할 수 있지만 핫 및 쿨 파일 공유는 [지역 하위 집합](storage-files-planning.md#storage-tiers)에서만 사용할 수 있습니다. Azure PowerShell preview 모듈이 나 Azure CLI를 사용 하 여 핫 또는 쿨 파일 공유를 만들 수 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal는 아직 핫 및 쿨 파일 공유 만들기를 지원 하지 않으며 기존 트랜잭션 최적화 파일 공유를 핫 또는 쿨로 이동 하는 것을 지원 하지 않습니다. PowerShell 또는 Azure CLI를 사용 하 여 파일 공유를 만드는 방법에 대 한 지침을 확인 하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myhotshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Hot

# You can also change an existing share's tier.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

> [!Note]  
> PowerShell을 통해 계층을 설정 하 고 변경 하는 기능은 미리 보기 Az. Storage PowerShell 모듈에서 제공 됩니다. 이러한 cmdlet 또는 해당 출력은 일반적으로 사용 가능한 Az. Storage PowerShell 모듈에서 해제 되기 전에 변경 될 수 있으므로이를 염두에 두면 스크립트를 만듭니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
파일 공유를 만들거나 특정 계층으로 이동 하는 기능은 최신 Azure CLI 업데이트에서 사용할 수 있습니다. Azure CLI 업데이트는를 사용 하는 운영 체제/Linux 배포에만 적용 됩니다. 시스템에서 Azure CLI를 업데이트 하는 방법에 대 한 지침은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조 하세요.

```bash
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2
# storage accounts. Standard tiers are only available in standard storage accounts.
shareName="myhotshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Hot"
```

> [!Note]  
> 매개 변수를 사용 하 여 계층을 설정 하는 기능에는 `--access-tier` 최신 Azure CLI 패키지의 미리 보기가 제공 됩니다. 이 명령 또는 해당 출력은 일반 공급으로 표시 되기 전에 변경 될 수 있으므로이를 염두에 두면 스크립트를 만듭니다.

---

## <a name="next-steps"></a>다음 단계
- [Azure File Sync 배포에 대](storage-sync-files-planning.md)한 Azure Files 또는 계획 [의 배포를 계획](storage-files-planning.md) 합니다. 
- [네트워킹 개요](storage-files-networking-overview.md).
- [Windows](storage-how-to-use-files-windows.md), [Macos](storage-how-to-use-files-mac.md)및 [Linux](storage-how-to-use-files-linux.md)에서 파일 공유를 연결 하 고 탑재 합니다.