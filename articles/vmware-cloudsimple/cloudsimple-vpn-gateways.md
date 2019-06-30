---
title: VMware 솔루션 CloudSimple-Azure에서의 VPN gateway
description: CloudSimple 사이트 간 VPN 및 지점-사이트 간 VPN 개념에 대해 알아봅니다
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a91d0f75b7e27065bd2ee0ea430a859d265afacb
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332045"
---
# <a name="vpn-gateways-overview"></a>VPN gateway 개요

VPN gateway는 온-프레미스 위치 또는 공용 인터넷을 통해 컴퓨터에 있는 CloudSimple 지역 네트워크 간에 암호화 된 트래픽을 보낼 사용 됩니다.  각 지역에는 하나의 VPN gateway만 있을 수 있습니다. 그러나 동일한 VPN Gateway에 대해 여러 연결을 만들 수 있습니다. 동일한 VPN Gateway에 대해 여러 연결을 만들면 모든 VPN 터널이 사용 가능한 게이트웨이 대역폭을 공유합니다.

CloudSimple 두 가지를 VPN gateway 제공합니다.

* 사이트 간 VPN Gateway
* 지점-사이트 간 VPN Gateway

## <a name="site-to-site-vpn-gateway"></a>사이트 간 VPN 게이트웨이

사이트 간 VPN gateway CloudSimple 지역 네트워크 및 온-프레미스 데이터 센터 간에 암호화 된 트래픽을 보낼 사용 됩니다. 온-프레미스 네트워크와 CloudSimple 지역 네트워크 간의 통신에 대 한 서브넷/CIDR 범위를 정의 하도록이 연결을 사용 합니다.

VPN gateway에서 온-프레미스 사설 클라우드에 서비스를 사용할 수 있도록 및 사설 클라우드, 온-프레미스 네트워크에서 서비스.  CloudSimple 온-프레미스 네트워크에서 연결에 대 한 정책 기반 VPN 서버를 제공 합니다.

사이트 간 VPN에 대 한 사용 사례는 다음과 같습니다.

* 온-프레미스 네트워크의 모든 워크스테이션에서 사설 클라우드 vCenter의 접근성입니다.
* 온-프레미스 Active Directory의 vCenter id 원본으로 사용 합니다.
* 편리 하 게 전송할 VM 템플릿, Iso, 및 기타 파일의 온-프레미스 리소스에서 사설 클라우드 vCenter 합니다.
* 온-프레미스 네트워크에서 사설 클라우드에서 실행 중인 워크 로드의 접근성입니다.

![사이트 간 VPN 연결 토폴로지](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> 1078 바이트 이하로 TCP MSS 고정 해야 합니다. 또는 VPN 장치가 MSS 고정를 지원 하지 않는 경우 설정할 수 있습니다 또는 MTU 1118 바이트로 터널 인터페이스에서 대신 합니다. 

### <a name="cryptographic-parameters"></a>암호화 매개 변수

사이트 간 VPN 연결을 보안 연결을 설정 하려면 다음 기본 암호화 매개 변수를 사용 합니다.  온-프레미스 VPN 장치에서 연결을 만들 때 매개 변수 일치 해야 합니다.

사이트 간 VPN 연결을 보안 연결을 설정 하려면 다음 기본 암호화 매개 변수를 사용 합니다.  온-프레미스 VPN 장치에서 연결을 만들 때 온-프레미스 VPN gateway를 지 원하는 다음 매개 변수 중 하나를 사용 합니다.

#### <a name="phase-1-proposals"></a>1 단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| IKE 버전 | IKEv1 | IKEv1 | IKEv1 |
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman 그룹 (DH 그룹) | 2 | 2 | 2 |
| 수명 | 28,800초 | 28,800초 | 28,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |


#### <a name="phase-2-proposals"></a>2 단계 제안 

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| 완벽 하 게 전달 완전 보안 그룹 (PFS 그룹) | 없음 | 없음 | 없음 |
| 수명 | 1,800 초 | 1,800 초 | 1,800 초 |
| 데이터 크기 | 4GB | 4GB | 4GB |

## <a name="point-to-site-vpn-gateway"></a>지점-사이트 간 VPN Gateway

지점-사이트 VPN CloudSimple 지역 네트워크와 클라이언트 컴퓨터 간에 암호화 된 트래픽을 전송 됩니다.  지점-사이트 간 VPN은 인맥과 사설 클라우드, 사설 클라우드 vCenter 및 워크 로드 Vm 포함 하는 가장 쉬운 방법은입니다.  지점-사이트 간 VPN 연결을 사용 하 여 원격으로 사설 클라우드에 연결 하는 경우.

## <a name="next-steps"></a>다음 단계

* [VPN gateway 설정](https://docs.azure.cloudsimple.com/vpn-gateway/)