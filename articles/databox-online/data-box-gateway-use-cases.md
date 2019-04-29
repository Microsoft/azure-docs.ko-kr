---
title: Microsoft Azure 데이터 상자 게이트웨이 사용 사례 | Microsoft Docs
description: Azure 데이터 상자 게이트웨이 Azure로 데이터를 전송할 수 있도록 가상 어플라이언스 저장소 솔루션에 대 한 사용 사례를 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 03/2/2019
ms.author: alkohli
ms.openlocfilehash: 37ec1d05d07f33343b9ff21380a277d00b242b7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754184"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway에 대한 사용 사례

Azure Data Box Gateway는 온-프레미스에 상주하고 Azure에 이미지, 미디어 및 기타 데이터를 전송하는 클라우드 스토리지 게이트웨이 디바이스입니다. 이 클라우드 스토리지 게이트웨이는 하이퍼바이저에 프로비전된 가상 머신입니다. NFS 및 SMB 프로토콜을 사용하여 데이터를 이 가상 디바이스에 작성한 다음, Azure에 전송합니다. 이 문서에서는 이 디바이스에 배포할 수 있는 시나리오에 대한 자세한 설명을 제공합니다.

다음 시나리오에 대해 Data Box Gateway를 사용합니다.

- 대용량의 데이터를 계속해서 수집
- 안전하고 효율적인 방식의 데이터 클라우드 보관의 경우
- Data Box를 사용하여 초기 대량 네트워크가 완료된 후 네트워크를 통한 증분 데이터 전송의 경우

이러한 각 시나리오는 다음 섹션에서 자세히 설명합니다.


## <a name="continuous-data-ingestion"></a>지속적인 데이터 수집

Data Box Gateway의 주요 장점 중 하나는 데이터 크기에 관계없이 지속적으로 데이터를 디바이스로 수집하여 클라우드에 복사하는 기능입니다.

