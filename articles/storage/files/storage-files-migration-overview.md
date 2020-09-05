---
title: Azure 파일 공유로 마이그레이션
description: Azure 파일 공유로의 마이그레이션에 대해 알아보고 마이그레이션 가이드를 찾습니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4223e3bc572a689472dce136b60599034566b274
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654262"
---
# <a name="migrate-to-azure-file-shares"></a>Azure 파일 공유로 마이그레이션

이 문서에서는 Azure 파일 공유로 마이그레이션하는 기본적인 사항을 설명 합니다.

이 문서에는 마이그레이션 기본 사항 및 마이그레이션 가이드의 테이블이 포함 되어 있습니다. 이러한 가이드는 파일을 Azure 파일 공유로 이동 하는 데 도움이 됩니다. 이 가이드는 데이터가 있는 위치 및 이동 하는 배포 모델 (클라우드 전용 또는 하이브리드)을 기반으로 구성 됩니다.

## <a name="migration-basics"></a>마이그레이션 기본 사항

Azure에는 사용 가능한 클라우드 저장소 유형이 여러 개 있습니다. Azure에 대 한 파일 마이그레이션의 기본적인 측면은 데이터에 적합 한 Azure storage 옵션을 결정 하는 것입니다.

[Azure 파일 공유](storage-files-introduction.md) 는 범용 파일 데이터에 적합 합니다. 이 데이터에는에 대 한 온-프레미스 SMB 또는 NFS 공유를 사용 하는 모든 항목이 포함 됩니다. [Azure File Sync](storage-sync-files-planning.md)를 사용 하 여 Windows Server 온-프레미스를 실행 하는 서버에서 여러 Azure 파일 공유의 콘텐츠를 캐시할 수 있습니다.

현재 온-프레미스 서버에서 실행 되는 앱의 경우 Azure 파일 공유에 파일을 저장 하는 것이 좋을 수 있습니다. 앱을 Azure로 이동 하 고 Azure 파일 공유를 공유 저장소로 사용할 수 있습니다. 이 시나리오에 대 한 [Azure 디스크](../../virtual-machines/managed-disks-overview.md) 를 고려할 수도 있습니다.

일부 클라우드 앱은 SMB 또는 컴퓨터 로컬 데이터 액세스 또는 공유 액세스에 의존 하지 않습니다. 이러한 앱에 대해 [Azure blob](../blobs/storage-blobs-overview.md) 과 같은 개체 저장소를 선택 하는 것이 가장 좋습니다.

마이그레이션의 핵심은 파일을 현재 저장소 위치에서 Azure로 이동할 때 적용 가능한 모든 파일 충실도를 캡처하는 것입니다. Azure storage 옵션에 지원 되는 양과 시나리오에 필요한 정도는 적절 한 Azure storage를 선택 하는 데 도움이 됩니다. 일반적으로 범용 파일 데이터는 파일 메타 데이터에 따라 달라 집니다. 앱 데이터는 그렇지 않을 수 있습니다.

파일의 두 가지 기본 구성 요소는 다음과 같습니다.

- **데이터 스트림**: 파일의 데이터 스트림은 파일 콘텐츠를 저장 합니다.
- **파일 메타 데이터**: 파일 메타 데이터에는 다음과 같은 하위 구성 요소가 있습니다.
   * 읽기 전용 같은 파일 특성
   * *NTFS 권한* 또는 *파일 및 폴더 acl* 이라고 하는 파일 사용 권한
   * 타임 스탬프, 특히 생성 및 마지막 수정 타임 스탬프
   * 더 많은 양의 비표준 속성을 저장 하는 데 사용할 수 있는 공간에 해당 하는 대체 데이터 스트림입니다.

마이그레이션의 파일 충실도는 다음과 같은 기능으로 정의할 수 있습니다.

- 원본에 적용 가능한 모든 파일 정보를 저장 합니다.
- 마이그레이션 도구를 사용 하 여 파일을 전송 합니다.
- 마이그레이션의 대상 저장소에 파일을 저장 합니다.

