---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: fffbb01cba89335c47cc1ec8f5ea5dcb52b318de
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109508433"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

**service** 정책에 대한 IoT Hub 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 선택합니다. 허브가 있는 리소스 그룹을 선택한 다음, 리소스 목록에서 허브를 선택합니다.

1. IoT Hub의 왼쪽 창에서 **공유 액세스 정책** 을 선택합니다.

1. 정책 목록에서 **ervice** 정책을 선택합니다.

1. **공유 액세스 키** 에서 **연결 문자열 -- 기본 키** 에 대한 복사 아이콘을 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.
