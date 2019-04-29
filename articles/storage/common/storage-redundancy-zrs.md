---
title: ZRS(영역 중복 스토리지)에서 고가용성 Azure Storage 응용 프로그램 빌드 | Microsoft Docs
description: ZRS(영역 중복 저장소)는 고가용성 애플리케이션을 빌드하는 간단한 방법을 제공합니다. ZRS는 데이터센터의 하드웨어 오류뿐 아니라 일부 지역적인 재해로부터 보호합니다.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: ab3984b29b3bdfac7599c68c14bd6cc5b671cdf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731422"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>지원 범위 및 국가별 가용성
ZRS는 현재 표준 범용 v2 계정 형식을 지원합니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

ZRS는 블록 Blob, 디스크가 아닌 페이지 Blob, 파일, 테이블 및 큐에 사용할 수 있습니다.

ZRS를 일반적으로 사용할 수 있는 지역은 다음과 같습니다.

- 동남아시아
- 서유럽
- 북유럽
- 프랑스 중부
- 일본 동부
- 영국 남부
- 미국 동부
- 미국 동부 2
- 미국 서부 2
- 미국 중부

Microsoft는 추가 Azure 지역에서 ZRS를 사용할 수 있도록 계속 지원하고 있습니다. 새 지역에 대한 정보는 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지에서 정기적으로 확인하세요.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>영역을 사용할 수 없게 되면 어떻게 되나요?
영역을 사용할 수 없게 되는 경우에도 읽기 및 쓰기 작업에는 계속 액세스할 수 있습니다. 일시적인 오류 처리에 대한 지침을 계속 따르는 것이 좋습니다. 이러한 방식에는 지수적 백오프를 사용하여 재시도 정책을 구현하는 것이 포함됩니다.

영역을 사용할 수 없는 경우 Azure에서 DNS 재지정과 같은 네트워킹 업데이트를 수행합니다. 업데이트가 완료되기 전에 데이터에 액세스하는 경우 이러한 업데이트는 애플리케이션에 영향을 줄 수 있습니다.

