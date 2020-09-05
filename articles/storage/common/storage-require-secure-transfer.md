---
title: 보안 연결을 보장 하려면 보안 전송 필요
titleSuffix: Azure Storage
description: Azure Storage 요청에 대 한 보안 전송을 요구 하는 방법에 대해 알아봅니다. 저장소 계정에 대 한 보안 전송이 필요한 경우 안전 하지 않은 연결에서 발생 하는 모든 요청이 거부 됩니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 22e012c36f5c2c6f195a7e3b21afe9001a4cad0d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077962"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>보안 연결을 보장 하려면 보안 전송 필요

저장소 계정에 대 한 **보안 전송 필요** 속성을 설정 하 여 보안 연결의 요청을 허용 하도록 저장소 계정을 구성할 수 있습니다. 보안 전송이 필요한 경우 안전 하지 않은 연결에서 발생 하는 모든 요청이 거부 됩니다. 모든 저장소 계정에 대해 항상 보안 전송이 필요한 것이 좋습니다.

보안 전송이 필요한 경우 HTTPS를 통해 Azure Storage REST API 작업에 대 한 호출을 수행 해야 합니다. HTTP를 통해 수행 된 모든 요청은 거부 됩니다.

저장소 계정에 보안 전송이 필요한 경우 암호화 없이 SMB를 통해 Azure 파일 공유에 연결 하지 못합니다. 안전 하지 않은 연결의 예로는 SMB 2.1, 암호화 되지 않은 SMB 3.0 또는 Linux SMB 클라이언트의 일부 버전에 대 한 연결을 들 수 있습니다.

기본적으로 **보안 전송 필요** 속성은 저장소 계정을 만들 때 사용 하도록 설정 됩니다.

> [!NOTE]
> Azure Storage에서 사용자 지정 도메인 이름에 대해 HTTPS를 지원하지 않으므로 사용자 지정 도메인 이름을 사용할 때 이 옵션이 적용되지 않습니다. 또한 클래식 스토리지 계정은 지원되지 않습니다.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Azure Portal에서 보안 전송 필요

[Azure Portal](https://portal.azure.com)에서 저장소 계정을 만들 때 **보안 전송 필요** 속성을 설정할 수 있습니다. 기존 스토리지 계정을 활성화할 수도 있습니다.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>새 스토리지 계정에 대해 보안 전송 필요

1. Azure Portal에서 **스토리지 계정 만들기** 창을 엽니다.
1. **보안 전송 필요**아래에서 **사용**을 선택 합니다.

   ![스토리지 계정 만들기 블레이드](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>기존 스토리지 계정에 대해 보안 전송 필요

1. Azure Portal에서 기존 스토리지 계정을 선택합니다.
1. 스토리지 계정 메뉴 창의 **설정** 아래에서 **구성**을 선택합니다.
1. **보안 전송 필요**아래에서 **사용**을 선택 합니다.

   ![스토리지 계정 메뉴 창](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>코드에서 보안 전송 필요

프로그래밍 방식으로 보안 전송을 요구 하려면 저장소 계정에서 _enableHttpsTrafficOnly_ 속성을 _True_ 로 설정 합니다. 저장소 리소스 공급자 REST API, 클라이언트 라이브러리 또는 도구를 사용 하 여이 속성을 설정할 수 있습니다.

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>PowerShell을 사용 하 여 보안 전송 필요

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

`Connect-AzAccount`를 실행하여 Azure와 연결합니다.

 다음 명령줄을 사용하여 설정을 확인합니다.

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

다음 명령줄을 사용하여 설정을 사용하도록 설정합니다.

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Azure CLI로 보안 전송 필요

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 다음 명령을 사용 하 여 설정을 확인 합니다.

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

다음 명령을 사용 하 여 설정을 사용 하도록 설정 합니다.

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>다음 단계

[Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
