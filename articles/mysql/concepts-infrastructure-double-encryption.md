---
title: 인프라 이중 암호화-Azure Database for MySQL
description: 인프라 이중 암호화를 사용 하 여 서비스 관리 키를 사용 하는 두 번째 암호화 계층을 추가 하는 방법에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: a2f319f7bbd3fa0c6c4efb7e225bfc85a76002f2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919712"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL 인프라 이중 암호화

> [!NOTE]
> 현재 이 기능을 사용하려면 액세스를 요청해야 합니다. 이렇게 하려면에 문의 하세요 AskAzureDBforMySQL@service.microsoft.com .

Azure Database for MySQL는 Microsoft의 관리 되는 키를 사용 하 여 데이터에 대해 [미사용 데이터의 저장소 암호화](concepts-security.md#at-rest) 를 사용 합니다. 백업을 비롯 한 데이터는 디스크에서 암호화 되며이 암호화는 항상 사용 되며 사용 하지 않도록 설정할 수 없습니다. 이 암호화는 Azure storage 암호화를 위해 FIPS 140-2의 유효성을 검사 한 암호화 모듈과 AES 256 비트 암호를 사용 합니다.

인프라 이중 암호화는 서비스 관리 키를 사용 하 여 두 번째 암호화 계층을 추가 합니다. FIPS 140-2 유효성 검사 암호화 모듈을 사용 하지만 다른 암호화 알고리즘을 사용 합니다. 이는 미사용 데이터에 대 한 추가 보호 계층을 제공 합니다. 인프라 이중 암호화에 사용 되는 키는 Azure Database for MySQL 서비스에 의해 관리 됩니다. 인프라 이중 암호화는 추가 암호화 계층이 성능에 영향을 줄 수 있으므로 기본적으로 사용 하도록 설정 되어 있지 않습니다.

> [!NOTE]
> Azure Database for MySQL이 "범용" 및 "메모리 최적화" 가격 책정 계층을 지원하는 모든 Azure 지역에서 이 기능을 사용할 수 있습니다.

인프라 계층 암호화는 저장소 장치나 네트워크 회선에 가장 가까운 계층에서 구현 되는 이점을 제공 합니다. Azure Database for MySQL는 서비스 관리 키를 사용 하 여 두 가지 암호화 계층을 구현 합니다. 여전히 서비스 계층에서 기술적으로는 아니지만 데이터를 휴지 상태의 데이터를 저장 하는 하드웨어와 매우 가깝습니다. 프로 비전 된 MySQL 서버에 대해 [고객 관리 키](concepts-data-encryption-mysql.md) 를 사용 하 여 미사용 데이터 암호화를 선택적으로 사용할 수 있습니다. 

인프라 계층의 구현 에서도 다양 한 키를 지원 합니다. 인프라는 컴퓨터 및 네트워크의 여러 클러스터를 인식 해야 합니다. 따라서 다양 한 키를 사용 하 여 인프라 공격과 다양 한 하드웨어 및 네트워크 오류의 폭발 한 반경을 최소화 합니다. 

> [!NOTE]
> 인프라 이중 암호화를 사용 하면 추가 암호화 프로세스 때문에 Azure Database for MySQL 서버에서 성능에 영향을 줄 수 있습니다.

## <a name="benefits"></a>이점

Azure Database for MySQL에 대 한 인프라 이중 암호화는 다음과 같은 이점을 제공 합니다.

1. **암호화 구현의 추가 다양성** -하드웨어 기반 암호화로의 계획 된 이동은 소프트웨어 기반 구현 외에 하드웨어 기반 구현을 제공 하 여 구현을 다양화 합니다.
2. **구현 오류** -인프라 계층에서 두 계층의 암호화 계층은 일반 텍스트 데이터를 노출 하는 상위 계층의 캐싱 또는 메모리 관리에서 발생 하는 모든 오류를 방지 합니다. 또한 두 계층은 일반적으로 암호화 구현 시 오류를 방지 합니다.

이러한 조합은 암호화를 공격 하는 데 사용 되는 일반적인 위협 및 약점 으로부터 강력한 보호를 제공 합니다.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>인프라 이중 암호화와 함께 지원 되는 시나리오

Azure Database for MySQL에서 제공 하는 암호화 기능은 함께 사용할 수 있습니다. 다음은 사용할 수 있는 다양 한 시나리오에 대 한 요약입니다.

|  ##   | 기본 암호화 | 인프라 이중 암호화 | 고객 관리 키를 사용 하 여 데이터 암호화  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *예*              | *아니요*                             | *아니요*                                         |
| 2     | *예*              | *예*                            | *아니요*                                         |
| 3     | *예*              | *아니요*                             | *예*                                        |
| 4     | *예*              | *예*                            | *예*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 시나리오 2와 4는 인프라 암호화의 추가 계층으로 인해 Azure Database for MySQL 서버의 작업 유형에 따라 처리량이 5-10% 감소 될 수 있습니다.
> - Azure Database for MySQL에 대 한 인프라 이중 암호화 구성은 서버를 만드는 동안에만 허용 됩니다. 서버를 프로 비전 한 후에는 저장소 암호화를 변경할 수 없습니다. 그러나 인프라 이중 암호화를 사용 하거나 사용 하지 않고 생성 된 서버에 대해서는 고객 관리 키를 사용 하 여 데이터 암호화를 계속 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

Azure Database for MySQL에서 서비스 관리 키를 사용 하는 인프라 이중 암호화에 대 한 지원에는 다음과 같은 제한 사항이 있습니다.

* 이 기능에 대 한 지원은 **범용** 및 메모리 액세스에 **최적화** 된 가격 책정 계층으로 제한 됩니다.
* 다음 지역에서 인프라 암호화를 사용 하 여 Azure Database for MySQL를 만들 수 있습니다.

   * 미국 동부
   * 미국 중남부
   * 미국 서부 2
   
* * 이 기능은 최대 16TB의 저장소를 지 원하는 지역 및 서버 에서만 지원 됩니다. 최대 16TB의 저장소를 지 원하는 Azure 지역 목록은 [저장소 설명서](concepts-pricing-tiers.md#storage)를 참조 하세요.

    > [!NOTE]
    > - 위에 나열 된 지역에서 만든 모든 **새** MySQL 서버는 고객 관리자 키로 데이터 암호화도 지원 합니다. 이 경우 PITR (지정 시간 복원) 또는 읽기 복제본을 통해 만든 서버는 "new"로 한정 되지 않습니다.
    > - 프로 비전 된 서버에서 최대 16TB를 지원 하는지 확인 하려면 포털의 가격 책정 계층 블레이드로 이동 하 여 저장소 슬라이더를 최대 16TB까지 이동할 수 있는지 확인 합니다. 슬라이더를 최대 4 TB 까지만 이동할 수 있는 경우 서버에서 고객 관리 키를 사용 하 여 암호화를 지원 하지 않을 수 있습니다. 그러나 데이터는 서비스 관리 키를 사용 하 여 항상 암호화 됩니다. AskAzureDBforMySQL@service.microsoft.com궁금한 점이 있으면에 문의 하세요.

## <a name="next-steps"></a>다음 단계

[Azure database For MySQL에 대 한 인프라 이중 암호화를 설정](howto-double-encryption.md)하는 방법에 대해 알아봅니다.
