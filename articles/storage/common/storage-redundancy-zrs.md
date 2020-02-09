---
title: ZRS (영역 중복 저장소)를 사용 하 여 항상 사용 가능한 응용 프로그램 빌드
titleSuffix: Azure Storage
description: ZRS(영역 중복 스토리지)는 고가용성 애플리케이션을 빌드하는 간단한 방법을 제공합니다. ZRS는 데이터센터의 하드웨어 오류뿐 아니라 일부 지역적인 재해로부터 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0e6b87ff34d6555fda50518198f9ae3839aa56e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719095"
---
# <a name="build-highly-available-applications-with-zone-redundant-storage-zrs"></a>ZRS (영역 중복 저장소)를 사용 하 여 항상 사용 가능한 응용 프로그램 빌드

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>지원 범위 및 국가별 가용성

ZRS는 현재 표준 범용 v2, FileStorage 및 BlockBlobStorage 저장소 계정 유형을 지원 합니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

범용 v2 ZRS 계정은 블록 blob, 디스크가 아닌 페이지 blob, 표준 파일 공유, 테이블 및 큐를 지원 합니다.

범용 v2 계정의 경우 ZRS는 일반적으로 다음 지역에서 사용할 수 있습니다.

- 동남아시아
- 북유럽
- 서유럽
- 프랑스 중부
- 일본 동부
- 남아프리카 북부
- 영국 남부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 서부 2

FileStorage 계정 (프리미엄 파일 공유) 및 BlockBlobStorage 계정 (프리미엄 블록 blob)의 경우 ZRS은 일반적으로 다음 지역에서 사용할 수 있습니다.

- 서유럽
- 미국 동부

Microsoft는 추가 Azure 지역에서 ZRS를 사용할 수 있도록 계속 지원하고 있습니다. 새 지역에 대한 정보는 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지에서 정기적으로 확인하세요.

**알려진 제한 사항**

