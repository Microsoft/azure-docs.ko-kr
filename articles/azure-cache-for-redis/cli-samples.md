---
title: Azure CLI를 사용하여 Azure Cache for Redis 관리
description: 'Azure Cache for Redis를 관리하기 위한 Azure CLI 샘플: 캐시 만들기, 캐시 삭제, 캐시 세부 정보, 호스트 이름, 포트 및 키 가져오기, 웹 앱 연결'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32530982bc2a9d1b5deb31b3bc71460462352258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536422"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure CLI를 사용하여 Azure Cache for Redis 관리

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| 캐시 만들기 | 설명 |
| ------------ | ----------- |
| [캐시 만들기](./scripts/create-cache.md) | 리소스 그룹 및 기본 계층 Azure Cache for Redis를 만듭니다. |
| [클러스터링을 사용하여 프리미엄 캐시 만들기](./scripts/create-premium-cache-cluster.md) | 클러스터링을 사용하여 리소스 그룹 및 프리미엄 계층 캐시를 만듭니다.|
| [캐시 세부 정보 가져오기](./scripts/show-cache.md) | 프로비전 상태를 포함한 Azure Cache for Redis 인스턴스에 대한 세부 정보를 가져옵니다. |
| [호스트 이름, 포트 및 키 가져오기](./scripts/cache-keys-ports.md) | Azure Cache for Redis 인스턴스에 대한 호스트 이름, 포트 및 키를 가져옵니다. |
|**웹앱과 캐시**| **설명**|
| [Azure Cache for Redis에 웹앱 연결](./../app-service/scripts/cli-connect-to-redis.md) | Azure 웹앱 및 Azure Cache for Redis를 만든 다음, Redis 연결 세부 정보를 앱 설정에 추가합니다. |
|**캐시 삭제**| **설명** |
| [캐시 삭제](./scripts/delete-cache.md) | Azure Cache for Redis 인스턴스 삭제  |

Azure CLI에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.