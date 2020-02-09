---
title: IoT 기준에 대한 Azure Security Center 이해 | Microsoft Docs
description: IoT 기준에 대한 Azure Security Center 개념에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176611"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>IoT 기준 및 사용자 지정 검사에 대한 Azure Security Center

이 문서에서는 IoT 기준에 대한 Azure Security Center 설명 하 고 기준 사용자 지정 검사의 모든 관련 속성을 요약 합니다.

## <a name="baseline"></a>기초

기준은 각 장치에 대한 표준 동작을 설정 하 고 예상 되는 표준의 비정상적인 동작 또는 편차를 보다 쉽게 설정할 수 있습니다.  

## <a name="baseline-custom-checks"></a>기준 사용자 지정 검사

기준 사용자 지정 검사는 장치의 **모듈 id** 쌍을 사용 하 여 각 장치 기준에 대한 검사의 사용자 지정 목록을 설정 합니다. 

## <a name="setting-baseline-properties"></a>기준 속성 설정

1. IoT Hub에서 변경 하려는 장치를 찾아 선택 합니다.
1. 장치를 클릭 한 다음 **azureiotsecurity** 모듈을 클릭 합니다.
1. **모듈 id**쌍을 클릭 합니다.
1. **기준 사용자 지정 검사** 파일을 장치에 업로드 합니다.
1. 보안 모듈에 기준 속성을 추가 하 고 **저장**을 클릭 합니다.

### <a name="baseline-custom-check-file-example"></a>기준 사용자 지정 검사 파일 예제

기준 사용자 지정 검사를 구성 하려면:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>기준 사용자 지정 검사 속성

| 이름| Status | 유효한 값| 기본값| 설명 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|필수: true |유효한 값:  **Boolean** |기본값: **false** |우선 순위가 높은 메시지가 전송 되기 전의 최대 시간 간격입니다.|
|baselineCustomChecksFilePath |필수: true|유효한 값:  **문자열**, **null** |기본값: **null** |기본 xml 구성의 전체 경로입니다.|
|baselineCustomChecksFileHash |필수: true|유효한 값:  **문자열**, **null** |기본값: **null** |xml 구성 파일의 `sha256sum`입니다. 추가 정보는 [sha256sum 참조](https://linux.die.net/man/1/sha256sum) 를 사용 합니다. |

추가 기준 예제를 검토 하려면 [사용자 지정 기준 예제-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) 및 [사용자 지정 기준 예제-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [원시 보안 데이터](how-to-security-data-access.md) 액세스
- [장치 조사](how-to-investigate-device.md)
- [보안 권장 사항](concept-recommendations.md) 이해 및 탐색
- [보안 경고](concept-security-alerts.md) 이해 및 탐색
