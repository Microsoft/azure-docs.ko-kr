---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224493"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>주요 트랜잭션 (지역<sup>1</sup>당 자격 증명 모음 당 10 초 내에 허용 되는 최대 트랜잭션 수):

|키 유형|HSM 키<br>키 만들기|HSM 키<br>모든 다른 트랜잭션|소프트웨어 키<br>키 만들기|소프트웨어 키<br>모든 다른 트랜잭션|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1,000|10|2, 000|
|RSA 3072 비트|5|250|10|500|
|RSA 4096 비트|5|125|10|250|
|ECC P-256|5|1,000|10|2, 000|
|ECC P-384|5|1,000|10|2, 000|
|ECC P-521|5|1,000|10|2, 000|
|ECC SECP256K1|5|1,000|10|2, 000|

> [!NOTE]
> 위의 표에서 RSA 2048 비트 소프트웨어 키의 경우 2000 GET 트랜잭션/10 초가 허용 됩니다. RSA 2048 비트 HSM 1000 키의 경우 10 초 당 트랜잭션 가져오기가 허용 됩니다.
>
> 제한 임계값은 가중치가 적용 되며 합계에 적용 됩니다. 예를 들어 앞의 표에 나와 있는 것 처럼 RSA HSM 키에 대한 GET 작업을 수행할 때 2048 비트 키와 비교 하 여 4096 비트 키를 사용 하는 것이 8 배 더 비쌉니다. 1000/125 = 8 이기 때문입니다.
>
> 지정 된 10 초 간격으로 Azure Key Vault 클라이언트는 `429` 조정 HTTP 상태 코드를 발생 하기 전에 다음 작업 중 *하나만* 수행할 수 있습니다.
> - 2000 RSA 2048 비트 소프트웨어-키 가져오기 트랜잭션
> - 1000 RSA 2048 비트 HSM-키 가져오기 트랜잭션
> - 125 RSA 4096 비트 HSM-키 가져오기 트랜잭션
> - 124 RSA 4096 비트 HSM-키 가져오기 트랜잭션 및 8 RSA 2048 비트 HSM-key GET 트랜잭션

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>비밀, 관리 되는 storage 계정 키 및 자격 증명 모음 트랜잭션:
| 트랜잭션 유형 | 지역 당 자격 증명 모음 당 10 초 내에 허용 되는 최대 트랜잭션<sup>1</sup> |
| --- | --- |
| 모든 트랜잭션 |2, 000 |

이러한 제한을 초과 하는 경우 제한을 처리 하는 방법에 대한 자세한 내용은 [Azure Key Vault 제한 지침](../articles/key-vault/key-vault-ovw-throttling.md)을 참조 하세요.

<sup>1</sup> 모든 트랜잭션 유형에 대한 구독 전체 제한은 key vault 제한 당 5 회입니다. 예를 들어 HSM-구독 당 다른 트랜잭션은 구독 당 10 초 내에 5000 트랜잭션으로 제한 됩니다.
