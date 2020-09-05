---
title: Azure Stack Edge GPU에서 인증서 사용 | Microsoft Docs
description: 을 사용 하는 이유, 장치에서 인증서를 업로드 하는 방법 등을 포함 하 여 Azure Stack Edge GPU 장치에서 인증서를 사용 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: bf7f9236c8f0835d0041b4b0c454a492330ef878
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268879"
---
# <a name="use-certificates-with-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 인증서 사용

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge 장치에 설치할 수 있는 인증서의 유형을 설명 합니다. 또한이 문서에는 만료 날짜를 설치 하 고 식별 하는 절차와 함께 각 인증서 종류에 대 한 세부 정보도 포함 되어 있습니다. 

## <a name="about-certificates"></a>인증서 정보

인증서는 신뢰할 수 있는 타사 (예: **인증 기관**)에 의해 **서명** (확인) 된 엔터티 (예: 도메인 이름)와 **공개 키** 간의 링크를 제공 합니다.  인증서는 신뢰할 수 있는 공용 암호화 키를 배포 하는 편리한 방법을 제공 합니다. 인증서를 통해 통신이 신뢰 되 고 암호화 된 정보를 올바른 서버로 전송 하 고 있는지 확인 합니다. 

Azure Stack Edge 장치가 처음으로 구성 되 면 자체 서명 된 인증서가 자동으로 생성 됩니다. 필요에 따라 사용자 고유의 인증서를 가져올 수 있습니다. 사용자 고유의 인증서를 가져올 계획인 경우 따라야 하는 지침이 있습니다.

## <a name="types-of-certificates"></a>인증서의 종류

Azure Stack Edge 장치에서 사용 되는 다양 한 유형의 인증서는 다음과 같습니다. 
- 서명 인증서
    - 루트 CA
    - 중급

- 끝점 인증서
    - 노드 인증서
    - 로컬 UI 인증서
    - 인증서 Azure Resource Manager
    - Blob storage 인증서
    - IoT 장치 인증서
    <!--- WiFi certificates
    - VPN certificates-->

- 암호화 인증서
    - 지원 세션 인증서

이러한 각 인증서에 대해서는 다음 섹션에서 자세히 설명 합니다.

## <a name="signing-chain-certificates"></a>체인 인증서 서명

인증서 또는 서명 인증 기관에 서명 하는 인증 기관에 대 한 인증서입니다. 

### <a name="types"></a>유형

이러한 인증서는 루트 인증서 또는 중간 인증서 일 수 있습니다. 루트 인증서는 항상 자체 서명 되거나 자체 서명 됩니다. 중간 인증서는 자체 서명 되지 않고 서명 기관에서 서명 됩니다.

### <a name="caveats"></a>제한 사항

- 루트 인증서는 서명 체인 인증서 여야 합니다.
- 루트 인증서는 다음과 같은 형식으로 장치에 업로드할 수 있습니다. 
    - **DER** – 파일 확장명으로 사용할 수 있습니다 `.cer` .
    - **Base-64 인코딩 또는 PEM** – 확장으로 사용할 수 있습니다 `.cer` .
    - **P7b** –이 형식은 루트 및 중간 인증서를 포함 하는 체인 인증서를 서명 하는 데만 사용 됩니다.
- 서명 체인 인증서는 다른 인증서를 업로드 하기 전에 항상 업로드 됩니다.


## <a name="node-certificates"></a>노드 인증서

<!--Your Azure Stack Edge device could be a 1-node device or a 4-node device.--> 장치의 모든 노드가 지속적으로 서로 통신 하므로 트러스트 관계가 있어야 합니다. 노드 인증서는 해당 신뢰를 설정 하는 방법을 제공 합니다. Https를 통한 원격 PowerShell 세션을 사용 하 여 장치 노드에 연결 하는 경우에도 노드 인증서가 재생 됩니다.

### <a name="caveats"></a>제한 사항

- 노드 인증서는 `.pfx` 내보낼 수 있는 개인 키를 사용 하 여 형식으로 제공 해야 합니다. 
- 하나의 와일드 카드 노드 인증서 또는 4 개의 개별 노드 인증서를 만들고 업로드할 수 있습니다. 
- DNS 도메인이 변경 되어도 장치 이름이 변경 되지 않으면 노드 인증서를 변경 해야 합니다. 사용자 고유의 노드 인증서를 가져오는 경우 장치 일련 번호를 변경할 수 없으며, 도메인 이름만 변경할 수 있습니다.
- 다음 표를 사용 하 여 노드 인증서를 만들 때 안내 합니다.
   
    |유형 |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |노드|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>끝점 인증서

