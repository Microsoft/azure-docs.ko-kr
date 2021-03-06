---
title: Azure 파일 공유 만들기
titleSuffix: Azure Files
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 Azure 파일 공유를 만드는 방법입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 24bee926d84c7a5be3f19c39d39285c2cd486824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211025"
---
# <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure 파일 공유를 만들려면이를 사용 하는 방법에 대 한 세 가지 질문에 답변해 야 합니다.

- **Azure 파일 공유에 대 한 성능 요구 사항은 무엇 인가요?**  
    Azure Files는 하드 디스크 기반 (HDD 기반) 하드웨어에서 호스트 되는 표준 파일 공유 및 반도체 디스크 기반 (SSD 기반) 하드웨어에 호스트 되는 프리미엄 파일 공유를 제공 합니다.

- **Azure 파일 공유에 대 한 중복성 요구 사항은 무엇 인가요?**  
    표준 파일 공유는 LRS (로컬 중복), ZRS (영역 중복), GRS (지역 중복) 또는 GZRS (지역 중복) 저장소를 제공 하지만, large file share 기능은 로컬 중복 및 영역 중복 파일 공유 에서만 지원 됩니다. 프리미엄 파일 공유는 어떤 형태의 지역 중복도 지원 하지 않습니다.

    프리미엄 파일 공유는 지역 하위 집합에서 로컬 중복 및 영역 중복성과 함께 사용할 수 있습니다. 현재 지역에서 프리미엄 파일 공유를 사용할 수 있는지 확인 하려면 Azure에 대 한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 페이지를 참조 하세요. ZRS를 지 원하는 지역에 대 한 자세한 내용은 [중복성 Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조 하세요.

- **어떤 크기의 파일 공유가 필요 한가요?**  
    로컬 및 영역 중복 저장소 계정에서 Azure 파일 공유는 최대 100 TiB 확장 될 수 있지만, 지리적 영역 및 지역 중복 저장소 계정에서 Azure 파일 공유는 최대 5 TiB 까지만 확장할 수 있습니다. 

이러한 세 가지 옵션에 대 한 자세한 내용은 [Azure Files 배포 계획](storage-files-planning.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소
- 이 문서에서는 독자들이 이미 Azure 구독을 만들었다고 가정합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure PowerShell을 사용하려면 [최신 버전을 설치](/powershell/azure/install-az-ps)하세요.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
Azure 파일 공유는 공유 스토리지 풀을 나타내는 최상위 개체인 *스토리지 계정* 에 배포됩니다. 이 저장소 풀은 여러 파일 공유를 배포 하는 데 사용할 수 있습니다. 

Azure는 고객이 가질 수 있는 다양 한 저장소 시나리오에 대해 여러 유형의 저장소 계정을 지원 하지만 Azure Files에는 두 가지 주요 유형의 저장소 계정이 있습니다. 만드는 데 필요한 저장소 계정 유형은 표준 파일 공유를 만들지, 아니면 프리미엄 파일 공유를 만들지에 따라 결정 됩니다. 

- **범용 버전 2(GPv2) 스토리지 계정**: GPv2 스토리지 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 스토리지 계정은 Azure 파일 공유 저장 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 스토리지 리소스를 저장할 수 있습니다. 파일 공유는 트랜잭션 최적화 (기본값), 핫 또는 쿨 계층에 배포 될 수 있습니다.

- **FileStorage 스토리지 계정**: FileStorage 스토리지 계정을 사용하면 프리미엄/반도체 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 스토리지 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal를 통해 저장소 계정을 만들려면 대시보드에서 **+ 리소스 만들기** 를 선택 합니다. 결과 Azure Marketplace 검색 창에서 **저장소 계정** 을 검색 하 고 결과 검색 결과를 선택 합니다. 그러면 저장소 계정에 대 한 개요 페이지가 나타납니다. **만들기** 를 선택 하 여 저장소 계정 만들기 마법사를 진행 합니다.

![브라우저에서 저장소 계정 빨리 만들기 옵션의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>기본 사항
저장소 계정 만들기를 완료 하는 첫 번째 섹션에는 **기본** 사항이 지정 되어 있습니다. 여기에는 저장소 계정을 만드는 데 필요한 모든 필드가 포함 됩니다. GPv2 저장소 계정을 만들려면 **성능** 라디오 단추가 *표준* 으로 설정 되어 있는지 확인 하 고 **계정 종류** 드롭다운 목록을 *StorageV2 (범용 v2)* 로 선택 합니다.

![StorageV2가 선택 된 표준 및 계정 종류가 선택 된 성능 라디오 단추의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage 저장소 계정을 만들려면 **성능** 라디오 단추가 *프리미엄* 으로 설정 되어 있는지 확인 하 고 **계정 종류** 드롭다운 목록을 *FileStorage* 로 선택 합니다.

![프리미엄이 선택 된 성능 라디오 단추의 스크린샷 및 FileStorage가 선택 된 계정 종류](media/storage-how-to-create-file-share/create-storage-account-2.png)

다른 기본 필드는 저장소 계정에 대 한 선택과는 별개입니다.
- **Storage 계정 이름**: 만들 저장소 계정 리소스의 이름입니다. 이 이름은 전역적으로 고유 해야 하지만 그렇지 않은 경우 원하는 이름을 지정할 수 있습니다. 저장소 계정 이름은 SMB를 통해 Azure 파일 공유를 탑재할 때 서버 이름으로 사용 됩니다.
- **Location**: 배포할 저장소 계정의 지역입니다. 리소스 그룹이 나 사용 가능한 다른 지역과 연결 된 지역이 될 수 있습니다.
- **복제**:이 필드는 복제 라고 하지만 실제로는 **중복성** 을 의미 합니다. 이는 LRS (로컬 중복), ZRS (영역 중복), GRS (지역 중복) 및 GZRS (지역 중복)와 같이 원하는 중복성 수준입니다. 이 드롭다운 목록에는 Azure 파일 공유에 적용 되지 않는 읽기 액세스 지역 중복 (GRS) 및 읽기 액세스 지역 중복 (RA-GZRS)도 포함 되어 있습니다. 이를 선택 하 여 저장소 계정에 만들어진 모든 파일 공유는 실제로 지역 중복 또는 지역 중복이 됩니다. 

#### <a name="networking"></a>네트워킹
네트워킹 섹션에서 네트워킹 옵션을 구성할 수 있습니다. 이러한 설정은 저장소 계정 만들기에 대 한 선택 사항이 며 원할 경우 나중에 구성할 수 있습니다. 이러한 옵션에 대 한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

#### <a name="data-protection"></a>데이터 보호
데이터 보호 섹션에서는 저장소 계정의 Azure 파일 공유에 대 한 일시 삭제 정책을 구성할 수 있습니다. Blob, 컨테이너, 컨테이너에 대 한 지정 시간 복원, 버전 관리 및 변경 피드에 대 한 일시 삭제와 관련 된 다른 설정은 Azure Blob storage에만 적용 됩니다.

#### <a name="advanced"></a>고급
고급 섹션에는 Azure 파일 공유에 대 한 몇 가지 중요 한 설정이 포함 되어 있습니다.

- **보안 전송 필요**:이 필드는 저장소 계정에 대 한 통신을 위해 저장소 계정에 암호화가 필요한 지 여부를 나타냅니다. SMB 2.1 지원이 필요한 경우이를 사용 하지 않도록 설정 해야 합니다.
- **대량 파일 공유**:이 필드는 최대 100 TiB을 확장 하는 파일 공유에 대 한 저장소 계정을 사용 하도록 설정 합니다. 이 기능을 사용 하도록 설정 하면 저장소 계정이 로컬 중복 및 영역 중복 저장소 옵션 으로만 제한 됩니다. GPv2 저장소 계정이 대량 파일 공유에 대해 사용 하도록 설정 된 후에는 대량 파일 공유 기능을 사용 하지 않도록 설정할 수 없습니다. 모든 프리미엄 파일 공유가 100 TiB까지 확장 될 수 있으므로 FileStorage storage 계정 (프리미엄 파일 공유용 저장소 계정)이이 옵션을 사용할 수 없습니다. 

![Azure Files에 적용 되는 중요 한 고급 설정의 스크린샷](media/storage-how-to-create-file-share/create-storage-account-3.png)

고급 탭에서 사용할 수 있는 다른 설정 (Azure Data Lake 저장소의 계층적 네임 스페이스, 기본 blob 계층, blob 저장소에 대 한 NFSv3 등)은 Azure Files에 적용 되지 않습니다.

> [!Important]  
> Blob 액세스 계층을 선택 해도 파일 공유의 계층에는 영향을 주지 않습니다.

#### <a name="tags"></a>태그
태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다. 이러한 항목은 선택 사항이 며 저장소 계정을 만든 후에 적용할 수 있습니다.

#### <a name="review--create"></a>검토 + 만들기
저장소 계정을 만드는 마지막 단계는 **검토 + 만들기** 탭에서 **만들기** 단추를 선택 하는 것입니다. 저장소 계정에 대 한 필수 필드를 모두 채우지 않으면이 단추를 사용할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 저장소 계정을 만들려면 cmdlet을 사용 합니다 `New-AzStorageAccount` . 이 cmdlet에는 많은 옵션이 있습니다. 필요한 옵션만 표시 됩니다. 고급 옵션에 대 한 자세한 내용은 [ `New-AzStorageAccount` cmdlet 설명서](/powershell/module/az.storage/new-azstorageaccount)를 참조 하세요.

저장소 계정 및 후속 파일 공유 만들기를 간소화 하기 위해 변수에 여러 매개 변수를 저장 합니다. 변수 콘텐츠를 원하는 값으로 바꿀 수 있지만 저장소 계정 이름은 전역적으로 고유 해야 합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `-SkuName`매개 변수는 원하는 중복성 유형과 관련이 있습니다. 지역 중복 또는 지역 중복 저장소 계정을 원하는 경우에도 매개 변수를 제거 해야 합니다 `-EnableLargeFileShare` .

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `-SkuName`매개 변수는 `Premium` 와 원하는 중복성 수준 (로컬 중복)을 모두 포함 하도록 변경 되었습니다 `LRS` . `-Kind` `FileStorage` `StorageV2` GPv2 Storage 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 때문에 매개 변수는 대신입니다.

```powershell
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

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `--sku`매개 변수는 원하는 중복성 유형과 관련이 있습니다. 지역 중복 또는 지역 중복 저장소 계정을 원하는 경우에도 매개 변수를 제거 해야 합니다 `--enable-large-file-share` .

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

프리미엄 Azure 파일 공유를 저장할 수 있는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `--sku`매개 변수는 `Premium` 와 원하는 중복성 수준 (로컬 중복)을 모두 포함 하도록 변경 되었습니다 `LRS` . `--kind` `FileStorage` `StorageV2` GPv2 Storage 계정 대신 FileStorage 저장소 계정에 프리미엄 파일 공유를 만들어야 하기 때문에 매개 변수는 대신입니다.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>파일 공유 만들기
저장소 계정을 만든 후에는 파일 공유를 만드는 것만 남았습니다. 이 프로세스는 프리미엄 파일 공유 또는 표준 파일 공유를 사용 하 고 있는지 여부에 관계 없이 거의 동일 합니다. 다음과 같은 차이점을 고려해 야 합니다.

표준 파일 공유는 트랜잭션 최적화 (기본값), 핫 또는 쿨 계층 중 하나로 배포할 수 있습니다. 저장소 계정의 **blob 액세스 계층** 에 의해 영향을 받지 않는 파일 공유 계층 당입니다 .이 속성은 Azure blob 저장소에만 관련 되며 Azure Files와는 관련이 없습니다. 배포 후 언제 든 지 공유의 계층을 변경할 수 있습니다. 프리미엄 파일 공유는 표준 계층으로 직접 변환할 수 없습니다.

> [!Important]  
> GPv2 스토리지 계정 유형(트랜잭션 최적화, 핫 및 쿨) 내의 계층 간에 공유를 이동할 수 있습니다. 계층 간 공유 이동으로 인해 트랜잭션이 발생합니다. 핫 계층에서 쿨 계층으로 이동하면 공유의 각 파일에 대해 쿨 계층의 쓰기 트랜잭션 요금이 발생하는 반면, 쿨 계층에서 핫 계층으로 이동하면 각 파일 공유에 대해 쿨 계층의 읽기 트랜잭션 요금이 발생합니다.

**할당량** 속성은 프리미엄 및 표준 파일 공유 간에 약간 다른 것을 의미 합니다.

- 표준 파일 공유의 경우에는 최종 사용자가 이동할 수 없는 Azure 파일 공유의 상한입니다. 할당량을 지정 하지 않으면 표준 파일 공유는 최대 100 TiB (또는 5 TiB, 저장소 계정에 대 한 대량 파일 공유 속성이 설정 되지 않은 경우)까지 확장 될 수 있습니다.

- 프리미엄 파일 공유의 경우 할당량은 **프로 비전 된 크기** 를 의미 합니다. 프로 비전 된 크기는 실제 사용량에 관계 없이 요금이 청구 되는 금액입니다. 프리미엄 파일 공유를 계획 하는 방법에 대 한 자세한 내용은 [premium 파일 공유 프로 비전](understanding-billing.md#provisioned-model)을 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)
방금 저장소 계정을 만든 경우 **리소스로 이동** 을 선택 하 여 배포 화면에서 이동할 수 있습니다. 저장소 계정에서 **파일 공유** 라는 타일을 선택 합니다. 저장소 계정에 대 한 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

![파일 공유 타일의 스크린샷](media/storage-how-to-create-file-share/create-file-share-1.png)

파일 공유 목록에 이전에이 저장소 계정에서 만든 파일 공유가 표시 되어야 합니다. 아직 파일 공유가 생성 되지 않은 경우 빈 테이블입니다. **+ 파일 공유** 를 선택 하 여 새 파일 공유를 만듭니다.

새 파일 공유 블레이드가 화면에 표시 됩니다. 새 파일 공유 블레이드의 필드를 완성 하 여 파일 공유를 만듭니다.

- **이름**: 만들 파일 공유의 이름입니다.
- **할당량**: 표준 파일 공유에 대 한 파일 공유의 할당량 프리미엄 파일 공유에 대 한 파일 공유의 프로 비전 된 크기입니다.
- **계층**: 파일 공유에 대해 선택한 계층입니다. 이 필드는 범용 **(GPv2) 저장소 계정** 에서만 사용할 수 있습니다. 트랜잭션 최적화, 핫 또는 쿨를 선택할 수 있습니다. 공유의 계층은 언제 든 지 변경할 수 있습니다. 마이그레이션 중에 가능한 가장 많이 사용한 계층을 선택 하 고, 트랜잭션 비용을 최소화 한 다음, 마이그레이션이 완료 된 후 원하는 경우 하위 계층으로 전환 하는 것이 좋습니다.

**만들기** 를 선택 하 여 새 공유 만들기를 완료 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Cmdlet을 사용 하 여 Azure 파일 공유를 만들 수 있습니다 [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) . 다음 PowerShell 명령은 `$resourceGroupName` `$storageAccountName` Azure PowerShell를 사용 하 여 저장소 계정 만들기 섹션에서 위에 정의 된 대로 변수를 설정 했다고 가정 합니다. 

다음 예제에서는 매개 변수를 사용 하 여 명시적 계층으로 파일 공유를 만드는 방법을 보여 줍니다 `-AccessTier` . 이 경우 예제에 표시 된 대로 미리 보기 Az. Storage 모듈을 사용 해야 합니다. GA Az. Storage 모듈을 사용 중이거나이 명령을 포함 하지 않았기 때문에 계층을 지정 하지 않은 경우 표준 파일 공유의 기본 계층은 트랜잭션 최적화입니다.

> [!Important]  
> 프리미엄 파일 공유의 경우 `-QuotaGiB` 매개 변수는 프로 비전 된 파일 공유 크기를 참조 합니다. 프로 비전 된 파일 공유 크기는 사용량에 관계 없이 요금이 청구 되는 금액입니다. 표준 파일 공유는 프로 비전 된 크기가 아닌 사용량을 기준으로 요금이 청구 됩니다.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
명령을 사용 하 여 Azure 파일 공유를 만들 수 있습니다 [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) . 다음 Azure CLI 명령에서는 `$resourceGroupName` `$storageAccountName` Azure CLI를 사용 하 여 저장소 계정 만들기 섹션에서 위에 정의 된 대로 변수를 설정 했다고 가정 합니다.

> [!Important]  
> 프리미엄 파일 공유의 경우 `--quota` 매개 변수는 프로 비전 된 파일 공유 크기를 참조 합니다. 프로 비전 된 파일 공유 크기는 사용량에 관계 없이 요금이 청구 되는 금액입니다. 표준 파일 공유는 프로 비전 된 크기가 아닌 사용량을 기준으로 요금이 청구 됩니다.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

### <a name="changing-the-tier-of-an-azure-file-share"></a>Azure 파일 공유의 계층 변경
범용 **v2 (GPv2) 저장소 계정** 에 배포 된 파일 공유는 트랜잭션 최적화, 핫 또는 쿨 계층에 있을 수 있습니다. 위에서 설명한 대로 트랜잭션 비용에 따라 언제 든 지 Azure 파일 공유의 계층을 변경할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
주 저장소 계정 페이지에서 **파일 공유**  를 선택 합니다. 파일 **공유** 라는 타일을 선택 합니다. 저장소 계정에 대 한 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

![파일 공유 타일의 스크린샷](media/storage-how-to-create-file-share/create-file-share-1.png)

파일 공유의 테이블 목록에서 계층을 변경할 파일 공유를 선택 합니다. 파일 공유 개요 페이지의 메뉴에서 **계층 변경** 을 선택 합니다.

![계층 변경 단추가 강조 표시 된 파일 공유 개요 페이지의 스크린샷](media/storage-how-to-create-file-share/change-tier-0.png)

결과 대화 상자에서 원하는 계층 (트랜잭션 최적화, 핫 또는 쿨)을 선택 합니다.

![계층 변경 대화 상자의 스크린샷](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell cmdlet은 `$resourceGroupName` `$storageAccountName` `$shareName` 이 문서의 이전 섹션에서 설명한 대로,, 변수를 설정 했다고 가정 합니다.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
다음 Azure CLI 명령은 `$resourceGroupName` `$storageAccountName` `$shareName` 이 문서의 이전 섹션에 설명 된 대로, 및 변수를 설정 했다고 가정 합니다.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대](storage-sync-files-planning.md)한 Azure Files 또는 계획 [의 배포를 계획](storage-files-planning.md) 합니다. 
- [네트워킹 개요](storage-files-networking-overview.md).
- [Windows](storage-how-to-use-files-windows.md), [Macos](storage-how-to-use-files-mac.md)및 [Linux](storage-how-to-use-files-linux.md)에서 파일 공유를 연결 하 고 탑재 합니다.