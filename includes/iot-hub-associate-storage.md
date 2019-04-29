---
title: 포함 파일
description: 포함 파일
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399167"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage 계정을 IoT Hub에 연결

시뮬레이션된 디바이스 앱에서 파일을 Blob에 업로드하므로 IoT Hub와 연결되는 [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) 계정이 있어야 합니다. IoT Hub와 Azure Storage 계정을 연결하면 IoT Hub는 SAS URI를 생성합니다. 디바이스는 Blob 컨테이너에 안전하게 파일을 업로드하는 데 SAS URI를 사용할 수 있습니다. IoT Hub 서비스 및 디바이스 SDK에서는 SAS URI를 생성하는 프로세스를 조정하여 파일을 업로드하는 데 디바이스를 사용할 수 있도록 합니다.

[Azure Portal을 사용하여 파일 업로드 구성](../articles/iot-hub/iot-hub-configure-file-upload.md)의 지침을 따릅니다. Blob 컨테이너가 IoT Hub와 연결되고 파일 알림을 사용하는지 확인합니다.

![포털에서 파일 알림 사용](./media/iot-hub-associate-storage/enable-file-notifications.png)