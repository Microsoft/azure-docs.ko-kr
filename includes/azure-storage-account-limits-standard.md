---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/13/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0ecb4f5fe54e9895898156893c1e686c6cc24e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392408"
---
다음 표에서는 Azure 범용 v1, v2 및 Blob storage 계정에 대한 기본 제한을 설명 합니다. *수신* 제한은 저장소 계정으로 전송 되는 요청의 모든 데이터를 나타냅니다. *송신* 한도는 저장소 계정에서 받은 응답의 모든 데이터를 나타냅니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 표준 및 프리미엄 계정 모두를 포함하는 구독당 지역당 스토리지 계정 수 | 250 |
| 최대 저장소 계정 용량 | 2, 미국 및 유럽, 500 TiB, 기타 모든 지역 (영국 포함)<sup>1</sup>|
| 저장소 계정 당 blob 컨테이너, blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 무제한 |
| 스토리지 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 저장소 계정 당 최대 수신<sup>1</sup> (미국, 유럽 지역) | 25gbps |
| 저장소 계정 당 최대 수신<sup>1</sup> (미국과 유럽 이외의 지역) | GRS/GRS를 사용 하는 경우 5gbps, LRS/ZRS<sup>2</sup> 의 경우 10gbps |
| 범용 v2 및 Blob 저장소 계정에 대한 최대 송신 (모든 지역) | 50Gbps |
| 범용 v1 저장소 계정에 대한 최대 송신 (미국 지역) | GRS/GRS를 사용 하는 경우 20gbps, LRS/ZRS<sup>2</sup> 의 경우 30gbps |
| 범용 v1 저장소 계정에 대한 최대 송신 (미국 외 지역) | GRS/GRS를 사용 하는 경우 10gbps, LRS/ZRS<sup>2</sup> 의 경우 15gbps |
| 저장소 계정 당 최대 가상 네트워크 규칙 수 | 200 |
| 저장소 계정 당 최대 IP 주소 규칙 수 | 200 |

<sup>1</sup> Azure Storage 표준 계정은 더 높은 용량 제한을 지원 하 고 요청으로 수신 하는 데 더 높은 제한을 지원 합니다. 수신에 대한 계정 제한을 늘리도록 요청하려면 [Azure 고객 지원팀](https://azure.microsoft.com/support/faq/)에 문의하세요. 자세한 내용은 [더 크고 더 높은 규모의 스토리지 계정 발표](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)를 참조하세요.

<sup>2</sup> 저장소 계정에 GRS (지역 중복 저장소) 또는 GZRS (지역 중복 저장소)를 사용 하는 읽기 액세스 권한이 있는 경우 보조 위치에 대한 송신 대상은 기본 위치와 동일 합니다. [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션은 다음과 같습니다.

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> 대부분의 시나리오에서는 범용 v2 저장소 계정을 사용 하는 것이 좋습니다. 가동 중지 시간 없이 데이터를 복사할 필요 없이 일반적인 용도의 v1 또는 Azure Blob storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다. 자세한 내용은 [범용 v2 저장소 계정으로 업그레이드](../articles/storage/common/storage-account-upgrade.md)를 참조 하세요.

애플리케이션의 요구가 단일 스토리지 계정의 확장성 목표를 초과하는 경우 여러 스토리지 계정을 사용하도록 애플리케이션을 빌드하면 됩니다. 그런 다음 해당 스토리지 계정 간에 데이터 개체를 분할할 수 있습니다. 볼륨 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

모든 스토리지 계정은 플랫 네트워크 토폴로지에서 실행되고 작성된 시기에 관계 없이 이 문서에 설명된 확장성 및 성능 목표를 지원합니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 일관성과 가용성이 뛰어난 클라우드 Storage 서비스](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)를 참조하세요.
