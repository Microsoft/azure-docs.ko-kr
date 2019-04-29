---
title: Azure Portal을 사용하여 스트리밍 이벤트 캡처 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Portal을 사용하여 Azure Event Hubs를 통해 이벤트 스트리밍을 캡처하도록 설정하는 방법을 설명합니다.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 9108c52529319288fba48dbad3c6f8aa6cb5f725
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822511"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Azure Event Hubs를 통해 이벤트 스트리밍을 캡처하도록 설정

Azure [Event Hubs 캡처][capture-overview]를 사용하면 Event Hubs의 스트리밍 데이터를 선택한 [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 계정에 자동으로 전달할 수 있습니다.

이벤트 허브 생성 시 [Azure Portal](https://portal.azure.com)을 사용하여 캡처를 구성할 수 있습니다. Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 컨테이너 또는 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) 계정으로 데이터를 캡처할 수 있습니다.

자세한 내용은 [Event Hubs 캡처 개요][capture-overview]를 참조하세요.

## <a name="capture-data-to-an-azure-storage-account"></a>Azure Storage 계정에 데이터 캡처  

이벤트 허브를 만드는 경우 **Event Hub 만들기** 포털 화면에서 **켜기** 단추를 클릭하여 캡처를 사용하도록 설정합니다. 그런 다음 **캡처 공급자** 상자에서 **Azure Storage**를 클릭하여 스토리지 계정 및 컨테이너를 지정합니다. Event Hubs 캡처는 저장소에서 서비스 간 인증을 사용하므로 저장소 연결 문자열을 지정할 필요가 없습니다. 리소스 선택기가 저장소 계정에 대한 리소스 URI를 자동으로 선택합니다. Azure Resource Manager를 사용하는 경우 이 URI를 문자열로 명백히 제공해야 합니다.

기본 시간은 5분입니다. 최소값은 1, 최대값은 15입니다. **크기**의 범위는 10-500MB입니다.

![캡처 시간 범위][1]

> [!NOTE]
> 캡처 기간 동안 이벤트가 하나도 발생하지 않으면 빈 파일 내보내기를 사용 또는 사용하지 않도록 설정할 수 있습니다. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Azure Data Lake Store 계정에 데이터를 캡처합니다.

Azure Data Lake Store에 데이터를 캡처하려면 Data Lake Store 계정 및 이벤트 허브를 만듭니다.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Azure Data Lake Store 계정 및 폴더 만들기

> [!NOTE]
> 현재 Event Hubs 캡처 기능은 지원만 Gen 1의 Azure Data Lake Store, Gen 2 없습니다. 

1. 지침에 따라 Data Lake Store Gen 1 계정을 만들 [Azure portal을 사용 하 여 Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)합니다.
2. [Event Hubs에 사용 권한 할당](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) 섹션의 지침을 따라 Data Lake Store 계정에 데이터를 쓸 수 있도록 Event Hubs에서 데이터를 캡처하고 Event Hubs에 사용 권한을 할당하려는 Data Lake Store 계정 내에 폴더를 만듭니다.  


### <a name="create-an-event-hub"></a>이벤트 허브 만들기

1. 이벤트 허브는 방금 만든 Azure Data Lake Store와 동일한 Azure 구독에 있어야 합니다. **Event Hub 만들기** 포털 페이지의 **캡처** 아래에서 **켜기** 단추를 클릭하여 이벤트 허브를 만듭니다. 
2. **Event Hub 만들기** 포털 페이지의 **캡처 공급자** 상자에서 **Azure Data Lake Store**를 선택합니다.
3. **Data Lake Store 선택**에서 이전에 만든 Data Lake Store 계정을 지정하고 **Data Lake 경로** 필드에서 만든 데이터 폴더에 대한 경로를 입력합니다.

    ![Data Lake Storage 계정 선택][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>기존 이벤트 허브에서 캡처 추가 및 구성

Event Hubs 네임스페이스에 있는 기존 이벤트 허브에 캡처를 구성할 수 있습니다. 기존 이벤트 허브에서 캡처를 사용하거나 캡처 설정을 변경하려면 네임스페이스를 클릭하여 개요 화면을 로드한 다음 캡처 설정을 사용하도록 설정하거나 변경할 이벤트 허브를 클릭합니다. 마지막으로 다음 그림과 같이 열린 페이지의 왼쪽에서 **캡처** 옵션을 클릭한 후 설정을 편집합니다.

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Azure Blob Storage 구성][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Azure Data Lake Storage 구성][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>다음 단계

- [Event Hubs 캡처 개요][capture-overview]를 참조하여 Event Hubs 캡처에 대해 자세히 알아봅니다.
- Azure Resource Manager 템플릿을 사용하여 Event Hubs 캡처를 구성할 수도 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 캡처를 사용하도록 설정](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)을 참조하세요.
- [원본으로 Event Hubs 네임스페이스를 사용하여 Azure Event Grid 구독을 만드는 방법 알아보기](store-captured-data-data-warehouse.md)
- [Azure Portal을 사용하여 Azure Data Lake Store 시작](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
