---
title: OPC UA 보안 클라이언트 및 OPC UA 서버의 OPC 자격 증명 모음-Azure를 사용 하 여 응용 프로그램 | Microsoft Docs
description: OPC UA 클라이언트 및 OPC UA OPC 자격 증명 모음을 사용 하 여 인증서를 새 키 쌍을 사용 하 여 서버 응용 프로그램을 보호 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450667"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>OPC UA 보안 클라이언트 및 OPC UA 서버 응용 프로그램 
OPC 자격 증명 모음은 클라우드 내 OPC UA 서버 및 클라이언트 애플리케이션에 대한 인증서 수명 주기를 구성, 등록 및 관리할 수 있는 마이크로서비스입니다. 이 문서에서는 OPC UA 클라이언트가 및 OPC UA OPC 자격 증명 모음을 사용 하 여 인증서를 새 키 쌍을 사용 하 여 서버 응용 프로그램을 보호 하는 방법을 보여 줍니다.

다음 설정 하는 OPC 클라이언트 OPC PLC에 대 한 연결이 테스트 됩니다. 기본적으로 연결 수 없는 구성 요소를 모두 올바른 인증서를 사용 하 여 아직 구성 하지는 않기 때문에 있습니다. 이 워크플로에서 수행 하지 OPC UA 구성 요소 자체 서명 된 인증서를 사용 하 고 OPC 자격 증명 모음을 통해 로그인 합니다. 이전을 참조 하세요 [시험대](howto-opc-vault-deploy-existing-client-plc-communication.md)합니다. 대신이 테스트는 구성 요소를 새 개인 키와 새 인증서를 사용 하 여 OPC 자격 증명 모음에 의해 생성 된 프로 비전 합니다. 이 OPC UA 보안에 대 한 배경 정보를 찾을 수 있습니다 [백서](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)합니다. OPC UA 사양에 완전 한 정보를 찾을 수 있습니다.

테스트: 테스트 환경 구성 됩니다.

OPC 자격 증명 모음 스크립트:
- OPC UA 클라이언트 및 OPC UA OPC 자격 증명 모음을 사용 하 여 인증서를 새 키 쌍을 사용 하 여 서버 응용 프로그램을 보호 합니다.

> [!NOTE]
> 자세한 내용은 참조는 GitHub [리포지토리](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)합니다.

## <a name="generate-a-new-certificate-and-private-key"></a>새 인증서와 개인 키를 생성 합니다. 
**준비**
- 환경 변수를 확인 `$env:_PLC_OPT` 고 `$env:_CLIENT_OPT` 정의 되지 않습니다. 예를 들어 `$env:_PLC_OPT=""` PowerShell에서
- 환경 변수를 설정 `$env:_OPCVAULTID` OPC 자격 증명 모음에 데이터를 다시 찾을 수 있습니다 하는 문자열입니다. 6 자리 수로 설정 하는 것이 좋습니다. 예를 들어 "123456" 변수의 값으로 사용 했습니다.
- Docker 볼륨 없는지 `opcclient` 또는 `opcplc`합니다. 확인 `docker volume ls` 를 사용 하 여 제거 하 고 `docker volume rm <volumename>`입니다. 또한 사용 하 여 컨테이너를 제거 해야 `docker rm <containerid>` 경우 볼륨 컨테이너에서 계속 사용 됩니다.

