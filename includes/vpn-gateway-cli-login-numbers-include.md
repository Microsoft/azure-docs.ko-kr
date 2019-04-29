---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411843"
---
1. [az login](/cli/azure/) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 로그인에 대한 자세한 내용은 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

   ```azurecli
   az login
   ```
2. Azure 구독이 두 개 이상인 경우 계정의 구독을 나열합니다.

   ```azurecli
   az account list --all
   ```
3. 사용할 구독을 지정합니다.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
