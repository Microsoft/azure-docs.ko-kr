---
title: IoT 장치 조사 가이드 미리 보기에 대 한 azure Security Center | Microsoft Docs
description: 이 방법 가이드 IoT 용 Azure Security Center를 사용 하 여 Log Analytics를 사용 하 여 의심 스러운 IoT 장치를 조사 하는 방법에 설명 합니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 6182662cb0da7fa5bcd3f329ada9ca5851490724
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357567"
---
# <a name="investigate-a-suspicious-iot-device"></a>의심스러운 IoT 디바이스 조사

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 서비스 경고 및 증명 정보에 대 한 azure Security Center (ASC) IoT 장치에서 의심 스러운 활동 징후 존재 장치가 노출 되는 하는 경우 참여 것으로 의심 되는 경우 명확한 징후를 제공 합니다. 

이 가이드에서는 조직에 잠재적인 위험을 확인 하 고, 수정 및 나중에 유사한 공격을 방지 하는 최선의 방법을 검색 하는 방법을 결정 하는 데 제공 조사 제안 사항을 사용 합니다.  

> [!div class="checklist"]
> * 디바이스 데이터 찾기
> * kql 쿼리를 사용하여 조사


## <a name="how-can-i-access-my-data"></a>내 데이터를 액세스 하려면 어떻게 해야 합니까?

기본적으로 IoT용 ASC는 Log Analytics 작업 영역에 보안 경고 및 추천 사항을 저장합니다. 원시 보안 데이터를 저장하도록 선택할 수도 있습니다.

데이터 스토리지에 대한 Log Analytics 작업 영역을 찾으려면 다음을 수행합니다.

1. IoT Hub를 엽니다. 
1. 아래 **보안**, 클릭 **개요**를 선택한 후 **설정**합니다.
1. Log Analytics 작업 영역의 구성 세부 정보를 변경합니다. 
1. **저장**을 클릭합니다. 

구성이 완료되면 다음을 수행하여 Log Analytics 작업 영역에 저장된 데이터에 액세스합니다.

1. IoT Hub에서 IoT용 ASC 경고를 선택하고 클릭합니다. 
1. **추가 조사**를 클릭합니다. 
1. **이 경고가 있는 디바이스를 확인하려면 여기를 클릭하여 DeviceId 열을 보십시오**를 선택합니다.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>의심스러운 IoT 디바이스에 대한 조사 단계

Insights 및 IoT 장치에 대 한 원시 데이터에 액세스 하려면 Log Analytics 작업 영역으로 이동 [데이터에 액세스 하려면](#how-can-i-access-my-data)합니다.

확인 하 고 다음 세부 정보 및 다음 kql 쿼리를 사용 하 여 활동에 대 한 장치 데이터를 조사 합니다.

### <a name="related-alerts"></a>관련된 경고

대략 비슷한 시간에 다른 경고가 트리거되었는지 확인하려면 다음 kql 쿼리를 사용합니다.

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>액세스 권한이 있는 사용자

이 디바이스에 액세스할 수 있는 사용자를 확인하려면 다음 kql 쿼리를 사용합니다. 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
이 데이터를 사용하여 다음을 검색합니다. 
  1. 어떤 사용자가 디바이스에 액세스할 수 있습니까?
  2. 액세스 권한이 있는 사용자를 예상 대로 사용 권한 수준을 있습니까? 

### <a name="open-ports"></a>포트 열기

장치에서 포트는 현재 사용 또는 사용 된를 확인 하려면 다음 kql 쿼리를 사용 합니다. 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. 디바이스에서 현재 활성 중인 수신 대기 소켓은 무엇입니까?
  2. 현재 활성화 되어 있는 수신 대기 소켓을 허용 합니다.
  3. 장치에 연결 된 모든 의심 스러운 원격 주소 있습니까?

### <a name="user-logins"></a>사용자 로그인

장치에 로그인 하는 사용자 확인 하려면 다음 kql 쿼리를 사용 합니다. 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. 어떤 사용자가 디바이스에 로그인했습니까?
  2. 로그인 하는 사용자가 로그인 할?
  3. 로그인한 사용자가 예상 IP 주소 또는 예기치 않은 IP 주소에서 연결했습니까?
  
### <a name="process-list"></a>프로세스 목록

프로세스 목록을 예상 대로 인지를 확인 하려면 다음 kql 쿼리를 사용 합니다. 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. 디바이스에서 의심스러운 프로세스가 실행되고 있습니까?
  2. 적절한 사용자가 프로세스를 실행했습니까?
  3. 명령줄 실행에 올바른 예상 인수가 포함되었습니까?

## <a name="next-steps"></a>다음 단계

디바이스를 조사하고 위험을 더 잘 이해한 후에는 IoT 솔루션 보안 상태를 향상시키기 위해 [사용자 지정 경고 구성](quickstart-create-custom-alerts.md)을 고려할 수 있습니다. 디바이스 에이전트가 아직 없는 경우 결과를 향상시키려면 [보안 에이전트를 배포](how-to-deploy-agent.md)하거나 [기존 디바이스 에이전트의 구성을 변경](how-to-agent-configuration.md)하는 것이 좋습니다. 