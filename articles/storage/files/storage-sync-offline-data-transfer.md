---
title: Azure Data Box를 사용 하 여 데이터를 Azure File Sync으로 마이그레이션
description: Azure File Sync와 호환 되는 방식으로 대량 데이터를 마이그레이션합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b1c167c71907e5f8af1006dfabd8f81ce4425d09
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291161"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>대량 데이터를 Azure File Sync으로 마이그레이션
다음 두 가지 방법으로 대량 데이터를 Azure File Sync로 마이그레이션할 수 있습니다.

* **Azure File Sync를 사용 하 여 파일을 업로드 합니다.** 이는 가장 간단한 방법입니다. 파일을 로컬에서 Windows Server 2012 R2 이상으로 이동 하 고 Azure File Sync 에이전트를 설치 합니다. 동기화를 설정 하면 파일이 서버에서 업로드 됩니다. (현재 고객은 2 일 마다 1 TiB의 평균 업로드 속도를 경험 합니다.) 서버에서 데이터 센터에 너무 많은 대역폭을 사용 하지 않도록 하려면 [대역폭 제한 일정](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)을 설정 하는 것이 좋습니다.
* **오프 라인으로 파일을 전송 합니다.** 충분 한 대역폭이 없는 경우 적절 한 시간 내에 Azure에 파일을 업로드 하지 못할 수 있습니다. 챌린지는 전체 파일 집합의 초기 동기화입니다. 이러한 문제를 해결 하려면 [Azure Data Box 제품군](https://azure.microsoft.com/services/storage/databox)과 같은 오프 라인 대량 마이그레이션 도구를 사용 합니다. 

이 문서에서는 Azure File Sync와 호환 되는 방식으로 오프 라인으로 파일을 마이그레이션하는 방법을 설명 합니다. 다음 지침에 따라 파일 충돌을 방지 하 고 동기화를 사용 하도록 설정한 후 파일 및 폴더 Acl (access control 목록) 및 타임 스탬프를 유지 합니다.

## <a name="migration-tools"></a>마이그레이션 도구
이 문서에서 설명 하는 프로세스는 Data Box 뿐만 아니라 오프 라인 마이그레이션 도구에도 적용 됩니다. 또한 인터넷을 통해 직접 작업 하는 AzCopy, Robocopy 또는 파트너 도구 및 서비스와 같은 도구에도 작동 합니다. 그러나 초기 업로드 챌린지를 극복 하려면이 문서의 단계를 수행 하 여 Azure File Sync와 호환 되는 방식으로 이러한 도구를 사용 합니다.

경우에 따라 Azure File Sync를 도입 하기 전에 Windows Server 간에 이동 해야 합니다. SMS ( [저장소 마이그레이션 서비스](https://aka.ms/storagemigrationservice) )가이를 지원할 수 있습니다. Azure File Sync (Windows Server 2012R2 이상)에서 지원 되는 서버 OS 버전으로 마이그레이션해야 하는지 아니면 단순히 마이그레이션해야 하는지 여부는 Azure File Sync에 대 한 새 시스템을 구입 하기 때문에 SMS에는 migr을 가져오는 데 도움이 되는 다양 한 기능 및 이점이 있습니다. 순조롭게 수행 되었습니다.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>도구를 사용 하 여 오프 라인으로 데이터 전송의 이점
오프 라인 마이그레이션에 Data Box와 같은 전송 도구를 사용할 경우의 주요 이점은 다음과 같습니다.

- 네트워크를 통해 모든 파일을 업로드할 필요는 없습니다. 큰 네임 스페이스의 경우이 도구를 통해 상당한 네트워크 대역폭과 시간을 절약할 수 있습니다.
- Azure File Sync 사용 하는 경우 사용 하는 전송 도구 (Data Box, Azure Import/Export 서비스 등)에 관계 없이 라이브 서버는 데이터를 Azure로 이동한 후 변경 되는 파일만 업로드 합니다.
- 오프 라인 대량 마이그레이션 도구에서 Acl을 전송 하지 않는 경우에도 Azure File Sync에서 파일 및 폴더 Acl을 동기화 합니다.
- Data Box 및 Azure File Sync에는 가동 중지 시간이 필요 하지 않습니다. Data Box를 사용 하 여 Azure로 데이터를 전송 하는 경우 네트워크 대역폭을 효율적으로 사용 하 고 파일 충실도를 유지 합니다. 데이터를 Azure로 이동한 후 변경 되는 파일만 업로드 하 여 네임 스페이스를 최신 상태로 유지할 수도 있습니다.

## <a name="prerequisites-for-the-offline-data-transfer"></a>오프 라인 데이터 전송에 대 한 필수 구성 요소
오프 라인 데이터 전송을 완료 하기 전에 마이그레이션하는 서버에서 동기화를 사용 하도록 설정 하면 안 됩니다. 시작 하기 전에 고려해 야 할 사항은 다음과 같습니다.

- 대량 마이그레이션에 Data Box을 사용 하려면 [Data Box에 대 한 배포 필수 구성 요소](../../databox/data-box-deploy-ordered.md#prerequisites)를 검토 합니다.
- 최종 Azure File Sync 토폴로지 계획: [Azure File Sync 배포에 대 한 계획](storage-sync-files-planning.md)
- 동기화하려는 파일 공유를 포함할 Azure Storage 계정을 선택합니다. 동일한 스토리지 계정의 임시 스테이징 공유 위치로 대량 마이그레이션이 실행되도록 합니다. 대량 마이그레이션은 동일한 스토리지 계정에 있는 최종 및 스테이징 공유 위치로만 수행할 수 있습니다.
- 대량 마이그레이션은 서버 위치와의 새 동기화 관계를 만들 때만 사용할 수 있습니다. 기존 동기화 관계로는 대량 마이그레이션을 사용하도록 설정할 수 없습니다.


## <a name="process-for-offline-data-transfer"></a>오프 라인 데이터 전송 프로세스
Azure Data Box와 같은 대량 마이그레이션 도구와 호환 되는 방식으로 Azure File Sync를 설정 하는 방법은 다음과 같습니다.

![Azure File Sync를 설정 하는 방법을 보여 주는 다이어그램](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 단계 | 세부 정보 |
|---|---------------------------------------------------------------------------------------|
| ![1단계](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box를 주문](../../databox/data-box-deploy-ordered.md)합니다. Data Box 제품군은 요구 사항에 맞는 [여러 제품](https://azure.microsoft.com/services/storage/databox/data) 을 제공 합니다. Data Box 수신 되 면 해당 설명서에 따라 Data Box: *\\< DeviceIPAddres\>* \<StorageAccountName_AzFile\>\<ShareName\>에서이 UNC 경로에 [데이터를 복사](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) 합니다. 여기서 *ShareName* 은 준비 공유의 이름입니다. Data Box를 Azure로 다시 보냅니다. |
| ![2단계](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 임시 준비 공유로 선택한 Azure 파일 공유에 파일이 표시 될 때까지 기다립니다. *이러한 공유에 대 한 동기화를 사용 하도록 설정 하지 마세요.* |
| ![3단계](media/storage-sync-files-offline-data-transfer/bullet_3.png) | 생성 Data Box는 각 파일 공유에 대해 비어 있는 새 공유를 만듭니다. 이 새 공유는 Data Box 공유와 동일한 저장소 계정에 있어야 합니다. [Azure 파일 공유를 만드는 방법](storage-how-to-create-file-share.md) |
| ![4단계:](media/storage-sync-files-offline-data-transfer/bullet_4.png) | 저장소 동기화 서비스에서 [동기화 그룹을 만듭니다](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) . 클라우드 엔드포인트으로 빈 공유를 참조 합니다. 모든 Data Box 파일 공유에 대해 이 단계를 반복합니다. [Azure File Sync를 설정](storage-sync-files-deployment-guide.md)합니다. |
| ![5단계](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [라이브 서버 디렉터리를 서버 엔드포인트로 추가](storage-sync-files-deployment-guide.md#create-a-server-endpoint)합니다. 프로세스에서 파일을 Azure로 이동 하 고 준비 공유를 참조 하도록 지정 합니다. 필요에 따라 클라우드 계층화를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 라이브 서버에서 서버 엔드포인트을 만드는 동안 준비 공유를 참조 합니다. **서버 엔드포인트 추가** 블레이드의 **오프 라인 데이터 전송**에서 **사용**을 선택 하 고 클라우드 엔드포인트과 동일한 저장소 계정에 있어야 하는 스테이징 공유를 선택 합니다. 여기에서 사용 가능한 공유 목록은 아직 동기화 되지 않은 저장소 계정 및 공유를 기준으로 필터링 됩니다. |

![새 서버 엔드포인트을 만드는 동안 오프 라인 데이터 전송을 사용 하도록 설정 하는 방법을 보여 주는 Azure Portal 사용자 인터페이스의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>공유 동기화
서버 엔드포인트을 만든 후에는 동기화가 시작 됩니다. 동기화 프로세스는 서버에 있는 각 파일이 Data Box 파일을 보관 한 준비 공유에 있는지도 확인 합니다. 파일이 있는 경우 동기화 프로세스는 서버에서 파일을 업로드 하지 않고 준비 공유에서 파일을 복사 합니다. 파일이 준비 공유에 없거나 로컬 서버에서 최신 버전을 사용할 수 있는 경우 동기화 프로세스가 로컬 서버에서 파일을 업로드 합니다.

> [!IMPORTANT]
> 서버 엔드포인트을 만드는 동안에만 대량 마이그레이션 모드를 사용 하도록 설정할 수 있습니다. 서버 엔드포인트을 설정한 후에는 이미 동기화 된 서버에서 네임 스페이스로 대량 마이그레이션된 데이터를 통합할 수 없습니다.

## <a name="acls-and-timestamps-on-files-and-folders"></a>파일 및 폴더에 대한 ACL 및 타임스탬프
Azure File Sync 사용 하는 대량 마이그레이션 도구가 처음에 Acl을 전송 하지 않은 경우에도 파일 및 폴더 Acl이 라이브 서버에서 동기화 되도록 합니다. 이로 인해 준비 공유는 파일 및 폴더에 대 한 Acl을 포함할 필요가 없습니다. 새 서버 엔드포인트을 만들 때 오프 라인 데이터 마이그레이션 기능을 사용 하도록 설정 하면 모든 파일 Acl이 서버에서 동기화 됩니다. 새로 만든 타임 스탬프 및 수정 된 타임 스탬프도 동기화 됩니다.

## <a name="shape-of-the-namespace"></a>네임스페이스의 모양
동기화를 사용 하도록 설정 하면 서버의 내용에 따라 네임 스페이스의 셰이프가 결정 됩니다. Data Box 스냅숏 및 마이그레이션이 완료 된 후 로컬 서버에서 파일이 삭제 되 면 이러한 파일은 라이브 동기화 네임 스페이스로 이동 하지 않습니다. 이러한 공유는 준비 공유에 유지 되지만 복사 되지 않습니다. 동기화는 라이브 서버에 따라 네임 스페이스를 유지 하기 때문에이 작업이 필요 합니다. Data Box *스냅숏은* 효율적인 파일 복사를 위한 스테이징 그라운드 일 뿐입니다. 라이브 네임 스페이스의 셰이프에 대 한 권한이 아닙니다.

## <a name="cleaning-up-after-bulk-migration"></a>대량 마이그레이션 후 정리 
서버에서 네임 스페이스의 초기 동기화가 완료 되 면 대량 마이그레이션된 파일 Data Box 준비 파일 공유를 사용 합니다. Azure Portal의 **서버 엔드포인트 속성** 블레이드에서 **오프 라인 데이터 전송** 섹션에서 상태가 **진행** 중에서 **완료**됨으로 변경 됩니다. 

![오프 라인 데이터 전송에 대 한 상태 및 사용 안 함 컨트롤이 있는 서버 엔드포인트 속성 블레이드의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

이제 준비 공유를 정리 하 여 비용을 절감할 수 있습니다.

1. **서버 엔드포인트 속성** 블레이드에서 상태가 **완료**인 경우 **오프 라인 데이터 전송 사용 안 함**을 선택 합니다.
2. 준비 공유를 삭제 하 여 비용을 절감 하는 것이 좋습니다. 준비 공유에는 파일 및 폴더 Acl이 포함 되지 않을 수 있으므로 그다지 유용 하지 않습니다. 백업 지정 시간을 위해 [Azure 파일 공유 동기화의 실제 스냅숏을](storage-snapshots-files.md)만듭니다. 일정에 따라 [스냅숏을 생성 하도록 Azure Backup를 설정할]( ../../backup/backup-afs.md) 수 있습니다.

상태가 **완료** 된 경우 나 잘못 된 구성으로 인해 취소 하려는 경우에만 오프 라인 데이터 전송 모드를 사용 하지 않도록 설정 합니다. 배포 하는 동안 모드를 사용 하지 않도록 설정 하면 준비 공유를 여전히 사용할 수 있어도 파일이 서버에서 업로드 되기 시작 합니다.

> [!IMPORTANT]
> 오프 라인 데이터 전송 모드를 사용 하지 않도록 설정한 후에는 다시 사용 하도록 설정할 수 없습니다. 대량 마이그레이션의 준비 공유를 계속 사용할 수 있는 경우에도 마찬가지입니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
