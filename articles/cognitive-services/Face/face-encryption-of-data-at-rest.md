---
title: 미사용 데이터의 얼굴 서비스 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리 하는 암호화 키를 제공 하며, CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 사용 하기 위해 미사용 데이터 암호화와 CMK를 사용 하 고 관리 하는 방법을 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: eab90fc2cb30ae8e9f1c19bdbefc6fbc88c32f76
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079271"
---
# <a name="face-service-encryption-of-data-at-rest"></a>미사용 데이터의 얼굴 서비스 암호화

Face 서비스는 데이터를 클라우드에 보관할 때 데이터를 자동으로 암호화 합니다. 얼굴 서비스 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층 에서만 사용할 수 있습니다. 고객 관리 키를 사용 하는 기능을 요청 하려면 [고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성 하 여 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. Face 서비스에서 CMK를 사용 하도록 승인 되 면 새 얼굴 리소스를 만들고이를 가격 책정 계층으로 선택 해야 합니다. E0 가격 책정 계층을 사용 하 여 얼굴 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 되는 id를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* CMK를 지 원하는 서비스의 전체 목록은 [Cognitive Services의 고객 관리 키](../encryption/cognitive-services-encryption-keys-portal.md) 를 참조 하세요.
* [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
