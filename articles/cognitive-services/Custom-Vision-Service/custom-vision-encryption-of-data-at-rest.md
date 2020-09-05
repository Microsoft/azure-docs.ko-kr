---
title: 휴지 상태의 데이터 암호화 Custom Vision
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리 하는 암호화 키를 제공 하며, CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Custom Vision에 대 한 미사용 데이터 암호화 및 CMK를 사용 하 고 관리 하는 방법을 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077758"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>휴지 상태의 데이터 암호화 Custom Vision

Azure Custom Vision는 데이터를 클라우드에 보관할 때 자동으로 암호화 합니다. Custom Vision 암호화를 통해 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족할 수 있습니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객이 관리 하는 키는 11 월 2020 일 이후 생성 된 리소스만 사용할 수 있습니다. Custom Vision에서 CMK를 사용 하려면 새 Custom Vision 리소스를 만들어야 합니다. 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 id를 설정할 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성

고객 관리 키는 현재 다음 지역에서 사용할 수 있습니다.

* 미국 중남부
* 미국 서부 2
* 미국 동부
* US Gov 버지니아

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* CMK를 지 원하는 서비스의 전체 목록은 [Cognitive Services의 고객 관리 키](../encryption/cognitive-services-encryption-keys-portal.md) 를 참조 하세요.
* [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
