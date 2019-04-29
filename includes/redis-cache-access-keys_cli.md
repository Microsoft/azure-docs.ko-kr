---
title: 포함 파일
description: 포함 파일
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485353"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Azure CLI를 사용하여 호스트 이름, 포트 및 액세스 키를 검색합니다.

호스트 이름 및 호출할 수 있습니다 Azure CLI를 사용 하 여 포트를 검색할 [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), 및 호출 수 키를 검색 하려면 [az redis 키 목록](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys)합니다. 다음 스크립트는 다음 두 명령을 호출 하 고 호스트 이름, 포트 및 키를 콘솔 에코 합니다.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

이 스크립트에 대 한 자세한 내용은 참조 하세요. [Redis에 대 한 Azure 캐시에 대 한 호스트 이름, 포트 및 키 가져오기](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md)합니다. Azure CLI에 대 한 자세한 내용은 참조 하세요 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 하 고 [Azure CLI를 사용 하 여 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)합니다.