ZRS는 여러 영역에 영구적인 영향을 주는 지역 재해로부터 데이터를 보호하지 못할 수 있습니다. 대신, 데이터를 일시적으로 사용할 수 없게 되면 ZRS에서 해당 데이터에 대한 복원력을 제공합니다. 지역 재해로부터 보호하려면 GRS(지역 중복 저장소)를 사용하는 것이 좋습니다. GRS에 대한 자세한 내용은 [GRS(영역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)를 참조하세요.

## <a name="converting-to-zrs-replication"></a>ZRS 복제로 변환
LRS, GRS 및 RA-GRS 간에 마이그레이션하는 것은 간단합니다. Azure Portal 또는 Storage 리소스 공급자 API를 사용하여 계정의 중복 유형을 변경합니다. 그런 다음, Azure에서는 데이터를 적절하게 복제합니다. 

ZRS 간에 데이터를 마이그레이션하려면 다른 전략이 필요합니다. ZRS 마이그레이션에는 단일 스토리지 스탬프에서 지역 내의 여러 스탬프로 데이터의 실제 이동이 포함됩니다.

가지 ZRS로의 마이그레이션 위한 두 가지 기본 옵션이 있습니다. 

- 기존 계정에서 새 ZRS 계정으로 데이터를 수동으로 복사하거나 이동할 수 있습니다.
- 실시간 마이그레이션을 요청합니다.

수동 마이그레이션을 수행하는 것이 좋습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션을 사용하면 타이밍을 제어할 수 있습니다.

수동 마이그레이션을 수행하려면 다음과 같은 옵션이 있습니다.
- Azure Storage 클라이언트 라이브러리 중 하나인 AzCopy와 같은 기존 도구나 신뢰할 수 있는 타사 도구를 사용합니다.
- Hadoop 또는 HDInsight와 친숙한 경우 원본 및 대상(ZRS) 계정 모두를 클러스터에 연결합니다. 그런 다음, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 병렬 처리합니다.
- Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 도구를 빌드합니다.

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 즉각적인 마이그레이션입니다. 

실시간 마이그레이션 중 원본 및 대상 스토리지 스탬프 간에 데이터를 마이그레이션하면서 스토리지 계정을 사용할 수 있습니다. 마이그레이션 과정에서 동일한 수준의 내구성 및 가용성 SLA와 일반적으로 수행 해야 합니다.

실시간 마이그레이션에 대한 다음과 같은 제한에 유의하십시오.

- Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 ZRS로 마이그레이션된 데이터가 필요한 경우에는 대신 수동 마이그레이션을 수행하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다. 
- 실시간 마이그레이션은 LRS 또는 GRS 복제를 사용하는 스토리지 계정에 대해서만 지원됩니다. 계정이 RA-GRS를 사용하는 경우 계속 진행하기 전에 먼저 계정의 복제 유형을 LRS 또는 GRS로 변경해야 합니다. 이 중간 단계에서는 마이그레이션 전에 RA-GRS에서 제공한 보조 읽기 전용 엔드포인트가 제거됩니다.
- 계정에 데이터가 있어야 합니다.
- 동일한 지역 내에서만 데이터를 마이그레이션할 수 있습니다. 데이터를 원본 계정과 다른 지역에 있는 ZRS 계정으로 마이그레이션하려면 수동 마이그레이션을 수행해야 합니다.
- 표준 스토리지 계정 유형만 실시간 마이그레이션을 지원합니다. 프리미엄 스토리지 계정은 수동으로 마이그레이션해야 합니다.
- LRS, GRS 또는 RA-GRS에서 ZRS 실시간 마이그레이션을 지원 되지 않습니다. 새 또는 기존 저장소 계정에 데이터를 수동으로 이동 해야 합니다.

실시간 마이그레이션은 [Azure 지원 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 통해 요청할 수 있습니다. 포털에서 ZRS로 변환하려는 스토리지 계정을 선택합니다.
1. **새 지원 요청** 선택
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. **서비스** 섹션에서 **스토리지 계정 관리** 및 ZRS로 변환하려는 리소스를 선택합니다. 
3. **다음**을 선택합니다. 
4. **문제** 섹션에서 다음 값을 지정합니다. 
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **범주**: **지역 내에서 ZRS로 마이그레이션**을 선택합니다.
    - **제목**: 설명이 포함된 제목, 예를 들어 **ZRS 계정 마이그레이션**을 입력합니다.
    - **세부 정보**: **세부 정보** 상자에 예를 들어 \_\_ 지역의 [LRS, GRS]에서 ZRS로 마이그레이션하려 한다는 추가 세부 정보를 입력합니다. 
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

지원 담당자가 사용자에게 연락하여 필요한 모든 지원을 제공합니다.

## <a name="live-migration-to-zrs-faq"></a>실시간 마이그레이션을 ZRS FAQ

**해야 계획 가동 중지 시간 마이그레이션 중?**

마이그레이션으로 인 한 가동 중지 시간 없이 있습니다. 실시간 마이그레이션 중 데이터 원본 및 대상 저장소 스탬프 간에 마이그레이션하는 동안 저장소 계정 작업을 계속할 수 있습니다. 마이그레이션 과정에서 동일한 수준의 내구성 및 가용성 SLA와 일반적으로 수행 해야 합니다.

**마이그레이션과 관련 된 데이터 손실을 있나요?**

마이그레이션과 관련 된 데이터 손실 되지 않습니다. 마이그레이션 과정에서 동일한 수준의 내구성 및 가용성 SLA와 일반적으로 수행 해야 합니다.

**마이그레이션이 완료 되 면 응용 프로그램에 필요한 업데이트?**

마이그레이션이 완료 되 면 계정 복제 유형의 "영역 중복 저장소 (ZRS)"으로 바뀝니다. 서비스 끝점에 SAS 키를 액세스 하 고 다른 계정 구성 옵션이 변경 되지 않은 한 그대로 유지 합니다.

**내 범용 v1 계정 ZRS로의 실시간 마이그레이션을 요청할 수 있나요?**

ZRS는 범용 v2 계정을 업그레이드 하도록 내릴 ZRS로 실시간 마이그레이션에 대 한 요청을 제출 하기 전에 범용 v2 계정 지원 합니다. 참조 [Azure storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 하 고 [범용 v2 저장소 계정으로 업그레이드](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) 대 한 자세한 내용은 합니다.

**내 읽기 액세스 지역 중복 저장소 (RA-GRS) 계정 ZRS로의 실시간 마이그레이션을 요청할 수 있나요?**

제출 하기 전에 ZRS로 실시간 마이그레이션에 대 한 요청을 응용 프로그램 또는 워크 로드에 더 이상 보조 읽기 전용 끝점에 대 한 액세스를 하며 지역 중복 저장소 (GRS) 복제 유형의 storage 계정 변경에 있는지 확인 합니다. 참조 [복제 전략 변경](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) 대 한 자세한 내용은 합니다.

**다른 지역에 내 저장소 계정 ZRS로의 실시간 마이그레이션을 요청할 수 있나요?**

데이터 소스 계정의 지역에서 다른 지역에 ZRS 계정으로 마이그레이션하려는 경우 수동 마이그레이션을 수행 해야 합니다.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS 클래식: 블록 Blob 중복성을 위한 레거시 옵션
> [!NOTE]
> Microsoft에서는 2021년 3월 31일에 ZRS 클래식 계정의 사용을 중단하고 마이그레이션할 예정입니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 제공됩니다. 
>
> ZRS 되 면 [공급](#support-coverage-and-regional-availability) 특정 지역의 고객이 해당 지역의 포털에서 ZRS 클래식 계정을 만들 수 없습니다. ZRS 클래식의 사용이 중단될 때까지 Microsoft PowerShell 및 Azure CLI를 사용하여 ZRS 클래식 계정을 만드는 것은 옵션입니다.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제된 데이터는 Microsoft가 보조 지역에 장애 조치(failover)를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식 계정을 LRS, GRS 또는 RA-GRS 계정으로 변환하거나, 이러한 계정에서 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 클래식은 GPv1(범용 V1) 저장소 계정의 **블록 Blob**에서만 사용할 수 있습니다. 저장소 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

LRS, ZRS 클래식, GRS 또는 RA-GRS 계정 간에 ZRS 계정 데이터를 수동으로 마이그레이션하려면 AzCopy, Azure Storage 탐색기, Azure PowerShell 또는 Azure CLI와 같은 도구 중 하나를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.

ZRS 클래식 계정을 ZRS를 사용할 수 있는 지역에서 Azure CLI 또는 Azure PowerShell을 사용 하거나 포털에서 ZRS를 업그레이드할 수도 있습니다.

로 업그레이드 하는 포털에서 ZRS 계정 구성 섹션으로 이동 하 고 업그레이드를 선택 합니다.![ZRS 클래식 포털에서 ZRS로 업그레이드](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

ZRS를 사용 하 여로 업그레이드 하려면 PowerShell 명령을 호출 합니다.
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

ZRS를 사용 하 여로 업그레이드 하려면 CLI 명령을 호출 합니다.
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>참고 항목
- [Azure Storage 복제](storage-redundancy.md)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복성](storage-redundancy-lrs.md)
- [GRS(영역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)