**빠른 시작**
1. 로 이동 합니다 [OPC 자격 증명 모음 웹 사이트](https://opcvault.azurewebsites.net/)

1. `Register New`을(를) 선택합니다.

1. 이전 테스트의 로그 출력에 표시 된 대로 OPC PLC 정보를 입력 `CreateSigningRequest information` 영역에서 입력된 필드에는 `Register New OPC UA Application` 페이지에서 `Server` ApplicationType로 합니다.

1. `Register`을(를) 선택합니다.

1. 다음 페이지에서 `Request New Certificate for OPC UA Application` 선택 `Request new KeyPair and Certificate`

1. 다음 페이지에서 `Generate a new Certificate with a Signing Request` 에 붙여 넣습니다 합니다 `CSR (base64 encoded)` 에 로그 출력에서 문자열을 `CreateRequest` 입력된 필드. 전체 문자열을 복사를 확인 합니다.

1. 다음 페이지에서 `Request New Certificate for OPC UA Application` 선택 `Request new Certificate with Signing Request`

1. 다음 페이지에서 `Generate a new KeyPair and for an OPC UA Application` 입력 `CN=OpcPlc` SubjectName,으로 `opcplc-<_OPCVAULTID>` (대체 `<_OPCVAULTID>` 기업과) DomainName, 선택 `PEM` PrivateKeyFormat으로 암호를 입력 하 고 (나중에로 지칭 `<certpassword-string>`)

1. `Generate New KeyPair`을(를) 선택합니다.

1. 이제를 이동 하는 앞으로 `View Certificate Request Details`입니다. 이 페이지의 인증서 저장소를 프로 비전 하는 데 필요한 모든 정보를 다운로드할 수 있습니다 `opc-plc`합니다.

1. 이 페이지에서:  
    - 선택 `Certificate` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<applicationcertbase64-string>` 나중에 있습니다. `Back`를 선택합니다.
    - 선택 `PrivateKey` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<privatekeybase64-string>` 나중에 있습니다. `Back`를 선택합니다.
    - 선택 `Issuer` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<addissuercertbase64-string>` 나중에 있습니다. `Back`를 선택합니다.
    - 선택 `Crl` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<updatecrlbase64-string>` 나중에 있습니다. `Back`를 선택합니다.

1. 라는 변수를 PowerShell에서 이제 설정 `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    웹 사이트에서 인출 옵션 값 Base64 문자열로 전달 된 문자열을 대체 합니다.  

1. 부터 전체 프로세스를 반복 `Register New` OPC 클라이언트에 대 한 합니다. 가지에 유의 해야 할 다음과 같은 차이점이 있습니다.
    - 로그 출력에서 사용 된 `opcclient`합니다.
    - 선택 `Client` 등록 하는 동안 ApplicationType로 합니다.
    - 사용 하 여 `$env:_CLIENT_OPT` PowerShell 변수 이름으로 합니다.

    > [!NOTE] 
    > 이 시나리오를 사용 하는 동안 있습니다 수 있는 인식 하는 `<addissuercertbase64-string>` 하 고 `<updatecrlbase64-string>` 값에 대 한 동일 `opcplc` 및 `opcclient`. 이 사용 사례에 대 한 true 이며 단계를 수행 하는 동안 일부 시간을 절약할 수 있습니다.

**빠른 시작**

리포지토리의 루트에서 다음 PowerShell 명령을 실행 합니다.

```
docker-compose -f connecttest.yml up
```

**확인**

두 구성 요소는 기존 응용 프로그램 인증서가 되지 않은 확인 합니다. 로그 출력을 확인 합니다. 다음은 OPC PLC의 출력 및 OPC 클라이언트에 유사한 로그 출력 합니다.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
응용 프로그램 인증서의 경우 준비 단계에서 설명 했 듯이 docker 볼륨을 제거 합니다.

로그에는 발급자 인증서 저장소에도 신뢰할 수 있는 피어 인증서 저장소와 같이 OPC 자격 증명 모음 CA 인증서가 설치 되어 있는지 확인 합니다. 다음은 OPC PLC의 로그 출력 및 OPC 클라이언트에 유사한 로그 출력. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC에서 이제 OPC 자격 증명 모음에서 서명 된 인증서를 사용 하 여 모든 OPC UA 클라이언트를 신뢰 해야 합니다.

로그에 개인 키 형식인 PEM으로 인식 되는 하 고 새 응용 프로그램 인증서를 설치 되어 있는지 확인 합니다. 다음은 OPC PLC의 로그 출력 및 OPC 클라이언트에 유사한 로그 출력. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

응용 프로그램 인증서 및 개인 키를 응용 프로그램 인증서 저장소에 설치 되어 이제 되며 OPC UA 응용 프로그램에서 사용 합니다.

OPC PLC 고 OPC 클라이언트 간의 연결을 성공적으로 설정할 수 있습니다 하 고 OPC PLC에서 OPC 클라이언트 데이터를 성공적으로 읽을 수를 확인 합니다. OPC 클라이언트 로그 출력에 다음 출력이 표시 됩니다.
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
이 출력을 표시 하는 경우 다음 OPC PLC 이제 신뢰 하는 것을 OPC 클라이언트 또는 그 반대로 이전이이 CA에서 서명 된 두 신뢰 인증서를 CA로 서명 된 인증서를 이제 둘 다 없으므로 합니다.

### <a name="a-testbed-for-opc-publisher"></a>OPC 게시자에 대 한 테스트 ###

**빠른 시작**

리포지토리의 루트에서 다음 PowerShell 명령을 실행 합니다.
```
docker-compose -f testbed.yml up
```

**확인**
- 데이터는 설정 하 여 구성한 iot Hub에 전송 됩니다 확인 `_HUB_CS` 를 사용 하 여 [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 하거나 [iothub explorer](https://github.com/Azure/iothub-explorer)합니다.
- OPC 테스트 클라이언트는 게시/게시를 취소 하려면 노드 OPC 테스트 서버에서 OPC 게시자를 구성 하려면 iot Hub 직접 메서드 호출 및 OPC 메서드 호출을 사용 하려고 합니다.
- 오류 메시지에 대 한 출력을 시청 하세요.

## <a name="next-steps"></a>다음 단계

이제 기존 프로젝트에 OPC 자격 증명 모음을 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [기존 프로젝트에 OPC 쌍 배포](howto-opc-twin-deploy-existing.md)