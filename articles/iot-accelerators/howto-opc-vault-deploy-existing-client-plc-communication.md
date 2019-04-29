---
title: OPC 클라이언트 및 OPC PLC OPC 자격 증명 모음-Azure와의 통신을 보호 | Microsoft Docs
description: OPC 자격 증명 모음 CA를 사용 하 여 인증서를 서명 하 여 OPC 클라이언트 및 OPC PLC의 통신을 보호 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451092"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>OPC 클라이언트 및 OPC PLC의 통신을 보호

OPC 자격 증명 모음은 구성, 등록 및 OPC UA 서버 및 클라우드에서 클라이언트 응용 프로그램에 대 한 인증서 수명 주기를 관리할 수 있는 마이크로 서비스. 이 문서에서는 OPC 자격 증명 모음 CA를 사용 하 여 인증서를 서명 하 여 OPC 클라이언트 및 OPC PLC의 통신을 보호 하는 방법을 보여 줍니다.

다음 설정 하는 OPC 클라이언트는 OPC PLC에 대 한 연결을 테스트합니다. 기본적으로 연결이 아니므로 가능한 두 가지 구성 요소는 올바른 인증서를 사용 하 여 프로 비전 되지 않습니다. OPC UA 구성 요소를 아직 인증서를 사용 하 여 프로 비전 되지 된 경우 시작 시 자체 서명 된 인증서를 생성 됩니다. 그러나 인증서를 인증 기관 (CA)에서 서명 및 OPC UA 구성 요소의 설치 수 있습니다. 이 OPC 클라이언트 및 OPC PLC 완료 되 면 연결이 사용 됩니다. 다음 워크플로 프로세스를 설명합니다. OPC UA 보안에 대 한 배경 정보를 찾을 수 있습니다 [이 문서](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) 백서입니다. OPC UA 사양에 완전 한 정보를 찾을 수 있습니다.

테스트: 테스트 환경 구성 됩니다.

OPC 자격 증명 모음 스크립트:
- OPC 자격 증명 모음 CA를 사용 하 여 인증서를 서명 하 여 OPC 클라이언트 및 OPC PLC의 통신을 보호 합니다.

> [!NOTE]
> 자세한 내용은 참조는 GitHub [리포지토리](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)합니다.

## <a name="generate-a-self-signed-certificate-on-startup"></a>시작 시 자체 서명 된 인증서 생성

**준비**

- 환경 변수를 확인 `$env:_PLC_OPT` 하 고 `$env:_CLIENT_OPT` 예를 들어, 정의 되지 `$env:_PLC_OPT=""` PowerShell에서.

- 환경 변수를 설정 `$env:_OPCVAULTID` OPC 자격 증명 모음에 데이터를 다시 찾을 수 있습니다 하는 문자열입니다. 영숫자 문자만 허용 됩니다. 예를 들어 "123456"이 변수의 값으로 사용 되었습니다.

- Docker 볼륨이 없으면 없는지 `opcclient` 또는 `opcplc`합니다. 확인 `docker volume ls` 를 사용 하 여 제거 하 고 `docker volume rm <volumename>`입니다. 또한 사용 하 여 컨테이너를 제거 해야 `docker rm <containerid>` 경우 볼륨 컨테이너에서 계속 사용 됩니다.

**빠른 시작**

리포지토리의 루트에서 다음 PowerShell 명령을 실행 합니다.

```
docker-compose -f connecttest.yml up
```

**확인**

로그에 인증서가 없습니다. 첫 번째 시작에 설치 되어 있는지 확인 합니다. OPC PLC (OPC 클라이언트에 대해 비슷한 표시)의 로그 출력을 여기:...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
보고 하는 인증서에 표시 되 면 위의 단계를 준비 하 고 docker 볼륨을 삭제 합니다.

OPC PLC에 대 한 연결에 실패 했음을 확인 합니다. OPC 클라이언트 로그 출력에 다음 출력이 표시 됩니다.

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
오류가 발생 한 원인은 인증서가 신뢰할 수 있는입니다. 즉 `opc-client` 에 연결 하려고 `opc-plc` 고 나타내는 다시 응답을 받았습니다 `opc-plc` 신뢰할 수 없는 `opc-client`, 결과일 `opc-plc` 인증서 유효성을 검사할 수 없습니다는 `opc-client` 제공 했습니다. 이 추가 인증서 구성이 없는 자체 서명 된 인증서를 `opc-plc`, 연결이 실패 합니다.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>로그인 하 고 OPC UA 구성 요소에서 인증서를 설치 합니다.

