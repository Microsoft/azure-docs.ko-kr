---
title: 고객이 관리 하는 키를 통해 저장소 데이터 암호화 Azure IoT Hub | Microsoft Docs
description: IoT Hub를 위해 고객이 관리 하는 키를 사용 하 여 저장소 데이터 암호화
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142200"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>IoT Hub를 위해 고객이 관리 하는 키를 사용 하 여 저장소 데이터 암호화

IoT Hub는 CMK (고객 관리 키)를 사용 하 여 미사용 데이터의 암호화를 지원 합니다 (BYOK (사용자 고유 키 사용) 라고도 함). Azure IoT Hub은 데이터 센터에 기록 되는 미사용 데이터 및 전송 중인 데이터의 암호화를 제공 하 고 액세스할 때 암호를 해독 합니다. 기본적으로 IoT Hub는 Microsoft 관리 키를 사용 하 여 미사용 데이터를 암호화 합니다. CMK를 사용 하 여 기본 암호화를 기반으로 하는 다른 암호화 계층을 가져올 수 있으며, [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)을 통해 관리 되는 키 암호화 키를 사용 하 여 미사용 데이터를 암호화 하도록 선택할 수 있습니다. 이를 통해 액세스 제어를 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다. IoT Hub에 대해 BYOK가 구성 된 경우 두 번째 보호 계층을 제공 하는 동시에 Azure Key Vault를 통해 암호화 키를 제어할 수 있도록 하는 이중 암호화도 제공 합니다.

이 기능을 사용 하려면 새 IoT Hub (기본 또는 표준 계층)을 만들어야 합니다. 이 기능을 사용해 보려면 [Microsoft 지원](https://azure.microsoft.com/support/create-ticket/)에 문의 하세요. Microsoft 지원에 문의할 때 회사 이름 및 구독 ID를 공유 하세요.


## <a name="next-steps"></a>다음 단계

* [IoT Hub에 대 한 자세한 정보](./about-iot-hub.md)

* [Azure Key Vault에 대해 자세히 알아보기](../key-vault/general/overview.md)