장치에서 노출 하는 모든 끝점의 경우 신뢰할 수 있는 통신을 위해 인증서가 필요 합니다. 끝점 인증서에는 REST Api를 통해 Azure Resource Manager 및 blob 저장소에 액세스할 때 필요한 인증서가 포함 됩니다. 

자신의 서명 된 인증서를 가져오는 경우 인증서의 해당 서명 체인도 필요 합니다. 서명 체인, Azure Resource Manager 및 장치의 blob 인증서의 경우 클라이언트 컴퓨터에 해당 인증서를 사용 하 여 장치를 인증 하 고 통신 해야 합니다.

### <a name="caveats"></a>제한 사항

- 끝점 인증서는 `.pfx` 개인 키를 사용 하 여 형식 이어야 합니다. 서명 체인은 DER 형식 ( `.cer` 파일 확장명) 이어야 합니다. 
- 고유한 끝점 인증서를 가져올 때 개별 인증서 또는 다중 도메인 인증서 일 수 있습니다. 
- 서명 체인을 가져오는 경우 끝점 인증서를 업로드 하기 전에 서명 체인 인증서를 업로드 해야 합니다.
- 장치 이름 또는 DNS 도메인 이름이 변경 되 면 이러한 인증서를 변경 해야 합니다.
- 와일드 카드 끝점 인증서를 사용할 수 있습니다.
- 끝점 인증서의 속성은 일반적인 SSL 인증서의 속성과 유사 합니다. 
- 끝점 인증서를 만들 때 다음 표를 사용 합니다.

    |유형 |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure 리소스 관리자|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |두 끝점 모두에 대 한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>로컬 UI 인증서

브라우저를 통해 장치의 로컬 웹 UI에 액세스할 수 있습니다. 이 통신의 보안을 유지 하기 위해 고유한 인증서를 업로드할 수 있습니다. 

### <a name="caveats"></a>제한 사항

- 로컬 UI 인증서는 `.pfx` 내보낼 수 있는 개인 키를 사용 하 여 형식으로도 업로드 됩니다.
- 로컬 UI 인증서를 업로드 한 후 브라우저를 다시 시작 하 고 캐시를 지워야 합니다. 브라우저에 대 한 구체적인 지침을 참조 하세요.

    |유형 |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |로컬 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>장치 인증서 IoT Edge

Azure Stack Edge 장치는 연결 된 IoT Edge 장치에서 계산을 사용 하는 IoT 장치 이기도 합니다. 이 IoT Edge 장치와이 장치에 연결할 수 있는 다운스트림 장치 간의 보안 통신을 위해 IoT Edge 인증서를 업로드할 수도 있습니다. 

장치에는 장치에서 계산 시나리오를 사용 하려는 경우에 사용할 수 있는 자체 서명 된 인증서가 있습니다. 그러나 Azure Stack Edge 장치가 다운스트림 장치에 연결 된 경우에는 사용자 고유의 인증서를 가져와야 합니다.

이 트러스트 관계를 사용 하도록 설정 하려면 다음 세 가지 IoT Edge 인증서가 설치 되어 있어야 합니다.

- **루트 인증 기관 또는 소유자 인증 기관**
- **장치 인증 기관** 
- **장치 키 인증서**

### <a name="caveats"></a>제한 사항

- IoT Edge 인증서는 형식으로 업로드 됩니다 `.pem` . 