**준비**
1. 단계 1의 로그 출력을 확인 하 고 OPC PLC 및 OPC 클라이언트에 대 한 "CreateSigningRequest 정보"를 인출 합니다. 다음 출력은 OPC PLC에 대해서만 표시 됩니다.

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. 로 이동 합니다 [OPC 자격 증명 모음 웹 사이트](https://opcvault.azurewebsites.net/)합니다.

1. `Register New`을(를) 선택합니다.

1. 로그 출력에서 OPC PLC 정보를 입력 `CreateSigningRequest information` 영역에서 입력된 필드에는 `Register New OPC UA Application` 페이지에서 `Server` ApplicationType로 합니다.

1. `Register`를 선택합니다.

1. 다음 페이지에서 `Request New Certificate for OPC UA Application` 선택 `Request new Certificate with Signing Request`합니다.

1. 다음 페이지에서 `Generate a new Certificate with a Signing Request` 에 붙여 넣습니다 합니다 `CSR (base64 encoded)` 에 로그 출력에서 문자열을 `CreateRequest` 입력된 필드. 전체 문자열을 복사를 확인 합니다.

1. `Generate New Certificate`를 선택합니다.

1. 이제를 이동 하는 앞으로 `View Certificate Request Details`입니다. 이 페이지의 인증서 저장소를 프로 비전 하는 데 필요한 모든 정보를 다운로드할 수 있습니다 `opc-plc`합니다.

1. 이 페이지에서:  
    - 선택 `Certificate` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<applicationcertbase64-string>` 나중에 있습니다. `Back`를 선택합니다.

    - 선택 `Issuer` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<addissuercertbase64-string>` 나중에 있습니다. `Back`를 선택합니다.

    - 선택 `Crl` 에서 `Download as Base64` 에 제공 된 텍스트 문자열을 복사 합니다 `EncodedBase64` 필드 및 이후 사용 하기 위해 저장 합니다. 참조로 `<updatecrlbase64-string>` 나중에 있습니다. `Back`를 선택합니다.

1. 라는 변수를 PowerShell에서 이제 설정 `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > 웹 사이트에서 인출 옵션 값 Base64 문자열로 전달 된 문자열을 대체 합니다.

부터 전체 프로세스를 반복 `Register New` (위 3 단계의) OPC 클라이언트에 대 한 합니다. 가지에 유의 해야 할 다음과 같은 차이점이 있습니다.

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

**확인** 두 구성 요소는 이제 응용 프로그램 인증서에 서명이 있는지 확인 합니다. 다음 출력에 대 한 로그 출력을 확인 합니다.

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
응용 프로그램 인증서는 CA에서 서명 하며

로그에 설치 된 인증서 이제 있는지 확인 합니다. 다음은 OPC PLC의 로그 출력 및 OPC 클라이언트와 유사한 출력이 합니다.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
응용 프로그램 인증서의 발급자는 CA `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` 및 OPC PLC이이 CA에서 서명 된 모든 인증서 또한 신뢰 합니다.


확인 하 고 OPC PLC에 대 한 연결이 성공적으로 만들어진 OPC 클라이언트 OPC PLC에서 데이터를 읽을 수 있습니다. OPC 클라이언트 로그 출력에 다음 출력이 표시 됩니다.
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
이 출력에 표시 되 면 OPC PLC 면 CA에서 서명 된 인증서를 이제 둘 이므로 어떤 위치에 인증서를 신뢰 모두 OPC 클라이언트 또는 그 반대로 이전을 신뢰 하는 이제이 CA에서 서명 합니다.

> [!NOTE] 
> OPC PLC에 대해서만 첫 번째 두 확인 단계에서는 주었지만 OPC 클라이언트도 확인할 수 해야 합니다.


## <a name="next-steps"></a>다음 단계

이제 기존 프로젝트에 OPC 자격 증명 모음을 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [기존 프로젝트에 OPC 쌍 배포](howto-opc-twin-deploy-existing.md)