- 보관 계층은 현재 ZRS 계정에서 지원 되지 않습니다. 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 을 참조 하세요.
- 관리 디스크는 ZRS을 지원 하지 않습니다. 표준 SSD Managed Disks에 대 한 스냅숏 및 이미지를 표준 HDD 저장소에 저장 하 고 [LRS 및 ZRS 옵션 중에서 선택할](https://azure.microsoft.com/pricing/details/managed-disks/)수 있습니다.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>영역을 사용할 수 없게 되면 어떻게 되나요?

영역을 사용할 수 없게 되는 경우에도 읽기 및 쓰기 작업에는 계속 액세스할 수 있습니다. 일시적인 오류 처리에 대한 지침을 계속 따르는 것이 좋습니다. 이러한 방식에는 지수적 백오프를 사용하여 재시도 정책을 구현하는 것이 포함됩니다.

영역을 사용할 수 없는 경우 Azure에서 DNS 재지정과 같은 네트워킹 업데이트를 수행합니다. 업데이트가 완료되기 전에 데이터에 액세스하는 경우 이러한 업데이트는 애플리케이션에 영향을 줄 수 있습니다.

ZRS는 여러 영역에 영구적인 영향을 주는 지역 재해로부터 데이터를 보호하지 못할 수 있습니다. 대신, 데이터를 일시적으로 사용할 수 없게 되면 ZRS에서 해당 데이터에 대한 복원력을 제공합니다. 지역 재해로부터 보호하려면 GRS(지역 중복 스토리지)를 사용하는 것이 좋습니다. GRS에 대한 자세한 내용은 [GRS(지역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)를 참조하세요.

## <a name="converting-to-zrs-replication"></a>ZRS 복제로 변환

LRS, GRS 및 RA-GRS 간에 마이그레이션하는 것은 간단합니다. Azure Portal 또는 Storage 리소스 공급자 API를 사용하여 계정의 중복 유형을 변경합니다. 그런 다음, Azure에서는 데이터를 적절하게 복제합니다. 

ZRS으로 데이터를 마이그레이션하려면 다른 전략이 필요 합니다. ZRS 마이그레이션에는 단일 스토리지 스탬프에서 지역 내의 여러 스탬프로 데이터의 실제 이동이 포함됩니다.

ZRS로의 마이그레이션을 위한 두 가지 기본 옵션이 있습니다. 

- 기존 계정에서 새 ZRS 계정으로 데이터를 수동으로 복사하거나 이동할 수 있습니다.
- 실시간 마이그레이션을 요청합니다.

> [!IMPORTANT]
> 실시간 마이그레이션은 프리미엄 파일 공유에 현재 지원 되지 않습니다. 현재 데이터를 수동으로 복사 하거나 이동 하는 것만 지원 됩니다.

특정 날짜에 마이그레이션을 완료해야 하는 경우 수동 마이그레이션을 수행하는 것이 좋습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션을 사용하면 타이밍을 제어할 수 있습니다.

수동 마이그레이션을 수행하려면 다음과 같은 옵션이 있습니다.
- Azure Storage 클라이언트 라이브러리 중 하나인 AzCopy와 같은 기존 도구나 신뢰할 수 있는 타사 도구를 사용합니다.
- Hadoop 또는 HDInsight와 친숙한 경우 원본 및 대상(ZRS) 계정 모두를 클러스터에 연결합니다. 그런 다음, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 병렬 처리합니다.
- Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 도구를 빌드합니다.

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다. 

실시간 마이그레이션 중 원본 및 대상 스토리지 스탬프 간에 데이터를 마이그레이션하면서 스토리지 계정을 사용할 수 있습니다. 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

실시간 마이그레이션에 대한 다음과 같은 제한에 유의하십시오.

- Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 ZRS로 마이그레이션된 데이터가 필요한 경우에는 대신 수동 마이그레이션을 수행하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다. 
- 실시간 마이그레이션은 LRS 복제를 사용 하는 저장소 계정에 대해서만 지원 됩니다. 계정에서 GRS 또는 GRS를 사용 하는 경우 계속 하기 전에 먼저 계정의 복제 유형을 LRS로 변경 해야 합니다. 이 중간 단계는 GRS/RA-GRS에서 제공 하는 보조 엔드포인트을 제거 합니다.
- 계정에 데이터가 있어야 합니다.
- 동일한 지역 내에서만 데이터를 마이그레이션할 수 있습니다. 데이터를 원본 계정과 다른 지역에 있는 ZRS 계정으로 마이그레이션하려면 수동 마이그레이션을 수행해야 합니다.
- 표준 스토리지 계정 유형만 실시간 마이그레이션을 지원합니다. 프리미엄 스토리지 계정은 수동으로 마이그레이션해야 합니다.
- ZRS에서 LRS, GRS 또는 RA-GRS로 실시간 마이그레이션은 지원 되지 않습니다. 데이터를 수동으로 새로운 스토리지 계정 또는 기존 스토리지 계정으로 이동해야 합니다.
- 관리 디스크는 LRS에만 사용할 수 있으며 ZRS로 마이그레이션할 수 없습니다. 표준 SSD Managed Disks에 대 한 스냅숏 및 이미지를 표준 HDD 저장소에 저장 하 고 [LRS 및 ZRS 옵션 중에서 선택할](https://azure.microsoft.com/pricing/details/managed-disks/)수 있습니다. 가용성 집합과 통합 [하려면 Azure managed Disks 소개를](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)참조 하세요.
- 보관 데이터를 포함 하는 LRS 또는 GRS 계정은 ZRS로 마이그레이션할 수 없습니다.

실시간 마이그레이션은 [Azure 지원 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 통해 요청할 수 있습니다. 포털에서 ZRS로 변환하려는 스토리지 계정을 선택합니다.
1. **새 지원 요청** 선택
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. **서비스** 섹션에서 **스토리지 계정 관리** 및 ZRS로 변환하려는 리소스를 선택합니다. 
3. **다음**을 선택합니다. 
4. **문제** 섹션에서 다음 값을 지정합니다. 
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **범주**: **ZRS로 마이그레이션을**선택 합니다.
    - **제목**: 설명이 포함된 제목, 예를 들어 **ZRS 계정 마이그레이션**을 입력합니다.
    - **세부 정보**: **세부** 정보 상자에 추가 세부 정보를 입력 합니다. 예를 들어 \_\_ 지역의 [LRS, GRS]에서 ZRS로 마이그레이션하려는 것입니다. 
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

지원 담당자가 사용자에게 연락하여 필요한 모든 지원을 제공합니다.

## <a name="live-migration-to-zrs-faq"></a>ZRS FAQ로 실시간 마이그레이션

**마이그레이션하는 동안 가동 중지 시간을 계획 해야 하나요?**

마이그레이션으로 인 한 가동 중지 시간은 없습니다. 실시간 마이그레이션 중에는 원본 및 대상 저장소 스탬프 간에 데이터를 마이그레이션하는 동안 저장소 계정을 계속 사용할 수 있습니다. 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

**마이그레이션과 관련 된 데이터 손실이 있나요?**

마이그레이션과 관련 된 데이터 손실은 없습니다. 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

**마이그레이션이 완료 되 면 응용 프로그램에 필요한 업데이트가 있나요?**

마이그레이션이 완료 되 면 계정의 복제 유형이 "ZRS (영역 중복 저장소)"로 변경 됩니다. 서비스 엔드포인트, 액세스 키, SAS 및 기타 모든 계정 구성 옵션은 변경 되지 않고 그대로 유지 됩니다.

**범용 v1 계정 (ZRS)에 대 한 실시간 마이그레이션을 요청할 수 있나요?**

ZRS는 일반 용도의 v2 계정만 지원 하므로 실시간 마이그레이션에 대 한 요청을 ZRS로 제출 하기 전에 계정을 범용 v2로 업그레이드 해야 합니다. 자세한 내용은 [Azure storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 및 [범용 v2 저장소 계정으로 업그레이드를](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) 참조 하세요.

**지역 중복 또는 읽기 액세스 지역 중복 저장소 (GRS/RA-GRS) 계정에 대 한 실시간 마이그레이션을 ZRS에 요청할 수 있나요?**

실시간 마이그레이션은 LRS 복제를 사용 하는 저장소 계정에 대해서만 지원 됩니다. 계정에서 GRS 또는 GRS를 사용 하는 경우 계속 하기 전에 먼저 계정의 복제 유형을 LRS로 변경 해야 합니다. 이 중간 단계는 GRS/RA-GRS에서 제공 하는 보조 엔드포인트을 제거 합니다. 또한 실시간 마이그레이션에 대 한 요청을 ZRS로 제출 하기 전에 응용 프로그램 또는 워크 로드에 보조 읽기 전용 엔드포인트에 대 한 액세스 권한이 더 이상 필요 하지 않은지 확인 하 고 저장소 계정의 복제 유형을 LRS (로컬 중복 저장소)로 변경 해야 합니다. 자세한 내용은 [복제 전략 변경](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) 을 참조 하십시오.

**내 저장소 계정에 대 한 실시간 마이그레이션을 요청 하 여 다른 지역에 ZRS?**

원본 계정의 지역과 다른 지역에 있는 ZRS 계정으로 데이터를 마이그레이션하려면 수동 마이그레이션을 수행 해야 합니다 .이 경우

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS 클래식: 블록 Blob 중복성을 위한 레거시 옵션
> [!NOTE]
> Microsoft에서는 2021년 3월 31일에 ZRS 클래식 계정의 사용을 중단하고 마이그레이션할 예정입니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 제공됩니다. 
>
> ZRS가 지역에서 [일반적으로 사용할 수](#support-coverage-and-regional-availability) 있게 되 면 고객은 해당 지역의 포털에서 ZRS 클래식 계정을 만들 수 없습니다. ZRS 클래식의 사용이 중단될 때까지 Microsoft PowerShell 및 Azure CLI를 사용하여 ZRS 클래식 계정을 만드는 것은 옵션입니다.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제된 데이터는 Microsoft가 보조 지역에 장애 조치(failover)를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식 계정을 LRS, GRS 또는 RA-GRS 계정으로 변환하거나, 이러한 계정에서 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 클래식은 GPv1(범용 V1) 스토리지 계정의 **블록 Blob**에서만 사용할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

LRS, ZRS 클래식, GRS 또는 RA-GRS 계정 간에 ZRS 계정 데이터를 수동으로 마이그레이션하려면 AzCopy, Azure Storage Explorer, Azure PowerShell 또는 Azure CLI와 같은 도구 중 하나를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.

ZRS 클래식 계정은 포털에서 ZRS로 업그레이드 하거나, ZRS를 사용할 수 있는 지역에서 Azure PowerShell 또는 Azure CLI를 사용 하 여 업그레이드할 수도 있습니다. Azure Portal에서 ZRS로 업그레이드 하려면 계정의 **구성** 섹션으로 이동 하 고 **업그레이드**를 선택 합니다.

![포털에서 ZRS 클래식을 ZRS로 업그레이드](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

PowerShell을 사용 하 여 ZRS로 업그레이드 하려면 다음 명령을 호출 합니다.
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

CLI를 사용 하 여 ZRS로 업그레이드 하려면 다음 명령을 호출 합니다.
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>참고 항목
- [Azure Storage 복제](storage-redundancy.md)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](storage-redundancy-lrs.md)
- [GRS(지역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)
