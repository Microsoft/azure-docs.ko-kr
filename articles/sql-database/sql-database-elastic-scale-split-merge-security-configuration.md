---
title: 분할-병합 보안 구성
description: 탄력적 크기 조정을 위해 분할/병합 서비스를 통해 암호화에 대해 409 인증서를 설정합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a916645f153f73a98e7fc5d4046bdf557e8acf2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823524"
---
# <a name="split-merge-security-configuration"></a>분할-병합 보안 구성

분할/병합 서비스를 사용하려면 보안을 올바르게 구성해야 합니다. 서비스는 Microsoft Azure SQL Database의 탄력적인 확장 기능에 속합니다. 자세한 내용은 [탄력적인 확장 분할 및 병합 서비스 자습서](sql-database-elastic-scale-configure-deploy-split-and-merge.md)를 참조하세요.

## <a name="configuring-certificates"></a>인증서 구성

인증서는 두 가지 방법으로 구성합니다. 

1. [SSL 인증서를 구성하려면](#to-configure-the-ssl-certificate)
2. [클라이언트 인증서를 구성하려면](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>인증서를 얻으려면

공용 CA(인증 기관) 또는 [Windows 인증서 서비스](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx)에서 인증서를 얻을 수 있습니다. 인증서를 가져올 때 이러한 방법이 일반적으로 사용됩니다.

이러한 옵션을 사용할 수 없는 경우 **자체 서명된 인증서**를 생성할 수 있습니다.

## <a name="tools-to-generate-certificates"></a>인증서를 생성하는 도구

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>도구를 실행하려면

* Visual Studio용 개발자 명령 프롬프트에서 [Visual Studio 명령 프롬프트를 참조하세요](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    설치되어 있는 경우 다음으로 이동합니다.
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* WDK 가져오기 [Windows 8.1: 키트 및 도구 다운로드](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>SSL 인증서를 구성하려면

통신을 암호화하고 서버를 인증하려면 SSL 인증서가 필요합니다. 아래 세 가지 시나리오 중 가장 적합한 시나리오를 선택하고 모든 단계를 실행합니다.

### <a name="create-a-new-self-signed-certificate"></a>자체 서명된 새로운 인증서 만들기

1. [자체 서명된 인증서 만들기](#create-a-self-signed-certificate)
2. [자체 서명된 SSL 인증서용 PFX 파일 만들기](#create-pfx-file-for-self-signed-ssl-certificate)
3. [클라우드 서비스에 SSL 인증서 업로드](#upload-ssl-certificate-to-cloud-service)
4. [서비스 구성 파일에서 SSL 인증서 업데이트](#update-ssl-certificate-in-service-configuration-file)
5. [SSL 인증 기관 가져오기](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>인증서 저장소에서 기존 인증서를 사용하려면
1. [인증서 저장소에서 SSL 인증서 내보내기](#export-ssl-certificate-from-certificate-store)
2. [클라우드 서비스에 SSL 인증서 업로드](#upload-ssl-certificate-to-cloud-service)
3. [서비스 구성 파일에서 SSL 인증서 업데이트](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>PFX 파일에서 기존 인증서를 사용하려면
1. [클라우드 서비스에 SSL 인증서 업로드](#upload-ssl-certificate-to-cloud-service)
2. [서비스 구성 파일에서 SSL 인증서 업데이트](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>클라이언트 인증서를 구성하려면
클라이언트 인증서는 서비스에 요청을 인증하는 데 필요합니다. 아래 세 가지 시나리오 중 가장 적합한 시나리오를 선택하고 모든 단계를 실행합니다.

### <a name="turn-off-client-certificates"></a>클라이언트 인증서 해제
1. [클라이언트 인증서 기반 인증 해제](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>자체 서명된 새로운 클라이언트 인증서 발급
1. [자체 서명된 인증 기관 만들기](#create-a-self-signed-certification-authority)
2. [클라우드 서비스에 CA 인증서 업로드](#upload-ca-certificate-to-cloud-service)
3. [서비스 구성 파일의 CA 인증서 업데이트](#update-ca-certificate-in-service-configuration-file)
4. [클라이언트 인증서 발급](#issue-client-certificates)
5. [클라이언트 인증서용 PFX 파일 만들기](#create-pfx-files-for-client-certificates)
6. [클라이언트 인증서 가져오기](#import-client-certificate)
7. [클라이언트 인증서 지문 복사](#copy-client-certificate-thumbprints)
8. [서비스 구성 파일에서 허용된 클라이언트 구성](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>기존 클라이언트 인증서 사용
1. [Find CA Public Key](#find-ca-public-key)
2. [클라우드 서비스에 CA 인증서 업로드](#upload-ca-certificate-to-cloud-service)
3. [서비스 구성 파일의 CA 인증서 업데이트](#update-ca-certificate-in-service-configuration-file)
4. [클라이언트 인증서 지문 복사](#copy-client-certificate-thumbprints)
5. [서비스 구성 파일에서 허용된 클라이언트 구성](#configure-allowed-clients-in-the-service-configuration-file)
6. [클라이언트 인증서 해지 확인 구성](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>허용된 IP 주소
특정 범위의 IP 주소에서만 서비스 엔드포인트에 액세스하도록 제한할 수 있습니다.

## <a name="to-configure-encryption-for-the-store"></a>저장소에 대한 암호화를 구성하려면
메타데이터 저장소에 저장된 자격 증명을 암호화하려면 인증서가 필요합니다. 아래 세 가지 시나리오 중 가장 적합한 시나리오를 선택하고 모든 단계를 실행합니다.

### <a name="use-a-new-self-signed-certificate"></a>자체 서명된 새로운 인증서 사용
1. [자체 서명된 인증서 만들기](#create-a-self-signed-certificate)
2. [자체 서명된 암호화 인증서용 PFX 파일 만들기](#create-pfx-file-for-self-signed-ssl-certificate)
3. [클라우드 서비스에 암호화 인증서 업로드](#upload-encryption-certificate-to-cloud-service)
4. [서비스 구성 파일에서 암호화 인증서 업데이트](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>인증서 저장소에서 기존 인증서 사용
1. [인증서 저장소에서 암호화 인증서 내보내기](#export-encryption-certificate-from-certificate-store)
2. [클라우드 서비스에 암호화 인증서 업로드](#upload-encryption-certificate-to-cloud-service)
3. [서비스 구성 파일에서 암호화 인증서 업데이트](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>PFX 파일에서 기존 인증서 사용
1. [클라우드 서비스에 암호화 인증서 업로드](#upload-encryption-certificate-to-cloud-service)
2. [서비스 구성 파일에서 암호화 인증서 업데이트](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>기본 구성
기본 구성에서는 HTTP 엔드포인트에 대한 모든 액세스를 거부합니다. 이러한 엔드포인트에 대한 요청에서는 데이터베이스 자격 증명과 같은 중요한 정보가 전송될 수 있으므로 이 설정을 사용하는 것이 좋습니다.
기본 구성에서는 HTTPS 엔드포인트에 대한 모든 액세스가 허용됩니다. 이 설정을 추가로 제한할 수 있습니다.

### <a name="changing-the-configuration"></a>구성 변경
및 엔드포인트에 적용 되는 액세스 제어 규칙 그룹은 **서비스 구성 파일**의 **\<endpointacls >** 섹션에서 구성 됩니다.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

액세스 제어 그룹의 규칙은 서비스 구성 파일의 \<Accesscontrol-namespace name = "" > 섹션에서 구성 됩니다. 

해당 형식에 대한 설명은 네트워크 Access Control 목록 설명서에 나와 있습니다.
예를 들어 100.100.0.0~100.100.255.255 범위의 IP만 HTTPS 엔드포인트에 액세스하도록 허용하려는 경우의 규칙은 다음과 같습니다.

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>서비스 거부 방지
서비스 거부 공격을 검색 및 방지할 수 있도록 지원하는 메커니즘이 두 가지 있습니다.

* 원격 호스트당 동시 요청 수 제한(기본적으로 해제됨)
* 원격 호스트당 액세스 속도 제한(기본적으로 설정됨)

이러한 메커니즘은 IIS의 동적 IP 보안에 자세히 설명되어 있는 기능을 기반으로 합니다. 이 구성을 변경할 때 다음과 같은 요인에 주의하세요.

* 프록시 및 원격 호스트 정보를 통한 Network Address Translation 디바이스의 동작
* 웹 역할의 모든 리소스에 대한 각 요청(예: 스크립트, 이미지 등 로드) 이 고려됨

## <a name="restricting-number-of-concurrent-accesses"></a>동시 액세스 수 제한
이 동작을 구성하는 설정은 다음과 같습니다.

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

이 보호를 사용하도록 설정하려면 DynamicIpRestrictionDenyByConcurrentRequests를 true로 변경합니다.

## <a name="restricting-rate-of-access"></a>액세스 속도 제한
이 동작을 구성하는 설정은 다음과 같습니다.

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>거부된 요청에 대한 응답 구성
다음 설정은 거부된 요청에 대한 응답을 구성합니다.

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

기타 지원되는 값에 대해서는 IIS의 동적 IP 보안에 대한 설명서를 참조하세요.

## <a name="operations-for-configuring-service-certificates"></a>서비스 인증서 구성 작업
이 항목은 참조용일 뿐입니다. 아래 항목에 나와 있는 구성 단계를 수행하세요.

* SSL 인증서 구성
* 클라이언트 인증서 구성

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기
다음 코드를 실행합니다.

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

사용자 지정하려면:

* -n을 서비스 URL로 바꿉니다. 와일드카드("CN=*.cloudapp.net") 및 대체 이름("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net")이 지원됩니다.
* -e 및 인증서 만료 날짜                 강력한 암호를 만들고 메시지가 표시되면 해당 암호를 지정합니다.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>자체 서명된 SSL 인증서용 PFX 파일 만들기
다음 코드를 실행합니다.

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

암호를 입력하고 다음 옵션을 사용하여 인증서를 내보냅니다.

* 예, 프라이빗 키를 내보냅니다.
* 확장된 속성 모두 내보내기

## <a name="export-ssl-certificate-from-certificate-store"></a>인증서 저장소에서 SSL 인증서 내보내기
* 인증서를 찾습니다.
* 작업-> 모든 작업 -> 내보내기를 클릭합니다.
* 다음 옵션을 사용하여 .PFX 파일로 인증서를 내보냅니다.
  * 예, 프라이빗 키를 내보냅니다.
  * 가능하면 인증 경로에 있는 인증서 모두 포함 *확장된 속성 모두 내보내기

## <a name="upload-ssl-certificate-to-cloud-service"></a>클라우드 서비스에 SSL 인증서 업로드
SSL 키 쌍이 포함된 기존 또는 생성된 .PFX 파일을 업로드합니다.

* 프라이빗 키 정보를 보호하는 암호를 입력합니다.

## <a name="update-ssl-certificate-in-service-configuration-file"></a>서비스 구성 파일에서 SSL 인증서 업데이트
클라우드 서비스에 업로드된 인증서의 지문으로 서비스 구성 파일의 다음 설정에 대한 지문 값을 업데이트합니다.

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>SSL 인증 기관 가져오기
서비스와 통신 하는 모든 계정/컴퓨터에서 다음 단계를 수행합니다.

* Windows 탐색기에서 CER 파일을 두 번 클릭합니다.
* 인증서 대화 상자에서 인증서 설치를 클릭합니다.
* 신뢰할 수 있는 루트 인증 기관 저장소로 인증서를 가져옵니다.

## <a name="turn-off-client-certificate-based-authentication"></a>클라이언트 인증서 기반 인증 해제
클라이언트 인증서 기반 인증만 지원되며, 이를 사용하지 않도록 설정하면 다른 메커니즘(예: Microsoft Azure Virtual Network)이 없는 한 서비스 엔드포인트에 대한 공용 액세스가 허용됩니다.

서비스 구성 파일에서 이러한 설정을 false로 변경하여 기능을 해제합니다.

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

그런 다음 CA 인증서 설정의 SSL 인증서와 동일한 지문을 복사합니다.

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>자체 서명된 인증 기관 만들기
다음 단계를 실행하여 인증 기관 역할을 할 자체 서명된 인증서를 만듭니다.

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

이를 사용자 지정하려면

* -e 및 인증 만료 날짜

## <a name="find-ca-public-key"></a>CA 공개 키 찾기
모든 클라이언트 인증서는 서비스에서 신뢰하는 인증 기관에서 발급해야 합니다. 클라우드 서비스에 업로드할 수 있도록 인증에 사용할 클라이언트 인증서를 발급한 인증 기관의 공용 키를 찾습니다.

공용 키가 포함된 파일을 사용할 수 없는 경우 인증서 저장소에서 이 파일을 내보냅니다.

* 인증서를 찾습니다.
  * 동일한 인증 기관에서 발급한 클라이언트 인증서를 검색합니다.
* 인증서를 두 번 클릭합니다.
* 인증서 대화 상자에서 인증 경로 탭을 선택합니다.
* 경로의 CA 항목을 두 번 클릭합니다.
* 인증서 속성을 기록합니다.
* **인증서** 대화 상자를 닫습니다.
* 인증서를 찾습니다.
  * 위에서 기록한 CA를 검색합니다.
* 작업-> 모든 작업 -> 내보내기를 클릭합니다.
* 다음 옵션을 사용하여 .CER 파일로 인증서를 내보냅니다.
  * **아니요, 프라이빗 키를 내보내지 않습니다.**
  * 가능하면 인증 경로에 있는 인증서 모두 포함
  * 확장된 속성 모두 내보내기

## <a name="upload-ca-certificate-to-cloud-service"></a>클라우드 서비스에 CA 인증서 업로드
CA 공개 키가 포함된 기존 또는 생성된 .CER 파일과 함께 인증서를 업로드합니다.

## <a name="update-ca-certificate-in-service-configuration-file"></a>서비스 구성 파일의 CA 인증서 업데이트
클라우드 서비스에 업로드된 인증서의 지문으로 서비스 구성 파일의 다음 설정에 대한 지문 값을 업데이트합니다.

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

동일한 지문으로 다음 설정의 값을 업데이트합니다.

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>클라이언트 인증서 발급
서비스에 액세스할 수 있는 권한이 부여된 각 개인은 단독 사용을 위해 발급된 클라이언트 인증서가 있어야 하며 자신의 프라이빗 키를 보호하기 위해 강력한 암호를 선택해야 합니다. 

다음 단계는 자체 서명된 CA 인증서를 생성하고 저장한 동일한 컴퓨터에서 실행해야 합니다.

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

사용자 지정:

* -n 및 이 인증서로 인증할 클라이언트의 ID
* -e 및 인증서 만료 날짜
* MyID.pvk 및 MyID.cer과 이 클라이언트 인증서의 고유한 파일 이름

이 명령은 암호를 만들어 한 번 사용하라는 메시지를 표시합니다. 강력한 암호를 사용하세요.

## <a name="create-pfx-files-for-client-certificates"></a>클라이언트 인증서용 PFX 파일 만들기
생성된 각 클라이언트 인증서에 대해 다음을 실행합니다.

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

사용자 지정:

    MyID.pvk and MyID.cer with the filename for the client certificate

암호를 입력하고 다음 옵션을 사용하여 인증서를 내보냅니다.

* 예, 프라이빗 키를 내보냅니다.
* 확장된 속성 모두 내보내기
* 이 인증서를 발급하는 개인이 내보내기 암호를 선택해야 합니다.

## <a name="import-client-certificate"></a>클라이언트 인증서 가져오기
클라이언트 인증서가 발급된 개별 사용자는 서비스와 통신하는 데 사용할 머신에서 키 쌍을 가져와야 합니다.

* Windows 탐색기에서 .PFX 파일을 두 번 클릭합니다.
* 적어도 다음 옵션을 사용하여 개인 저장소에 인증서를 가져옵니다.
  * 확장된 속성 모두 포함 옵션 선택

## <a name="copy-client-certificate-thumbprints"></a>클라이언트 인증서 지문 복사
클라이언트 인증서가 발급된 각 개인이 서비스 구성 파일에 추가될 인증서의 지문을 가져오려면 다음 단계를 따라야 합니다.

* Certmgr.exe 실행
* 개인 탭 선택
* 인증에 사용할 클라이언트 인증서를 두 번 클릭합니다.
* 표시되는 인증서 대화 상자에서 세부 정보 탭을 선택합니다.
* 표시가 모두를 나타내는지 확인합니다.
* 목록에서 지문이라는 필드를 선택합니다.
* 지문 값을 복사 합니다.
  * 첫 번째 숫자 앞에 표시 되지 않는 유니코드 문자를 삭제 합니다.
  * 모든 공백 삭제

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>서비스 구성 파일에서 허용된 클라이언트 구성
서비스 구성 파일에서 다음 설정의 값을 서비스에 대한 액세스가 허용된 클라이언트 인증서의 지문 목록(쉼표로 구분)으로 업데이트합니다.

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>클라이언트 인증서 해지 확인 구성
기본 설정은 인증 기관으로 클라이언트 인증서 해지 상태를 확인하지 않습니다. 확인을 설정하려면 클라이언트 인증서를 발급한 인증 기관에서 이러한 확인을 지원하는 경우 X509RevocationMode 열거에 정의된 값 중 하나로 다음 설정을 변경합니다.

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>자체 서명된 암호화 인증서용 PFX 파일 만들기
암호화 인증서에 대해 다음을 실행합니다.

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

사용자 지정:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

암호를 입력하고 다음 옵션을 사용하여 인증서를 내보냅니다.

* 예, 프라이빗 키를 내보냅니다.
* 확장된 속성 모두 내보내기
* 클라우드 서비스에 인증서를 업로드할 때 암호가 필요합니다.

## <a name="export-encryption-certificate-from-certificate-store"></a>인증서 저장소에서 암호화 인증서 내보내기
* 인증서를 찾습니다.
* 작업-> 모든 작업 -> 내보내기를 클릭합니다.
* 다음 옵션을 사용하여 .PFX 파일로 인증서를 내보냅니다. 
  * 예, 프라이빗 키를 내보냅니다.
  * 가능하면 인증 경로에 있는 인증서 모두 포함 
* 확장된 속성 모두 내보내기

## <a name="upload-encryption-certificate-to-cloud-service"></a>클라우드 서비스에 암호화 인증서 업로드
암호화 키 쌍이 포함된 기존 또는 생성된 .PFX 파일을 업로드합니다.

* 프라이빗 키 정보를 보호하는 암호를 입력합니다.

## <a name="update-encryption-certificate-in-service-configuration-file"></a>서비스 구성 파일에서 암호화 인증서 업데이트
클라우드 서비스에 업로드된 인증서의 지문으로 서비스 구성 파일의 다음 설정에 대한 지문 값을 업데이트합니다.

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>일반 인증서 작업
* SSL 인증서 구성
* 클라이언트 인증서 구성

## <a name="find-certificate"></a>인증서를 찾습니다.
다음 단계를 수행하세요.

1. Mmc.exe를 실행합니다.
2. 파일-> 스냅인 추가/제거로 이동합니다.
3. **인증서**를 선택합니다.
4. **추가**를 클릭합니다.
5. 인증서 저장소 위치를 선택합니다.
6. **Finish**를 클릭합니다.
7. **확인**을 클릭합니다.
8. **인증서**를 확장합니다.
9. 인증서 저장소 노드를 확장합니다.
10. 인증서 하위 노드를 확장합니다.
11. 목록에서 인증서를 선택합니다.

## <a name="export-certificate"></a>인증서 내보내기
**인증서 내보내기 마법사**에서 다음을 수행합니다.

1. **다음**을 누릅니다.
2. **예**를 선택한 다음, **프라이빗 키 내보내기**를 선택합니다.
3. **다음**을 누릅니다.
4. 원하는 출력 파일 형식을 선택합니다.
5. 원하는 옵션을 선택합니다.
6. **암호**를 확인합니다.
7. 강력한 암호를 입력하고 이를 확인합니다.
8. **다음**을 누릅니다.
9. 인증서를 저장할 파일 이름을 입력하거나 찾습니다(.PFX 확장명을 사용하여).
10. **다음**을 누릅니다.
11. **Finish**를 클릭합니다.
12. **확인**을 클릭합니다.

## <a name="import-certificate"></a>인증서 가져오기
인증서 가져오기 마법사에서:

1. 저장소 위치를 선택합니다.
   
   * 현재 사용자 계정으로 실행되는 프로세스만 서비스에 액세스하는 경우 **현재 사용자** 를 선택합니다.
   * 컴퓨터의 다른 프로세스에서 서비스에 액세스하는 경우 **로컬 컴퓨터** 를 선택합니다.
2. **다음**을 누릅니다.
3. 파일에서 가져오는 경우 파일 경로를 확인합니다.
4. .PFX 파일을 가져오는 경우:
   1. 프라이빗 키를 보호하는 암호를 입력합니다.
   2. 가져오기 옵션을 선택합니다.
5. 다음 저장소에 인증서 저장을 선택합니다.
6. **찾아보기**를 클릭합니다.
7. 원하는 저장소를 선택합니다.
8. **Finish**를 클릭합니다.
   
   * 신뢰할 수 있는 루트 인증 기관 저장소를 선택한 경우 **예**를 클릭합니다.
9. 모든 대화 상자 창에서 **확인** 을 클릭합니다.

## <a name="upload-certificate"></a>인증서 업로드
[Azure Portal](https://portal.azure.com/)에서

1. **Cloud Services**를 선택합니다.
2. 클라우드 서비스를 선택합니다.
3. 최상위 메뉴에서 **인증서**를 클릭합니다.
4. 아래쪽 메뉴 모음에서 **업로드**를 클릭합니다.
5. 인증서 파일을 선택합니다.
6. .PFX 파일인 경우 프라이빗 키에 대한 암호를 입력합니다.
7. 완료되면 목록의 새 항목에서 인증서 지문을 복사합니다.

## <a name="other-security-considerations"></a>기타 보안 고려 사항
이 문서에 설명된 SSL 설정은 HTTPS 엔드포인트를 사용하는 경우 해당 클라이언트와 서비스 간의 통신을 암호화합니다. 데이터베이스 액세스에 대한 자격 증명 및 기타 잠재적으로 중요한 정보가 통신에 포함되므로 이러한 암호화가 중요합니다. 단, 서비스에서 Microsoft Azure 구독의 메타데이터 스토리지에 대해 제공한 Microsoft Azure SQL 데이터베이스의 내부 테이블에 있는 자격 증명을 비롯하여 내부 상태를 유지합니다. 해당 데이터베이스는 서비스 구성 파일(.CSCFG 파일)에서 다음 설정의 일부로 정의됩니다. 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

이 데이터베이스에 저장된 자격 증명은 암호화됩니다. 그러나 서비스 배포의 웹 역할과 작업자 역할 모두 최신 상태를 유지하고 저장된 자격 증명의 암호화 및 암호 해독에 사용되는 인증서와 메타데이터 데이터베이스에 액세스할 때 보안을 유지해야 합니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

