---
title: Azure Stack Edge GPU storage 계정 관리 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Stack Edge에서 저장소 계정을 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 3b190a108651f4b127c2f009c383613922a59018
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254301"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge"></a>Azure Portal를 사용 하 여 Azure Stack에 지 저장소 계정 관리

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge에서 Edge 저장소 계정을 관리 하는 방법을 설명 합니다. Azure Portal 또는 로컬 웹 UI를 통해 Azure Stack Edge를 관리할 수 있습니다. Azure Portal를 사용 하 여 장치에서 Edge storage 계정을 추가 하거나 삭제할 수 있습니다.

## <a name="about-edge-storage-accounts"></a>Edge 저장소 계정 정보

SMB, NFS 또는 REST 프로토콜을 통해 Azure Stack Edge 장치에서 데이터를 전송할 수 있습니다. REST Api를 사용 하 여 Blob 저장소에 데이터를 전송 하려면 Azure Stack Edge에 Edge storage 계정을 만들어야 합니다. 

Azure Stack Edge 장치에 추가 하는 Edge 저장소 계정은 Azure Storage 계정에 매핑됩니다. Edge 저장소 계정에 기록 된 모든 데이터는 클라우드에 자동으로 푸시됩니다.

두 가지 유형의 계정에 대해 자세히 설명 하는 다이어그램과 이러한 각 계정에서 Azure로 데이터를 이동 하는 방법은 다음과 같습니다.

![Blob storage 계정에 대 한 다이어그램](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Edge 저장소 계정 추가
> * Edge 저장소 계정 삭제


## <a name="add-an-edge-storage-account"></a>Edge 저장소 계정 추가

Edge 저장소 계정을 만들려면 다음 절차를 수행 합니다.

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Edge 저장소 계정 삭제

Edge 저장소 계정을 삭제 하려면 다음 단계를 수행 합니다.

1. 리소스의 **구성 > 저장소 계정** 으로 이동 합니다. 저장소 계정 목록에서 삭제 하려는 저장소 계정을 선택 합니다. 위쪽 명령 모음에서 **저장소 계정 삭제**를 선택 합니다.

    ![저장소 계정 목록으로 이동](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. **저장소 계정 삭제** 블레이드에서 삭제할 저장소 계정을 확인 하 고 **삭제**를 선택 합니다.

    ![저장소 계정 확인 및 삭제](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

저장소 계정 목록이 삭제를 반영 하도록 업데이트 됩니다.


## <a name="add-delete-a-container"></a>컨테이너 추가, 삭제

이러한 저장소 계정에 대 한 컨테이너를 추가 하거나 삭제할 수도 있습니다.

컨테이너를 추가 하려면 다음 단계를 수행 합니다.

1. 관리 하려는 저장소 계정을 선택 합니다. 위쪽 명령 모음에서 **+ 컨테이너 추가**를 선택 합니다.

    ![컨테이너를 추가할 저장소 계정 선택](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. 컨테이너의 이름을 제공 합니다. 이 컨테이너는이 계정에 매핑된 Azure storage 계정 뿐만 아니라 Edge 저장소 계정에 만들어집니다. 

    ![Edge 컨테이너 추가](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

컨테이너 목록이 새로 추가 된 컨테이너를 반영 하도록 업데이트 됩니다.

![업데이트 된 컨테이너 목록](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

이제이 목록에서 컨테이너를 선택 하 고 맨 위 명령 모음에서 **+ 컨테이너 삭제** 를 선택할 수 있습니다. 

![컨테이너 삭제](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>스토리지 키 동기화

장치의 Edge (로컬) 저장소 계정에 대 한 액세스 키를 동기화 할 수 있습니다. 

저장소 계정 액세스 키를 동기화 하려면 다음 단계를 수행 합니다.

1. 리소스에서 관리 하려는 저장소 계정을 선택 합니다. 위쪽 명령 모음에서 **동기화 저장소 키**를 선택 합니다.

    ![동기화 저장소 키 선택](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. 확인하라는 메시지가 표시되면 **예**를 선택합니다.

    ![동기화 저장소 키 선택](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](azure-stack-edge-j-series-manage-users.md)하는 방법을 알아봅니다.
