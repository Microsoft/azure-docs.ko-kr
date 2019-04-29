---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419602"
---
### <a name="noconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 게이트웨이 연결 없음

#### <a name="to-add-additional-address-prefixes"></a>추가 주소 접두사를 추가하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. *추가 주소 범위 추가* 상자에 IP 주소 공간을 추가합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

#### <a name="to-remove-address-prefixes"></a>주소 접두사를 제거하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. 제거하려는 접두사를 포함하는 줄에서 **‘...’** 를 클릭합니다.
3. **제거**를 클릭합니다.
4. **Save** 를 클릭하여 설정을 저장합니다.

### <a name="withconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 기존 게이트웨이 연결

게이트웨이 연결이 있고 로컬 네트워크 게이트웨이에 포함된 IP 주소 접두사를 추가 또는 제거하려면 다음 단계를 순서대로 수행해야 합니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다. IP 주소 접두사를 수정할 때 VPN Gateway를 삭제할 필요가 없습니다. 연결만 제거하면 됩니다.

#### <a name="1-remove-the-connection"></a>1. 연결을 제거합니다.

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **연결**을 클릭합니다.
2. 각 연결의 줄에서 **...** 를 클릭하고 **삭제**를 클릭합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

#### <a name="2-modify-the-address-prefixes"></a>2. 주소 접두사를 수정합니다.

추가 주소 접두사를 추가하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. IP 주소 공간을 추가합니다.
3. **Save** 를 클릭하여 설정을 저장합니다.

주소 접두사를 제거하려면:

1. 로컬 네트워크 게이트웨이 리소스의 **설정** 섹션에서 **구성**을 클릭합니다.
2. 제거하려는 접두사를 포함하는 줄에서 **...** 를 클릭합니다.
3. **제거**를 클릭합니다.
4. **Save** 를 클릭하여 설정을 저장합니다.

#### <a name="3-recreate-the-connection"></a>3. 연결을 다시 만듭니다.

1. VNet에 대한 Virtual Network 게이트웨이로 이동합니다. (로컬 네트워크 게이트웨이가 아님)
2. Virtual Network 게이트웨이의 **설정** 섹션에서 **연결**을 클릭합니다.
3. **+ 추가**를 클릭하여 **연결 추가** 블레이드를 엽니다.
4. 연결을 다시 만듭니다.
5. **확인**을 클릭하여 연결을 만듭니다.