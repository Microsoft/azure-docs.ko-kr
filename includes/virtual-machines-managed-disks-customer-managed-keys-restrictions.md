---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98859777"
---
- 2,048비트, 3,072비트 및 4,096비트 크기의 [소프트웨어 및 HSM RSA 키](../articles/key-vault/keys/about-keys.md)만 지원되며 다른 키 또는 크기는 지원되지 않습니다.
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) 키에는 Azure 키 자격 증명 모음의 **프리미엄** 계층이 필요합니다.
- 서버 쪽 암호화 및 고객 관리형 키를 사용하여 암호화된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리형 키를 사용하여 암호화해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객 관리형 키로 암호화된 디스크에서 만든 스냅샷은 동일한 고객 관리형 키를 사용하여 암호화해야 합니다.
- 고객 관리형 키와 관련된 모든 리소스(Azure Key Vault, 디스크 암호화 집합, VM, 디스크 및 스냅샷)는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리형 키로 암호화된 디스크, 스냅샷 및 이미지는 다른 리소스 그룹 및 구독으로 이동할 수 없습니다.
- 현재 또는 이전에 Azure Disk Encryption을 통해 암호화된 관리 디스크는 고객 관리형 키를 사용하여 암호화할 수 없습니다.
- 구독당 지역당 최대 1000개의 디스크 암호화 집합을 만들 수 있습니다.
- 공유 이미지 갤러리에서 고객 관리형 키를 사용하는 방법에 대한 자세한 정보는 [미리 보기: 이미지 암호화를 위해 고객 관리형 키 사용](../articles/virtual-machines/image-version-encryption.md)을 참조하세요.
