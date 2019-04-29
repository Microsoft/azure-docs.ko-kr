---
title: IoT 미리 보기에 대 한 Azure Security Center에 대 한 인증 방법을 | Microsoft Docs
description: IoT 서비스에 대 한 Azure Security Center를 사용 하는 경우 사용 가능한 다른 인증 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d1f9d77d1f87e8d201e2cb034401bb3cae14f41b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359073"
---
# <a name="security-agent-authentication-methods"></a>보안 에이전트 인증 방법 

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT Hub를 사용 하 여 인증 하려면 AzureIoTSecurity 에이전트를 사용 하 여 사용할 수 있습니다 다른 인증 방법을 설명 합니다.

각 장치에 보안 센터 ASC (Azure) IoT Hub에 IoT 용으로 등록 보안 모듈이 필요 합니다. 장치를 인증 하려면 ASC IoT에 대 한 두 가지 방법 중 하나를 사용할 수 있습니다. 기존 IoT 솔루션에 가장 적합 한 방법을 선택 합니다. 

> [!div class="checklist"]
> * 보안 모듈 옵션
> * 장치 옵션

## <a name="authentication-methods"></a>인증 방법

인증을 수행 하도록 AzureIoTSecurity 에이전트는 두 가지 방법:

 - **모듈** 인증 모드<br>
   모듈은 장치 쌍 독립적으로 인증 됩니다.
   보안 에이전트 보안 모듈 (대칭 키에만 해당)를 통해 전용된 인증 방법을 사용 하려는 경우이 인증 유형을 사용 합니다.
        
 - **장치** 인증 모드<br>
    이 방법에서는 보안 에이전트는 처음 장치 id를 사용 하 여 인증합니다. IoT 에이전트용 ASC는 초기 인증을 받은 후 다음을 수행 합니다.는 **REST** REST API를 사용 하 여 장치 인증 데이터를 사용 하 여 IoT Hub를 호출 합니다. 그런 다음 ASC IoT 에이전트에 대 한 IoT Hub에서 보안 모듈 인증 방법 및 데이터를 요청합니다. 마지막 단계로, IoT 에이전트용 ASC IoT 모듈에 대 한 ASC에 대 한 인증을 수행합니다.
    
    보안 에이전트는 기존 장치 인증 메서드를 다시 사용 (자체 서명 된 인증서 또는 대칭 키)를 원하는 경우이 인증 유형을 사용 합니다. 

참조 [보안 에이전트 설치 매개 변수](#security-agent-installation-parameters) 를 구성 하는 방법을 알아봅니다.
                                
## <a name="authentication-methods-known-limitations"></a>알려진 제한 하는 인증 방법

- **모듈** 인증 모드는 대칭 키 인증만 지원 합니다.
- CA 서명 인증서에서 지원 되지 않습니다 **장치** 인증 모드입니다.  

## <a name="security-agent-installation-parameters"></a>보안 에이전트 설치 매개 변수

때 [보안 에이전트 배포](how-to-deploy-agent.md), 인수로 인증 세부 정보를 제공 해야 합니다.
이러한 인수는 다음 표에 나와 있습니다.


|매개 변수|설명|옵션|
|---------|---------------|---------------|
|**identity**|인증 모드| **모듈** 또는 **장치**|
|**type**|인증 유형|**SymmetricKey** 또는 **SelfSignedCertificate**|
|**filePath**|인증서 또는 대칭 키를 포함 하는 파일에 대 한 전체 경로 절대| |
|**gatewayHostname**|IoT Hub의 FQDN|예제: ContosoIotHub.azure-devices.net|
|**deviceId**|디바이스 ID|예제: MyDevice1|
|**certificateLocationKind**|인증서 저장소 위치|**LocalFile** 또는 **저장소**|


보안 에이전트 설치 스크립트를 사용 하는 경우 다음 구성은 자동으로 수행 됩니다.

보안 에이전트 인증을 수동으로 편집 하려면 구성 파일을 편집 합니다. 

## <a name="change-authentication-method-after-deployment"></a>배포 후 인증 메서드 변경

설치 스크립트를 사용 하 여 보안 에이전트를 배포, 구성 파일을 자동으로 생성 됩니다.

배포 후 인증 메서드를 변경 하려면 구성 파일을 수동으로 편집할 필요 합니다.


### <a name="c-based-security-agent"></a>C#-보안 에이전트 기반

편집할 _Authentication.config_ 다음 매개 변수를 사용 하 여:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C 기반 보안 에이전트

편집할 _LocalConfiguration.json_ 다음 매개 변수를 사용 하 여:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>참고 항목
- [보안 에이전트 개요](security-agent-architecture.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [원시 보안 데이터에 액세스](how-to-security-data-access.md)
