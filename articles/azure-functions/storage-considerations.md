---
title: Azure Functions에 대 한 저장소 고려 사항
description: Azure Functions 및 저장 된 데이터 암호화에 대 한 저장소 요구 사항을 알아봅니다.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f094996ca44ec36d46330e54eac56b28794ef22e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190294"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions에 대 한 저장소 고려 사항

Azure Functions 함수 앱 인스턴스를 만들 때 Azure Storage 계정이 필요 합니다. 함수 앱에서 사용할 수 있는 저장소 서비스는 다음과 같습니다.


|저장소 서비스  | 함수 사용  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | 바인딩 상태와 기능 키를 유지 관리 합니다.  <br/>[Durable Functions에서 작업 허브](durable/durable-functions-task-hubs.md)에도 사용 됩니다. |
| [Azure 파일](../storage/files/storage-files-introduction.md)  | [소비 계획](functions-scale.md#consumption-plan)에서 함수 앱 코드를 저장 하 고 실행 하는 데 사용 되는 파일 공유입니다. |
| [Azure Queue storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions에서 작업 허브에](durable/durable-functions-task-hubs.md)사용 됩니다.   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions에서 작업 허브에](durable/durable-functions-task-hubs.md)사용 됩니다.       |

> [!IMPORTANT]
> 소비 호스팅 계획을 사용할 경우 함수 코드 및 바인딩 구성 파일은 기본 스토리지 계정의 Azure File Storage에 저장됩니다. 기본 스토리지 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

함수 앱을 만들 때 Blob, 큐 및 테이블 저장소를 지 원하는 범용 Azure Storage 계정을 만들거나 연결 해야 합니다. 이는 함수는 트리거 관리 및 함수 실행 로깅 등의 작업 Azure Storage에 의존 하기 때문입니다. 일부 저장소 계정은 큐 및 테이블을 지원 하지 않습니다. 이러한 계정에는 blob 전용 저장소 계정, Azure Premium Storage 및 ZRS 복제를 사용 하는 범용 저장소 계정이 포함 됩니다. 이러한 지원 되지 않는 계정은 함수 앱을 만들 때 Storage 계정 블레이드에서 필터링 됩니다.

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요. 

함수 앱에서 기존 저장소 계정을 사용할 수 있지만, 이러한 요구 사항을 충족 하는지 확인 해야 합니다. 함수 앱 만들기 흐름의 일부로 생성 된 저장소 계정은 이러한 저장소 계정 요구 사항을 충족 하도록 보장 됩니다.  

## <a name="storage-account-guidance"></a>저장소 계정 지침

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제 된 경우 함수 앱이 실행 되지 않습니다. 저장소 관련 문제를 해결 하려면 [저장소 관련 문제를 해결 하는 방법](functions-recover-storage-account.md)을 참조 하세요. 함수 앱에서 사용 하는 저장소 계정에는 다음과 같은 추가 고려 사항이 적용 됩니다.

### <a name="storage-account-connection-setting"></a>저장소 계정 연결 설정

저장소 계정 연결은 [Azurewebjobsstorage 응용 프로그램 설정](./functions-app-settings.md#azurewebjobsstorage)에서 유지 관리 됩니다. 

저장소 키를 다시 생성할 때 저장소 계정 연결 문자열을 업데이트 해야 합니다. [저장소 키 관리에 대 한 자세한 내용은 여기를 참조](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)하세요.

### <a name="shared-storage-accounts"></a>공유 저장소 계정

여러 함수 앱에서 문제 없이 동일한 저장소 계정을 공유할 수 있습니다. 예를 들어 Visual Studio에서 Azure Storage 에뮬레이터를 사용 하 여 여러 앱을 개발할 수 있습니다. 이 경우 에뮬레이터는 단일 저장소 계정 처럼 작동 합니다. 함수 앱에서 사용 하는 것과 동일한 저장소 계정을 사용 하 여 응용 프로그램 데이터를 저장할 수도 있습니다. 그러나 프로덕션 환경에서는이 방법이 항상 좋은 것은 아닙니다.

### <a name="optimize-storage-performance"></a>저장소 성능 최적화

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>저장소 데이터 암호화

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 자세한 내용은 [미사용 데이터에 대 한 암호화 Azure Storage](../storage/common/storage-service-encryption.md)를 참조 하세요.

기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 blob 및 파일 데이터의 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 이러한 키는 저장소 계정에 액세스할 수 있는 함수에 대 한 Azure Key Vault에 있어야 합니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure Key Vault를 사용 하 여 고객 관리 키 구성](../storage/common/storage-encryption-keys-portal.md)을 참조 하세요.  

## <a name="next-steps"></a>다음 단계

Azure Functions 호스팅 옵션에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 비율 크기 조정 및 호스팅](functions-scale.md)


