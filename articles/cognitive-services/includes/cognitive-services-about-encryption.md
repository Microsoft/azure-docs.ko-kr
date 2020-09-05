---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069915"
---
## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256 비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 암호화 및 암호 해독은 투명 합니다. 즉, 암호화 및 액세스를 관리 합니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성 된 경우 두 번째 보안 계층을 제공 하는 동시에 두 번째 암호화가 제공 되며,이를 통해 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.