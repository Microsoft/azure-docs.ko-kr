---
title: 저장소 계정 장애 조치 (failover) 시작
titleSuffix: Azure Storage
description: 스토리지 계정의 기본 엔드포인트를 사용할 수 없는 경우 계정 장애 조치(failover)를 시작하는 방법을 알아봅니다. 장애 조치(failover)는 스토리지 계정의 주 지역이 되도록 보조 지역을 업데이트합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 74f4c30f7aa018502f3437fd4a577f8c2a9de491
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077947"
---
# <a name="initiate-a-storage-account-failover"></a>저장소 계정 장애 조치 (failover) 시작

어떤 이유로 든 지역 중복 저장소 계정의 기본 끝점을 사용할 수 없게 되 면 계정 장애 조치 (failover)를 시작할 수 있습니다. 계정 장애 조치(failover)는 스토리지 계정의 기본 엔드포인트가 되도록 보조 엔드포인트를 업데이트합니다. 장애 조치(failover)가 완료되면 클라이언트가 새로운 주 지역에 쓰기 시작할 수 있습니다. 강제 장애 조치(failover)를 사용하면 애플리케이션에 대해 고가용성을 유지할 수 있습니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에 대해 계정 장애 조치(failover)를 시작하는 방법을 보여 줍니다. 계정 장애 조치 (failover)에 대해 자세히 알아보려면 [재해 복구 및 저장소 계정 장애 조치 (failover](storage-disaster-recovery-guidance.md))를 참조 하세요.

> [!WARNING]
> 일반적으로 계정 장애 조치(failover)를 수행하면 일부 데이터가 손실됩니다. 계정 장애 조치(failover)의 의미를 파악하고 데이터 손실에 대비하려면 [계정 장애 조치(failover) 프로세스 이해](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

저장소 계정에서 계정 장애 조치 (failover)를 수행 하려면 먼저 저장소 계정이 지역에서 복제를 사용 하도록 구성 되어 있는지 확인 합니다. 저장소 계정은 다음과 같은 중복성 옵션 중 하나를 사용할 수 있습니다.

- GRS (지역 중복 저장소) 또는 읽기 액세스 지역 중복 저장소 (RA-GRS)
- GZRS (지역 중복 저장소) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)

Azure Storage 중복성에 대 한 자세한 내용은 [중복성 Azure Storage](storage-redundancy.md)를 참조 하세요.

## <a name="initiate-the-failover"></a>장애 조치 (failover) 시작

## <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 계정 장애 조치(failover)를 시작하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동합니다.
1. **설정**에서 **지역 복제**를 선택합니다. 다음 이미지는 스토리지 계정의 지역 복제 및 장애 조치(failover) 상태를 보여 줍니다.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="지역 복제 및 장애 조치(failover) 상태가 표시된 스크린샷":::

1. 스토리지 계정이 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성되었는지 확인합니다. 구성되지 않은 경우 **설정**에서 **구성**을 선택하여 계정을 지역 중복으로 업데이트합니다.
1. **마지막 동기화 시간** 속성은 보조 지역이 주 지역보다 지연된 정도를 나타냅니다. **마지막 동기화 시간**을 통해 장애 조치(failover)가 완료된 후의 데이터 손실 범위를 예측할 수 있습니다. **마지막 동기화 시간** 속성을 확인 하는 방법에 대 한 자세한 내용은 [저장소 계정에 대 한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조 하세요.
1. **장애 조치 (failover) 준비를**선택 합니다.
1. 확인 대화 상자를 검토합니다. 준비되면 **예**를 입력하여 장애 조치(failover)를 확인하고 시작합니다.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="계정 장애 조치(failover)에 대한 확인 대화 상자가 표시된 스크린샷":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

계정 장애 조치 (failover) 기능은 일반 공급 되지만 PowerShell 용 미리 보기 모듈을 계속 사용 합니다. PowerShell을 사용 하 여 계정 장애 조치 (failover)를 시작 하려면 먼저 Az. Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) 모듈을 설치 해야 합니다. 다음 단계에 따라 모듈을 설치합니다.

1. 모든 이전 Azure PowerShell 설치를 제거합니다.

    - **설정**에 있는 **앱 및 기능** 설정을 사용하여 Windows에서 이전에 설치한 Azure PowerShell을 제거합니다.
    - 에서 모든 **Azure** 모듈을 제거 `%Program Files%\WindowsPowerShell\Modules` 합니다.

1. 최신 버전의 PowerShellGet이 설치되어 있는지 확인합니다. Windows PowerShell 창을 열고 다음 명령을 실행하여 최신 버전을 설치합니다.

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet을 설치한 후 PowerShell 창을 닫았다가 다시 엽니다.

1. Azure PowerShell의 최신 버전을 설치합니다.

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 계정 장애 조치 (failover)를 지 원하는 Azure Storage 미리 보기 모듈을 설치 합니다.

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

PowerShell에서 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>계정 장애 조치(failover)의 중요한 의미

스토리지 계정에 대해 계정 장애 조치(failover)를 시작하면 보조 엔드포인트가 기본 엔드포인트가 되도록 보조 엔드포인트에 대한 DNS 레코드가 업데이트됩니다. 장애 조치(failover)를 시작하기 전에 스토리지 계정에 대한 잠재적 영향을 파악해야 합니다.

장애 조치 (failover)를 시작 하기 전에 데이터 손실의 범위를 예측 하려면 **마지막 동기화 시간** 속성을 선택 합니다. **마지막 동기화 시간** 속성을 확인 하는 방법에 대 한 자세한 내용은 [저장소 계정에 대 한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조 하세요.

장애 조치(failover) 후에는 새로운 주 지역에서 스토리지 계정 유형이 LRS(로컬 중복 스토리지)로 자동 변환됩니다. 계정에 대해 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 다시 사용할 수 있습니다. LRS에서 GRS 또는 RA-GRS로 변환하는 경우 추가 비용이 발생합니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

스토리지 계정에 대해 GRS를 다시 사용하면 계정의 데이터가 새 보조 지역으로 복제되기 시작합니다. 복제 시간은 복제되는 데이터의 양에 따라 다릅니다.  

## <a name="next-steps"></a>다음 단계

- [재해 복구 및 저장소 계정 장애 조치(failover)](storage-disaster-recovery-guidance.md)
- [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md)
- [자습서: Blob Storage에서 고가용성 애플리케이션 빌드](../blobs/storage-create-geo-redundant-storage.md)
