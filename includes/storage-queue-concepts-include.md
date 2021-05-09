---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028065"
---
## <a name="what-is-queue-storage"></a>Queue Storage란?

Azure Queue Storage는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 스토리지 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. Queue Storage는 비동기적으로 처리할 작업의 백로그를 만드는 데 자주 사용됩니다.

## <a name="queue-service-concepts"></a>큐 서비스 개념

Azure 큐 서비스에는 다음 구성 요소가 포함됩니다.

![Azure 큐 서비스 구성 요소](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Storage 계정:** Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 스토리지 계정에 대한 자세한 내용은 [스토리지 계정 개요](../articles/storage/common/storage-account-overview.md)를 참조하세요.
* **큐:** 큐에는 메시지 집합이 포함됩니다. 모든 메시지는 큐에 있어야 합니다. 큐 이름은 모두 소문자여야 합니다. 큐의 명명에 대한 자세한 내용은 [큐 및 메타데이터 명명](/rest/api/storageservices/Naming-Queues-and-Metadata)을 참조하세요.
* **메시지:** 최대 64KB인 임의 형식의 메시지입니다. 메시지가 큐에 남아 있을 수 있는 최대 시간은 7일입니다. 2017-07-29 이상 버전에서 허용되는 최대 TTL(Time to Live)은 모든 양수 또는 메시지가 만료되지 않는 -1입니다. 이 매개 변수를 생략하면 기본 TTL(Time to Live)은 7일입니다.
* **URL 형식:** 큐는 http://`<storage account>`.queue.core.windows.net/`<queue>` URL 형식을 사용하여 주소를 지정할 수 있습니다.

    다음 URL은 다이어그램에 있는 큐의 주소를 지정합니다.

    `http://myaccount.queue.core.windows.net/incoming-orders`