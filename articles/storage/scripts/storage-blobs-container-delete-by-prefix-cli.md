---
title: Azure CLI 스크립트 샘플 - 접두사별 컨테이너 삭제 | Microsoft Docs
description: 컨테이너 이름 접두사를 기준으로 Azure Storage Blob 컨테이너를 삭제한 다음, 배포를 정리합니다. 스크립트 샘플에서 사용되는 명령은 도움말 링크를 참조하세요.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87901881"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Azure CLI 스크립트를 사용하여 컨테이너 이름 접두사를 기준으로 컨테이너 삭제

이 스크립트는 Azure Blob Storage에서 몇 가지 샘플 컨테이너를 만들고 나서 컨테이너 이름의 접두사를 기준으로 일부 컨테이너를 삭제합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, 나머지 컨테이너 및 모든 관련된 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 컨테이너 이름 접두사를 기준으로 컨테이너를 삭제합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account) | 지정된 리소스 그룹에서 Azure Storage 계정을 만듭니다. |
| [az storage container create](/cli/azure/storage/container) | Azure Blob Storage에서 컨테이너를 만듭니다. |
| [az storage container list](/cli/azure/storage/container) | Azure Storage 계정의 컨테이너를 나열합니다. |
| [az storage container delete](/cli/azure/storage/container) | Azure Storage 계정의 컨테이너를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 스토리지 CLI 스크립트 샘플은 [Azure Storage에 대한 Azure CLI 샘플](../blobs/storage-samples-blobs-cli.md)에서 찾을 수 있습니다.
