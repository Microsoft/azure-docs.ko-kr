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
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764018"
---
클라이언트 인증서를 생성하는 경우 생성하는 데 사용한 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 생성한 클라이언트 인증서를 내보내야 합니다.

1. 클라이언트 인증서를 내보내려면 **사용자 인증서 관리**를 엽니다. 기본적으로 생성하는 클라이언트 인증서는 'Certificates - Current User\Personal\Certificates'에 있습니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭한 다음 **내보내기**를 클릭하여 **인증서 내보내기 마법사**를 엽니다.

   ![내보내기](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. 인증서 내보내기 마법사에서 **다음**을 클릭하여 계속합니다.

   ![다음](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. **예, 개인 키를 내보냅니다.** 를 선택하고 **다음**을 클릭합니다.

   ![개인 키 내보내기](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. **파일 내보내기 형식** 페이지에서 선택된 기본값을 유지합니다. **가능하면 인증 경로에 있는 인증서 모두 포함**을 선택했는지 확인합니다. 이 설정은 성공적인 클라이언트 인증에 필요한 루트 인증서 정보를 추가로 내보냅니다. 이를 사용하지 않으면 클라이언트에 신뢰할 수 있는 루트 인증서가 없어 클라이언트 인증에 실패합니다. 그런 후 **다음**을 클릭합니다.

   ![내보내기 파일 형식](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. **보안** 페이지에서 개인 키를 보호해야 합니다. 암호를 사용하도록 선택하는 경우 이 인증서에 대해 설정한 암호를 기록해두거나 기억합니다. 그런 후 **다음**을 클릭합니다.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. **내보낼 파일**에서 인증서를 내보낼 위치를 **찾아보기**합니다. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **다음**을 클릭합니다.

   ![내보낼 파일](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. **마침** 을 클릭하여 인증서를 내보냅니다.

   ![마침](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)