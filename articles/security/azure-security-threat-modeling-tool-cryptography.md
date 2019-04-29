---
title: 암호화 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 608792d8389a87bad3521d3a48947b20dd036d67
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121217"
---
# <a name="security-frame-cryptography--mitigations"></a>보안 프레임: 암호화 | 완화 

| 제품/서비스 | 문서 |
| --------------- | ------- |
| **웹 애플리케이션** | <ul><li>[승인된 대칭 블록 암호화 및 키 길이만 사용](#cipher-length)</li><li>[대칭 암호화에 승인된 블록 암호화 모드 및 초기화 벡터 사용](#vector-ciphers)</li><li>[승인된 비대칭 알고리즘, 키 길이 및 패딩 사용](#padding)</li><li>[승인된 난수 생성기 사용](#numgen)</li><li>[대칭 스트림 암호화 사용 금지](#stream-ciphers)</li><li>[승인된 MAC/HMAC/키 해시 알고리즘 사용](#mac-hash)</li><li>[승인된 암호화 해시 함수만 사용](#hash-functions)</li></ul> |
| **데이터베이스** | <ul><li>[강력한 암호화 알고리즘을 사용하여 데이터베이스 데이터 암호화](#strong-db)</li><li>[암호화되고 디지털 서명되어야 하는 SSIS 패키지](#ssis-signed)</li><li>[중요한 데이터베이스 보안 개체에 디지털 서명 추가](#securables-db)</li><li>[SQL 서버 EKM을 사용하여 암호화 키 보호](#ekm-keys)</li><li>[데이터베이스 엔진에 암호화 키를 공개하지 않아야 하는 경우 AlwaysEncrypted 기능 사용](#keys-engine)</li></ul> |
| **IoT 디바이스** | <ul><li>[IoT 디바이스에 안전하게 암호화 키 저장](#keys-iot)</li></ul> | 
| **IoT 클라우드 게이트웨이** | <ul><li>[IoT Hub 인증에 충분한 길이의 임의 대칭 키 생성](#random-hub)</li></ul> | 
| **Dynamics CRM 모바일 클라이언트** | <ul><li>[PIN 사용이 필요하고 원격 지우기를 허용하는 디바이스 관리 정책이 있는지 확인](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook 클라이언트** | <ul><li>[PIN/암호/자동 잠금이 필요하고 모든 데이터를 암호화(예: BitLocker)하는 디바이스 관리 정책이 있는지 확인합니다.](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Identity Server를 사용할 때 서명 키가 롤오버되는지 확인](#rolled-server)</li><li>[Identity Server에서 암호화된 강력한 클라이언트 ID와 클라이언트 비밀이 사용되는지 확인](#client-server)</li></ul> | 

## <a id="cipher-length"></a>승인된 대칭 블록 암호화 및 키 길이만 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>제품에는 조직의 Crypto Advisor에서 명시적으로 승인한 대칭 블록 암호화 및 관련 키 길이만 사용해야 합니다. 승인된 Microsoft 대칭 알고리즘에는 다음과 같은 블록 암호화가 포함됩니다.</p><ul><li>새로운 코드의 경우 AES-128, AES-192, AES-256이 허용됩니다.</li><li>기존 코드를 사용하는 이전 버전과의 호환성을 위해 3개 키를 사용하는 3DES가 허용됩니다.</li><li>대칭 블록 암호화를 사용하는 제품의 경우:<ul><li>새로운 코드에는 AES(Advanced Encryption Standard)가 필요합니다.</li><li>이전 버전과의 호환성을 위해 기존 코드에 3DES(3키 3중 Data Encryption Standard)가 허용됩니다.</li><li>RC2, DES, 2키 3DES, DESX 및 Skipjack을 포함한 다른 모든 블록 암호화는 이전 데이터를 해독하는 데만 사용할 수 있으며, 암호화에 사용되는 경우 교체해야 합니다.</li></ul></li><li>대칭 블록 암호화 알고리즘의 경우 최소 키 길이는 128비트입니다. 새 코드에 권장되는 유일한 블록 암호화 알고리즘은 AES입니다(AES-128, AES-192 및 AES-256 모두 허용됨).</li><li>현재 3키 3DES는 기존 코드에서 이미 사용 중인 경우 허용되지만, AES로 전환하는 것이 좋습니다. DES, DESX, RC2 및 Skipjack은 더 이상 안전한 것으로 간주되지 않습니다. 이러한 알고리즘은 이전 버전과의 호환성을 위해 기존 데이터를 해독하는 데만 사용할 수 있으며, 권장되는 블록 암호화를 사용하여 데이터를 다시 암호화해야 합니다</li></ul><p>모든 대칭 블록 암호화는 적절한 IV(초기화 벡터)를 사용해야 하는 승인된 암호화 모드와 함께 사용해야 합니다. 적절한 IV는 일반적으로 난수이며, 절대로 상수 값이 아닙니다.</p><p>조직의 Crypto Board에서 검토한 후에 기존 암호화 알고리즘 또는 기타 승인되지 않은 암호화 알고리즘 및 기존 데이터(새로 작성된 데이터와 반대)를 읽는 데 필요한 더 작은 키 길이를 사용하도록 허용될 수 있습니다. 그러나 이 요구 사항에 대한 예외를 제출해야 합니다. 또한 엔터프라이즈 배포에서는 약한 암호화를 사용하여 데이터를 읽는 경우 제품에서 관리자에게 경고하는 것도 고려해야 합니다. 이러한 경고는 설명적이고 실행 가능해야 합니다. 어떤 경우에는 그룹 정책으로 약한 암호화 사용을 제어하도록 하는 것이 적절할 수 있습니다.</p><p>관리되는 암호화 민첩성을 위해 허용되는 .NET 알고리즘(기본 설정 순서대로)</p><ul><li>AesCng(FIPS 규격)</li><li>AuthenticatedAesCng(FIPS 규격)</li><li>AESCryptoServiceProvider(FIPS 규격)</li><li>AESManaged(비 FIPS 규격)</li></ul><p>이러한 알고리즘은 Machine.config 파일을 변경해야 `SymmetricAlgorithm.Create` 또는 `CryptoConfig.CreateFromName` 메서드를 통해 지정할 수 있습니다. 또한 .NET 3.5 이전의 .NET 버전에서 AES는 `RijndaelManaged`로 명명되었고, `AesCng`와 `AuthenticatedAesCng`는 CodePlex를 통해 사용할 수 있으며, 기본 OS에 CNG가 필요합니다.</p>

## <a id="vector-ciphers"></a>대칭 암호화에 승인된 블록 암호화 모드 및 초기화 벡터 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 모든 대칭 블록 암호화는 승인된 대칭 암호화 모드와 함께 사용해야 합니다. 유일하게 승인된 모드는 CBC 및 CTS입니다. 특히 ECB(Electronic Code Book) 작동 모드는 피해야 합니다. ECB를 사용하려면 조직의 Crypto Board에서 검토해야 합니다. OFB, CFB, CTR, CCM 및 GCM 또는 다른 암호화 모드의 모든 사용은 조직의 Crypto Board에서 검토해야 합니다. 동일한 IV(초기화 벡터)를 "스트리밍 암호화 모드"의 블록 암호화(예: CTR)로 다시 사용하면 암호화된 데이터가 노출될 수 있습니다. 또한 모든 대칭 블록 암호화도 적절한 IV와 함께 사용해야 합니다. 적절한 IV는 암호화된 강력한 난수이며, 절대로 상수 값이 아닙니다. |

## <a id="padding"></a>승인된 비대칭 알고리즘, 키 길이 및 패딩 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>금지된 암호화 알고리즘의 사용은 제품 보안에 상당한 위험을 초래하므로 피해야 합니다. 제품에서는 조직의 Crypto Board에서 명시적으로 승인한 암호화 알고리즘, 관련 키 길이 및 패딩만 사용해야 합니다.</p><ul><li>**RSA -** 암호화, 키 교환 및 서명에 사용할 수 있습니다. RSA 암호화는 OAEP 또는 RSA-KEM 패딩 모드만 사용해야 합니다. 기존 코드는 호환성을 위해서만 PKCS #1 v1.5 패딩 모드를 사용할 수 있습니다. 널 패딩 사용은 명시적으로 금지됩니다. 새 코드에는 2,048비트 이상의 키가 필요합니다. 기존 코드는 조직의 Crypto Board에서 검토한 후 이전 버전과의 호환성을 위해서만 2,048비트 미만의 키를 지원할 수 있습니다. 1,024비트 미만의 키는 이전 데이터의 암호 해독/확인에만 사용할 수 있으며, 암호화 또는 서명 작업에 사용되는 경우 교체해야 합니다.</li><li>**ECDSA -** 서명에만 사용할 수 있습니다. 새 코드에는 256비트 이상의 키가 있는 ECDSA가 필요합니다. ECDSA 기반 서명은 NIST에서 승인한 세 가지 곡선(P-256, P-384 또는 P521) 중 하나를 사용해야 합니다. 철저히 분석된 곡선은 조직의 Crypto Board에서 검토한 후에만 사용할 수 있습니다.</li><li>**ECDH -** 키 교환에만 사용할 수 있습니다. 새 코드에는 256비트 이상의 키가 있는 ECDH가 필요합니다. ECDH 기반 키 교환은 NIST에서 승인한 세 가지 곡선(P-256, P-384 또는 P521) 중 하나를 사용해야 합니다. 철저히 분석된 곡선은 조직의 Crypto Board에서 검토한 후에만 사용할 수 있습니다.</li><li>**DSA -** 조직의 Crypto Board에서 검토하고 승인한 후에 허용될 수 있습니다. 보안 관리자에게 문의하여 조직의 Crypto Board 검토를 예약합니다. DSA 사용이 승인되면 2,048비트 미만의 키 사용을 금지해야 합니다. CNG는 Windows 8부터 2,048비트 이상의 키 길이를 지원합니다.</li><li>**Diffie-Hellman -** 세션 키 관리에만 사용할 수 있습니다. 새 코드에는 2,048비트 이상의 키가 필요합니다. 기존 코드는 조직의 Crypto Board에서 검토한 후 이전 버전과의 호환성을 위해서만 2,048비트 미만의 키를 지원할 수 있습니다. 1,024비트 미만의 키는 사용할 수 없습니다.</li><ul>

## <a id="numgen"></a>승인된 난수 생성기 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>제품에서는 승인된 난수 발생기를 사용해야 합니다. 따라서 의사 난수 함수(예: rand C 런타임 함수, System.Random .NET Framework 클래스 또는 GetTickCount 시스템 함수)는 이러한 코드에 절대로 사용할 수 없습니다. 이중 타원 곡선 난수 생성기(DUAL_EC_DRBG) 알고리즘의 사용은 금지됩니다.</p><ul><li>**CNG -** BCryptGenRandom(호출자가 0보다 큰 IRQL[즉 PASSIVE_LEVEL]에서 실행되지 않는 한 BCRYPT_USE_SYSTEM_PREFERRED_RNG 플래그를 사용하는 것이 좋음)</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64 -** RtlGenRandom(새 구현에서는 BCryptGenRandom 또는 CryptGenRandom을 사용해야 함) * rand_s * SystemPrng(커널 모드의 경우)</li><li>**.NET -** RNGCryptoServiceProvider 또는 RNGCng</li><li>**Windows 스토어 앱-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom 또는 .GenerateRandomNumber</li><li>**Apple OS X(10.7+)/iOS(2.0+)-** int SecRandomCopyBytes(SecRandomRef random, size_t count, uint8_t \*bytes )</li><li>**Apple OS X(<10.7)-** /dev/random을 사용하여 난수를 검색합니다.</li><li>**Java(Google Android Java 코드 포함) -** java.security.SecureRandom 클래스입니다. Android 4.3(Jelly Bean)의 경우 개발자는 Android 권장 해결 방법을 수행하고 /dev/urandom 또는/dev/random에서 엔트로피를 사용하여 명시적으로 PRNG를 초기화하도록 애플리케이션을 업데이트해야 합니다.</li></ul>|

## <a id="stream-ciphers"></a>대칭 스트림 암호화 사용 금지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 대칭 스트림 암호화(예: RC4)는 사용하지 않아야 합니다. 대칭 스트림 암호화 대신 제품에서 키 길이가 128비트 이상인 AES와 같은 블록 암호화를 사용해야 합니다. |

## <a id="mac-hash"></a>승인된 MAC/HMAC/키 해시 알고리즘 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>제품에서는 승인된 MAC(메시지 인증 코드) 또는 HMAC(해시 기반 메시지 인증 코드) 알고리즘만 사용해야 합니다.</p><p>MAC는 받는 사람이 비밀 키를 사용하여 보낸 사람의 신뢰성과 메시지 무결성을 모두 확인할 수 있도록 메시지에 연결되는 정보입니다. 모든 기본 해시 또는 대칭 암호화 알고리즘을 사용하도록 승인한 경우에만 해시 기반 MAC([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) 또는 [블록 암호화 기반 MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf)를 사용할 수 있습니다. 현재 여기에는 HMAC-SHA2 함수(HMAC-SHA256, HMAC-SHA384 및 HMAC-SHA512)과 CMAC/OMAC1 및 OMAC2 블록 암호화 기반 MAC(AES 기반)가 포함됩니다.</p><p>HMAC-SHA1은 플랫폼 호환성을 위해 사용할 수 있지만 이 절차에 대한 예외를 제출하고 조직의 Crypto 검토를 거쳐야 합니다. 128비트 미만으로의 HMAC 잘림은 허용되지 않습니다. 고객의 방법을 사용하여 키와 데이터를 해시하는 것은 승인되지 않으며 사용하기 전에 조직의 Crypto Board 검토를 거쳐야 합니다.</p>|

## <a id="hash-functions"></a>승인된 암호화 해시 함수만 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>제품에서는 SHA-2 해시 알고리즘 제품군(SHA256, SHA384 및 SHA512)을 사용해야 합니다. 짧은 MD5 해시를 고려하여 설계된 데이터 구조에 맞추기 위해 128비트 출력 길이와 같이 더 짧은 해시가 필요한 경우 제품 팀이 SHA2 해시 중 하나(일반적으로 SHA256)를 자를 수 있습니다. SHA384는 SHA512의 잘린 버전입니다. 보안을 위해 암호화 해시를 128비트 미만으로 자르는 것은 허용되지 않습니다. 새 코드에서는 MD2, MD4, MD5, SHA-0, SHA-1 또는 RIPEMD 해시 알고리즘을 사용하면 안됩니다. 해시 충돌은 이러한 알고리즘에 대해 컴퓨터를 통해 실행 가능하며 효과적으로 해독할 수 있습니다.</p><p>관리되는 암호화 민첩성을 위해 허용되는 .NET 해시 알고리즘(기본 설정 순서대로)</p><ul><li>SHA512Cng(FIPS 규격)</li><li>SHA384Cng(FIPS 규격)</li><li>SHA256Cng(FIPS 규격)</li><li>SHA512Managed (비 FIPS 규격)-() 사용 하 여 SHA512 HashAlgorithm.Create 또는 CryptoConfig.CreateFromName에 대 한 호출에서 알고리즘 이름으로</li><li>SHA384Managed (비 FIPS 규격)-() 사용 하 여 SHA384 HashAlgorithm.Create 또는 CryptoConfig.CreateFromName에 대 한 호출에서 알고리즘 이름으로</li><li>SHA256Managed (비 FIPS 규격)-() 사용 하 여 SHA256 HashAlgorithm.Create 또는 CryptoConfig.CreateFromName에 대 한 호출에서 알고리즘 이름으로</li><li>SHA512CryptoServiceProvider(FIPS 규격)</li><li>SHA256CryptoServiceProvider(FIPS 규격)</li><li>SHA384CryptoServiceProvider(FIPS 규격)</li></ul>| 

## <a id="strong-db"></a>강력한 암호화 알고리즘을 사용하여 데이터베이스 데이터 암호화

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [암호화 알고리즘 선택](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **단계** | 암호화 알고리즘은 권한이 없는 사용자가 쉽게 바꿀 수 없는 데이터 변환을 정의합니다. SQL Server를 사용하면 관리자와 개발자가 DES, 3중 DES, TRIPLE_DES_3KEY, RC2, RC4, 128비트 RC4, DESX, 128비트 AES, 192비트 AES 및 256비트 AES를 포함한 여러 알고리즘 중에서 선택할 수 있습니다. |

## <a id="ssis-signed"></a>암호화되고 디지털 서명되어야 하는 SSIS 패키지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [디지털 서명을 사용하여 패키지 원본 확인](https://msdn.microsoft.com/library/ms141174.aspx), [위협 요소 및 취약성 완화(Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **단계** | 패키지의 원본은 해당 패키지를 만든 개인 또는 조직입니다. 알 수 없거나 신뢰할 수 없는 원본에서 패키지를 실행하는 것은 위험할 수 있습니다. SSIS 패키지의 무단 변조를 방지하려면 디지털 서명을 사용해야 합니다. 또한 저장/전송 중에 패키지의 기밀성을 보장하려면 SSIS 패키지를 암호화해야 합니다. |

## <a id="securables-db"></a>중요한 데이터베이스 보안 개체에 디지털 서명 추가

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [ADD SIGNATURE(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **단계** | 중요한 데이터베이스 보안 개체의 무결성을 확인해야 하는 경우 디지털 서명을 사용해야 합니다. 저장 프로시저, 함수, 어셈블리 또는 트리거와 같은 데이터베이스 보안 개체는 디지털로 서명할 수 있습니다. 이러한 디지털 서명이 유용할 수 있는 경우는 다음과 같습니다. ISV(Independent Software Vendor)에서 고객 중 한 사람에게 전달되는 소프트웨어에 대한 지원을 제공한다고 가정해 보겠습니다. ISV는 지원을 제공하기 전에 소프트웨어의 데이터베이스 보안 개체가 실수로 또는 악의적으로 변조되지 않았는지 확인하려고 합니다. 보안 개체가 디지털 서명된 경우 ISV는 해당 디지털 서명을 확인하고 무결성의 유효성을 검사할 수 있습니다.| 

## <a id="ekm-keys"></a>SQL 서버 EKM을 사용하여 암호화 키 보호

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [SQL Server EKM(확장 가능 키 관리)](https://msdn.microsoft.com/library/bb895340), [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](https://msdn.microsoft.com/library/dn198405) |
| **단계** | SQL Server EKM(확장 가능 키 관리)을 사용하면 데이터베이스 파일을 보호하는 암호화 키를 스마트 카드, USB 디바이스 또는 EKM/HSM 모듈과 같은 외부 디바이스에 저장할 수 있습니다. 또한 데이터베이스 관리자(sysadmin 그룹의 멤버 제외)로부터 데이터를 보호할 수 있습니다. 데이터베이스 사용자만 외부 EKM/HSM 모듈에 액세스할 수 있는 암호화 키를 사용하여 데이터를 암호화할 수 있습니다. |

## <a id="keys-engine"></a>데이터베이스 엔진에 암호화 키를 공개하지 않아야 하는 경우 AlwaysEncrypted 기능 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure, 온-프레미스 |
| **특성**              | SQL 버전 - V12, MsSQL2016 |
| **참조**              | [상시 암호화(데이터베이스 엔진)](https://msdn.microsoft.com/library/mt163865) |
| **단계** | 상시 암호화는 Azure SQL Database 또는 SQL Server 데이터베이스에 저장된 신용 카드 번호 또는 주민 등록 번호(예: 미국 사회 보장 번호)와 같은 중요한 데이터를 보호하기 위해 고안된 기능입니다. 상시 암호화를 사용하면 클라이언트에서 클라이언트 애플리케이션 내의 중요한 데이터를 암호화하고 데이터베이스 엔진(SQL Database 또는 SQL Server)에 암호화 키를 공개하지 않을 수 있습니다. 따라서 상시 암호화는 데이터를 소유하고 볼 수 있는 사용자와 데이터를 관리하지만 액세스 권한이 없는 사용자를 구별합니다. |

## <a id="keys-iot"></a>IoT 디바이스에 안전하게 암호화 키 저장

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 디바이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 디바이스 OS - Windows IoT Core, 디바이스 연결 - Azure IoT 디바이스 SDK |
| **참조**              | [Windows IoT Core의 TPM](https://developer.microsoft.com/windows/iot/docs/tpm)(영문), [Windows IoT Core에서 TPM 설정](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm)(영문), [Azure IoT 디바이스 SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM)(영문) |
| **단계** | 대칭 또는 인증서 개인 키는 TPM 또는 스마트 카드 칩과 같은 하드웨어로 보호된 저장소에 안전하게 보관됩니다. Windows 10 IoT Core는 TPM의 사용자를 지원하며, 사용할 수 있는 몇 가지 호환 가능한 TPM이 있습니다. https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm 펌웨어 또는 불연속 TPM을 사용하는 것이 좋습니다. 소프트웨어 TPM은 개발 및 테스트 용도로만 사용해야 합니다. TPM을 사용할 수 있고 이 TPM에 키를 프로비전하는 경우 토큰을 생성하는 코드는 중요한 정보를 하드 코딩하지 않고 작성해야 합니다. | 

### <a name="example"></a>예
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
여기서 보여주듯이 디바이스 기본 키는 코드에 없지만 TPM의 슬롯 0에 저장됩니다. TPM 디바이스는 IoT Hub에 연결하는 데 사용되는 수명이 짧은 SAS 토큰을 생성합니다. 

## <a id="random-hub"></a>IoT Hub 인증에 충분한 길이의 임의 대칭 키 생성

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 게이트웨이 선택 - Azure IoT Hub |
| **참조**              | N/A  |
| **단계** | IoT Hub는 디바이스 ID 레지스트리를 포함하고, 디바이스를 프로비전하는 동안 임의 대칭 키를 자동으로 생성합니다. Azure IoT Hub ID 레지스트리의 이 기능을 사용하여 인증에 사용되는 키를 생성하는 것이 좋습니다. 또한 IoT Hub를 사용하면 디바이스를 만드는 동안 키를 지정할 수 있습니다. 디바이스 프로비전 중에 IoT Hub 외부에서 키를 생성하는 경우 임의 대칭 키 또는 256비트 이상의 키를 만드는 것이 좋습니다. |

## <a id="pin-remote"></a>PIN 사용이 필요하고 원격 지우기를 허용하는 디바이스 관리 정책이 있는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM 모바일 클라이언트 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | PIN 사용이 필요하고 원격 지우기를 허용하는 디바이스 관리 정책이 있는지 확인합니다. |

## <a id="bitlocker"></a>PIN/암호/자동 잠금이 필요하고 모든 데이터를 암호화(예: BitLocker)하는 디바이스 관리 정책이 있는지 확인합니다.

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM Outlook 클라이언트 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | PIN/암호/자동 잠금이 필요하고 모든 데이터를 암호화(예: BitLocker)하는 디바이스 관리 정책이 있는지 확인합니다. |

## <a id="rolled-server"></a>Identity Server를 사용할 때 서명 키가 롤오버되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Identity Server-키, 서명 및 암호화](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **단계** | Identity Server를 사용할 때 서명 키가 롤오버되는지 확인합니다. 참조 섹션의 링크에서는 Identity Server를 사용하는 애플리케이션을 중단하지 않고도 서명 키를 롤오버하도록 계획하는 방법을 설명합니다. |

## <a id="client-server"></a>Identity Server에서 암호화된 강력한 클라이언트 ID와 클라이언트 비밀이 사용되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>Identity Server에서 암호화된 강력한 클라이언트 ID와 클라이언트 비밀이 사용되는지 확인합니다. 클라이언트 ID와 비밀을 생성하는 동안 다음 지침을 사용해야 합니다.</p><ul><li>클라이언트 ID로 임의 GUID를 생성합니다.</li><li>비밀로 암호화된 임의 256비트 키를 생성합니다.</li></ul>|
