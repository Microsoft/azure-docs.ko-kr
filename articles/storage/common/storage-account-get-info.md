---
title: .NET을 사용 하 여 저장소 계정 유형 및 SKU 이름 가져오기
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용 하 여 계정 유형 및 SKU 이름을 Azure Storage 가져오는 방법에 대해 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fa1e258b07ab98040cbbc5217be789e0bb1b783
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020136"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET을 사용 하 여 저장소 계정 유형 및 SKU 이름 가져오기

이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 blob에 대 한 Azure Storage 계정 유형 및 SKU 이름을 가져오는 방법을 보여 줍니다.

계정 정보는 버전 2018-03-28부터 제공 됩니다.

## <a name="about-account-type-and-sku-name"></a>계정 유형 및 SKU 이름 정보

**계정 유형**: 올바른 계정 유형에는 `BlobStorage` , `BlockBlobStorage` , `FileStorage` , `Storage` 및가 포함 됩니다 `StorageV2` . [Azure storage 계정 개요](storage-account-overview.md) 에는 다양 한 저장소 계정에 대 한 설명을 포함 하 여 자세한 정보가 포함 되어 있습니다.

**Sku 이름**: 유효한 sku 이름 `Premium_LRS` 에는,, `Premium_ZRS` `Standard_GRS` , `Standard_GZRS` , `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` `Standard_ZRS` ,, 및가 포함 됩니다. SKU 이름은 대/소문자를 구분 하며, 지 수 [클래스](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)의 문자열 필드입니다.

## <a name="retrieve-account-information"></a>계정 정보 검색

Blob와 연결 된 저장소 계정 유형 및 SKU 이름을 가져오려면 [Getaccountproperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) 또는 [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) 메서드를 호출 합니다.

다음 코드 예제에서는 읽기 전용 계정 속성을 검색 하 고 표시 합니다.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

[Azure Portal](https://portal.azure.com) 및 Azure REST API를 통해 저장소 계정에서 수행할 수 있는 다른 작업에 대해 알아봅니다.

- [계정 정보 가져오기 작업 (REST)](/rest/api/storageservices/get-account-information)
