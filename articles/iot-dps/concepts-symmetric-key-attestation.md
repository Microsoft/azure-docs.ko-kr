---
title: Azure IoT Hub 장치 프로 비전 서비스-대칭 키 증명
description: 이 문서에서는 DPS (IoT 장치 프로 비전 서비스)를 사용 하 여 대칭 키 증명에 대 한 개념적 개요를 제공 합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975298"
---
# <a name="symmetric-key-attestation"></a>Szimmetrikus kulcsú igazolás

이 문서에서는 장치 프로 비전 서비스에서 대칭 키를 사용 하는 경우 id 증명 프로세스를 설명 합니다. 

대칭 키 증명은 장치 프로 비전 서비스 인스턴스를 사용 하 여 장치를 인증 하는 간단한 방법입니다. 이 증명 방법은 장치 프로 비전을 처음 접하는 개발자를 위한 "Hello 세계" 환경을 나타내거나 엄격한 보안 요구 사항을 충족 하지 않습니다. [TPM](concepts-tpm-attestation.md) 또는 [x.509 인증서](concepts-security.md#x509-certificates) 를 사용 하는 장치 증명은 더 안전 하며 보다 엄격한 보안 요구 사항에 사용 해야 합니다.

또한 대칭 키 등록는 Azure IoT를 통해 클라우드로 부트스트랩 하기 위해 제한 된 보안 기능을 제공 하는 레거시 장치에 뛰어난 방법을 제공 합니다. 레거시 장치의 대칭 키 증명에 대 한 자세한 내용은 [레거시 장치에서 대칭 키를 사용 하는 방법](how-to-legacy-device-symm-key.md)을 참조 하세요.


## <a name="symmetric-key-creation"></a>대칭 키 만들기

기본적으로 장치 프로 비전 서비스는 **자동 생성 키** 옵션을 사용 하 여 새 등록를 저장할 때 기본 길이가 32 바이트인 새 대칭 키를 만듭니다.

![대칭 키 자동 생성](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

이 옵션을 사용 하지 않도록 설정 하 여 등록에 대 한 고유한 대칭 키를 제공할 수도 있습니다. 사용자 고유의 대칭 키를 지정 하는 경우 키의 길이는 16 바이트에서 64 바이트 사이 여야 합니다. 또한 대칭 키는 유효한 Base64 형식으로 제공 해야 합니다.



## <a name="detailed-attestation-process"></a>자세한 증명 프로세스

장치 프로 비전 서비스를 사용 하는 대칭 키 증명은 IoT hub에서 지 원하는 것과 동일한 [보안 토큰](../iot-hub/iot-hub-devguide-security.md#security-token-structure) 을 사용 하 여 장치를 식별 합니다. 이러한 보안 토큰은 [SAS (공유 액세스 서명) 토큰](../service-bus-messaging/service-bus-sas.md)입니다. 

SAS 토큰에는 대칭 키를 사용 하 여 만든 해시 된 *서명이* 있습니다. 증명을 통해 제공 되는 보안 토큰이 인증 되었는지 여부를 확인 하기 위해 장치 프로 비전 서비스에서 서명을 다시 만듭니다.

SAS 토큰의 형식은 다음과 같습니다.

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

각 토큰의 구성 요소는 다음과 같습니다.

| Value (Díj) | Leírás |
| --- | --- |
| 서명과 |HMAC-SHA256 서명 문자열입니다. 개별 등록의 경우이 서명은 대칭 키 (기본 또는 보조)를 사용 하 여 해시를 수행 하 여 생성 됩니다. 등록 그룹의 경우 등록 그룹 키에서 파생 된 키를 사용 하 여 해시를 수행 합니다. 해시는 `URL-encoded-resourceURI + "\n" + expiry`형식의 메시지에 대해 수행 됩니다. **중요**: HMAC-SHA256 계산을 수행 하는 데 사용 하기 전에 base64에서 키를 디코딩해야 합니다. 또한 서명 결과는 URL로 인코딩해야 합니다. |
| ResourceURI |장치 프로 비전 서비스 인스턴스의 범위 ID부터 시작 하 여이 토큰을 사용 하 여 액세스할 수 있는 등록 엔드포인트의 URI입니다. Például: `{Scope ID}/registrations/{Registration ID}` |
| 만료 |1 월 1970 일에 epoch 00:00:00 UTC 이후의 시간 (초)에 대 한 UTF8 문자열입니다. |
| {URL 인코딩-resourceURI} |소문자 URL-소문자 리소스 URI의 인코딩입니다. |
| PolicyName |이 토큰이 참조 하는 공유 액세스 정책의 이름입니다. 대칭 키 증명을 사용 하 여 프로 비전 할 때 사용 **되는 정책 이름입니다.** |

장치가 개별 등록으로 증명 장치는 개별 등록 항목에 정의 된 대칭 키를 사용 하 여 SAS 토큰에 대 한 해시 된 서명을 만듭니다.

SAS 토큰을 만드는 코드 예제는 [보안 토큰](../iot-hub/iot-hub-devguide-security.md#security-token-structure)을 참조 하세요.

대칭 키 증명에 대 한 보안 토큰 만들기는 Azure IoT C SDK에서 지원 됩니다. Azure IoT C SDK를 사용 하 여 개별 등록을 증명 하는 예제는 [대칭 키를 사용 하 여 시뮬레이션 된 장치 프로 비전](quick-create-simulated-device-symm-key.md)을 참조 하세요.


## <a name="group-enrollments"></a>그룹 등록

등록 그룹의 대칭 키는 프로 비전 할 때 장치에서 직접 사용 되지 않습니다. 대신 등록 그룹에 속한 장치는 파생 된 장치 키를 사용 하 여 프로 비전 합니다. 

먼저 등록 그룹을 사용 하 여 각 장치 증명에 대해 고유한 등록 ID가 정의 됩니다. 등록 ID의 유효한 문자는 소문자 영숫자 및 대시 ('-')입니다. 이 등록 ID는 장치를 식별 하는 고유한 항목 이어야 합니다. 예를 들어 레거시 장치는 여러 보안 기능을 지원 하지 않을 수 있습니다. 레거시 장치에는 해당 장치를 고유 하 게 식별 하는 데 사용할 수 있는 MAC 주소 또는 일련 번호가 있을 수 있습니다. 이 경우 등록 ID는 다음과 비슷한 MAC 주소와 일련 번호로 구성 될 수 있습니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

이 정확한 예제는 [대칭 키를 사용 하 여 레거시 장치를 프로 비전 하는 방법](how-to-legacy-device-symm-key.md) 문서에서 사용 됩니다.

장치에 대해 등록 ID가 정의 되 면 등록 그룹의 대칭 키를 사용 하 여 파생 된 장치 키를 생성 하는 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 해시를 계산 합니다. 등록 ID의 해시는 다음 C# 코드를 사용 하 여 수행할 수 있습니다.

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

그러면 결과 장치 키가 증명에 사용할 SAS 토큰을 생성 하는 데 사용 됩니다. 등록 그룹의 각 장치는 고유한 파생 키에서 생성 된 보안 토큰을 사용 하 여 증명 해야 합니다. 등록 그룹 대칭 키를 증명에 직접 사용할 수 없습니다.

#### <a name="installation-of-the-derived-device-key"></a>파생 된 장치 키 설치

이상적으로 장치 키는 공장에서 파생 되 고 설치 됩니다. 이 방법을 통해 장치에 배포 된 소프트웨어에는 그룹 키가 포함 되지 않도록 보장 합니다. 장치에 MAC 주소 또는 일련 번호가 할당 되 면 장치를 파생 하 여 장치에 삽입할 수 있지만 제조업체에서 해당 키를 저장 하도록 선택 합니다.

그룹 등록 키 (**K**)를 사용 하 여 각 장치 등록 ID를 해시 하 여 팩터리에서 생성 된 장치 키 테이블을 보여 주는 다음 다이어그램을 살펴보십시오. 

![공장에서 할당 된 장치 키](./media/concepts-symmetric-key-attestation/key-diversification.png)

각 장치의 id는 팩터리에 설치 된 등록 ID 및 파생 된 장치 키로 표시 됩니다. 장치 키는 다른 위치로 복사 되지 않으며 그룹 키는 장치에 저장 되지 않습니다.

장치 키가 팩터리에 설치 되어 있지 않으면 [하드웨어 보안 모듈 HSM](concepts-security.md#hardware-security-module) 을 사용 하 여 장치 id를 안전 하 게 저장 해야 합니다.

## <a name="next-steps"></a>Következő lépések

이제 대칭 키 증명을 이해 했으므로 다음 문서를 확인 하 여 자세히 알아보세요.

* [빠른 시작: 대칭 키를 사용 하 여 시뮬레이트된 장치 프로 비전](quick-create-simulated-device-symm-key.md)
* [자동 프로 비전의 개념에 대해 알아보기](./concepts-auto-provisioning.md)
* [자동 프로 비전을 사용 하 여 시작](./quick-setup-auto-provision.md) 
