---
title: 저장소 계정에 대 한 마지막 동기화 시간 속성 확인
titleSuffix: Azure Storage
description: 지리적으로 복제 된 저장소 계정에 대 한 **마지막 동기화 시간** 속성을 확인 하는 방법을 알아봅니다. **마지막 동기화 시간** 속성은 주 지역의 모든 쓰기가 보조 지역에 성공적으로 작성 된 마지막 시간을 나타냅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165489"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>저장소 계정에 대 한 마지막 동기화 시간 속성 확인

저장소 계정을 구성할 때 주 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복사 하도록 지정할 수 있습니다. 지역에서 복제는 자연 재해와 같이 주 지역에서 상당한 가동 중단이 발생 하는 경우 데이터에 대 한 내구성을 제공 합니다. 보조 지역에 대 한 읽기 액세스를 추가로 사용 하도록 설정 하면 주 지역을 사용할 수 없게 되는 경우 데이터를 읽기 작업에 사용할 수 있습니다. 주 지역이 응답 하지 않는 경우 보조 지역에서 읽기로 원활 하 게 전환 하도록 응용 프로그램을 디자인할 수 있습니다.

GRS (지역 중복 저장소) 및 GZRS (지역 중복 저장소) (미리 보기)는 모두 데이터를 보조 지역에 비동기적으로 복제 합니다. 보조 지역에 대 한 읽기 액세스의 경우 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하도록 설정 합니다. Azure Storage에서 제공 하는 중복성에 대 한 다양 한 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)(영문)을 참조 하세요.

이 문서에서는 주 지역과 보조 지역 간의 불일치를 평가할 수 있도록 저장소 계정에 대 한 **마지막 동기화 시간** 속성을 확인 하는 방법을 설명 합니다.

## <a name="about-the-last-sync-time-property"></a>마지막 동기화 시간 속성 정보

지역에서 복제는 비동기 이기 때문에 주 지역에 기록 된 데이터는 중단 발생 시 보조 지역에 아직 쓰여지지 않았을 수 있습니다. **마지막 동기화 시간** 속성은 주 지역에서 데이터를 마지막으로 보조 지역에 쓴 시간을 나타냅니다. 마지막 동기화 시간 전에 주 지역에 대 한 모든 쓰기는 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 속성 이후 주 지역에 대 한 쓰기는 아직 읽기에 사용할 수도 있고 사용 하지 못할 수도 있습니다.

**마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다.

## <a name="get-the-last-sync-time-property"></a>마지막 동기화 시간 속성 가져오기

PowerShell 또는 Azure CLI를 사용 하 여 **마지막 동기화 시간** 속성의 값을 검색할 수 있습니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 마지막 동기화 시간을 가져오려면 지역에서 복제 통계 가져오기를 지 원하는 Azure Storage 미리 보기 모듈을 설치 합니다. 예를 들어:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

그런 다음 저장소 계정의 **GeoReplicationStats. LastSyncTime** 속성을 확인 합니다. 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 마지막 동기화 시간을 가져오려면 저장소 계정의 lastSyncTime 속성을 확인 **geoReplicationStats** . `--expand` 매개 변수를 사용 하 여 **geoReplicationStats**아래에 중첩 된 속성의 값을 반환 합니다. 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

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
- [저장소 계정에 대 한 중복성 옵션 변경](redundancy-migration.md)
- [읽기 액세스 지역 중복 저장소를 사용 하 여 항상 사용 가능한 응용 프로그램 디자인](storage-designing-ha-apps-with-ragrs.md)