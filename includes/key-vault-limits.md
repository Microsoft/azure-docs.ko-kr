---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461325"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>키 트랜잭션 (지역당 자격 증명 모음 당 10 초 내에 허용 되는 최대 트랜잭션<sup>1</sup>):

|키 유형|HSM 키<br>키 만들기|HSM 키<br>모든 다른 트랜잭션|소프트웨어 키<br>키 만들기|소프트웨어 키<br>모든 다른 트랜잭션|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1,000|10|2,000|
|3,072 비트 RSA|5|250|10|500|
|RSA 4,096-bit|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 위의 표에서 알 수 RSA 2,048 비트 소프트웨어 키의 10 초당 2,000 개의 GET 트랜잭션을 허용 됩니다. RSA 2,048 비트 HSM-키, 10 초 GET 트랜잭션 1,000 개 허용 됩니다.
>
> 조정 임계값은가 중, 및 합계에 적용 됩니다. 예를 들어, 위의 표에 표시 된 것 처럼 RSA HSM-키에서 GET 작업을 수행할 때 8 배 더 비용도 비싸다는 사실을 2,048 비트 키를 비교할 4096 비트 키를 사용 합니다. 있기 때문입니다 1,000/125 = 8.
>
> 10 초 간격을 지정 된 Azure Key Vault 클라이언트를 수행할 수 있습니다 *하나만* 발생 하기 전에 다음 작업을 `429` 제한 HTTP 상태 코드:
> - 2,000 개의 RSA 2,048 비트 키 소프트웨어 GET 트랜잭션
> - RSA 2,048 비트 HSM 키 GET 트랜잭션 1,000 개
> - 125 RSA 4096 비트 HSM 키 GET 트랜잭션
> - 124 RSA 4096 비트 HSM-키 GET 트랜잭션 및 8 RSA 2,048 비트 HSM 키 가져오기

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>암호, 관리 되는 저장소 계정 키 및 자격 증명 모음 트랜잭션:
| 트랜잭션 유형 | 지역당 자격 증명 모음 당 10 초 내에 허용 되는 최대 트랜잭션<sup>1</sup> |
| --- | --- |
| 모든 트랜잭션 |2,000 |

이러한 제한에 도달 하는 경우 제한을 처리 하는 방법에 대 한 자세한 내용은 [Azure Key Vault 제한 지침](../articles/key-vault/key-vault-ovw-throttling.md)합니다.

<sup>1</sup> 는 구독 전체의 모든 트랜잭션 유형에 대 한 제한은 5 번 key vault 제한 수입니다. 예를 들어 HSM-다른 구독 당 트랜잭션은 5,000 개의 트랜잭션 제한 구독 당 10 초에서입니다.
