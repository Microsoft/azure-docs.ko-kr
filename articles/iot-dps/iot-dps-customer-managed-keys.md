---
title: 고객 관리 키를 통해 미사용 Azure 장치 프로 비전 서비스 데이터 암호화 | Microsoft Docs
description: 장치 프로 비전 서비스에 대 한 고객 관리 키를 사용 하 여 저장소 데이터 암호화
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967179"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>장치 프로 비전 서비스에 대 한 고객 관리 키를 사용 하 여 저장소 데이터 암호화

## <a name="overview"></a>개요

DPS (장치 프로 비전 서비스)는 CMK (고객이 관리 하는 키)를 사용 하 여 미사용 데이터의 암호화를 지원 합니다 (BYOK). DPS는 데이터 센터에 기록 된 대로 미사용 데이터 및 전송 중인 데이터의 암호화를 제공 하 고 사용자가 액세스할 때 암호를 해독 합니다. 기본적으로 DPS는 Microsoft 관리 키를 사용 하 여 미사용 데이터를 암호화 합니다. CMK를 사용 하면 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 통해 관리 되는 키 암호화 키를 사용 하 여 미사용 데이터를 암호화 하도록 선택 하 여 기본 플랫폼 암호화 위에 추가 암호화 계층을 사용할 수 있습니다. 이렇게 하면 키를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있는 유연성을 제공 합니다. DPS에 대해 CMK가 구성 된 경우 데이터를 보호 하는 두 계층의 보호를 사용 하 여 이중 암호화를 사용 하도록 설정 하는 것을 의미 합니다. 

이 기능을 사용 하려면 새 DPS를 만들어야 합니다. 이 기능을 사용해 보려면 [Microsoft 지원](https://azure.microsoft.com/support/create-ticket/)에 문의 하세요. Microsoft 지원에 문의할 때 회사 이름 및 구독 ID를 공유 하세요.


## <a name="next-steps"></a>다음 단계

* [장치 프로 비전 서비스에 대 한 자세한 정보](./index.yml)

* [Azure Key Vault에 대해 자세히 알아보기](../key-vault/general/overview.md)