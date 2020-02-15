---
title: 스토리지 계정 장애 조치(failover)(미리 보기) 시작 - Azure Storage
description: 스토리지 계정의 기본 엔드포인트를 사용할 수 없는 경우 계정 장애 조치(failover)를 시작하는 방법을 알아봅니다. 장애 조치(failover)는 스토리지 계정의 주 지역이 되도록 보조 지역을 업데이트합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7529cfbd0ab75d0113e5cea666bc04aa1b15d30b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157721"
---
# <a name="initiate-a-storage-account-failover-preview"></a>스토리지 계정 장애 조치(failover)(미리 보기) 시작

어떤 이유로든 지역 중복 스토리지 계정의 기본 엔드포인트를 사용할 수 없게 되면 계정 장애 조치(failover)(미리 보기)를 시작할 수 있습니다. 계정 장애 조치(failover)는 스토리지 계정의 기본 엔드포인트가 되도록 보조 엔드포인트를 업데이트합니다. 장애 조치(failover)가 완료되면 클라이언트가 새로운 주 지역에 쓰기 시작할 수 있습니다. 강제 장애 조치(failover)를 사용하면 애플리케이션에 대해 고가용성을 유지할 수 있습니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에 대해 계정 장애 조치(failover)를 시작하는 방법을 보여 줍니다. 계정 장애 조치(failover)에 대한 자세한 내용은 [Azure Storage의 재해 복구 및 계정 장애 조치(Failover)(미리 보기)](storage-disaster-recovery-guidance.md)를 참조하세요.

> [!WARNING]
> 일반적으로 계정 장애 조치(failover)를 수행하면 일부 데이터가 손실됩니다. 계정 장애 조치(failover)의 의미를 파악하고 데이터 손실에 대비하려면 [계정 장애 조치(failover) 프로세스 이해](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

스토리지 계정에 대해 계정 장애 조치(failover)를 수행하기 전에 다음 단계를 수행했는지 확인합니다.

- 계정 장애 조치(failover) 미리 보기를 등록합니다. 등록 방법에 대한 자세한 내용은 [미리 보기 정보](storage-disaster-recovery-guidance.md#about-the-preview)를 참조하세요.
- 스토리지 계정이 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 사용하도록 구성되었는지 확인합니다. 지역 중복 저장소에 대 한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조 하세요.

## <a name="important-implications-of-account-failover"></a>계정 장애 조치(failover)의 중요한 의미

스토리지 계정에 대해 계정 장애 조치(failover)를 시작하면 보조 엔드포인트가 기본 엔드포인트가 되도록 보조 엔드포인트에 대한 DNS 레코드가 업데이트됩니다. 장애 조치(failover)를 시작하기 전에 스토리지 계정에 대한 잠재적 영향을 파악해야 합니다.

장애 조치(failover)를 시작하기 전에 가능한 데이터 손실 범위를 예측하려면 **PowerShell cmdlet을 사용하여**마지막 동기화 시간`Get-AzStorageAccount` 속성을 확인하고 `-IncludeGeoReplicationStats` 매개 변수를 포함합니다. 그런 다음, 계정의 `GeoReplicationStats` 속성을 확인합니다. \

장애 조치(failover) 후에는 새로운 주 지역에서 스토리지 계정 유형이 LRS(로컬 중복 스토리지)로 자동 변환됩니다. 계정에 대해 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 다시 사용할 수 있습니다. LRS에서 GRS 또는 RA-GRS로 변환하는 경우 추가 비용이 발생합니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

스토리지 계정에 대해 GRS를 다시 사용하면 계정의 데이터가 새 보조 지역으로 복제되기 시작합니다. 복제 시간은 복제되는 데이터의 양에 따라 다릅니다.  

## <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 계정 장애 조치(failover)를 시작하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동합니다.
2. **설정**에서 **지역 복제**를 선택합니다. 다음 이미지는 스토리지 계정의 지역 복제 및 장애 조치(failover) 상태를 보여 줍니다.

    ![지역 복제 및 장애 조치(failover) 상태가 표시된 스크린샷](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. 스토리지 계정이 GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성되었는지 확인합니다. 구성되지 않은 경우 **설정**에서 **구성**을 선택하여 계정을 지역 중복으로 업데이트합니다. 
4. **마지막 동기화 시간** 속성은 보조 지역이 주 지역보다 지연된 정도를 나타냅니다. **마지막 동기화 시간**을 통해 장애 조치(failover)가 완료된 후의 데이터 손실 범위를 예측할 수 있습니다.
5. **장애 조치(failover)(미리 보기) 준비**를 선택합니다. 
6. 확인 대화 상자를 검토합니다. 준비되면 **예**를 입력하여 장애 조치(failover)를 확인하고 시작합니다.

    ![계정 장애 조치(failover)에 대한 확인 대화 상자가 표시된 스크린샷](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 계정 장애 조치(failover)를 시작하려면 먼저 6.0.1 미리 보기 모듈을 설치해야 합니다. 다음 단계에 따라 모듈을 설치합니다.

1. 모든 이전 Azure PowerShell 설치를 제거합니다.

    - **설정**에 있는 **앱 및 기능** 설정을 사용하여 Windows에서 이전에 설치한 Azure PowerShell을 제거합니다.
    - `%Program Files%\WindowsPowerShell\Modules`에서 모든 **Azure** 모듈을 제거 합니다.

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

1. PowerShell 창을 닫았다가 다시 엽니다.
 
PowerShell에서 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 장애 조치(failover)를 시작하려면 다음 명령을 실행합니다.

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 재해 복구 및 계정 장애 조치(failover)(미리 보기)](storage-disaster-recovery-guidance.md)
- [RA-GRS를 사용하여 항상 사용 가능한 애플리케이션 디자인](storage-designing-ha-apps-with-ragrs.md)
- [자습서: Blob storage를 사용 하 여 고가용성 응용 프로그램 빌드](../blobs/storage-create-geo-redundant-storage.md) 
