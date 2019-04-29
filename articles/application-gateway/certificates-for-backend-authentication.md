---
title: Azure Application Gateway의 허용 목록 백 엔드에 필요한 인증서
description: 이 문서에서는 Azure Application Gateway의 백 엔드 인스턴스를 허용 목록에 필요한 인증 인증서를 신뢰할 수 있는 루트 인증서는 SSL 인증서를 변환할 수 하는 방법의 예제를 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831729"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Azure Application Gateway를 사용 하 여 허용 목록 백 엔드에 대 한 인증서 만들기

종단 간 SSL을 수행 하려면 application gateway에 백 엔드 인스턴스를 인증/신뢰할 수 있는 루트 인증서를 업로드 하 여 허용 목록에 추가할 필요 합니다. V1 SKU의 경우 인증 인증서가 필요한 v2 SKU의 경우 허용 목록에 대 한 루트 인증서는 신뢰할 수 있는. 반면 인증서

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - (V1 SKU)에 대 한 백 엔드 인증서에서 인증서를 내보내기
> - (V2 SKU)에 대 한 백 엔드 인증서에서 신뢰할 수 있는 루트 인증서 내보내기

## <a name="prerequisites"></a>필수 조건

인증 인증서 또는 응용 프로그램 게이트웨이와 백 엔드 인스턴스를 허용 목록에 대 한 필요한 신뢰할 수 있는 루트 인증서를 생성 하는 기존 백 엔드 인증서가 필요 합니다. 백 엔드 인증서는 SSL 인증서와 동일 하거나 다른 보안 강화를 위해 수 있습니다. 응용 프로그램 게이트웨이 메커니즘을 제공 하지 있습니다 만들거나 SSL 인증서를 구입 합니다. 테스트를 위해 자체 서명 된 인증서를 만들 수 있습니다 하지만 프로덕션 워크 로드 하지 사용 해야 합니다. 

## <a name="export-authentication-certificate-for-v1-sku"></a>(V1 SKU)에 대 한 인증 인증서를 내보내기

인증 인증서가 응용 프로그램 게이트웨이 v1의 백 엔드 인스턴스를 허용 목록에 추가 하는 데 필요한 SKU입니다. 인증 인증서는 Base-64에서 백 엔드 서버 인증서의 공개 키로 인코딩된 X.509 (. CER) 형식입니다. 이 예제에서는 백 엔드 인증서에 대 한 SSL 인증서를 사용 하 고 인증을 인증으로 사용할 해당 공개 키를 내보냅니다. 또한이 예제에서는 필요한 인증서를 내보내려면 Windows 인증서 관리자 도구를 사용는 했습니다. 편의 따라 다른 도구를 사용 하도록 선택할 수 있습니다.

SSL 인증서를에서 공개 키.cer 파일 (개인 키 아님)를 내보냅니다. 다음 단계는.cer를 내보내는 파일 Base-64에서로 인코딩된 X.509 (. 인증서의 CER) 형식:

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리**를 엽니다. 일반적으로 'Certificates-Current User\Personal\Certificates'에서의 인증서를 찾아 마우스 오른쪽 단추로 클릭 합니다. **모든 태스크**를 클릭한 다음 **내보내기**를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사**가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr*을 입력합니다.

   ![내보내기](./media/certificates-for-backend-authentication/export.png)

2. 마법사에서 **다음**을 클릭합니다.

   ![인증서 내보내기](./media/certificates-for-backend-authentication/exportwizard.png)

