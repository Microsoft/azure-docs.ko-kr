---
title: Azure Storage 계정
titleSuffix: Azure Media Services
description: Azure Media Services와 함께 사용할 Azure 저장소 계정을 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 120bd403d84f9912bd309f965f3b69306fabcc24
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267074"
---
# <a name="azure-storage-accounts"></a>Azure Storage 계정

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다.

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용을 방지 하기 위해 Media Services 계정과 동일한 위치에 저장소 계정을 사용 하는 것이 좋습니다.

하나의 **기본** 저장소 계정이 있어야 하며, Media Services 계정에 연결 된 **보조** 저장소 계정을 개수에 제한 없이 사용할 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 전용 계정은 **주**로 허용 되지 않습니다.

최신 기능과 성능을 활용할 수 있도록 GPv2를 사용 하는 것이 좋습니다. 저장소 계정에 대해 자세히 알아보려면 [Azure Storage 계정 개요](../../storage/common/storage-account-overview.md)를 참조 하세요.

> [!NOTE]
> 핫 액세스 계층만 Azure Media Services와 함께 사용할 수 있지만, 다른 액세스 계층을 사용 하 여 적극적으로 사용 되지 않는 콘텐츠의 저장소 비용을 줄일 수 있습니다.

저장소 계정에 대해 선택할 수 있는 다양 한 Sku가 있습니다. 자세한 내용은 [스토리지 계정](/cli/azure/storage/account?view=azure-cli-latest)을 참조하세요. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션을 위해 SKU를 선택할 때 `--sku Standard_RAGRS` 비즈니스 연속성을 위한 지리적 복제를 제공 하는을 고려해 야 합니다.

## <a name="assets-in-a-storage-account"></a>스토리지 계정의 자산

Media Services v 3에서 저장소 Api를 사용 하 여 자산에 파일을 업로드 합니다. 자세한 내용은 [Azure Media Services v3의 자산](assets-concept.md)을 참조 하세요.

> [!Note]
> Media Services Api를 사용 하지 않고 Media Services SDK에서 생성 된 blob 컨테이너의 콘텐츠를 변경해 서는 안 됩니다.

## <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호 하기 위해 자산을 저장소 쪽 암호화로 암호화 해야 합니다. 다음 표는 Media Services v3에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v3|
|---|---|---|
|저장소 암호화 Media Services| AES-256 암호화, Media Services에서 관리 하는 키입니다. |지원 되지 않습니다. <sup>(1)</sup>|
|[휴지 상태의 데이터에 대 한 Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)|Azure Storage에서 제공 하는 서버 쪽 암호화, Azure 또는 고객이 관리 하는 키입니다.|지원됨.|
|[저장소 클라이언트 쪽 암호화](../../storage/common/storage-client-side-encryption.md)|Azure storage에서 제공 하는 클라이언트 쪽 암호화는 Key Vault에서 고객이 관리 하는 키입니다.|지원 안 됨|

<sup>1</sup> Media Services v3에서 저장소 암호화 (AES-256 암호화)는 이전 버전과의 호환성을 위해서만 지원 됩니다. 즉, v 2를 Media Services 사용 하 여 자산을 만든 경우에만 v3에서 기존 저장소 암호화 된 자산을 사용할 수 있지만 새로 만들 수는 없습니다.

## <a name="storage-account-errors"></a>저장소 계정 오류

Media Services 계정의 "연결 끊김" 상태는 스토리지 액세스 키가 변경되어 해당 계정이 하나 이상의 연결된 스토리지 계정에 액세스할 수 없는 경우를 나타냅니다. Media Services가 계정에서 많은 작업을 수행하려면 최신 스토리지 액세스 키가 필요합니다.

다음은 연결된 스토리지 계정에 대한 액세스 권한이 Media Services 계정에서 없어지는 주요 시나리오입니다.

|문제|솔루션|
|---|---|
|Media Services 계정이나 연결된 스토리지 계정이 별도의 구독으로 마이그레이션되었습니다. |저장소 계정 또는 Media Services 계정이 모두 동일한 구독에 있도록 마이그레이션합니다. |
|이것이 지원되던 초기 Media Services 계정이기 때문에, Media Services 계정이 다른 구독에 속하는 연결된 스토리지 계정을 사용합니다. 모든 초기 Media Services 계정은 최신 Azure 리소스 관리자 기반 계정으로 변환 되 고 연결 끊김 상태가 됩니다. |저장소 계정 또는 Media Services 계정이 모두 동일한 구독에 있도록 마이그레이션하십시오.|

## <a name="azure-storage-firewall"></a>Azure Storage 방화벽

Azure Media Services는 Azure Storage 방화벽이 나 [개인 끝점](../../storage/common/storage-network-security.md) 을 사용 하는 저장소 계정을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

Media Services 계정에 스토리지 계정을 연결하는 방법을 알아보려면 [계정 만들기](./create-account-howto.md)를 참조하세요.
