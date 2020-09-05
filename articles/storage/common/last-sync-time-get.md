---
title: 스토리지 계정에 대한 마지막 동기화 시간 속성 확인
titleSuffix: Azure Storage
description: 지역 복제 스토리지 계정에 대한 마지막 동기화 시간 속성을 확인하는 방법을 알아보세요. 마지막 동기화 시간 속성은 주 지역에 기록된 모든 데이터가 보조 지역에 성공적으로 기록된 마지막 시간을 나타냅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cad80a554db0424ad8635e81cbfb4225705e9c89
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073015"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>스토리지 계정에 대한 마지막 동기화 시간 속성 확인

스토리지 계정을 구성할 때 주 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복사하도록 지정할 수 있습니다. 지역 복제는 주 지역에 자연 재해와 같은 상당한 가동 중단이 발생하는 경우 데이터에 내구성을 제공합니다. 보조 지역에 대한 읽기 액세스를 추가적으로 사용하도록 설정하면 주 지역을 사용할 수 없는 경우에도 데이터 읽기 작업을 수행할 수 있습니다. 주 지역이 응답하지 않는 경우 보조 지역으로 원활하게 전환하여 데이터를 읽도록 애플리케이션을 설계할 수 있습니다.

GRS(지역 중복 스토리지) 및 GZRS(지역-영역 중복 스토리지)는 모두 데이터를 보조 지역에 비동기적으로 복제합니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GRS(읽기 액세스 지역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용하도록 설정합니다. Azure Storage에서 제공하는 다양한 중복성 옵션에 대한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

이 문서에서는 주 지역과 보조 지역 간의 불일치를 평가할 수 있도록 스토리지 계정의 **마지막 동기화 시간** 속성을 확인하는 방법을 설명합니다.

## <a name="about-the-last-sync-time-property"></a>마지막 동기화 시간 속성 정보

지역 복제는 비동기 작업이기 때문에 가동 중단 발생 시 주 지역에 기록된 데이터가 보조 지역에 아직 기록되지 않았을 수 있습니다. **마지막 동기화 시간** 속성은 주 지역의 데이터가 보조 지역에 성공적으로 기록된 마지막 시간을 나타냅니다. 마지막 동기화 시간 전에 주 지역에 기록된 모든 데이터는 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 속성 이후 주 지역에 기록된 데이터는 아직 읽지 못할 수도 있습니다.

**마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다.

## <a name="get-the-last-sync-time-property"></a>마지막 동기화 시간 속성 가져오기

PowerShell 또는 Azure CLI를 사용하여 **마지막 동기화 시간** 속성의 값을 검색할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 마지막 동기화 시간을 가져오려면 [Az. storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈의 버전 1.11.0 이상을 설치 합니다. 그런 다음, 스토리지 계정의 **GeoReplicationStats.LastSyncTime** 속성을 확인합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정의 마지막 동기화 시간을 가져오려면 스토리지 계정의 **geoReplicationStats.lastSyncTime** 속성을 확인하세요. `--expand` 매개 변수를 사용하여 **geoReplicationStats**에서 중첩된 속성의 값을 반환합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>참고 항목

- [Azure Storage 중복성](storage-redundancy.md)
- [스토리지 계정의 중복성 옵션 변경](redundancy-migration.md)
- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md)
