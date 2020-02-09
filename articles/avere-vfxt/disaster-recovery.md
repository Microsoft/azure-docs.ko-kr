---
title: Azure 용 Avere vFXT에 대한 재해 복구 지침
description: Avere vFXT에서 Azure에 대한 데이터를 실수로 삭제 하거나 중단 하지 않도록 보호 하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966657"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Azure 용 Avere vFXT에 대한 재해 복구 지침

이 문서에서는 Azure workflow에 대한 Avere vFXT을 보호 하는 전략을 간략하게 설명 하 고 사고 또는 중단 으로부터 복구할 수 있도록 데이터를 백업 하는 지침을 제공 합니다.

Azure에 대한 Avere vFXT는 일시적으로 데이터를 캐시에 저장 합니다. 데이터는 백 엔드 저장소 시스템 온-프레미스 하드웨어 시스템, Azure Blob 저장소 컨테이너 또는 둘 모두에 장기간 저장 됩니다.

중단 및 가능한 데이터 손실을 방지 하려면 다음 네 가지 영역을 고려 하세요.

* Azure 시스템의 Avere vFXT를 사용할 수 없게 되는 경우 가동 중지 시간 방지
* 클러스터 캐시의 데이터 보호
* 백 엔드 NAS 하드웨어 저장소에서 데이터 보호
* 백 엔드 Azure Blob 클라우드 저장소에서 데이터 보호

Azure 고객에 대한 모든 Avere vFXT는 이러한 항목에 대한 계획을 포함 하는 고유한 포괄적인 재해 복구 계획을 만들어야 합니다. VFXT 클러스터에서 사용 하는 응용 프로그램에 복원 력을 빌드할 수도 있습니다. 도움말은 [다음 단계](#next-steps) 에서 링크를 참조 하세요.

## <a name="protect-against-downtime"></a>가동 중지 시간 으로부터 보호

중복성은 Azure 용 Avere vFXT 제품에 기본 제공 됩니다.

* 클러스터는 항상 사용 가능 하며, 개별 클러스터 노드는 중단을 최소화 하면서 장애 조치할 수 있습니다.
* 캐시에서 변경 된 데이터는 장기 저장을 위해 백 엔드 코어 필터 (하드웨어 NAS 또는 Azure Blob)에 정기적으로 기록 됩니다.

Azure 클러스터의 각 Avere vFXT는 단일 가용성 영역에 있어야 하지만 지역 가동 중단 시 액세스를 빠르게 제공 하기 위해 서로 다른 영역 또는 다른 지역에 있는 중복 클러스터를 사용할 수 있습니다.

데이터에 대한 액세스 권한을 잃지 않으려면 저장소 컨테이너를 여러 지역에 배치할 수도 있습니다. 그러나 지역 간 트랜잭션은 지역 내에 유지 되는 트랜잭션 보다 대기 시간이 높고 비용이 더 높습니다.

## <a name="protect-data-in-the-cluster-cache"></a>클러스터 캐시의 데이터 보호

캐시 된 데이터는 정기적인 종료 전에 항상 핵심 필터에 기록 되지만, 미제어 종료에서는 캐시의 변경 된 데이터가 손실 될 수 있습니다.

클러스터를 사용 하 여 파일 읽기를 최적화 하는 경우 손실 되는 변경 내용이 없습니다. 클러스터를 사용 하 여 파일 변경 내용을 캐시 하는 경우 (쓰기) 핵심 filers [캐시 정책을](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) 조정할지 여부를 고려 합니다.<!-- link to legacy doc --> 장기 저장소에 데이터를 기록 하는 빈도를 사용자 지정 합니다.

일반적으로 복구 계획은 더 많은 데이터를 보관 하는 백 엔드 저장소 시스템을 백업 하는 데 집중 해야 하며, 일반적으로 오류 발생 후 워크플로를 다시 설정 하는 데 더 중요 합니다.

## <a name="protect-data-in-nas-core-filers"></a>NAS 코어의 데이터 보호

허용 되는 메서드를 사용 하 여 NAS 공급자가 권장 하는 대로 스냅숏 및 전체 백업을 포함 하 여 온-프레미스 NAS 하드웨어 핵심 필터에 저장 된 데이터를 보호 합니다. 이러한 핵심 필터에 대한 재해 복구는이 문서의 범위를 벗어나는 것입니다.

## <a name="protect-data-in-azure-blob-storage"></a>Azure Blob 저장소의 데이터 보호

Azure 용 Avere vFXT는 Azure Blob core filers에 대해 LRS (로컬 중복 저장소)를 사용 합니다. 즉, Blob 컨테이너의 데이터는 데이터 센터 내의 일시적인 하드웨어 오류에 대한 보호를 위해 자동으로 복사 됩니다.

이 섹션에서는 드문 지역 전체 중단 또는 실수로 인 한 삭제 로부터 Blob storage의 데이터를 추가로 보호 하는 방법에 대한 팁을 제공 합니다.

Azure Blob storage에서 데이터를 보호 하기 위한 모범 사례는 다음과 같습니다.

* 재해 복구 계획에 따라 결정 되는 자주 다른 지역의 다른 저장소 계정에 중요 한 데이터를 복사 합니다.
* 실수로 삭제 하거나 손상 되지 않도록 모든 대상 시스템에서 데이터에 대한 액세스를 제어 합니다. 데이터 저장소에서 [리소스 잠금을](../azure-resource-manager/management/lock-resources.md) 사용 하는 것이 좋습니다.
* Blob core filers에 대한 Avere vFXT for Azure [cloud snapshot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) 기능을 사용 하도록 설정 합니다.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT core 필터 데이터를 백업 계정에 복사 합니다.

다른 계정에서 데이터 백업을 설정 하려면 다음 단계를 수행 합니다.

1. 필요한 경우 새 암호화 키를 생성 하 고 영향을 받는 시스템 외부에 안전 하 게 저장 합니다.

   Avere vFXT for Azure 클러스터에서 데이터를 암호화 하는 경우에는 데이터를 다른 저장소 계정에 복사 하기 전에 새 암호화 키를 생성 해야 합니다. 이러한 키와 암호를 안전한 기능에 안전 하 게 저장 하 고 지역 오류의 영향을 받지 않습니다.

   컨테이너를 원래 클러스터에 다시 추가 하는 경우에도 클러스터에 컨테이너를 추가할 때이 키를 제공 해야 합니다.

   [클라우드 암호화 설정](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>) 읽기<!-- link to legacy doc site --> 자세한 내용은을 (를).

   컨테이너가 Azure의 기본 제공 암호화만 사용 하는 경우이 단계를 건너뛸 수 있습니다.

1. 시스템에서 핵심 필터을 제거 합니다. 이렇게 하면 클러스터에서 변경 된 모든 데이터를 백 엔드 저장소에 기록 합니다.

   백업 후 핵심 필터을 다시 추가 해야 하지만, 제거 하는 것이 모든 데이터가 백 엔드에 완전히 기록 되도록 보장 하는 가장 좋은 방법입니다. "Suspend" 옵션은 때때로 캐시에서 변경 된 데이터를 남길 수 있습니다. <!-- xxx true? or just metadata? -->

   백업 후 컨테이너를 다시 추가할 때 복제할 수 있도록 핵심 필터의 이름 및 연결 정보 (제어판의 **네임 스페이스** 페이지에 나열 됨)를 기록해 둡니다.

   클러스터 제어판을 사용 하 여 핵심 필터을 제거 합니다. [클러스터 제어판을 열고](avere-vfxt-cluster-gui.md) core **필터** > core **필터 관리**를 선택 합니다. 백업 하려는 저장소 시스템을 찾아 **제거** 단추를 사용 하 여 클러스터에서 삭제 합니다.

1. 다른 지역에 있는 다른 저장소 계정에 비어 있는 새 Blob storage 컨테이너를 만듭니다.

1. 편리한 복사 도구를 사용 하 여 핵심 필터의 데이터를 새 컨테이너에 복사 합니다. 복사는 Azure에 대한 Avere vFXT에서 사용 하는 독점 클라우드 파일 시스템 형식을 방해 하지 않고 데이터를 변경 하지 않고 복제 해야 합니다. Azure 기반 도구는 [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)및 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)를 포함 합니다.

