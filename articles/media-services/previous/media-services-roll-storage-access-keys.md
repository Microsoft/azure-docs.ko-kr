---
title: 스토리지 액세스 키 롤링 후 Media Services 업데이트 | Microsoft Docs
description: 이 문서에서는 스토리지 액세스 키를 롤링한 후 Media Services를 업데이트하는 방법에 대한 지침을 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: c68bedb37722fb6a8b7ad9dccdeaaaa4fab9d020
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264133"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>스토리지 액세스 키 롤링 후 Media Services 업데이트

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

새 AMS(Azure Media Services) 계정을 만들 때 미디어 콘텐츠를 저장하는 데 사용되는 Azure Storage 계정을 선택하도록 요청받습니다. Media Services 계정에 스토리지 계정을 둘 이상 추가할 수 있습니다. 이 문서에서는 스토리지 키를 회전하는 방법을 보여 줍니다. 또한 미디어 계정에 스토리지 계정을 추가하는 방법도 보여 줍니다. 

이 문서에서 설명하는 작업을 수행하려면 [Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) 및 [Powershell](/powershell/module/az.media)을 사용해야 합니다.  자세한 내용은 [PowerShell을 사용 하 여 Azure 리소스를 관리 하는 방법 및 리소스 관리자](../../azure-resource-manager/management/manage-resource-groups-powershell.md)을 참조 하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

새 스토리지 계정이 만들어지면 Azure는 2개의 512비트 스토리지 액세스 키를 만들며, 이는 스토리지 계정에 대한 액세스를 인증하는 데 사용됩니다. 스토리지 연결을 보다 안전하게 유지하려면 스토리지 액세스 키를 주기적으로 다시 생성하고 회전하는 것이 좋습니다. 2개의 액세스 키(기본 및 보조)는 다른 액세스 키를 다시 생성하는 동안 하나의 액세스 키를 사용하여 스토리지 계정에 대한 연결을 유지할 수 있도록 제공됩니다. 이 과정은 "액세스 키 롤링"이라고도 합니다.

Media Services는 제공되는 스토리지 키에 따라 달라집니다. 특히, 자산을 스트림 또는 다운로드하는 데 사용되는 로케이터는 지정된 스토리지 액세스 키에 따라 달라집니다. AMS 계정을 만들 때 기본적으로 기본 스토리지 액세스 키에 종속되지만 사용자는 AMS의 스토리지 키를 업데이트할 수 있습니다. 이 문서에 설명된 다음과 같은 절차에 따라 사용할 키를 Media Services에 알려 주어야 합니다.  

>[!NOTE]
> 여러 스토리지 계정이 있는 경우 각각의 스토리지 계정마다 이 과정을 수행해야 합니다. 스토리지 키를 회전하는 순서는 고정되어 있지 않습니다. 보조 키를 먼저 회전시킨 다음 기본 키를 회전하거나 그 반대로 회전할 수 있습니다.
>
> 이 문서에 설명된 단계를 프로덕션 계정에서 실행하기 전에 사전 프로덕션 계정에서 테스트해야 합니다.
>

## <a name="steps-to-rotate-storage-keys"></a>스토리지 키를 회전하는 단계 
 
 1. PowerShell cmdlet 또는 [Azure](https://portal.azure.com/) 포털을 통해 스토리지 계정 기본 키를 변경합니다.
 2. 적절 한 매개 변수를 사용 하 여 AzMediaServiceStorageKeys cmdlet을 호출 하 여 미디어 계정을 강제로 저장소 계정 키를 선택 합니다.
 
    다음 예제에서는 키를 스토리지 계정에 동기화하는 방법을 보여 줍니다.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. 1 시간 정도 기다립니다. 스트리밍 시나리오가 작동하는지 확인합니다.
 4. PowerShell cmdlet 또는 Azure Portal을 통해 스토리지 계정 보조 키를 변경합니다.
 5. 적절 한 매개 변수를 사용 하 여 AzMediaServiceStorageKeys powershell을 호출 하 여 새 저장소 계정 키를 선택 하도록 미디어 계정을 강제 합니다. 
 6. 1 시간 정도 기다립니다. 스트리밍 시나리오가 작동하는지 확인합니다.
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell cmdlet 예제 

다음 예제에서는 스토리지 계정을 가져와서 AMS 계정과 동기화하는 방법을 보여 줍니다.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```
 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS 계정에 스토리지 계정을 추가하는 단계

[Media Services 계정에 여러 스토리지 계정 연결](./media-services-managing-multiple-storage-accounts.md) 문서에서는 AMS 계정에 스토리지 계정을 추가하는 방법을 보여 줍니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>승인
이 문서를 만들 때 기여한 다음 사람들에게 감사 드리고자 합니다. Cenk Dingiloglu, Milan Gada, Seva Titov
