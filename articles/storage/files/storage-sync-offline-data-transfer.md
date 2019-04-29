---
title: 데이터를 Azure Data Box 및 기타 메서드를 사용 하 여 Azure File Sync로 마이그레이션
description: Azure File Sync와 호환 되는 방식으로 데이터를 대량으로 마이그레이션하십시오.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759373"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Azure File Sync로 대량 데이터 마이그레이션
두 가지 방법으로 Azure File Sync에 대량 데이터를 마이그레이션할 수 있습니다.

* **Azure File Sync를 사용 하 여 파일을 업로드 합니다.** 이것이 가장 간단한 방법입니다. 로컬 Windows Server 2012 R2 이상으로 파일을 이동 하 고 Azure File Sync 에이전트를 설치 합니다. 동기화를 설정한 후 서버에서 파일 업로드 됩니다. (고객 현재 환경에 대 한 일 마다 1tib의 평균 업로드 속도.) 서버에 사용 하지 않는 대역폭의 너무 많은 데이터 센터에 대 한 하도록 설정 하려는 [대역폭 제한 일정](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)합니다.
* **오프 라인 파일을 전송 합니다.** 충분 한 대역폭에 없는 합당 한 시간에에서 Azure에 파일을 업로드할 수 없습니다 수 있습니다. 과제는 파일의 전체 집합의 초기 동기화 합니다. 이 문제를 해결 하기 위해 사용 하 여 오프 라인 대량 마이그레이션 도구와 같은 합니다 [Azure Data Box 제품군](https://azure.microsoft.com/services/storage/databox)합니다. 

이 문서에서는 Azure File Sync와 호환 되는 방식으로 오프 라인 파일을 마이그레이션하는 방법을 설명 합니다. 파일 충돌을 방지 하 고 동기화를 사용 하도록 설정한 후에 파일 및 폴더 액세스 제어 목록 (Acl) 타임 스탬프를 유지 하기 위해 다음이 지침을 따릅니다.

## <a name="online-migration-tools"></a>온라인 마이그레이션 도구
Data Box 뿐만 아니라 다른 오프 라인 마이그레이션 도구에 대 한이 문서에서 설명 하는 프로세스입니다. 또한 AzCopy, Robocopy 또는 파트너 도구 및 서비스와 같은 온라인 도구에 대 한 작동합니다. 하지만 초기 해결 챌린지를 업로드, Azure File Sync와 호환 되는 방식으로 이러한 도구를 사용 하려면이 문서의 단계를 수행 합니다.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>도구를 사용 하 여 오프 라인으로 데이터를 전송 하는 이점
오프 라인 마이그레이션에 대 한 데이터 상자 처럼 전송 도구를 사용 하는 주요 이점은 다음과 같습니다.

- 네트워크를 통해 모든 파일을 업로드할 필요가 없습니다. 큰 네임 스페이스의 경우이 도구 상당한 네트워크 대역폭 및 시간을 절약할 수 있습니다.
- Azure File Sync를 사용 하는 경우 (Data Box, Azure Import/Export 서비스 및 등)를 사용 하는 전송 도구에 관계 없이, 라이브 서버에서 Azure로 데이터를 이동 후에 변경 된 파일만 업로드 합니다.
- Azure File Sync는 오프 라인 대량 마이그레이션 도구는 Acl을 전송 하지 않습니다 하는 경우에 파일 및 폴더 Acl을 동기화 합니다.
- Data Box 및 Azure File Sync에는 가동 중지 시간 없이 필요합니다. Data Box를 사용 하 여 Azure로 데이터를 전송할 때 네트워크 대역폭을 효율적으로 사용 하 고 파일 충실도 유지 합니다. 또한 네임 스페이스 최신 상태로 유지 Azure에 데이터를 이동 후에 변경 된 파일만 업로드 하 여 합니다.

## <a name="prerequisites-for-the-offline-data-transfer"></a>오프 라인 데이터 전송에 대 한 필수 구성 요소
오프 라인 데이터 전송 완료 하기 전에 마이그레이션하려는 서버에서 동기화를 사용 하지 마십시오. 시작 하기 전에 고려해 야 할 다른 작업은 다음과 같습니다.

- 대량 마이그레이션을 위해 Data Box를 사용하려는 경우 [Data Box의 배포 필수 구성 요소](../../databox/data-box-deploy-ordered.md#prerequisites)를 검토합니다.
- 최종 Azure File Sync 토폴로지를 계획 합니다. [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- 동기화하려는 파일 공유를 포함할 Azure Storage 계정을 선택합니다. 동일한 스토리지 계정의 임시 스테이징 공유 위치로 대량 마이그레이션이 실행되도록 합니다. 대량 마이그레이션은 동일한 스토리지 계정에 있는 최종 및 스테이징 공유 위치로만 수행할 수 있습니다.
- 대량 마이그레이션은 서버 위치와의 새 동기화 관계를 만들 때만 사용할 수 있습니다. 기존 동기화 관계로는 대량 마이그레이션을 사용하도록 설정할 수 없습니다.


## <a name="process-for-offline-data-transfer"></a>오프 라인 데이터 전송에 대 한 프로세스
Azure Data Box와 같은 대량 마이그레이션 도구와 호환 되는 방식으로 Azure File Sync를 설정 하는 방법을 다음과 같습니다.

![Azure File Sync를 설정 하는 방법을 보여 주는 다이어그램](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 단계 | 세부 정보 |
|---|---------------------------------------------------------------------------------------|
| ![1단계](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box를 주문](../../databox/data-box-deploy-ordered.md)합니다. Data Box 제품군 제품 [여러 제품](https://azure.microsoft.com/services/storage/databox/data) 요구 사항에 맞게 합니다. 데이터 상자에를 받게 되 면에 따라 해당 [데이터를 복사 하는 설명서](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) Data Box의이 UNC 경로로:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>* 합니다. 이때 *ShareName* 준비 공유의 이름입니다. Data Box를 Azure로 다시 보냅니다. |
| ![2단계](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 임시 준비 공유로 선택한 Azure 파일 공유에 파일이 표시 될 때까지 기다립니다. *이러한 공유에 동기화를 사용 하지 마십시오.* |
| ![3단계](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Data Box를 생성 하는 각 파일 공유에 대 한 새 빈 공유를 만듭니다. 이 새 공유 데이터 상자 공유와 동일한 저장소 계정에 있어야 합니다. [Azure 파일 공유를 만드는 방법](storage-how-to-create-file-share.md) |
| ![4단계:](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [동기화 그룹을 만드는](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) 저장소 동기화 서비스에서. 클라우드 끝점으로 빈 공유 참조입니다. 모든 Data Box 파일 공유에 대해 이 단계를 반복합니다. [Azure File Sync 설정](storage-sync-files-deployment-guide.md)합니다. |
| ![5단계](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [라이브 서버 디렉터리를 서버 엔드포인트로 추가](storage-sync-files-deployment-guide.md#create-a-server-endpoint)합니다. 프로세스에서 파일을 Azure로 이동 하는 지정 하 고 스테이징 공유 참조입니다. 클라우드 계층화가 필요에 따라 사용 하지 않도록 설정 하거나 설정할 수 있습니다. 라이브 서버에서 서버 엔드포인트를 만드는 동안 준비 공유를 참조 합니다. 에 **서버 끝점 추가** 블레이드 아래에서 **오프 라인으로 데이터 전송**를 선택 **사용**를 선택한 다음 클라우드로 동일한 저장소 계정에 있어야 하는 준비 공유 끝점입니다. 여기에서 사용 가능한 공유 목록은 저장소 계정 및 공유를 이미 동기화 되지으로 필터링 됩니다. |

![새 서버 엔드포인트를 만드는 동안 오프 라인 데이터 전송을 활성화 하는 방법을 보여 주는 Azure portal 사용자 인터페이스 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>공유 동기화
서버 끝점을 만든 후 동기화가 시작 됩니다. 동기화 프로세스 서버에 있는 각 파일 스테이징 공유 Data Box 파일을 보관 하는 위치에 있는지 여부를 결정 합니다. 파일이 존재 하는 있는 경우 동기화 프로세스 서버에서 업로드 하는 것이 아니라 준비 공유에서 파일을 복사 합니다. 준비 공유에 파일이 없는 경우 또는 최신 버전을 로컬 서버의 사용 가능한 경우 동기화 프로세스는 로컬 서버에서 파일을 업로드 합니다.

> [!IMPORTANT]
> 서버 엔드포인트를 만드는 동안에 대량 마이그레이션 모드를 사용할 수 있습니다. 서버 끝점을 설정한 후에 네임 스페이스에 이미 동기화 서버에서 데이터 대량 마이그레이션를 통합할 수는 없습니다.

## <a name="acls-and-timestamps-on-files-and-folders"></a>파일 및 폴더에 대한 ACL 및 타임스탬프
Azure File Sync 파일 및 폴더 Acl 했던 대량 마이그레이션 도구 Acl를 처음에 전송 하지 않은 경우에 라이브 서버에서 동기화 된 것을 확인 합니다. 이 인해 준비 공유 파일 및 폴더에 대 한 모든 Acl을 포함할 필요가 없습니다. 새 서버 끝점을 만들 때 오프 라인 데이터 마이그레이션 기능을 사용 하면 모든 Acl은 파일 서버의 동기화 됩니다. 새로 생성 되 고 수정 된 타임 스탬프가 동기화도 됩니다.

## <a name="shape-of-the-namespace"></a>네임스페이스의 모양
동기화를 사용 하면 서버의 내용을 네임 스페이스의 셰이프를 결정 합니다. Data Box 스냅숏 및 마이그레이션이 완료 된 후 로컬 서버에서 파일이 삭제 되는 경우 이러한 파일은 라이브, 동기화 네임 스페이스로 이동 하지 않습니다. 준비 공유에 유지 하지만 복사 되지 않습니다. 동기화에서는 라이브 서버에 따라 네임 스페이스를 유지 하므로 이것이 필요 합니다. Data Box *스냅숏* 효율적으로 파일 복사에 대 한 스테이징 처음부터 방금 됩니다. 라이브 네임 스페이스의 셰이프에 대 한 인증 기관 아닙니다.

## <a name="cleaning-up-after-bulk-migration"></a>대량 마이그레이션 후 정리 
서버에 네임 스페이스의 초기 동기화 완료 되 면 Data Box 대량 마이그레이션 파일 스테이징 파일 공유를 사용 합니다. 에 **서버 끝점 속성** 블레이드에서 Azure portal에서에 **오프 라인으로 데이터 전송** 섹션에서 변경 된 상태 **진행** 를 **완료 됨** . 

![오프 라인 데이터 전송에 대 한 상태 및 사용 안 함 컨트롤 위치한 서버 끝점 속성 블레이드의 스크린 샷](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

이제 비용 절감을 위해 준비 공유 정리할 수 있습니다.

1. 에 **서버 끝점 속성** 블레이드에서 상태가 **완료 됨**를 선택 **오프 라인 데이터 전송을 사용 하지 않도록 설정**합니다.
2. 비용 절감을 위해 준비 공유를 삭제 하는 것이 좋습니다. 준비 공유 파일 및 폴더의 Acl을 포함 하지 않습니다 아마도 하므로 매우 유용 하지 않습니다. 백업 시점 목적으로 만들 실제 [동기화 할 Azure 파일 공유의 스냅숏을](storage-snapshots-files.md)합니다. 할 수 있습니다 [스냅숏을 Azure Backup 설정]( ../../backup/backup-azure-files.md) 일정에 따라 합니다.

상태가 있는 경우에 오프 라인 데이터 전송 모드를 사용 하지 않도록 설정 **Completed** 또는 잘못 된 구성으로 인해 취소 하려는 경우. 배포 중 모드를 사용 하지 않으면 파일 스테이징 공유가 여전히 사용할 수 있는 경우에 서버에서 업로드 하기 시작 합니다.

> [!IMPORTANT]
> 오프 라인 데이터 전송 모드를 비활성화 하면 사용할 수 없습니다 것 마찬가지로 대량 마이그레이션에서 스테이징 공유를 여전히 사용할 수 있는 경우에 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
