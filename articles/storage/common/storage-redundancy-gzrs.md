---
title: GZRS (지역 중복 저장소) (미리 보기)를 사용 하 여 항상 사용 가능한 Azure Storage 응용 프로그램 빌드 | Microsoft Docs
description: GZRS (지역 중복 저장소)는 GRS (지역 중복 저장소)에서 제공 하는 지역 가동 중단 으로부터 보호 하는 ZRS (영역 중복 저장소)의 고가용성을 결혼 합니다. GZRS 저장소 계정의 데이터는 주 지역의 Azure 가용성 영역에 복제 되 고 지역 재해 로부터 보호 하기 위해 보조 지역에도 복제 됩니다.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c102941920d2b8746a49be47ef4c5613fa0bc281
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719078"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>GZRS (지역 중복 저장소) (미리 보기)를 사용 하 여 항상 사용 가능한 Azure Storage 응용 프로그램 빌드

GZRS (지역 중복 저장소) (미리 보기)는 지역 중복 저장소 ( [GRS)](storage-redundancy-grs.md)에서 제공 하는 지역 가동 중단 으로부터 보호 하는 [ZRS (영역 중복 저장소)](storage-redundancy-zrs.md) 의 고가용성을 결혼 합니다. GZRS 저장소 계정의 데이터는 주 지역의 3 개 [Azure 가용성 영역](../../availability-zones/az-overview.md) 에 복제 되 고 지역 재해 로부터 보호 하기 위해 보조 지역에도 복제 됩니다. 각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 자세한 내용 및 예외는 [설명서](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)를 참조 하세요.

GZRS 저장소 계정을 사용 하 여 가용성 영역을 사용할 수 없게 되거나 복구할 수 없는 경우 계속 해 서 데이터를 읽고 쓸 수 있습니다. 또한 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해가 발생 한 경우에도 데이터를 지속적으로 사용할 수 있습니다. GZRS는 지정 된 연도 동안 최소 99.99999999999999% (16 9의) 개체 내 구성을 제공 하도록 설계 되었습니다. 또한 GZRS는 LRS, ZRS, GRS 또는 RA-GRS와 동일한 확장성 목표를 제공 합니다. 응용 프로그램이 주 지역에서 재해가 발생 한 경우 데이터를 읽을 수 있어야 하는 경우 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하 여 보조 지역의 데이터에 대 한 읽기 액세스를 선택적으로 사용할 수 있습니다.

일관성, 내구성, 고가용성, 뛰어난 성능 및 재해 복구에 대 한 복원 력을 필요로 하는 응용 프로그램에 GZRS를 사용 하는 것이 좋습니다. 지역 재해가 발생 한 경우 보조 지역에 대 한 읽기 액세스의 추가 보안을 위해 저장소 계정에 대해 RA GZRS을 사용 하도록 설정 합니다.

## <a name="about-the-preview"></a>미리 보기 정보

범용 v2 저장소 계정만 GZRS 및 RA-GZRS을 지원 합니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요. GZRS 및 RA-GZRS는 블록 blob, 페이지 blob (VHD 디스크가 아님), 파일, 테이블 및 큐를 지원 합니다.

GZRS 및 RA-GZRS는 현재 다음 지역에서 미리 보기로 제공 됩니다.

- 동남아시아
- 북유럽
- 서유럽
- 일본 동부
- 영국 남부
- 미국 동부
- 미국 동부 2
- 미국 중부

Microsoft는 추가 Azure 지역에서 GZRS 및 RA GZRS을 계속 사용할 수 있습니다. 지원 되는 지역에 대 한 자세한 내용은 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지를 정기적으로 확인 하세요.

