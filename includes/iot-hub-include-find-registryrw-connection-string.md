---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883843"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**RegistryReadWrite** 정책에 대한 IoT Hub 연결 문자열을 가져오려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹**을 선택 합니다. 허브가 있는 리소스 그룹을 선택한 다음 리소스 목록에서 허브를 선택 합니다.

2. 허브의 왼쪽 창에서 **공유 액세스 정책**을 선택 합니다.

3. 정책 목록에서 **registryReadWrite** 정책을 선택 합니다.

4. **공유 액세스 키**에서 **연결 문자열--기본 키** 의 복사 아이콘을 선택 하 고 값을 저장 합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)을 참조 하세요.
