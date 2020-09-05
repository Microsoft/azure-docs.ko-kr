---
title: 특정 시점에서 키-값 쌍 검색
titleSuffix: Azure App Configuration
description: 키 값에 대 한 변경 기록을 유지 관리 하는 Azure 앱 구성의 지정 시간 스냅숏을 사용 하 여 이전 키-값 쌍을 검색 합니다.
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b1d559d82cb22d8a787785c6d8c6a5101d89793a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586617"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅샷

Azure 앱 구성은 키 값에 대 한 변경 기록을 유지 관리 합니다. 이 레코드는 키-값 변경의 타임 라인을 제공 합니다. 키 값 기록을 다시 생성 하 고 키 기록 기간 (무료 계층 상점의 경우 7 일, 표준 계층 매장의 경우 30 일) 내에 언제 든 지 과거 값을 제공할 수 있습니다. 이 기능을 사용 하 여 뒤로 "시간 이동" 하 고 이전 키-값을 검색할 수 있습니다. 예를 들어 응용 프로그램을 이전 구성으로 롤백하려면 최신 배포 이전에 사용 되는 구성 설정을 복구할 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

Azure Portal 또는 CLI를 사용 하 여 이전 키-값을 검색할 수 있습니다. Azure CLI에서 `az appconfig revision list` 적절 한 매개 변수를 추가 하 여 필요한 값을 검색 합니다.  저장소 이름 ()을 제공 `--name <app-config-store-name>` 하거나 연결 문자열 ()을 사용 하 여 Azure 앱 구성 인스턴스를 지정 합니다 `--connection-string <your-connection-string>` . 특정 시점 ()을 지정 하 `--datetime` 고 반환할 최대 항목 수 ()를 지정 하 여 출력을 제한 `--top` 합니다.

Azure CLI를 로컬로 설치 하지 않은 경우 필요에 따라 Azure Cloud Shell를 사용할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

키 값에 대해 기록 된 모든 변경 내용을 검색 합니다.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

키와 레이블 및에 대해 기록 된 모든 변경 내용을 검색 `environment` `test` `prod` 합니다.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

계층 키 공간에서 기록 된 모든 변경 내용을 검색 `environment:prod` 합니다.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

특정 시점에서 키에 대해 기록 된 모든 변경 내용을 검색 `color` 합니다.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

키 값에 기록 된 마지막 10 개 변경 내용을 검색 하 고 `key` , `label` 및 타임 스탬프에 대 한 값만 반환 합니다 `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
