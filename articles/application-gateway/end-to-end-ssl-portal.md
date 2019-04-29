---
title: 빠른 시작-Azure Application Gateway-Azure portal 사용 하 여 종단 간 SSL 암호화 구성 | Microsoft Docs
description: 종단 간 SSL 암호화를 사용 하 여 Azure Application Gateway를 만들려면 Azure portal을 사용 하는 방법에 알아봅니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831970"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>포털을 사용 하 여 Application Gateway를 사용 하 여 종단 간 SSL 구성

이 문서에서는 application gateway를 사용 하 여 종단 간 SSL 암호화를 구성 하려면 Azure portal을 사용 하는 방법을 보여 줍니다. v1 SKU입니다.  

> [!NOTE]
> Application Gateway v2 SKU 사용 하도록 설정 하면 종단 간 구성에 대 한 신뢰할 수 있는 루트 인증서가 필요 합니다. 신뢰할 수 있는 루트 인증서를 추가 하는 데 대 한 포털 지원이 제공 되지 않습니다. 따라서 V2 SKU의 경우 볼 [PowerShell을 사용 하 여 종단 간 SSL 구성](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway를 사용하여 엔드투엔드 SSL을 구성하려면 게이트웨이에 사용할 인증서와 백 엔드 서버에 사용할 인증서가 필요합니다. 게이트웨이 인증서는 SSL 프로토콜 사양에 따라 대칭 키를 파생하는 데 사용됩니다. 대칭 키 암호화 및 게이트웨이에 전송 된 트래픽을 암호 해독에 사용 됩니다. 엔드투엔드 SSL 암호화의 경우 백 엔드가 Application Gateway를 통해 허용 목록에 추가되어야 합니다. 이렇게 하려면 application gateway에 인증 인증서 라고도 하며, 백 엔드 서버의 공개 인증서를 업로드 합니다. 인증서를 추가하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 그러면 종단 간 통신의 보안이 유지됩니다.

자세한 내용은 참조 하세요 [SSL 종료 및 종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)합니다.

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>종단 간 SSL을 사용 하 여 새 응용 프로그램 게이트웨이 만들기

종단 간 SSL 암호화를 사용 하 여 새 응용 프로그램 게이트웨이 만들려면 먼저 새 application gateway를 만드는 동안 SSL 종료를 사용 하도록 설정 해야 합니다. 이렇게 하면 클라이언트 및 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화 합니다. 그런 다음 해야 백 엔드 HTTP 설정에는 서버에 대 한 허용 목록에 추가 인증서 응용 프로그램 게이트웨이와 백 엔드 서버 간의 종단 간 SSL 암호화를 수행 하므로 통신에 SSL 암호화를 사용 하도록 설정 합니다.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>새 application gateway를 만드는 동안 SSL 종료를 사용 하도록 설정

이 문서를 참조 하는 방법 [새 응용 프로그램 게이트웨이 만드는 동안 SSL 종료를 사용 하도록 설정](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)합니다.

### <a name="whitelist-certificates-for-backend-servers"></a>백 엔드 서버에 대 한 허용 목록에 추가 인증서

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 선택 **http-settings** 왼쪽된 메뉴에서. Azure에 기본 HTTP 설정, 자동으로 생성 **appGatewayBackendHttpSettings**application gateway를 만든 경우. 

3. 선택 **appGatewayBackendHttpSettings**합니다.

4. 아래 **프로토콜**를 선택 **HTTPS**합니다. 에 대 한 창을 **백 엔드 인증 인증서** 나타납니다. 

5. 아래 **백 엔드 인증 인증서**, 선택 **새로 만들기**합니다.

6. 적절 한 입력 **이름을**입니다.

7. 사용 하 여 인증서를 업로드 합니다 **업로드 CER 인증서** 상자입니다.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 이 단계에서 제공하는 인증서는 백 엔드에 있는 .pfx 인증서의 공개 키이어야 합니다. 백 엔드 서버에 설치된 인증서(루트 인증서 제외)를 CER(Claim, Evidence, Reasoning) 형식으로 내보내고 이 단계에서 사용합니다. 이 단계에서는 Application Gateway를 통해 백 엔드를 허용 목록에 추가합니다.

8. **저장**을 선택합니다.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>기존 application gateway에 대 한 종단 간 SSL을 사용 하도록 설정

기존 application gateway에서 종단 간 SSL 암호화를 구성 하려면 수신기에 첫 번째 사용 SSL 종료 해야 합니다. 이렇게 하면 클라이언트 및 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화 합니다. 그런 다음 해야 백 엔드 HTTP 설정에는 서버에 대 한 허용 목록에 추가 인증서 응용 프로그램 게이트웨이와 백 엔드 서버 간의 종단 간 SSL 암호화를 수행 하므로 통신에 SSL 암호화를 사용 하도록 설정 합니다.

HTTPS 프로토콜 및 인증서를 사용 하 여 수신기를 사용 하 여 SSL 종료를 사용 하도록 설정 해야 합니다. 기존 수신기의 프로토콜을 변경할 수 없습니다. 따라서 새 수신기를 만들거나 기존 수신기를 사용 하 여 HTTPS 프로토콜 및 인증서를 사용 하 여 선택할 수 있습니다. 무시 해도 전자를 선택할 경우에 아래 언급 된 단계를 **기존 application gateway에서 SSL 사용 종료** 직접 이동 하 고 **백 엔드 서버에 대 한 허용 목록에 추가 인증서** 섹션입니다. 후자를 선택 하면 다음이 단계를 수행 합니다. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>기존 application gateway에서 SSL 종료를 사용 하도록 설정

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 선택 **수신기** 왼쪽된 메뉴에서.

3. 선택할 **기본** 하 고 **다중 사이트** 요구 사항에 따라 수신기입니다.

4. 아래 **프로토콜**를 선택 **HTTPS**합니다. 에 대 한 창을 **인증서** 나타납니다.

5. 클라이언트 및 응용 프로그램 게이트웨이 간의 SSL 종료를 위한 사용 하려고 하는 PFX 인증서를 업로드 합니다.

   > [!NOTE]
   > 테스트를 위해 자체 서명 된 인증서를 사용할 수 있습니다. 프로덕션 워크 로드에 대 한 자체 서명 된 인증서를 사용 하지 해야 합니다. 설명 하는 방법 [자체 서명 된 인증서 만들기](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. 에 필요한 다른 설정을 추가 합니다 **수신기** 요구 사항에 따라 합니다.

7. **확인**을 선택하여 저장합니다.

### <a name="whitelist-certificates-for-backend-servers"></a>백 엔드 서버에 대 한 허용 목록에 추가 인증서

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 선택 **http-settings** 왼쪽된 메뉴에서. 할 수 있습니다 하거나 허용 목록에 추가 인증서를 기존 백 엔드 HTTP 설정 또는 새 HTTP 설정을 만듭니다. 에 기본 HTTP 설정에 대 한 인증서를 허용 목록에 추가 될 예정 단계 아래 **appGatewayBackendHttpSettings**합니다.

3. 선택 **appGatewayBackendHttpSettings**합니다.

4. 아래 **프로토콜**를 선택 **HTTPS**합니다. 에 대 한 창을 **백 엔드 인증 인증서** 나타납니다. 

5. 아래 **백 엔드 인증 인증서**, 선택 **새로 만들기**합니다.

6. 적절 한 입력 **이름을**입니다.

7. 사용 하 여 인증서를 업로드 합니다 **업로드 CER 인증서** 상자입니다.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > 이 단계에서 제공하는 인증서는 백 엔드에 있는 .pfx 인증서의 공개 키이어야 합니다. 백 엔드 서버에 설치된 인증서(루트 인증서 제외)를 CER(Claim, Evidence, Reasoning) 형식으로 내보내고 이 단계에서 사용합니다. 이 단계에서는 Application Gateway를 통해 백 엔드를 허용 목록에 추가합니다.

8. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
