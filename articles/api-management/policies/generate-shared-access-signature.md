---
title: 샘플 API Management 정책 - 공유 액세스 서명 생성
titleSuffix: Azure API Management
description: Azure API Management 정책 샘플 - 식을 사용하여 공유 액세스 서명을 생성하고 rewrite-uri 정책을 사용하여 Azure Storage로 요청을 전달하는 방법을 보여줍니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078428"
---
# <a name="generate-shared-access-signature"></a>공유 액세스 서명 생성

이 문서에서는 식을 사용하여 [공유 액세스 서명](../../storage/common/storage-sas-overview.md)을 생성하고 rewrite-uri 정책을 사용하여 Azure Storage로 요청을 전달하는 방법을 보여 주는 Azure API Management 정책 샘플을 제공합니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-reference.md)