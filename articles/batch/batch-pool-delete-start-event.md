---
title: Azure Batch 풀 삭제 시작 이벤트 | Microsoft Docs
description: Batch 풀 삭제 시작 이벤트에 대한 참조입니다.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774542"
---
# <a name="pool-delete-start-event"></a>풀 삭제 시작 이벤트

 이 이벤트는 풀 삭제 작업이 시작되면 내보내집니다. 풀 삭제는 비동기 이벤트이므로 풀 삭제 완료 이벤트는 삭제 작업이 완료되면 내보진다고 예상할 수 있습니다.

 다음 예에서는 풀 삭제 시작 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool1"
}
```

|요소|형식|메모|
|-------------|----------|-----------|
|id|String|풀의 ID입니다.|

<!-- Update_Description: update metedata properties -->