---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747502"
---
1. 사용 된 `dps-keygen` 명령줄 유틸리티는 연결 문자열을 생성 하려면:

    설치 하는 [생성기 유틸리티 키](https://github.com/Azure/dps-keygen), 다음 명령을 실행:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 연결 문자열을 생성 하려면 이전에 적어둔 연결 세부 정보를 사용 하 여 다음 명령을 실행 합니다.

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 연결 문자열을 복사 합니다 `dps-keygen` 장치 코드에서 사용 하는 출력입니다.