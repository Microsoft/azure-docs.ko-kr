---
title: 인증서 사용 - Azure Batch
description: 인증서를 사용하여 애플리케이션 인증 사용
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146388"
---
# <a name="using-certificates-with-batch"></a>Batch로 인증서 사용

현재 Batch로 인증서를 사용하는 주된 이유는 엔드포인트로 인증해야 하는 풀에서 실행 중인 애플리케이션이 있기 때문입니다. 

인증서가 아직 없는 경우 `makecert` 명령줄 도구를 사용하여 자체 서명된 인증서를 만들 수 있습니다.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Azure Portal을 통해 수동으로 인증서 업로드

1. 인증서를 업로드할 Batch 계정에서 **인증서**를 선택한 다음, **추가**를 선택합니다. 

2. .pfx 또는 .cer 확장명으로 인증서를 업로드합니다. 

업로드되면 인증서 목록에 인증서가 추가되고 지문을 확인할 수 있습니다.

이제 Batch 풀을 만들 때 풀 내의 인증서로 이동하여 해당 풀에 업로드한 인증서를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Batch에는 인증서 API [AZ batch certificate create](/cli/azure/batch/certificate)가 있습니다.

Key Vault 사용에 대한 자세한 내용은 [Batch를 사용하여 Key Vault에 안전하게 액세스](credential-access-key-vault.md)를 참조하세요.
