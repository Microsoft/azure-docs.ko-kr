---
title: 사용자 지정 프로브 만들기 - Azure Application Gateway - Azure Portal | Microsoft Docs
description: 포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브를 만드는 방법을 알아봅니다.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122390"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

이 문서에서는 Azure Portal을 통해 기존 애플리케이션 게이트웨이에 사용자 지정 프로브를 추가합니다. 사용자 지정 프로브는 특정 상태 확인 페이지를 사용하는 애플리케이션이나 기본 웹 애플리케이션에서 성공적으로 응답을 제공하지 않는 애플리케이션에 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway 아직 없는 경우 방문 [Application Gateway 만들기](application-gateway-create-gateway-portal.md)를 참조하여 사용할 Application Gateway를 만듭니다.

## <a name="createprobe"></a>프로브 만들기

프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계는 프로브를 만드는 것입니다. 두 번째 단계로 애플리케이션 게이트웨이의 백 엔드 http 설정에 프로브를 추가합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 평가판](https://azure.microsoft.com/free)에 등록할 수 있습니다.

1. Azure Portal의 [즐겨찾기] 창에서 [모든 리소스]를 클릭합니다. [모든 리소스] 블레이드에서 애플리케이션 게이트웨이를 클릭합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 partners.contoso.net을 입력합니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

1. **프로브**를 클릭한 다음 **추가** 단추를 클릭하여 프로브를 추가합니다.

   ![정보가 입력된 프로브 추가 블레이드][1]

1. **상태 프로브 추가** 블레이드에서 프로브에 필요한 정보를 입력하고, 완료되면 **확인**을 클릭합니다.

   |**설정** | **값** | **세부 정보**|
   |---|---|---|
   |**Name**|customProbe|포털에서 액세스할 수 있는 프로브의 이름입니다.|
   |**프로토콜**|HTTP 또는 HTTPS | 상태 프로브에서 사용하는 프로토콜입니다.|
   |**호스트**|예: contoso.com|프로브에 사용되는 호스트 이름입니다. 다중 사이트를 Application Gateway에 구성하는 경우에만 적용할 수 있습니다. 그렇지 않으면 '127.0.0.1'을 사용합니다. 이 값은 VM 호스트 이름과 다릅니다.|
   |**Path**|/ 또는 다른 경로|사용자 지정 프로브의 전체 url 중 나머지 부분입니다. 유효한 경로는 '/'로 시작합니다. Http의 기본 경로:\//contoso.com 삼아 '/' |
   |**간격(초)**|30|상태를 확인하기 위해 프로브가 실행되는 주기입니다. 30초 이하 값을 설정하는 것은 권장되지 않습니다.|
   |**시간 제한(초)**|30|프로브에서 시간 초과되기 전에 대기하는 시간입니다. 시간 제한 간격은 http 호출이 백 엔드 상태 페이지를 사용하도록 설정할 수 있을만큼 충분히 높아야 합니다.|
   |**비정상 임계값**|3|비정상으로 간주되는 실패한 시도 횟수입니다. 임계값이 0이면 상태 확인에 실패한 경우 백 엔드는 즉시 비정상인 것으로 결정됩니다.|

   > [!IMPORTANT]
   > 호스트 이름은 서버 이름과 같지 않습니다. 이 값은 애플리케이션 서버에서 실행 중인 가상 호스트의 이름입니다. 프로브는 http://(host name):(port from httpsetting)/urlPath로 전송됩니다.

## <a name="add-probe-to-the-gateway"></a>게이트웨이에 프로브 추가

프로브를 만들었으므로 이제 게이트웨이에 추가해야 합니다. 프로브 설정은 애플리케이션 게이트웨이의 백 엔드 http 설정에 지정됩니다.

1. 애플리케이션 게이트웨이에서 **HTTP 설정**을 클릭하여 구성 블레이드를 불러온 다음, 창에 나열된 현재 백 엔드 http 설정을 클릭합니다.

   ![https 설정 창][2]

1. **appGatewayBackEndHttpSettings** 설정 블레이드에서 **사용자 지정 프로브 사용** 확인란을 선택하고, 앞서의 [프로브 만들기](#createprobe) 섹션에서 만든 프로브를 **사용자 지정 프로브** 드롭다운에서 선택합니다.
   완료되면 **저장**을 클릭하여 해당 설정을 적용합니다.

기본 프로브는 웹 애플리케이션에 대한 기본 액세스를 확인합니다. 사용자 지정 프로브를 만들었으므로 애플리케이션 게이트웨이에서 정의된 사용자 지정 경로를 사용하여 선택된 백 엔드 서버의 상태를 모니터링합니다. 정의된 기준에 따라 애플리케이션 게이트웨이에서 프로브에 지정된 경로를 확인합니다. host:Port/path에 대한 호출에서 HTTP 200-399 상태 응답을 반환하지 않으면 비정상 임계값에 도달한 후에 서버가 회전에서 제외됩니다. 비정상 인스턴스에 대해 프로빙이 계속되며 다시 정상 상태가 되는지 확인합니다. 인스턴스가 정상 서버 풀에 다시 추가되면 트래픽이 다시 흐르기 시작하고 평소처럼 사용자가 지정한 간격으로 인스턴스에 대한 프로빙이 계속됩니다.

## <a name="next-steps"></a>다음 단계

Azure Application Gateway를 사용하여 SSL 오프로드를 구성하는 방법은 [SSL 오프로드 구성](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

