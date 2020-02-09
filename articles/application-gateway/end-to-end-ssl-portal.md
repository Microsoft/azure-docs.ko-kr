---
title: 포털을 사용 하 여 종단 간 SSL 암호화 구성
titleSuffix: Azure Application Gateway
description: Azure Portal를 사용 하 여 종단 간 SSL 암호화를 사용 하는 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075134"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>포털에서 Application Gateway를 사용 하 여 종단 간 SSL 구성

이 문서에서는 Azure Portal를 사용 하 여 Azure 애플리케이션 Gateway v1 SKU를 통해 종단 간 SSL(Secure Sockets Layer) (SSL) 암호화를 구성 하는 방법을 설명 합니다.

> [!NOTE]
> Application Gateway v2 SKU에는 종단 간 구성을 사용 하기 위해 신뢰할 수 있는 루트 인증서가 필요 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

Application gateway를 사용 하 여 종단 간 SSL을 구성 하려면 게이트웨이에 대한 인증서가 필요 합니다. 백 엔드 서버에도 인증서가 필요 합니다. 게이트웨이 인증서는 SSL 프로토콜 사양과 호환 되는 대칭 키를 파생 하는 데 사용 됩니다. 대칭 키는 게이트웨이로 전송 된 트래픽을 암호화 하 고 암호 해독 하는 데 사용 됩니다. 

종단 간 SSL 암호화의 경우 응용 프로그램 게이트웨이에서 올바른 백 엔드 서버를 허용 해야 합니다. 이 액세스를 허용 하려면 응용 프로그램 게이트웨이에 대한 인증 인증서 (v1) 또는 신뢰할 수 있는 루트 인증서 (v2) 라고도 하는 백 엔드 서버의 공용 인증서를 업로드 합니다. 인증서를 추가 하면 application gateway가 알려진 백 엔드 인스턴스만 통신 합니다. 이 구성은 종단 간 통신의 보안을 강화 합니다.

자세히 알아보려면 [ssl 종료 및 종단 간 ssl](https://docs.microsoft.com/azure/application-gateway/ssl-overview)을 참조 하세요.

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>종단 간 SSL을 사용 하 여 새 응용 프로그램 게이트웨이 만들기

종단 간 SSL 암호화를 사용 하 여 새 응용 프로그램 게이트웨이를 만들려면 먼저 새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료를 사용 하도록 설정 해야 합니다. 이 작업을 통해 클라이언트와 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화를 사용할 수 있습니다. 그런 다음 HTTP 설정에서 백 엔드 서버에 대한 인증서를 안전한 받는 사람 목록에 배치 해야 합니다. 이 구성을 통해 응용 프로그램 게이트웨이와 백 엔드 서버 간 통신에 SSL 암호화를 사용할 수 있습니다. 종단 간 SSL 암호화를 수행 합니다.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료를 사용 하도록 설정

자세히 알아보려면 [새 응용 프로그램 게이트웨이를 만드는 동안 SSL 종료 사용](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)을 참조 하세요.

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/루트 인증서 추가

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택 합니다. 응용 프로그램 게이트웨이를 만들 때 Azure에서 기본 HTTP 설정인 **appGatewayBackendHttpSettings**을 자동으로 만들었습니다. 

3. **AppGatewayBackendHttpSettings**를 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 창이 표시 됩니다.

5. **새로 만들기**를 선택합니다.

6. **이름** 필드에 적절 한 이름을 입력 합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택 합니다.

   표준 및 WAF (v1) 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드 해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 루트 인증서를 .cer 형식으로 업로드 해야 합니다. 잘 알려진 ca (인증 기관)에서 백 엔드 인증서를 발급 한 경우에는 **잘 알려진 Ca 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서 추가](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![루트 인증서](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **저장**을 선택합니다.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 대한 종단 간 SSL 사용

종단 간 SSL 암호화를 사용 하 여 기존 응용 프로그램 게이트웨이를 구성 하려면 먼저 수신기에서 SSL 종료를 사용 하도록 설정 해야 합니다. 이 작업을 통해 클라이언트와 응용 프로그램 게이트웨이 간의 통신에 SSL 암호화를 사용할 수 있습니다. 그런 다음, 안전한 받는 사람 목록의 HTTP 설정에 백 엔드 서버용 인증서를 저장 합니다. 이 구성을 통해 응용 프로그램 게이트웨이와 백 엔드 서버 간 통신에 SSL 암호화를 사용할 수 있습니다. 종단 간 SSL 암호화를 수행 합니다.

SSL 종료를 사용 하도록 설정 하려면 HTTPS 프로토콜과 인증서를 사용 하 여 수신기를 사용 해야 합니다. 이러한 조건을 충족 하는 기존 수신기를 사용 하거나 새 수신기를 만들 수 있습니다. 이전 옵션을 선택 하는 경우 다음 "기존 응용 프로그램 게이트웨이에서 SSL 종료 사용" 섹션을 무시 하 고 "백 엔드 서버에 대한 인증/신뢰할 수 있는 루트 인증서 추가" 섹션으로 바로 이동할 수 있습니다.

후자 옵션을 선택 하는 경우 다음 절차의 단계를 적용 합니다.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에서 SSL 종료를 사용 하도록 설정

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **수신기** 를 선택 합니다.

3. 요구 사항에 따라 **기본** 또는 **다중 사이트** 수신기 중 하나를 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **인증서** 에 대한 창이 표시 됩니다.

5. 클라이언트와 응용 프로그램 게이트웨이 간의 SSL 종료에 사용 하려는 PFX 인증서를 업로드 합니다.

   > [!NOTE]
   > 테스트 목적으로 자체 서명 된 인증서를 사용할 수 있습니다. 그러나이 방법은 관리가 어렵고 완전히 안전 하지 않으므로 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [자체 서명 된 인증서 만들기](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)를 참조 하세요.

6. 요구 사항에 따라 **수신기**에 대한 다른 필수 설정을 추가 합니다.

7. **확인**을 선택하여 저장합니다.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/신뢰할 수 있는 루트 인증서 추가

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택 합니다. 안전한 받는 사람 목록의 기존 백 엔드 HTTP 설정에 인증서를 추가 하거나 새 HTTP 설정을 만들 수 있습니다. (다음 단계에서는 기본 HTTP 설정인 **appGatewayBackendHttpSettings**에 대한 인증서가 안전한 받는 사람 목록에 추가 됩니다.)

3. **AppGatewayBackendHttpSettings**를 선택 합니다.

4. **프로토콜**에서 **HTTPS**를 선택 합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 창이 표시 됩니다. 

5. **새로 만들기**를 선택합니다.

6. **이름** 필드에 적절 한 이름을 입력 합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택 합니다.

   표준 및 WAF (v1) 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드 해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 및 WAF_v2 응용 프로그램 게이트웨이의 경우 백 엔드 서버 인증서의 루트 인증서를 .cer 형식으로 업로드 해야 합니다. 잘 알려진 CA에서 백 엔드 인증서를 발급 한 경우 **잘 알려진 Ca 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서 추가](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