1. 데이터를 백업 컨테이너에 복사한 후 [저장소 구성](avere-vfxt-add-storage.md)에 설명 된 대로 원래 컨테이너를 클러스터에 다시 추가 합니다.

   * 클라이언트 워크플로를 변경할 필요가 없도록 동일한 core 필터 이름 및 연결 정보를 사용 합니다.
   * **버킷 내용** 값을 기존 데이터 옵션으로 설정 합니다.
   * 클러스터가 클러스터에 의해 암호화 된 경우 해당 콘텐츠에 대한 현재 암호화 키를 입력 해야 합니다. 1 단계에서 업데이트 한 키입니다.

첫 번째 백업 이후 백업의 경우 새 저장소 컨테이너를 만들 필요가 없습니다. 그러나 현재 키가 저장 된 장소에 저장 되도록 백업을 수행할 때마다 새 암호화 키를 생성 하는 것이 좋습니다.

### <a name="access-a-backup-data-source-during-an-outage"></a>중단 중 백업 데이터 원본 액세스

Azure 클러스터의 Avere vFXT에서 백업 컨테이너에 액세스 하려면 다음 프로세스를 수행 합니다.

1. 필요한 경우 영향을 받지 않는 지역에서 Azure 클러스터에 대한 새 Avere vFXT를 만듭니다.

   > [!TIP]
   > Azure 클러스터에 대해 Avere vFXT를 만들 때 생성 템플릿 및 매개 변수의 복사본을 저장할 수 있습니다. 주 클러스터를 만들 때이 정보를 저장 하는 경우이 정보를 사용 하 여 동일한 속성을 가진 교체 클러스터를 만들 수 있습니다. [요약](avere-vfxt-deploy.md#validation-and-purchase) 페이지에서 **템플릿 및 매개 변수 다운로드** 링크를 클릭 합니다. 클러스터를 만들기 전에 정보를 파일에 저장 합니다.

1. 중복 Blob 컨테이너를 가리키는 새 cloud core 필터를 추가 합니다.

   핵심 필터 만들기 마법사의 **버킷 내용** 설정에서 대상 컨테이너가 이미 데이터를 포함 하도록 지정 해야 합니다. (이 설정을 실수로 비워 두면 시스템에서 경고를 표시 해야 합니다 **.)**  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 필요한 경우 새 클러스터를 탑재 하거나 원본 대신 새 핵심 필터 클라이언트를 업데이트 합니다. 원본 컨테이너와 동일한 이름 및 병합 경로를 사용 하 여 교체 코어 필터를 추가 하는 경우 새 IP 주소에 새 클러스터를 탑재 해야 할 경우를 제외 하 고는 클라이언트 프로세스를 업데이트할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* Azure의 Avere vFXT에 대한 설정을 사용자 지정 하는 방법에 대한 자세한 내용은 [클러스터 튜닝](avere-vfxt-tuning.md)을 참조 하세요.
* Azure에서 재해 복구 및 복원 력 있는 응용 프로그램 빌드에 대해 자세히 알아보세요.

  * [Azure 복구력 기술 지침](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [지역 전체의 서비스 중단에서 복구](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 애플리케이션에 대한 재해 복구 및 고가용성](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
