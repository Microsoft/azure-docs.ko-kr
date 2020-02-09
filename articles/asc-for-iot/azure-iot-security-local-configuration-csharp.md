---
title: '| 용 C# IoT 보안 에이전트 로컬 구성 파일에 대한 Azure Security Center 이해 Microsoft Docs'
description: IoT 보안 서비스의 Azure Security Center에 대한 자세한 내용은의 C#보안 에이전트 로컬 구성 파일을 확인 하세요.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 0172ada68ffa652fb0c301c89238beca4f4ce2f9
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664199"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>로컬 구성 파일 (C# 에이전트) 이해


IoT 보안 에이전트의 Azure Security Center은 로컬 구성 파일의 구성을 사용 합니다.

보안 에이전트는 에이전트가 시작 될 때 구성 파일을 한 번 읽습니다. 로컬 구성 파일에 있는 구성에는 인증 구성과 기타 에이전트 관련 구성이 모두 포함 됩니다.

보안 C# 에이전트는 여러 구성 파일을 사용 합니다.

- **일반 구성** -에이전트 관련 구성입니다.
- **인증 구성-인증** 관련 구성 (인증 세부 정보 포함).
- **Securityiotinterface .config** -IoT 관련 구성.

구성 파일에는 기본 구성이 포함 되어 있습니다. 에이전트를 설치 하는 동안 인증 구성이 채워지고 에이전트가 다시 시작 될 때 구성 파일에 대한 변경 내용이 적용 됩니다. 

## <a name="configuration-file-location"></a>구성 파일 위치
Linux의 경우:
- 운영 체제 구성 파일은 `/var/ASCIoTAgent`에 있습니다.

Windows의 경우:
- 운영 체제 구성 파일은 보안 에이전트의 디렉터리 내에 있습니다. 

### <a name="generalconfig-configurations"></a>일반 구성

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| 인 | GUID | 에이전트 고유 식별자 |
| readRemoteConfigurationTimeout | timespan | IoT Hub에서 원격 구성을 가져오는 기간입니다. 에이전트가 지정 된 시간 내에 구성을 인출할 수 없는 경우 작업 시간이 초과 됩니다.|
| schedulerInterval | timespan | 내부 스케줄러 간격입니다. |
| producerInterval | timespan | 이벤트 생산자 작업자 간격입니다. |
| consumerInterval | timespan | 이벤트 소비자 작업자 간격입니다. |
| highPriorityQueueSizePercentage | 0 < 번호 < 1 | 우선 순위가 높은 메시지의 전용 총 캐시 부분입니다. |
| logLevel | "Off", "치명적", "Error", "Warning", "Information", "Debug"  | 이 심각도가 같은 로그 메시지는 디버그 콘솔 (Linux의 Syslog)에 기록 됩니다. |
| fileLogLevel |  "Off", "치명적", "Error", "Warning", "Information", "Debug"| 이 심각도가 같은 로그 메시지는 파일 (Linux의 Syslog)에 기록 됩니다. |
| diagnosticVerbosityLevel | "None", "Some", "All", | 진단 이벤트의 자세한 표시 수준입니다. 없음-진단 이벤트가 전송 되지 않고 중요도가 높은 진단 이벤트가 모두 전송 되며 모든 로그가 진단 이벤트로도 전송 됩니다. |
| logFilePath | 파일 경로 | FileLogLevel > Off 인 경우 로그는이 파일에 기록 됩니다. |
| defaultEventPriority | "높음", "낮음", "꺼짐" | 기본 이벤트 우선 순위입니다. |

### <a name="generalconfig-example"></a>일반 .config 예
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>인증 .config

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| moduleName | 문자열 | 보안 모듈 id의 이름입니다. 이 이름은 장치의 모듈 id 이름과 일치 해야 합니다. |
| deviceId | 문자열 | Azure IoT Hub에 등록 된 장치의 ID입니다. || schedulerInterval | TimeSpan 문자열 | 내부 스케줄러 간격입니다. |
| 게이트웨이 호스트 이름 | 문자열 | Azure Iot Hub의 호스트 이름입니다. 일반적으로 < 내 허브 >. azure-devices.net |
| filePath | 문자열-파일의 경로 | 인증 암호를 포함 하는 파일의 경로입니다.|
| type | "SymmetricKey", "New-selfsignedcertificate" | 인증에 대한 사용자 암호입니다. 사용자 비밀이 대칭 키인 경우 *SymmetricKey* 을 선택 하 고, 비밀이 자체 서명 된 인증서 인 경우 *자체 서명 된 인증서* 를 선택 합니다. |
| ID | "DPS", "모듈", "장치" | 인증 id-dps를 통해 인증을 수행 하는 경우 dps, 모듈 자격 증명을 사용 하 여 인증을 수행 하는 경우 모듈, 장치 자격 증명을 사용 하 여 인증 하는 경우 장치
| certificateLocationKind |  "LocalFile", "Store" | LocalFile 인증서가 파일에 저장 된 경우 인증서가 인증서 저장소에 있는 경우 저장 합니다. |
| idScope | 문자열 | DPS의 ID 범위 |
| registrationId | 문자열  | DPS 장치 등록 ID입니다. |
|

### <a name="authenticationconfig-example"></a>인증 .config 예
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>Securityi이상 인터페이스 .config

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT Hub 전송 유형입니다. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface .config 예
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대한 Azure Security Center에 대해 자세히 알아보기
- IoT [서비스](quickstart-onboard-iot-hub.md) 에 대한 Azure Security Center 사용
- IoT 서비스에 대한 Azure Security Center [FAQ](resources-frequently-asked-questions.md) 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- 보안 [경고](concept-security-alerts.md) 이해