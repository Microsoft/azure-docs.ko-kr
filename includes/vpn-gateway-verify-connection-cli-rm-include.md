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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411816"
---
[az network vpn-connection show](/cli/azure/network/vpn-connection) 명령을 사용하여 연결이 성공했는지 확인할 수 있습니다. 예제에서  --name은 테스트하려는 연결의 이름을 의미합니다. 연결이 계속 설정 중이면 연결 상태가 '연결 중'으로 표시됩니다. 연결이 설정되면 상태가 '연결됨'으로 변경됩니다.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
