---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419600"
---
### <a name="gwipnoconnection"></a> 로컬 네트워크 게이트웨이 IP 주소를 수정하려면 - 게이트웨이 연결 없음

예제를 사용하여 게이트웨이 연결이 없는 로컬 네트워크 게이트웨이를 수정합니다. 이 값을 수정할 때 주소 접두사를 함께 수정할 수도 있습니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. **IP 주소** 상자에서 IP 주소를 수정합니다.
3. **저장**을 클릭하여 설정을 저장합니다.

### <a name="gwipwithconnection"></a>로컬 네트워크 게이트웨이 IP 주소를 수정하려면 - 기존 게이트웨이 연결

연결된 로컬 네트워크 게이트웨이를 수정하려면 먼저 연결을 제거해야 합니다. 연결을 제거한 후 게이트웨이 IP 주소를 수정하고 새 연결을 다시 만들 수 있습니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다. 게이트웨이 IP 주소를 수정할 때 VPN Gateway를 삭제할 필요가 없습니다. 연결만 제거하면 됩니다.
 
#### <a name="1-remove-the-connection"></a>1. 연결을 제거합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **연결**을 클릭합니다.
2. 연결의 줄에서 **...** 를 클릭하고 **삭제**를 클릭합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

#### <a name="2-modify-the-ip-address"></a>2. IP 주소를 수정합니다.

이와 동시에 주소 접두사를 수정할 수도 있습니다.

1. **IP 주소** 상자에서 IP 주소를 수정합니다.
2. **저장**을 클릭하여 설정을 저장합니다.

#### <a name="3-recreate-the-connection"></a>3. 연결을 다시 만듭니다.

1. VNet에 대한 Virtual Network 게이트웨이로 이동합니다. (로컬 네트워크 게이트웨이가 아님)
2. Virtual Network 게이트웨이의 **설정** 섹션에서 **연결**을 클릭합니다.
3. **+ 추가**를 클릭하여 **연결 추가** 블레이드를 엽니다.
4. 연결을 다시 만듭니다.
5. **확인**을 클릭하여 연결을 만듭니다.