인증서 IoT Edge에 대 한 자세한 내용은 [Azure IoT Edge 인증서 정보](../iot-edge/iot-edge-certs.md#iot-edge-certificates)를 참조 하세요.

## <a name="support-session-certificates"></a>지원 세션 인증서

Azure Stack Edge 장치에 문제가 발생 하는 경우 해당 문제를 해결 하기 위해 장치에서 원격 PowerShell 지원 세션이 열릴 수 있습니다. 이 지원 세션을 통해 암호화 된 보안 통신을 사용 하도록 설정 하려면 인증서를 업로드할 수 있습니다.

### <a name="caveats"></a>제한 사항

- `.pfx`암호 해독 도구를 사용 하 여 개인 키가 있는 해당 인증서가 클라이언트 컴퓨터에 설치 되어 있는지 확인 합니다.
- 인증서의 **키 사용** 필드가 **인증서 서명이**아닌지 확인 합니다. 이를 확인 하려면 인증서를 마우스 오른쪽 단추로 클릭 하 고 **열기** 를 선택한 다음 **세부 정보** 탭에서 **키 사용**을 찾습니다. 


### <a name="caveats"></a>제한 사항

- 지원 세션 인증서는 확장을 사용 하 여 DER 형식으로 제공 해야 합니다 `.cer` .


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>인증서 만들기 (선택 사항)

다음 섹션에서는 서명 체인 및 끝점 인증서를 만드는 절차에 대해 설명 합니다.

### <a name="certificate-workflow"></a>인증서 워크플로

사용자 환경에서 작동 하는 장치에 대 한 인증서를 만드는 방법을 정의 합니다. IT 관리자가 제공 하는 인증서를 사용할 수 있습니다. 

**개발 또는 테스트 목적 으로만 Windows PowerShell을 사용 하 여 로컬 시스템에서 인증서를 만들 수도 있습니다.** 클라이언트에 대 한 인증서를 만드는 동안 다음 지침을 따르세요.

1. 다음 유형의 인증서를 만들 수 있습니다.

    - 단일 FQDN (정규화 된 도메인 이름)으로 사용할 유효한 단일 인증서를 만듭니다. 예를 들면 *mydomain.com*입니다.
    - 주 도메인 이름 및 여러 하위 도메인을 보호 하는 와일드 카드 인증서를 만듭니다. 예: **. mydomain.com*.
    - 단일 인증서에서 여러 도메인 이름을 포함 하는 SAN (주체 대체 이름) 인증서를 만듭니다. 

2. 사용자 고유의 인증서를 가져오는 경우 서명 체인의 루트 인증서가 필요 합니다. [서명 체인 인증서를 만드는](#create-signing-chain-certificate)단계를 참조 하세요.

3. 그런 다음 어플라이언스, blob 및 Azure Resource Manager의 로컬 UI에 대 한 끝점 인증서를 만들 수 있습니다. 어플라이언스, blob 및 Azure Resource Manager에 대해 3 개의 개별 인증서를 만들거나 3 개의 끝점 모두에 대해 인증서를 하나 만들 수 있습니다. 자세한 단계는 [서명 및 끝점 인증서 만들기](#create-signed-endpoint-certificates)를 참조 하세요.

4. 3 개의 개별 인증서를 만들지 아니면 인증서 하나를 만들지에 관계 없이 각 인증서 유형에 대해 제공 된 지침에 따라 주체 이름 (SN) 및 주체 대체 이름 (SAN)을 지정 합니다. 

### <a name="create-signing-chain-certificate"></a>서명 체인 인증서 만들기

관리자 모드에서 실행 되는 Windows PowerShell을 통해 이러한 인증서를 만듭니다. **이러한 방식으로 만든 인증서는 개발 또는 테스트 용도로만 사용 해야 합니다.**

서명 체인 인증서는 한 번만 만들어야 합니다. 다른 끝점 인증서는 서명을 위해이 인증서를 참조 합니다.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>서명 된 끝점 인증서 만들기

관리자 모드에서 실행 되는 Windows PowerShell을 통해 이러한 인증서를 만듭니다.

이러한 예제에서는 다음을 사용 하 여 장치에 대 한 끝점 인증서를 만듭니다.
    - 장치 이름: `DBE-HWDC1T2`
    - DNS 도메인: `microsoftdatabox.com`

를 장치에 대 한 인증서를 만들 장치에 대 한 이름 및 DNS 도메인으로 바꿉니다.
 
**Blob 끝점 인증서**

개인 저장소에서 Blob 끝점에 대 한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager 끝점 인증서**

개인 저장소의 Azure Resource Manager 끝점에 대 한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**장치 로컬 웹 UI 인증서**

개인 저장소에 있는 장치의 로컬 웹 UI에 대 한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**모든 끝점에 대 한 단일 다중 SAN 인증서**

개인 저장소의 모든 끝점에 대해 단일 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

인증서를 만든 후 다음 단계는 Azure Stack Edge 장치에서 인증서를 업로드 하는 것입니다.


## <a name="upload-certificates"></a>인증서 업로드 

기본적으로 장치에 대해 만든 인증서는 클라이언트의 **개인 저장소** 에 있습니다. 이러한 인증서는 클라이언트에서 적절 한 형식 파일로 내보내야 합니다. 그러면 장치에 업로드할 수 있습니다.

1. 루트 인증서는 확장을 사용 하 여 DER 형식으로 내보내야 합니다 `.cer` . 자세한 단계는 [DER 형식으로 인증서 내보내기](#export-certificates-as-der-format)를 참조 하세요.
2. 끝점 인증서는 개인 키를 사용 하 여 *.pfx* 파일로 내보내야 합니다. 자세한 단계는 [개인 키를 사용 하 여 인증서를 *.Pfx* 파일로 내보내기](#export-certificates-as-pfx-format-with-private-key)를 참조 하세요. 
3. 그런 다음 로컬 웹 UI의 인증서 페이지에서 **+ 인증서 추가** 옵션을 사용 하 여 장치에서 루트 및 끝점 인증서를 업로드 합니다. 

    1. 먼저 루트 인증서를 업로드 합니다. 로컬 웹 UI에서 **인증서 > + 인증서 추가**로 이동 합니다.

        ![서명 체인 인증서 추가](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. 그런 다음 끝점 인증서를 업로드 합니다. 

        ![서명 체인 인증서 추가](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        *.Pfx* 형식의 인증서 파일을 선택 하 고 인증서를 내보낼 때 입력 한 암호를 입력 합니다. Azure Resource Manager 인증서를 적용 하는 데 몇 분 정도 걸릴 수 있습니다.

        서명 체인이 먼저 업데이트 되지 않고 끝점 인증서를 업로드 하려고 하면 오류가 발생 합니다.

        ![인증서 적용 오류](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        뒤로 돌아가서 서명 체인 인증서를 업로드 한 다음, 끝점 인증서를 업로드 하 고 적용 합니다.

> [!IMPORTANT]
> 장치 이름 또는 DNS 도메인을 변경 하는 경우 새 인증서를 만들어야 합니다. 그런 다음 클라이언트 인증서와 장치 인증서를 새 장치 이름 및 DNS 도메인으로 업데이트 해야 합니다. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>클라이언트에서 장치에 액세스 하는 인증서 가져오기

장치에 만들고 업로드 한 인증서를 적절 한 인증서 저장소에 Windows 클라이언트 (장치에 액세스)에서 가져와야 합니다.

1. 이제 DER로 내보낸 루트 인증서를 클라이언트 시스템의 **신뢰할 수 있는 루트 인증 기관** 에서 가져와야 합니다. 자세한 단계는 [신뢰할 수 있는 루트 인증 기관 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조 하세요.

2. 로 내보낸 끝점 인증서는 `.pfx` 확장을 사용 하 여 DER로 내보내야 합니다 `.cer` . `.cer`그런 다음 시스템의 **개인 인증서 저장소** 에서 가져옵니다. 자세한 단계는 [개인 인증서 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조 하세요.

### <a name="import-certificates-as-der-format"></a>DER 형식으로 인증서 가져오기

Windows 클라이언트에서 인증서를 가져오려면 다음 단계를 수행 합니다.

1. 파일을 마우스 오른쪽 단추로 클릭 하 고 **인증서 설치**를 선택 합니다. 그러면 인증서 가져오기 마법사가 시작됩니다.

    ![인증서 가져오기 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. **저장소 위치**에 대해 **로컬 컴퓨터**를 선택 하 고 **다음**을 선택 합니다.

    ![인증서 가져오기 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. **모든 인증서를 다음 저장소에**저장을 선택한 다음 **찾아보기**를 선택 합니다. 

    - 개인 저장소로 가져오려면 원격 호스트의 개인 저장소로 이동 하 고 **다음**을 선택 합니다.

        ![인증서 가져오기 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - 신뢰할 수 있는 저장소로 가져오려면 신뢰할 수 있는 루트 인증 기관으로 이동한 후 **다음**을 선택 합니다.

        ![인증서 가져오기 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. **마침**을 선택합니다. 가져오기가 성공적으로 수행 되었다는 효과에 대 한 메시지가 표시 됩니다.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>개인 키를 사용 하 여 인증서를 .pfx 형식으로 내보내기

다음 단계를 수행 하 여 Windows 컴퓨터에서 개인 키가 있는 SSL 인증서를 내보냅니다. 

> [!IMPORTANT]
> 인증서를 만드는 데 사용한 것과 동일한 컴퓨터에서 다음 단계를 수행 합니다. 

1. 로컬 컴퓨터 인증서 저장소를 *시작 하려면 인증서* 저장소를 실행 합니다.

1. **개인** 폴더를 두 번 클릭 한 다음 **인증서**를 클릭 합니다.

    ![인증서 내보내기 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. 백업할 인증서를 마우스 오른쪽 단추로 클릭 하 고 **모든 작업 > 내보내기** ...를 선택 합니다.

    ![인증서 내보내기 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 인증서 내보내기 마법사의 지침에 따라 .pfx 파일에 인증서를 백업 합니다.

    ![인증서 내보내기 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. **예, 개인 키를 내보냅니다 .를**선택 합니다.

    ![인증서 내보내기 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. **가능 하면 인증서 경로에 모든 인증서 포함**을 선택 하 고 **모든 확장 속성을 내보내고** **인증서 개인 정보를 사용 하도록 설정**합니다. 

    > [!IMPORTANT]
    > **내보내기가 성공한 경우 개인 키 삭제 옵션**을 선택 하지 마십시오.

    ![인증서 내보내기 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. 기억할 암호를 입력 하세요. 암호를 확인합니다. 암호는 개인 키를 보호 합니다.

    ![인증서 내보내기 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 집합 위치에 파일을 저장 하도록 선택 합니다.

    ![인증서 내보내기 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. **마침**을 선택합니다.

    ![인증서 내보내기 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. 내보내기가 성공적으로 완료 되었다는 메시지가 표시 됩니다. **확인**을 선택합니다.

    ![인증서 내보내기 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

.Pfx 파일 백업은 이제 선택한 위치에 저장 되며 안전 하 게 유지 하기 위해 이동 하거나 저장할 준비가 됩니다.


### <a name="export-certificates-as-der-format"></a>인증서를 DER 형식으로 내보내기

1. 로컬 컴퓨터 인증서 저장소를 *시작 하려면 인증서* 저장소를 실행 합니다.

1. 개인 인증서 저장소에서 루트 인증서를 선택 합니다. 마우스 오른쪽 단추를 클릭 하 고 **모든 작업 > 내보내기 ...** 를 선택 합니다.

    ![인증서 내보내기 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 인증서 마법사가 열립니다. **DER로 인코딩된 이진 x.509 (.cer)** 형식을 선택 합니다. **다음**을 선택합니다.

    ![인증서 내보내기 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. .Cer 형식 파일을 내보낼 위치를 찾아 선택 합니다.

    ![인증서 내보내기 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)


4. **마침**을 선택합니다.

    ![인증서 내보내기 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>지원 되는 인증서 알고리즘

 Azure Stack Edge 장치에서는 RSA (Rivest – Rivest-shamir-adleman – Rivest-shamir-adleman) 인증서만 지원 됩니다. ECDSA (타원 Curve Digital Signature Algorithm) 인증서를 사용 하는 경우 장치 동작은 결정 되지 않습니다.

 RSA 공개 키를 포함 하는 인증서를 RSA 인증서 라고 합니다. ECC (타원 Curve 암호화) 공개 키가 포함 된 인증서를 ECDSA (타원 Curve Digital Signature Algorithm) 인증서 라고 합니다. 


## <a name="view-certificate-expiry"></a>인증서 만료 보기

사용자 고유의 인증서를 가져오는 경우 인증서는 일반적으로 1 년 또는 6 개월 이내에 만료 됩니다. 인증서의 만료 날짜를 보려면 장치의 로컬 웹 UI에 있는 **인증서** 페이지로 이동 합니다. 특정 인증서를 선택 하는 경우 인증서의 만료 날짜를 확인할 수 있습니다.

## <a name="rotate-certificates"></a>인증서 회전

이 릴리스에서는 인증서 회전이 구현 되어 있지 않습니다. 또한 인증서에 대 한 보류 중인 만료 날짜에 대 한 알림이 표시 되지 않습니다. 

장치의 로컬 웹 UI에 있는 인증서 **페이지에서 인증서 만료** 날짜를 확인 합니다. 인증서 만료가 임박 하면 [인증서 만들기 및 업로드](azure-stack-edge-j-series-manage-certificates.md)의 자세한 지침에 따라 새 인증서를 만들고 업로드 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치 배포](azure-stack-edge-gpu-deploy-prep.md)
