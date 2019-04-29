---
title: Azure Application Gateway 사용자 지정 오류 페이지 만들기
description: 이 문서는 Application Gateway 사용자 지정 오류 페이지를 작성하는 방법을 보여 줍니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 02/14/2019
ms.date: 02/26/2019
ms.author: v-junlch
ms.openlocfilehash: abfe33ff679bef125d9bf5b78e1790a1a4c64863
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832037"
---
# <a name="create-application-gateway-custom-error-pages"></a>Application Gateway 사용자 지정 오류 페이지 만들기

Application Gateway를 사용하면 기본 오류 페이지를 표시하는 대신 사용자 지정 오류 페이지를 만들 수 있습니다. 사용자 지정 오류 페이지를 사용하여 자체 브랜딩과 레이아웃을 사용할 수 있습니다.

예를 들어, 웹 애플리케이션에 연결할 수 없는 경우 고유한 유지 관리 페이지를 정의할 수 있습니다. 또는 악성 요청이 웹 애플리케이션으로 전송된 경우 권한이 없는 액세스 페이지를 작성할 수 있습니다.

사용자 지정 오류 페이지는 다음 두 가지 시나리오에 대해 지원됩니다.

- **유지 관리 페이지** - 502 잘못된 게이트웨이 페이지 대신에 이 사용자 지정 오류 페이지가 전송됩니다. Application Gateway에 트래픽을 라우팅할 백엔드가 없는 경우 표시됩니다. 예를 들어, 예약된 유지 관리가 있거나 예상치 못한 문제점이 백엔드 풀 액세스에 영향을 미치는 경우입니다.
- **승인되지 않은 액세스 페이지** - 403 권한이 없는 액세스 페이지 대신에 이 사용자 지정 오류 페이지가 전송됩니다. Application Gateway WAF가 악성 트래픽을 발견하고 이를 차단하는 경우 표시됩니다.

백엔드 서버에서 오류가 발생한 경우에는 수정되지 상태로 다시 호출자에게 전달됩니다. 사용자 지정 오류 페이지가 표시되지 않습니다. 요청이 백엔드에 도달할 수 없을 때 애플리케이션 게이트웨이가 사용자 지정 오류 페이지를 표시할 수 있습니다.

사용자 지정 오류 페이지는 전역 수준 및 수신기 수준에서 정의할 수 있습니다.

- **전역 수준** - 오류 페이지는 해당 애플리케이션 게이트웨이에 배치된 모든 웹 애플리케이션의 트래픽에 적용됩니다.
- **수신기 수준** - 해당 수신기에서 수신된 트래픽에 오류 페이지가 적용됩니다.
- **모두** - 수신기 수준에서 정의된 사용자 지정 오류 페이지가 전역 수준에서 하나의 세트를 대체합니다.

사용자 지정 오류 페이지를 작성하려면 다음이 있어야 합니다.
- HTTP 응답 상태 코드.
- 오류 페이지의 해당 위치. 
- 위치에 대해 공개적으로 액세스 가능한 Azure Storage Blob.
- * .htm 또는 *.html 확장자 유형. 

오류 페이지의 크기는 1MB 미만이어야 합니다. 이미지가 오류 페이지에 링크된 경우, 사용자 지정 오류 페이지에서 공용으로 액세스 가능한 절대 URL 또는 base64 인코드된 이미지 인라인이어야 합니다. 이미지가 동일한 Blob 위치에 있는 상대 링크는 현재 지원되지 않습니다. 

오류 페이지를 지정한 후에는 애플리케이션 게이트웨이가 저장소 Blob 위치에서 이를 다운로드하여 로컬 애플리케이션 게이트웨이 캐시에 저장합니다. 그런 다음, 오류 페이지는 애플리케이션 게이트웨이에서 직접 제공됩니다. 기존 사용자 지정 오류 페이지를 수정하려면 애플리케이션 게이트웨이 구성에서 다른 Blob 위치를 가리켜야 합니다. 애플리케이션 게이트웨이는 정기적으로 BLOB 위치를 검사하여 새 버전을 가져옵니다.

## <a name="portal-configuration"></a>포털 구성

1. 포털에서 Application Gateway를 탐색하고 애플리케이션 게이트웨이를 선택하세요.

    ![ag-overview](./media/custom-error/ag-overview.png)
2. **수신기**를 클릭하고 오류 페이지를 지정할 특정 수신기로 이동하세요.

    ![Application Gateway 수신기](./media/custom-error/ag-listener.png)
3. 수신기 수준에서 403 WAF 오류 또는 502 유지 관리 페이지에 대한 사용자 지정 오류 페이지를 구성합니다.

    > [!NOTE]
    > Azure Portal에서 전역 수준 사용자 지정 오류 페이지 만들기는 현재 지원되지 않습니다.

4. 지정된 오류 상태 코드에 대해 공개적으로 액세스 가능한 BLOB URL을 지정하고 저장**을 클릭하세요**. Application Gateway가 이제 사용자 지정 오류 페이지로 구성됩니다.

   ![Application Gateway 오류 코드](./media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell 구성

Azure PowerShell을 사용하여 사용자 지정 오류 페이지를 구성할 수 있습니다. 예를 들어, 글로벌 사용자 지정 오류 페이지:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

또는 수신기 수준 오류 페이지:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

자세한 내용은 [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) 및 [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Application Gateway 진단에 대한 자세한 내용은 [백엔드 건강, 진단 로그 및 Application Gateway에 대한 메트릭](application-gateway-diagnostics.md)을 참조하세요.

<!-- Update_Description: wording update -->