데이터는 게이트웨이 디바이스에 작성되므로 디바이스는 데이터를 Azure Storage에 업로드합니다. 디바이스는 특정 임계값에 도달할 때 메타데이터를 유지하면서 파일을 로컬로 제거하여 스토리지를 자동으로 관리합니다. 메타데이터의 로컬 복사본을 유지하면 게이트웨이 디바이스는 파일이 업데이트될 때만 변경을 업로드할 수 있습니다. 게이트웨이 디바이스에 업로드된 데이터는 [데이터 업로드 주의 사항](data-box-gateway-limits.md#data-upload-caveats)의 지침에 따라야 합니다.

디바이스가 데이터로 차면 데이터가 클라우드에 업로드되는 속도를 일치시키도록 수신 속도 제한을 시작합니다(필요한 경우). 디바이스에서 지속적인 수집을 모니터링하려면 경고를 사용합니다. 이러한 경고는 제한이 시작되면 발생하고 제한이 중지되면 사라집니다.

## <a name="cloud-archival-of-data"></a>데이터의 클라우드 보관

데이터를 클라우드에 장기간 보존하려는 경우 Data Box Gateway를 사용합니다. 장기 보존에 대해 스토리지의 **보관** 계층을 사용할 수 있습니다.

보관 계층은 최소 180일 동안 드물게 액세스된 데이터를 저장하도록 최적화됩니다. **보관** 계층은 스토리지 비용은 가장 저렴하지만 액세스 비용은 가장 비쌉니다. 자세한 내용은 [보관 액세스 계층](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)으로 이동하세요.

### <a name="move-data-to-archive-tier"></a>보관 계층으로 데이터 이동

시작하기 전에 실행 중인 Data Box Gateway 디바이스가 있는지 확인합니다. [자습서: Azure Data Box Gateway를 배포하도록 준비](data-box-gateway-deploy-prep.md)에 설명된 단계를 따르고 작동 가능 디바이스를 가질 때까지 다음 단계로 계속해서 진행합니다.

- Data Box Gateway 디바이스를 사용하여 [Data Box Gateway를 통해 데이터 전송](data-box-gateway-deploy-add-shares.md)에 설명된 일반적인 전송 절차를 통해 Azure에 데이터를 업로드합니다.
- 데이터가 업로드된 후 보관 계층으로 이동해야 합니다. 두 가지 방법으로 Blob 계층을 설정할 수 있습니다. Azure PowerShell 스크립트 또는 Azure Storage 수명 주기 관리 정책  
    - Azure PowerShell을 사용하는 경우 다음 [단계](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier)를 따라 데이터를 보관 계층으로 이동합니다.
    - Azure 수명 주기 관리를 사용하는 경우 다음 단계를 따라 데이터를 보관 계층으로 이동합니다.
        - 보관 계층을 사용하도록 Blob 수명 주기 관리 서비스의 미리보기를 [등록](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview)합니다.
        - 다음 정책을 사용하여 [수집 시 데이터를 보관](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest)합니다.
- Blob이 보관으로 표시되면 핫 또는 콜드 계층으로 이동하지 않는 한 게이트웨이에서 더 이상 수정할 수 없습니다. 파일이 로컬 스토리지에 있는 경우 로컬 복사본에 대한 변경 내용은 보관 계층으로 업로드되지 않습니다(삭제 포함).
- 보관 스토리지에서 데이터를 읽으려면 Blob 계층을 핫 또는 쿨로 변경하여 다시 하이드레이션되어야 합니다. 게이트웨이에서 [공유를 새로 고치](data-box-gateway-manage-shares.md#refresh-shares)는 것은 Blob을 다시 하이드레이션하지 않습니다.

자세한 내용은 [Azure Blob Storage 수명 주기를 관리](/azure/storage/common/storage-lifecycle-management-concepts)하는 방법에 대해 자세히 알아봅니다.

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>증분 전송 후 초기 대량 전송

증분 전송 후에 많은 양의 데이터의 대량 업로드를 수행하려는 경우 Data Box 및 Data Box Gateway를 함께 사용합니다. 오프라인 모드에서 대량 전송에 대해 Data Box를 사용하여 네트워크를 통한 증분 전송(진행 중인 피드)에 대해 Data Box Gateway를 사용합니다.

### <a name="seed-the-data-with-data-box"></a>Data Box를 사용하여 데이터 시드

다음 단계를 따라 Data Box에 데이터를 복사하고 Azure Storage에 업로드합니다.

1. [Data Box를 주문](/azure/databox/data-box-deploy-ordered)합니다.
2. [Data Box를 설정](/azure/databox/data-box-deploy-set-up)합니다.
3. [SMB를 통해 Data Box에 데이터를 복사](/azure/databox/data-box-deploy-copy-data)합니다.
4. [Data Box를 반환하고, Azure에 대한 데이터 업로드를 확인](/azure/databox/data-box-deploy-picked-up)합니다.
5. Azure에 대한 데이터 업로드가 완료되면 모든 데이터는 Azure 스토리지 컨테이너에 있어야 합니다. Data Box에 대한 스토리지 계정에서 모든 데이터가 복사되었는지 확인하려면 Blob(및 파일) 컨테이너로 이동합니다. 이 이름을 나중에 사용하므로 컨테이너 이름을 기록해 둡니다. 예를 들어 다음 스크린샷에서 `databox` 컨테이너는 증분 전송에 사용됩니다.

    ![Data Box의 데이터가 있는 컨테이너](media/data-box-gateway-use-cases/data-container1.png)

이 대량 전송은 초기 시드 단계를 완료합니다.

### <a name="ongoing-feed-with-data-box-gateway"></a>Data Box Gateway를 사용하여 진행 중인 피드

Data Box Gateway에서 지속적인 수집을 위해 다음 단계를 수행합니다.

1. Data Box Gateway에서 클라우드 공유를 만듭니다. 이 공유는 Azure Storage 계정에 데이터를 자동으로 업로드합니다. Data Box Gateway 리소스에서 **공유**로 이동하고 **+ 공유 추가**를 클릭합니다.

    ![+ 공유 추가 클릭](media/data-box-gateway-use-cases/add-share1.png)

2. 이 공유가 시드된 데이터를 포함하는 컨테이너에 매핑되는지 확인합니다. **Blob 컨테이너 선택**에 대해 **기존 항목 사용**을 선택하고 Data Box의 데이터가 전송된 컨테이너를 찾습니다.

    ![공유 설정](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. 공유를 만든 후 공유를 새로 고칩니다. 이 작업은 Azure의 콘텐츠를 사용하여 온-프레미스 공유를 새로 고칩니다.

    ![공유 새로 고침](media/data-box-gateway-use-cases/refresh-share1.png)

    공유가 동기화되는 경우 Data Box Gateway는 파일이 클라이언트에서 수정된 경우 증분 변경 내용을 업로드합니다.

## <a name="next-steps"></a>다음 단계

- [Data Box Gateway 시스템 요구 사항](data-box-gateway-system-requirements.md)을 검토합니다.
- [Data Box Gateway 제한](data-box-gateway-limits.md)을 알아봅니다.
- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.