---
title: Redis 샘플에 대한 Azure Cache Azure CLI
description: 'Redis 용 Azure 캐시에 대한 Azure CLI 샘플: 캐시를 만들고, 캐시를 삭제 하 고, 캐시 세부 정보, 호스트 이름, 포트 및 키를 가져오고, 웹 앱에 연결 합니다.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: c43e23b4bf46258cc91b06a0912d03e85a5c7a14
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411354"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure Cache for Redis용 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| | |
|---|---|
|**캐시 만들기**||
| [캐시 만들기](./scripts/create-cache.md) | 리소스 그룹 및 기본 계층 Azure Cache for Redis를 만듭니다. |
| [클러스터링을 사용하여 프리미엄 캐시 만들기](./scripts/create-premium-cache-cluster.md) | 클러스터링을 사용하여 리소스 그룹 및 프리미엄 계층 캐시를 만듭니다.|
| [캐시 세부 정보 가져오기](./scripts/show-cache.md) | 프로비전 상태를 포함한 Azure Cache for Redis 인스턴스에 대한 세부 정보를 가져옵니다. |
| [호스트 이름, 포트 및 키 가져오기](./scripts/cache-keys-ports.md) | Azure Cache for Redis 인스턴스에 대한 호스트 이름, 포트 및 키를 가져옵니다. |
|**웹앱과 캐시**||
| [Azure Cache for Redis에 웹앱 연결](./../app-service/scripts/cli-connect-to-redis.md) | Azure 웹앱 및 Azure Cache for Redis를 만든 다음, Redis 연결 세부 정보를 앱 설정에 추가합니다. |
|**캐시 삭제**||
| [캐시 삭제](./scripts/delete-cache.md) | Azure Cache for Redis 인스턴스 삭제  |
| | |

Azure CLI에 대한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 및 [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요.
