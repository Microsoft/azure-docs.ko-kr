---
title: Azure Storage 탐색기를 사용 하 여 Azure managed disks 관리
description: Azure Storage 탐색기를 사용 하 여 지역에서 Azure 관리 디스크를 업로드, 다운로드 및 마이그레이션하고 관리 디스크의 스냅숏을 만드는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749381"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage 탐색기를 사용 하 여 Azure managed disks 관리

Storage 탐색기 1.10.0를 사용 하면 사용자가 관리 디스크를 업로드, 다운로드 및 복사 하 고 스냅숏을 만들 수 있습니다. 이러한 추가 기능으로 인해 Storage 탐색기를 사용 하 여 온-프레미스에서 Azure로 데이터를 마이그레이션하고 Azure 지역 간에 데이터를 마이그레이션할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료 하려면 다음이 필요 합니다.
- Azure 구독
- 하나 이상의 Azure managed disks
- 최신 버전의 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

Storage 탐색기 Azure에 연결 되지 않은 경우 리소스를 관리 하는 데 사용할 수 없습니다. 이 섹션에서는 Storage 탐색기를 사용 하 여 리소스를 관리할 수 있도록 Azure 계정에 연결 하는 방법을 설명 합니다.

1. Azure Storage 탐색기를 시작 하 고 왼쪽에 있는 **플러그** 인 아이콘을 클릭 합니다.

    ![플러그 인 아이콘을 클릭 합니다.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **Azure 계정 추가**를 선택 하 고 **다음**을 클릭 합니다.

    ![Azure 계정 추가](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. **Azure 로그인** 대화 상자에서 azure 자격 증명을 입력 합니다.

    ![Azure 로그인 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 목록에서 구독을 선택하고 **적용**을 클릭합니다.

    ![구독 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>온-프레미스 VHD에서 관리 디스크 업로드

1. 왼쪽 창에서 **디스크** 를 확장 하 고 디스크를 업로드할 리소스 그룹을 선택 합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. **업로드**를 선택합니다.

    ![업로드 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. **Vhd 업로드** 에서 원본 VHD, 디스크 이름, OS 유형, 디스크를 업로드할 지역 및 계정 유형을 지정 합니다. 일부 지역에서는 가용성 영역이 지원 되며, 해당 지역에 대해 선택한 영역을 선택할 수 있습니다.
1. **만들기** 를 선택 하 여 디스크 업로드를 시작 합니다.

    ![Vhd 업로드 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 이제 업로드 상태가 **활동**에 표시 됩니다.

    ![업로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 업로드가 완료 되 고 오른쪽 창에 디스크가 표시 되지 않으면 **새로 고침**을 선택 합니다.

## <a name="download-a-managed-disk"></a>관리 디스크 다운로드

다음 단계에서는 관리 되는 디스크를 온-프레미스 VHD에 다운로드 하는 방법을 설명 합니다. 디스크의 상태를 **분리 해야 다운로드할** 수 있습니다. **연결 된** 디스크는 다운로드할 수 없습니다.

1. 왼쪽 창에서 아직 확장 되지 않은 **경우 디스크를 확장 하** 고 디스크를 다운로드 하려는 리소스 그룹을 선택 합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 다운로드 하려는 디스크를 선택 합니다.
1. **다운로드** 를 선택 하 고 디스크를 저장할 위치를 선택 합니다.

    ![관리 디스크 다운로드](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **저장** 을 선택 하면 디스크가 다운로드를 시작 합니다. 다운로드 상태가 **활동**에 표시 됩니다.

    ![다운로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

Storage 탐색기를 사용 하면 지역 내에서 또는 지역 간에 관리 되 디스크를 복사할 수 있습니다. 디스크를 복사 하려면:

1. 왼쪽의 **디스크** 드롭다운에서 복사할 디스크가 포함 된 리소스 그룹을 선택 합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 복사 하려는 디스크를 선택 하 고 **복사**를 선택 합니다.

    ![관리 디스크 복사](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 왼쪽 창에서 디스크를 붙여 넣을 리소스 그룹을 선택 합니다.

    ![리소스 그룹 선택 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 오른쪽 창에서 **붙여넣기** 를 선택 합니다.

    ![관리 디스크 붙여넣기](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. **디스크 붙여넣기** 대화 상자에서 값을 입력 합니다. 지원 되는 지역에서 가용성 영역을 지정할 수도 있습니다.

    ![디스크 붙여넣기 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **붙여넣기** 를 선택 하면 디스크가 복사를 시작 하 고 상태가 **활동**에 표시 됩니다.

    ![붙여넣기 상태 복사](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>스냅샷 만들기

1. 왼쪽의 **디스크** 드롭다운에서 스냅숏으로 사용할 디스크가 포함 된 리소스 그룹을 선택 합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽에서 스냅숏 할 디스크를 선택 하 고 **스냅숏 만들기**를 선택 합니다.

    ![스냅샷 만들기](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. **스냅숏 만들기**에서 스냅숏을 만들 리소스 그룹 뿐만 아니라 스냅숏의 이름을 지정 합니다. 그런 다음 **만들기**를 선택합니다.

    ![스냅숏 만들기 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 스냅숏이 만들어지면 **작업** 에서 **포털에서 열기** 를 선택 하 여 Azure Portal에서 스냅숏을 볼 수 있습니다.

    ![포털에서 스냅숏 열기](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>다음 단계


[Azure Portal를 사용 하 여 VHD에서 VM을 만드는](windows/create-vm-specialized-portal.md)방법에 대해 알아봅니다.

[Azure Portal를 사용 하 여 관리 되는 데이터 디스크를 WINDOWS VM에 연결](windows/attach-managed-disk-portal.md)하는 방법을 알아봅니다.