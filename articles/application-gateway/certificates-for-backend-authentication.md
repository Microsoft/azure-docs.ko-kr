---
title: 백 엔드 서버를 허용 하는 데 필요한 인증서
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 애플리케이션 게이트웨이에서 백 엔드 인스턴스를 허용 하는 데 필요한 인증 인증서와 신뢰할 수 있는 루트 인증서로 SSL 인증서를 변환 하는 방법에 대한 예제를 제공 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 48944c513bd075e3859503fdadc4001261dc8c4a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048165"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Azure 애플리케이션 게이트웨이를 사용 하 여 백 엔드를 허용 하는 인증서 만들기

종단 간 SSL을 수행 하려면 인증/신뢰할 수 있는 루트 인증서를 업로드 하 여 백 엔드 인스턴스가 허용 되도록 Application Gateway 해야 합니다. V1 SKU의 경우 인증 인증서가 필요 하지만 v2 SKU의 경우 인증서를 허용 하는 데 신뢰할 수 있는 루트 인증서가 필요 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - 백 엔드 인증서에서 인증 인증서 내보내기 (v1 SKU의 경우)
> - 백 엔드 인증서에서 신뢰할 수 있는 루트 인증서 내보내기 (v2 SKU 용)

## <a name="prerequisites"></a>선행 조건

Application Gateway를 사용 하 여 백 엔드 인스턴스를 허용 하는 데 필요한 인증 인증서 또는 신뢰할 수 있는 루트 인증서를 생성 하려면 기존 백엔드 인증서가 필요 합니다. 백 엔드 인증서는 SSL 인증서와 동일 하거나 보안 강화를 위해 서로 다를 수 있습니다. Application Gateway는 SSL 인증서를 만들거나 구입 하는 메커니즘을 제공 하지 않습니다. 테스트를 위해 자체 서명 된 인증서를 만들 수 있지만 프로덕션 워크 로드에는 사용 하지 않아야 합니다. 

## <a name="export-authentication-certificate-for-v1-sku"></a>인증 인증서 내보내기 (v1 SKU의 경우)

Application Gateway v1 SKU의 백 엔드 인스턴스를 허용 하려면 인증 인증서가 필요 합니다. 인증 인증서는 Base-64로 인코딩된 x.509 ()에 있는 백 엔드 서버 인증서의 공개 키입니다. CER) 형식을 지정 합니다. 이 예제에서는 백 엔드 인증서에 SSL 인증서를 사용 하 고 인증 인증으로 사용할 공개 키를 내보냅니다. 또한이 예제에서는 Windows 인증서 관리자 도구를 사용 하 여 필요한 인증서를 내보냅니다. 편리한 다른 도구를 사용 하도록 선택할 수 있습니다.

