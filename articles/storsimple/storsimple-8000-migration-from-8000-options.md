---
title: StorSimple 8000 시리즈 장치에서 데이터 마이그레이션 옵션
description: StorSimple 8000 시리즈에서 데이터를 마이그레이션하는 옵션에 대 한 개요를 제공 합니다.
services: storsimple
author: twooley
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: twooley
ms.openlocfilehash: 92ab570b3e06d67d7986a8103e9622e95eb57e7e
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783912"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>StorSimple 8000 시리즈의 데이터 마이그레이션 옵션

> [!IMPORTANT]
> 2022 년 12 월 31 일에 StorSimple 8000 시리즈는 EOS (지원 종료) 상태에 도달 합니다. StorSimple 8000 시리즈 고객은 문서에 설명 된 대체 방법 중 하나로 마이그레이션하는 것이 좋습니다.

StorSimple 8000 시리즈는 12 월 2022의 [지원 종료](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) 에 도달 하 고 있습니다. StorSimple 8000 시리즈를 실행 하는 고객에 게 다른 Azure 자사 하이브리드 서비스로 업그레이드 하는 옵션이 있습니다. 이 문서에서는 데이터를 마이그레이션하는 데 사용할 수 있는 Azure 하이브리드 옵션에 대해 설명합니다.

## <a name="migration-options"></a>마이그레이션 옵션

StorSimple 8000 시리즈를 사용 하는 고객은 Azure 또는 타사 옵션을 사용할 수 있습니다.

### <a name="azure-options"></a>Azure 옵션

#### <a name="migrate-to-azure-file-sync"></a>Azure 파일 동기화로 마이그레이션

이러한 새 마이그레이션 옵션을 사용 하면 고객이 조직의 파일 공유를 Azure Files에 저장할 수 있습니다. 그러면 이러한 파일 공유에서 AFS(Azure 파일 동기화)를 사용하여 온-프레미스 액세스를 위해 중앙 집중화됩니다. AFS는 Windows Server 호스트에 배포할 수 있습니다. 그러면 실제 데이터 마이그레이션이 호스트 복사본으로 수행되거나 마이그레이션 도구를 사용하여 수행됩니다.

Azure File Sync로 데이터를 마이그레이션하는 방법에 대 한 자세한 내용은 [StorSimple 8100 및 8600 migration to Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)로 이동 합니다.

### <a name="third-party-options"></a>타사 옵션

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura Freedom NAS로 마이그레이션

StorSimple 5000-7000 시리즈 및 StorSimple 8000 시리즈 고객은 Panzura 자유 NAS로 마이그레이션하여 Azure에서 데이터를 유지 하도록 선택할 수 있습니다. Panzura 자유도 솔루션은 데이터 센터, 사무실, 공용 및 사설 클라우드에 걸친 NAS 솔루션을 제공 합니다. 솔루션을 통해 NFS, SMB 및 모바일 클라이언트에 대한 로컬, 하이브리드 및 클라우드 내 데이터 워크플로를 활성화할 수 있습니다.

이 마이그레이션은 Panzura에서 지원하며, 고객은 [Panzura 웹 사이트](https://panzura.com/migrate-storsimple-panzura/)에서 마이그레이션 지원을 요청하여 시작할 수 있습니다.

#### <a name="migrate-to-nasuni"></a>Nasuni로 마이그레이션

Nasuni를 사용 하면 전체 StorSimple 환경을 안정적이 고 안전한 고성능 파일 서비스 플랫폼으로 이동할 수 있습니다. Nasuni는 온-프레미스 파일 저장소의 보안 및 성능을 제공 하는 동시에 Azure의 확장성 및 내구성과 결합 합니다.  최고의 Azure ISV (독립 소프트웨어 공급 업체)로 서, Nasuni는 StorSimple 데이터를 여러 위치에서 공유 하 고 공동 작업할 수 있는 최신 플랫폼으로 이동 하는 데 필요한 모든 도구를 제공 합니다.

지금 시작: [Nasuni 웹 사이트](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>마이그레이션 - 질문과 대답

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>17. StorSimple 8000 시리즈 장치가 서비스의 끝에 도달 하는 경우는 언제 인가요?

A. StorSimple 8000 시리즈는 12 월 2022의 [지원 종료](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) 에 도달 합니다. 지원 종료는 Microsoft에서 12 월 2022 이후 이러한 장치의 하드웨어 및 소프트웨어에 대 한 지원을 더 이상 제공할 수 없음을 의미 합니다. 이제는 디바이스에서 데이터를 마이그레이션하도록 계획하는 것이 좋습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>17. Azure에 저장된 데이터는 어떻게 되나요?  

A. 새 서비스로 마이그레이션한 후에도 Azure에서 데이터를 계속 사용할 수 있습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>17. StorSimple 디바이스에 로컬로 저장된 데이터는 어떻게 되나요?

A. 마이그레이션 문서에서 설명한 대로 로컬 디바이스에 있는 데이터는 새 서비스에 복사할 수 있습니다.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>17. StorSimple 8000 시리즈 어플라이언스를 유지 하려면 어떻게 되나요?

A. 서비스는 계속 작동하지만 Microsoft는 더 이상 하드웨어 및 소프트웨어 지원을 제공하지 않습니다. 비즈니스 연속성을 위해 마이그레이션을 사용하는 것이 좋습니다.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>17. StorSimple 8000 시리즈 장치에서 데이터를 마이그레이션하는 데 사용할 수 있는 옵션은 무엇 인가요?

A. 해당 시나리오에 따라 StorSimple 8000 시리즈 사용자는 다음과 같은 마이그레이션 옵션을 사용할 수 있습니다.

* **Azure 파일 동기화로 마이그레이션**: Azure 원시 형식으로 전환하려면 이 옵션을 사용합니다. Azure 파일 동기화는 파일 공유를 중앙 집중식으로 관리하는 데 사용할 수 있습니다.

* **기타 옵션**: Microsoft 지원에 문의 하 여 여기에 나열 되지 않은 마이그레이션 옵션에 대해 논의할 수 있습니다.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>17. 다른 스토리지 솔루션으로 마이그레이션할 수 있나요?

A. 예. 데이터의 호스트 복사본을 사용하여 다른 스토리지 솔루션으로 마이그레이션할 수 있습니다.

### <a name="q-is-migration-supported-by-microsoft"></a>17. Microsoft에서 마이그레이션을 지원하나요?

A. 8000 시리즈에서 마이그레이션은 완전히 지원 되는 작업입니다. 실제로는 먼저 고객 지원팀에 문의한 후에 마이그레이션을 시작하는 것이 좋습니다. 마이그레이션은 현재 지원형 작업입니다. StorSimple 8000 시리즈 장치에서 데이터를 마이그레이션하려면 [storsimple 지원에 문의 하세요](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>17. Azure File Sync로 마이그레이션에 대 한 가격 책정 모델은 무엇 인가요?

A. Azure File Sync를 사용 하는 경우 서비스에 대 한 구독 요금이 적용 될 수 있습니다. 또한 고객은 지속적인 저장소 비용을 지불 해야 합니다. 예측에 대 한 자세한 내용은 [AFS 가격]( https://azure.microsoft.com/pricing/details/storage/files/) 을 참조 하세요.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>17. 마이그레이션을 완료하는 데 얼마나 오래 걸리나요?

A. 데이터 마이그레이션을 완료하는 데 걸리는 시간은 선택한 데이터의 양과 선택한 업그레이드 옵션에 따라 다릅니다.

## <a name="next-steps"></a>다음 단계

* [StorSimple 8000 시리즈에서 Azure File Sync로 데이터 마이그레이션](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
