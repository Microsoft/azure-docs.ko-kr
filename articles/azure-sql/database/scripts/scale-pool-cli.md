---
title: 'Azure CLI: 탄력적 풀 크기 조정'
description: Azure CLI 예제 스크립트를 사용하여 Azure SQL Database에서 탄력적 풀의 크기를 조정합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/25/2019
ms.openlocfilehash: bcd94c5557de5ddcd26f251639b63908af7ae78b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707865"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Azure CLI를 사용하여 Azure SQL Database에서 탄력적 풀 크기 조정

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 Azure CLI 스크립트 예제는 Azure SQL Database에서 탄력적 풀을 만들고, 풀링된 데이터베이스를 이동하고, 탄력적 풀 컴퓨팅 크기를 변경합니다.

Azure CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

### <a name="sign-in-to-azure"></a>Azure에 로그인

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>스크립트 실행

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>샘플 참조

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | 서버 명령입니다. |
| [az sql db](/cli/azure/sql/db) | 데이터베이스 명령입니다. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | 탄력적 풀 명령입니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../az-cli-script-samples-content-guide.md)에서 찾을 수 있습니다.