SSL 인증서에서 공개 키 .cer 파일 (개인 키 아님)을 내보냅니다. 다음 단계는 Base-64로 인코딩된 .cer 파일을 내보내는 데 도움이 됩니다. x.509 (. 인증서에 대한 CER 형식:

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리**를 엽니다. 일반적으로 ' 인증서-현재 User\Personal\Certificates '에서 인증서를 찾아 마우스 오른쪽 단추로 클릭 합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr*을 입력합니다.

   ![내보내기](./media/certificates-for-backend-authentication/export.png)

2. 마법사에서 **다음**을 클릭합니다.

   ![인증서 내보내기](./media/certificates-for-backend-authentication/exportwizard.png)

3. **아니요, 프라이빗를 내보내지 않습니다.** 를 선택한 후, **다음**을 클릭합니다.

   ![아니요, 프라이빗 키를 내보내지 않습니다](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음**을 클릭합니다.

   ![Base-64로 인코딩됨](./media/certificates-for-backend-authentication/base64.png)

5. **내보낼 파일**에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후에 **다음**을 클릭합니다.

   ![찾아보기](./media/certificates-for-backend-authentication/browse.png)

6. **마침** 을 클릭하여 인증서를 내보냅니다.

   ![마침](./media/certificates-for-backend-authentication/finish.png)

7. 인증서가 성공적으로 내보내졌습니다.

   ![성공](./media/certificates-for-backend-authentication/success.png)

   내보낸 인증서는 다음과 비슷합니다.

   ![내보냄](./media/certificates-for-backend-authentication/exported.png)

8. 메모장을 사용하여 내보낸 인증서를 열면 이 예제와 비슷한 내용이 표시됩니다. 파란색의 섹션에는 application gateway에 업로드 되는 정보가 포함 되어 있습니다. 메모장을 사용 하 여 인증서를 열 때 이와 유사 하지 않은 것은 일반적으로 64로 인코딩된 Base-x.509 ()를 사용 하 여 내보내지 않는 것입니다. CER) 형식을 지정 합니다. 또한 다른 텍스트 편집기를 사용하려면 일부 편집자가 백그라운드에서 의도하지 않은 형식을 도입할 수도 있음을 이해해야 합니다. 이 경우 인증서의 텍스트를 Azure로 업로드할 때 문제가 발생할 수 있습니다.

   ![메모장에서 열기](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>신뢰할 수 있는 루트 인증서 내보내기 (v2 SKU 용)

Application gateway v2 SKU에서 백 엔드 인스턴스를 허용 목록 하려면 신뢰할 수 있는 루트 인증서가 필요 합니다. 루트 인증서는 Base-64로 인코딩된 x.509 (. CER) 백 엔드 서버 인증서의 루트 인증서를 형식으로 지정 합니다. 이 예제에서는 백 엔드 인증서에 SSL 인증서를 사용 하 고 해당 공개 키를 내보낸 다음 신뢰할 수 있는 CA의 루트 인증서를 공개 키에서 base64 인코딩 형식으로 내보내 신뢰할 수 있는 루트 인증서를 가져옵니다. 중간 인증서를 서버 인증서와 함께 사용 하 여 백 엔드 서버에 설치 해야 합니다.

다음 단계는 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 위의 **백 엔드 인증서 (V1 SKU)에서 인증 인증서 내보내기** 섹션에 언급 된 1-9 단계를 사용 하 여 백 엔드 인증서에서 공개 키를 내보냅니다.

2. 공개 키를 내보낸 후 파일을 엽니다.

   ![권한 부여 인증서 열기](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![인증서 정보](./media/certificates-for-backend-authentication/general.png)

3. 인증 기관을 보려면 인증 경로 보기로 이동 합니다.

   ![인증서 세부 정보](./media/certificates-for-backend-authentication/certdetails.png)

4. 루트 인증서를 선택 하 고 **인증서 보기**를 클릭 합니다.

   ![인증서 경로](./media/certificates-for-backend-authentication/rootcert.png)

   루트 인증서 세부 정보가 표시 됩니다.

   ![인증서 정보](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. **자세히** 보기로 이동 하 고 **파일에 복사** ...를 클릭 합니다.

   ![루트 인증서 복사](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 이 시점에서 백 엔드 인증서에서 루트 인증서의 세부 정보를 추출 했습니다. **인증서 내보내기 마법사**가 표시 됩니다. 이제 위의 **백 엔드 인증서 (V1 SKU)에서 인증 인증서 내보내기** 섹션에 언급 된 2-9 단계를 사용 하 여 Base-64로 인코딩된 x.509 (. CER) 형식을 지정 합니다.

## <a name="next-steps"></a>다음 단계

이제 Base-64로 인코딩된 인증 인증서/신뢰할 수 있는 루트 인증서가 x.509 (. CER) 형식을 지정 합니다. 종단 간 SSL 암호화를 위해 백엔드 서버를 허용 목록 응용 프로그램 게이트웨이에이를 추가할 수 있습니다. [종단 간 SSL 암호화를 구성 하는 방법을](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)참조 하세요.