3. **아니요, 개인 키를 내보내지 않습니다.** 를 선택한 후 **다음**을 클릭합니다.

   ![아니요, 개인 키를 내보내지 않습니다](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음**을 클릭합니다.

   ![Base-64로 인코딩됨](./media/certificates-for-backend-authentication/base64.png)

5. **내보낼 파일**에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름**에는 인증서 파일의 이름을 입력합니다. 그런 후 **다음**을 클릭합니다.

   ![찾아보기](./media/certificates-for-backend-authentication/browse.png)

6. **마침** 을 클릭하여 인증서를 내보냅니다.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. 인증서가 성공적으로 내보내졌습니다.

   ![성공](./media/certificates-for-backend-authentication/success.png)

   내보낸 인증서는 다음과 비슷합니다.

   ![내보냄](./media/certificates-for-backend-authentication/exported.png)

8. 메모장을 사용하여 내보낸 인증서를 열면 이 예제와 비슷한 내용이 표시됩니다. 파란색 섹션에에서는 application gateway에 업로드 되는 정보를 포함 합니다. 메모장에서 인증서를 열었지만 이와 비슷하게 표시되지 않으면, 일반적으로 Base-64로 인코딩된 X.509(.CER) 형식을 사용하여 인증서를 내보내지 않았음을 의미합니다. 또한 다른 텍스트 편집기를 사용하려면 일부 편집자가 백그라운드에서 의도하지 않은 형식을 도입할 수도 있음을 이해해야 합니다. 이 경우 인증서의 텍스트를 Azure로 업로드할 때 문제가 발생할 수 있습니다.

   ![메모장에서 열기](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>(V2 SKU)에 대 한 신뢰할 수 있는 루트 인증서 내보내기

응용 프로그램 게이트웨이 v2의 백 엔드 인스턴스를 허용 목록에 추가 하는 데 필요한 루트 인증서가 신뢰할 수 있는 SKU입니다. 루트 인증서는 Base-64로 인코딩된 X.509 (. 백 엔드 서버 인증서의 CER) 형식으로 루트 인증서입니다. 이 예제에서는 백 엔드 인증서에 대 한 SSL 인증서를 사용 하 여, 해당 공개 키를 내보낼 하 고 신뢰할 수 있는 루트 인증서를 가져오는 base64로 인코딩된 형식으로 공개 키에서 신뢰할 수 있는 CA의 루트 인증서를 내보냅니다. 

다음 단계를 사용 하면 인증서의.cer 파일을 내보낼 수 있습니다.

1. 1-9 섹션에서 설명한 단계를 사용 하 여 **(v1 SKU)에 대 한 백 엔드 인증서에서 인증서 내보내기** 백 엔드 인증서의 공개 키를 내보내려면 위의 합니다.

2. 공개 키를 내보낸 후 파일을 엽니다.

   ![열기 권한 부여 인증서](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![인증서에 대 한](./media/certificates-for-backend-authentication/general.png)

3. 인증 기관 인증서 경로 뷰를 이동 합니다.

   ![인증서 세부 정보](./media/certificates-for-backend-authentication/certdetails.png)

4. 루트 인증서를 선택 하 고 클릭 **인증서 보기**합니다.

   ![인증서 경로](./media/certificates-for-backend-authentication/rootcert.png)

   루트 인증서 세부 정보를 볼 수 있어야 합니다.

   ![인증서 정보](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. 으로 이동 합니다 **세부 정보** 클릭 **파일에 복사 하는 중...**

   ![루트 인증서 복사](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 이 시점에서 압축을 푼 경우 루트 인증서의 세부 정보에서 백 엔드 인증서입니다. 표시 됩니다는 **인증서 내보내기 마법사**합니다. 이제 2-9 섹션에서 설명한 단계를 사용 하 여 **(v1 SKU)에 대 한 백 엔드 인증서에서 인증서 내보내기** 위에 내보낼 신뢰할 수 있는 루트 인증서에서 Base-64로 인코딩된 X.509 (. CER) 형식입니다.

## <a name="next-steps"></a>다음 단계

루트 인증서에서 Base-64로 인코딩된 X.509 인증서/신뢰할 수 있는 인증 이제 (합니다. CER) 형식입니다. 추가할 수 있습니다이 허용 목록에 application gateway에 종단 간 SSL 암호화에 대 한 백 엔드 서버. 참조 [종단 간 SSL 암호화를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)합니다.