미리 보기 가격 책정에 대 한 자세한 내용은 [blob](https://azure.microsoft.com/pricing/details/storage/blobs), [파일](https://azure.microsoft.com/pricing/details/storage/files/), [큐](https://azure.microsoft.com/pricing/details/storage/queues/)및 [테이블](https://azure.microsoft.com/pricing/details/storage/tables/)에 대 한 GZRS 미리 보기 가격 책정을 참조 하세요.

> [!IMPORTANT]
> 프로덕션 워크 로드에는 미리 보기 기능을 사용 하지 않는 것이 좋습니다.

## <a name="how-gzrs-and-ra-gzrs-work"></a>GZRS 및 RA GZRS 작동 방식

GZRS 또는 RA GZRS를 사용 하는 저장소 계정에 데이터를 쓰면 해당 데이터가 먼저 세 개의 가용성 영역에 걸쳐 주 지역에서 동기식으로 복제 됩니다. 그런 다음 데이터는 수백 마일 떨어진 두 번째 지역으로 비동기식으로 복제 됩니다. 데이터가 보조 지역에 기록 되 면 [LRS (로컬 중복 저장소)](storage-redundancy-lrs.md)를 사용 하 여 해당 지역 내에서 동기적으로 세 번 복제 됩니다.

> [!IMPORTANT]
> 비동기 복제는 데이터를 주 지역에 쓰고 보조 지역에 복제 되는 시간 사이에 지연이 발생 합니다. 지역 재해의 경우 주 지역에서 해당 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.

저장소 계정을 만들 때 해당 계정의 데이터를 복제 하는 방법을 지정 하 고 해당 계정에 대 한 주 지역도 지정 합니다. 지리적으로 복제 된 계정에 대 한 쌍을 이루는 보조 지역은 주 지역에 따라 결정 되며 변경할 수 없습니다. Azure에서 지원하는 지역에 대한 최신 정보는 [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요. GZRS 또는 RA-GZRS를 사용 하 여 저장소 계정을 만드는 방법에 대 한 자세한 내용은 [저장소 계정 만들기](storage-account-create.md)를 참조 하세요.

### <a name="use-ra-gzrs-for-high-availability"></a>고가용성을 위해 RA GZRS 사용

저장소 계정에 대해 GZRS를 사용 하도록 설정 하면 저장소 계정에 대 한 기본 엔드포인트 뿐만 아니라 보조 엔드포인트에서 데이터를 읽을 수 있습니다. 보조 엔드포인트은 계정 이름에 접미사 *-보조* 를 추가 합니다. 예를 들어, Blob service에 대한 기본 엔드포인트가 `myaccount.blob.core.windows.net`인 경우, 보조 엔드포인트는 `myaccount-secondary.blob.core.windows.net`입니다. 스토리지 계정에 대한 액세스 키는 기본 및 보조 엔드포인트에 대해 동일합니다.

지역 가동 중단이 발생 하는 경우 RA-GZRS을 활용 하려면이 시나리오를 처리 하기 위해 응용 프로그램을 미리 디자인 해야 합니다. 응용 프로그램은 기본 엔드포인트에서 읽고 써야 하지만 주 지역을 사용할 수 없게 되는 경우 보조 엔드포인트을 사용 하도록 전환 해야 합니다. RA를 사용 하 여 고가용성을 설계 하는 방법에 대 한 지침은 [GZRS 또는 GZRS를 사용 하 여 항상 사용 가능한 응용 프로그램 디자인](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs)을 참조 하세요.

데이터는 보조 지역에 비동기식으로 복제 되므로 보조 지역은 주로 주 지역 뒤에 있습니다. 보조 지역에 복제 된 쓰기 작업을 확인 하기 위해 응용 프로그램은 저장소 계정에 대 한 마지막 동기화 시간을 확인 합니다. 마지막 동기화 시간 이전에 주 지역에 작성 된 모든 쓰기 작업은 보조 지역에 성공적으로 복제 되어 보조 지역에서 읽을 수 있습니다. 마지막 동기화 시간 이후 주 지역에 작성 된 모든 쓰기 작업은 보조 지역으로 복제 될 수도 있고 그렇지 않을 수도 있습니다. 즉, 읽기 작업에 사용할 수 없습니다.

Azure PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용 하 여 **마지막 동기화 시간** 속성의 값을 쿼리할 수 있습니다. **마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다.

GZRS를 통한 성능 및 확장성에 대 한 추가 지침은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](storage-performance-checklist.md)을 참조 하세요.

### <a name="availability-zone-outages"></a>가용성 영역 중단

주 지역의 가용성 영역에 영향을 주는 장애가 발생 하는 경우 응용 프로그램은 해당 지역에 대 한 다른 가용성 영역을 사용 하 여 저장소 계정에 대 한 읽기 및 쓰기 작업을 원활 하 게 계속할 수 있습니다. GZRS 또는 ZRS를 사용 하는 경우 일시적인 오류 처리에 대 한 모범 사례를 계속 수행 하는 것이 좋습니다. 이러한 방식에는 지수적 백오프를 사용하여 재시도 정책을 구현하는 것이 포함됩니다.

가용성 영역을 사용할 수 없게 되 면 Azure 배포한는 DNS와 같은 네트워킹 업데이트를 업데이트 합니다. 업데이트를 완료 하기 전에 데이터에 액세스 하는 경우 이러한 업데이트는 응용 프로그램에 영향을 줄 수 있습니다.

### <a name="regional-outages"></a>지역 중단

오류가 전체 주 지역에 영향을 주는 경우 Microsoft는 먼저 주 지역 복원을 시도 합니다. 복원이 불가능 한 경우 Microsoft는 보조 지역으로 장애 조치 (failover) 하 여 보조 지역이 새 주 지역이 됩니다. 저장소 계정에 RA-GZRS가 설정 된 경우이 시나리오에 맞게 설계 된 응용 프로그램은 장애 조치 (failover)를 기다리는 동안 보조 지역에서 읽을 수 있습니다. 저장소 계정에서 GZRS를 사용 하도록 설정 하지 않은 경우 장애 조치 (failover)가 완료 될 때까지 응용 프로그램이 보조 데이터베이스에서 읽을 수 없게 됩니다.

> [!NOTE]
> GZRS 및 RA-GZRS는 현재 미국 동부 지역 에서만 미리 보기로 제공 됩니다. 고객이 관리 하는 계정 장애 조치 (failover) (미리 보기)는 미국 동부 2에서 아직 사용할 수 없으므로 현재 GZRS 및 RA GZRS 계정을 사용 하 여 계정 장애 조치 (failover) 이벤트를 관리할 수 없습니다. 미리 보기 중에 Microsoft는 GZRS 및 RA GZRS 계정에 영향을 주는 장애 조치 (failover) 이벤트를 관리 합니다.

데이터는 보조 지역에 비동기적으로 복제 되기 때문에 주 지역에 영향을 주는 오류로 인해 주 지역을 복구할 수 없는 경우 데이터가 손실 될 수 있습니다. 주 지역에 대 한 최신 쓰기와 보조 지역에 대 한 마지막 쓰기 사이의 간격을 RPO (복구 지점 목표) 라고 합니다. RPO는 데이터를 복구할 수 있는 시점을 나타냅니다. 현재는 데이터를 보조 지역으로 복제 하는 데 걸리는 시간에 대 한 SLA는 없지만 일반적으로는 15 분 미만의 RPO가 Azure Storage 됩니다.

RTO (복구 시간 목표)는 장애 조치 (failover)를 수행 하 고 저장소 계정을 다시 온라인 상태로 전환 하는 데 걸리는 시간을 측정 한 것입니다. 이 측정값은 보조 위치를 가리키도록 기본 DNS 항목을 변경 하 여 Azure에서 장애 조치 (failover)를 수행 하는 데 필요한 시간을 나타냅니다.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>저장소 계정을 GZRS 또는 RA로 마이그레이션-GZRS

기존 저장소 계정을 GZRS 또는 RA-GZRS로 마이그레이션할 수 있습니다. LRS, GRS 또는 RA-GRS 계정에서 마이그레이션하는 과정에서 기존 ZRS 계정에서 GZRS 또는 RA-GZRS를 쉽게 마이그레이션할 수 있습니다. 다음 섹션에서는 두 경우 모두 마이그레이션하는 방법에 대해 설명 합니다.

**알려진 제한 사항**

- 보관 계층은 현재 (RA-) GZRS 계정에서 지원 되지 않습니다. 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 을 참조 하세요.
- 관리 디스크는 (RA-) GZRS을 지원 하지 않습니다. 표준 SSD Managed Disks에 대 한 스냅숏 및 이미지를 표준 HDD 저장소에 저장 하 고 [LRS 및 ZRS 옵션 중에서 선택할](https://azure.microsoft.com/pricing/details/managed-disks/)수 있습니다.

### <a name="migrating-from-a-zrs-account"></a>ZRS 계정에서 마이그레이션

기존 ZRS 계정을 RA-GZRS로 변환 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) cmdlet을 사용 하 여 계정에 대 한 SKU를 변경 합니다. 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>LRS, GRS 또는 RA-GRS 계정에서 마이그레이션

LRS, GRS 또는 RA-GRS 계정에서 GZRS 또는 GZRS로 마이그레이션하는 두 가지 옵션이 있습니다.

- 기존 계정에서 데이터를 새 GZRS 또는 RA GZRS 계정으로 수동으로 복사 하거나 이동할 수 있습니다.
- 실시간 마이그레이션을 요청할 수 있습니다.

#### <a name="perform-a-manual-migration"></a>수동 마이그레이션 수행

특정 날짜에 마이그레이션이 완료 되어야 하는 경우 수동 마이그레이션을 수행 하는 것이 좋습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션을 사용하면 타이밍을 제어할 수 있습니다.

기존 계정에서 GZRS 또는 RA GZRS 계정으로 데이터를 수동으로 마이그레이션하려면 데이터를 효율적으로 복사할 수 있는 도구를 사용 합니다. 몇 가지 예를 들면 다음과 같습니다.

- AzCopy 또는 신뢰할 수 있는 타사 도구와 같은 유틸리티를 사용 합니다. AzCopy에 대 한 자세한 내용은 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조 하세요.
- Hadoop 또는 HDInsight에 대해 잘 알고 있는 경우 원본 및 대상 저장소 계정을 모두 클러스터에 연결 합니다. 다음으로, DistCp와 같은 도구를 사용 하 여 데이터 복사 프로세스를 병렬화 합니다.
- Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 도구를 빌드합니다.

#### <a name="perform-a-live-migration"></a>실시간 마이그레이션 수행

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다.

실시간 마이그레이션 중에는 원본 및 대상 저장소 계정 간에 데이터를 마이그레이션하는 동안 저장소 계정을 사용할 수 있습니다. 실시간 마이그레이션 프로세스 중에는 계정이 내구성 및 가용성에 대 한 SLA를 계속 충족 합니다. 실시간 마이그레이션으로 인 한 가동 중지 시간 또는 데이터 손실은 없습니다.

범용 v2 계정만 GZRS/RA-GZRS을 지원 하므로 GZRS/RA-GZRS로 실시간 마이그레이션 요청을 제출 하기 전에 계정을 일반 용도의 v2로 업그레이드 해야 합니다. 자세한 내용은 [Azure storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 및 [범용 v2 저장소 계정으로 업그레이드](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)를 참조 하세요.

마이그레이션이 완료 되 면 저장소 계정의 복제 설정이 **GZRS (지역 중복 저장소)** 또는 **읽기 액세스 지역 중복 저장소 (RA-GZRS)** 로 업데이트 됩니다. 서비스 엔드포인트, 액세스 키, SAS (공유 액세스 서명) 및 기타 모든 계정 구성 옵션은 변경 되지 않고 그대로 유지 됩니다.

실시간 마이그레이션에 대한 다음과 같은 제한에 유의하십시오.

- Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 GZRS 또는 RA-GZRS로 데이터를 마이그레이션해야 하는 경우에는 대신 수동 마이그레이션을 수행 하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다.
- 실시간 마이그레이션은 GRS 또는 RA GRS 복제를 사용 하는 저장소 계정에 대해서만 지원 됩니다. 계정에서 LRS를 사용 하는 경우 계속 하기 전에 먼저 계정의 복제 유형을 GRS 또는 RA-GRS로 변경 해야 합니다. 이 중간 단계는 GRS/RA-GRS에서 제공 하는 보조 엔드포인트을 추가 합니다.
- 계정에 데이터가 있어야 합니다.
- 동일한 지역 내에서만 데이터를 마이그레이션할 수 있습니다.
- 표준 스토리지 계정 유형만 실시간 마이그레이션을 지원합니다. 프리미엄 스토리지 계정은 수동으로 마이그레이션해야 합니다.
- GZRS 또는 GZRS 계정에서 LRS, GRS 또는 RA-GRS 계정으로 실시간 마이그레이션은 지원 되지 않습니다. 데이터를 수동으로 새로운 스토리지 계정 또는 기존 스토리지 계정으로 이동해야 합니다.
- RA-GRS에서 RA-GZRS로 실시간 마이그레이션을 요청할 수 있습니다. 그러나 RA-GRS에서 GZRS로의 마이그레이션은 지원 되지 않습니다. 이 경우 GZRS에 대 한 실시간 마이그레이션을 요청한 다음 GZRS를 사용 하도록 저장소 계정을 수동으로 변환 해야 합니다.
- 관리 디스크는 LRS만 지원 하며 GZRS 또는 RA-GZRS로 마이그레이션할 수 없습니다. 가용성 집합과 통합 하는 방법에 대해서는 [Azure managed Disks 소개](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)를 참조 하세요.
- 표준 SSD Managed Disks에 대 한 스냅숏 및 이미지를 표준 HDD 저장소에 저장 하 고 [LRS, ZRS, GZRS 및 RA-GZRS 옵션 중에서 선택할](https://azure.microsoft.com/pricing/details/managed-disks/)수 있습니다.
- GZRS에는 대량 파일 공유가 포함 된 계정이 지원 되지 않습니다.

실시간 마이그레이션을 요청 하려면 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 사용 합니다. 포털에서 GZRS 또는 RA-GZRS로 마이그레이션할 저장소 계정을 선택 하 고 다음 지침을 따릅니다.

1. **새 지원 요청**을 선택 합니다.
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. **서비스** 섹션에서 **Storage 계정 관리** 를 선택 하 고 마이그레이션할 계정을 지정 합니다.
3. **다음**을 선택합니다.
4. **문제** 섹션에서 다음 값을 지정합니다.
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **범주**: **지역 내에서 (RA-) GZRS로 마이그레이션을**선택 합니다.
    - **제목**: 설명 제목을 입력 합니다 (예: **(RA-) GZRS 계정 마이그레이션)** .
    - **세부 정보**: **세부** 정보 상자에 추가 세부 정보를 입력 합니다. 예를 들어 \_\_ 지역의 [LRS, GRS]에서 GZRS로 마이그레이션해야 합니다. " 또는 "\_\_ 지역의 [LRS, RA-GRS]에서 RA-GZRS로 마이그레이션해야 합니다."
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

지원 담당자는 도움을 요청 하 여 도움을 받을 수 있습니다.

## <a name="see-also"></a>참고 항목

- [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [표준 스토리지 계정의 확장성 및 성능 목표](scalability-targets-standard-account.md)