마이그레이션이 원활 하 게 진행 되도록 하려면 [요구 사항에 가장 적합 한 복사 도구](#migration-toolbox) 를 확인 하 고 원본에 대 한 저장소 대상을 일치 시킵니다.

이전 정보를 고려 하 여 Azure에서 범용 파일의 대상 저장소가 [azure 파일 공유](storage-files-introduction.md)임을 확인할 수 있습니다.

Azure blob의 개체 저장소와는 달리, Azure 파일 공유는 기본적으로 파일 메타 데이터를 저장할 수 있습니다. 또한 Azure 파일 공유는 파일 및 폴더 계층, 특성 및 사용 권한을 유지 합니다. NTFS 권한은 온-프레미스에 있기 때문에 파일 및 폴더에 저장할 수 있습니다.

온-프레미스 도메인 컨트롤러인 Active Directory 사용자는 기본적으로 Azure 파일 공유에 액세스할 수 있습니다. 따라서 Azure Active Directory Domain Services 사용자 (Azure AD DS)를 사용할 수 있습니다. 각은 현재 id를 사용 하 여 공유 권한 및 파일 및 폴더 Acl에 따라 액세스 권한을 얻습니다. 이 동작은 사용자가 온-프레미스 파일 공유에 연결 하는 것과 유사 합니다.

대체 데이터 스트림은 현재 Azure 파일 공유의 파일에 저장할 수 없는 파일 충실도의 주요 측면입니다. Azure File Sync 사용 되는 경우 온-프레미스에 유지 됩니다.

Azure [AD 인증](storage-files-identity-auth-active-directory-enable.md) 및 azure 파일 공유에 대 한 [azure AD DS 인증](storage-files-identity-auth-active-directory-domain-service-enable.md) 에 대해 자세히 알아보세요.

## <a name="migration-guides"></a>마이그레이션 가이드

다음 표에는 자세한 마이그레이션 가이드가 나와 있습니다.

표를 사용 하는 방법:

1. 파일이 현재 저장 된 원본 시스템에 대 한 행을 찾습니다.

1. 다음 대상 중 하나를 선택 합니다.

   - 온-프레미스에서 Azure 파일 공유의 콘텐츠를 캐시 하는 Azure File Sync를 사용 하는 하이브리드 배포
   - 클라우드의 Azure 파일 공유

   선택한 항목과 일치 하는 대상 열을 선택 합니다.

1. 원본 및 대상의 교집합 내에서 테이블 셀은 사용 가능한 마이그레이션 시나리오를 나열 합니다. 자세히 마이그레이션 가이드에 직접 연결 하려면 하나를 선택 합니다.

링크가 없는 시나리오는 아직 게시 되지 않은 마이그레이션 가이드를 포함 하지 않습니다. 업데이트에 대 한이 테이블을 수시로 확인 합니다. 새 가이드가 제공 될 때 게시 됩니다.

| 원본 | 대상: </br>하이브리드 배포 | 대상: </br>클라우드 전용 배포 |
|:---|:--|:--|
| | 도구 조합:| 도구 조합: |
| Windows Server 2012 R2 이상 | <ul><li>[Azure 파일 동기화](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync 및 Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>[클라우드에서 Azure File Sync 및 미리 시드 된 파일](storage-sync-offline-data-transfer.md#azure-file-sync-and-pre-seeded-files-in-the-cloud)</li><li>Azure File Sync 및 저장소 마이그레이션 서비스</li></ul> | <ul><li>Azure 파일 동기화</li><li>Azure File Sync 및 Data Box</li><li>Azure File Sync 및 저장소 마이그레이션 서비스</li><li>RoboCopy</li></ul> |
| Windows Server 2012 및 이전 버전 | <ul><li>Azure File Sync 및 Data Box</li><li>Azure File Sync 및 저장소 마이그레이션 서비스</li></ul> | <ul><li>Azure File Sync 및 저장소 마이그레이션 서비스</li><li>RoboCopy</li></ul> |
| NAS (네트워크 연결 저장소) | <ul><li>[Azure File Sync 및 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 또는 Samba | <ul><li>[Azure File Sync 및 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple 클라우드 어플라이언스 8100 또는 StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync 및 StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure 파일 동기화](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>마이그레이션 도구 상자

### <a name="file-copy-tools"></a>파일 복사 도구

Microsoft 및 기타에서 사용할 수 있는 몇 가지 파일 복사 도구가 있습니다. 마이그레이션 시나리오에 적합 한 도구를 선택 하려면 다음과 같은 기본적인 질문을 고려해 야 합니다.

* 이 도구는 파일 복사를 위한 원본 및 대상 위치를 지원 하나요?

* 도구가 원본 및 대상 저장소 위치 사이에서 네트워크 경로나 사용 가능한 프로토콜 (예: REST, SMB 또는 NFS)을 지원 하나요?

* 도구가 원본 및 대상 위치에서 지원 되는 필요한 파일 충실도를 유지 합니까?

    경우에 따라 대상 저장소는 원본과 동일한 충실도를 지원 하지 않습니다. 대상 저장소가 요구 사항에 맞게 충분 한 경우이 도구는 대상의 파일 충실도 기능과만 일치 해야 합니다.

* 도구에 마이그레이션 전략에 맞는 기능이 있나요?

    예를 들어 도구를 사용 하 여 가동 중지 시간을 최소화할 수 있는지 여부를 고려 합니다.
    
    도구에서 소스를 대상으로 미러링 하는 옵션을 지 원하는 경우 원본에 액세스할 수 있는 동안 동일한 원본 및 대상에서 여러 번 실행 하는 경우가 많습니다.

    도구를 처음 실행 하면 데이터가 대량 복사 됩니다. 이 초기 실행은 잠시 동안 지속 될 수 있습니다. 비즈니스 프로세스에 대 한 데이터 원본을 오프 라인으로 전환 하는 데 걸리는 시간이 오래 지속 되는 경우가 많습니다.

    소스를 대상으로 미러링 ( **robocopy/MIR**) 하 여 동일한 원본 및 대상에서 도구를 다시 실행할 수 있습니다. 이전 실행 후에 발생 하는 원본 변경 내용만 전송 해야 하므로 실행 속도가 훨씬 빠릅니다. 이러한 방식으로 복사 도구를 다시 실행 하면 가동 중지 시간을 크게 줄일 수 있습니다.

다음 표에서는 Microsoft 도구 및 Azure 파일 공유에 대 한 현재 적합성을 분류 합니다.

| 권장 | 도구 | Azure 파일 공유에 대 한 지원 | 파일 충실도 유지 |
| :-: | :-- | :---- | :---- |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 지원됨. Azure 파일 공유는 네트워크 드라이브로 탑재할 수 있습니다. | 완전 한 충실도 * |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 파일 동기화 | 기본적으로 Azure 파일 공유에 통합 됩니다. | 완전 한 충실도 * |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| 스토리지 마이그레이션 서비스 | 간접적으로 지원 됩니다. Azure 파일 공유를 SMS 대상 서버에 네트워크 드라이브로 탑재할 수 있습니다. | 완전 한 충실도 * |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy, 버전 10.4 이상| 지원됨. | 완전 한 충실도 * |
|![완전히 권장 되지 않음](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | 지원됨. | 메타 데이터를 복사 하지 않습니다. [Data Box은 Azure File Sync와 함께 사용할 수 있습니다](storage-sync-offline-data-transfer.md). |
|![완전히 권장 되지 않음](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage 탐색기 버전 1.14 | 지원됨. | Acl을 복사 하지 않습니다. 타임 스탬프를 지원 합니다.  |
|![권장하지 않음](media/storage-files-migration-overview/circle-red-x.png)| Azure 데이터 팩터리 | 지원됨. | 메타 데이터를 복사 하지 않습니다. |
|||||

*\* 전체 충실도: Azure 파일 공유 기능을 충족 하거나 초과 합니다.*

### <a name="migration-helper-tools"></a>마이그레이션 도우미 도구

이 섹션에서는 마이그레이션을 계획 하 고 실행 하는 데 도움이 되는 도구에 대해 설명 합니다.

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation의 RoboCopy

RoboCopy는 파일 마이그레이션에 가장 적합 한 도구 중 하나입니다. Windows의 일부로 제공 됩니다. 주요 [RoboCopy 설명서](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 는이 도구의 여러 옵션에 대 한 유용한 리소스입니다.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize Software GmbH

Azure File Sync는 주로 항목 (파일 및 폴더) 수를 사용 하 여 크기를 조정 하며 전체 저장소 크기와는 그렇지 않습니다. TreeSize 도구를 사용 하 여 Windows Server 볼륨의 항목 수를 결정할 수 있습니다.

도구를 사용 하 여 [Azure File Sync 배포](storage-sync-files-deployment-guide.md)전에 큐브 뷰를 만들 수 있습니다. 배포 후 클라우드 계층화가 사용 되는 경우에도이를 사용할 수 있습니다. 이 시나리오에서는 항목 수와 서버 캐시를 가장 많이 사용 하는 디렉터리를 볼 수 있습니다.

테스트 된 버전의 도구는 버전 4.4.1입니다. 클라우드 계층 파일과 호환 됩니다. 이 도구는 일반적인 작업 중 계층화 된 파일의 회수를 발생 하지 않습니다.

## <a name="next-steps"></a>다음 단계

1. 원하는 Azure 파일 공유 (클라우드 전용 또는 하이브리드) 배포에 대 한 계획을 만듭니다.
1. 사용 가능한 마이그레이션 가이드 목록을 검토 하 여 Azure 파일 공유의 원본 및 배포와 일치 하는 상세 가이드를 확인 하세요.

이 문서에서 설명 하는 Azure Files 기술에 대 한 자세한 내용은 다음과 같습니다.

* [Azure 파일 공유 개요](storage-files-introduction.md)
* [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
* [Azure File Sync: 클라우드 계층화](storage-sync-cloud-tiering.md)
