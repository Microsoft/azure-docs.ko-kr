---
title: Microsoft Azure의 이중 암호화
description: 이 문서에서는 Microsoft Azure 미사용 데이터 및 전송 중인 데이터에 대해 이중 암호화를 제공 하는 방법을 설명 합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227323"
---
# <a name="double-encryption"></a>이중 암호화
이중 암호화는 한 암호화 계층의 손상 으로부터 보호 하기 위해 두 개 이상의 독립적인 암호화 계층을 사용 하도록 설정 하는 경우입니다. 두 계층의 암호화를 사용 하면 데이터 암호화와 함께 제공 되는 위협을 완화 합니다. 예를 들면 다음과 같습니다.

- 데이터 암호화의 구성 오류
- 암호화 알고리즘의 구현 오류
- 단일 암호화 키의 손상

Azure는 미사용 데이터 및 전송 중인 데이터에 대해 이중 암호화를 제공 합니다.

## <a name="data-at-rest"></a>미사용 데이터
휴지 상태의 데이터에 대 한 두 가지 암호화 계층을 사용 하도록 설정 하는 Microsoft의 접근 방식은 다음과 같습니다.

- **고객 관리 키를 사용 하는 디스크 암호화** 디스크 암호화를 위한 고유한 키를 제공 합니다. 사용자 고유의 키를 Key Vault (BYOK – Bring Your Own Key)로 가져오거나 Azure Key Vault에서 새 키를 생성 하 여 원하는 리소스를 암호화할 수 있습니다.
- **플랫폼 관리 키를 사용 하는 인프라 암호화**  기본적으로 디스크는 플랫폼 관리 암호화 키를 사용 하 여 미사용 시 자동으로 암호화 됩니다.

## <a name="data-in-transit"></a>전송 중 데이터
전송 중인 데이터에 대해 두 가지 수준의 암호화를 사용 하도록 설정 하는 Microsoft의 접근 방식은 다음과 같습니다.

- **TLS (Transport Layer Security) 1.2를 사용 하 여 전송 암호화-클라우드 서비스와 사용자 간에 이동할 때 데이터를 보호**합니다. 트래픽 대상이 동일한 지역의 다른 도메인 컨트롤러인 경우에도 데이터 센터에서 나가는 모든 트래픽이 전송 중에 암호화 됩니다. TLS 1.2은 사용 되는 기본 보안 프로토콜입니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.
- **인프라 계층에서 제공 되는 추가 암호화 계층**입니다. IEEE 802.1 AE MAC 보안 표준 (MACsec 라고도 함)을 사용 하는 데이터 링크 계층 암호화 방법은 기본 네트워크 하드웨어의 지점 간 지점에서 적용 됩니다. Azure 고객 트래픽이 데이터 센터 간에 이동 하는 경우 (또는 Microsoft를 대신 하 여 microsoft에서 제어 하지 않는 물리적 경계 외부에 있는 경우), 패킷이 전송 되기 전에 장치에서 암호화 되 고 해독 되므로 실제 "메시지 가로채기 (man-in-the-middle)" 또는 스누핑/wiretapping 공격을 방지 합니다. 이 기술은 네트워크 하드웨어 자체에 통합 되어 있으므로 네트워크 하드웨어에 대 한 회선 속도로 암호화 된 링크 대기 시간 증가를 제공 하지 않습니다. 이 MACsec 암호화는 지역 내 또는 지역 내에서 이동 하는 모든 Azure 트래픽에 대해 기본적으로 설정 되며, 사용 하도록 설정 하는 데 필요한 작업은 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure에서 암호화를 사용](encryption-overview.md)하는 방법을 알아봅니다.
