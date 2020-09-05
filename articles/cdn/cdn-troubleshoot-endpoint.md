---
title: Azure CDN 끝점 문제 해결-404 상태 코드
description: 404 HTTP 응답 상태 코드를 반환 하는 Azure Content Delivery Network 끝점의 문제를 해결 하는 방법에 대해 알아봅니다.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192488"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404 상태 코드를 반환하는 Azure CDN 엔드포인트 문제 해결
이 문서에서는 404 HTTP 응답 상태 코드를 반환하는 Azure CDN(Content Delivery Network) 엔드포인트의 문제를 해결할 수 있습니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.

## <a name="symptom"></a>증상
CDN 프로필 및 엔드포인트을 만들었지만 콘텐츠를 CDN에서 사용할 수 없습니다. CDN URL을 통해 콘텐츠에 액세스하려는 사용자에게는 HTTP 404 상태 코드가 수신됩니다. 

## <a name="cause"></a>원인
몇 가지 가능한 원인은 다음과 같습니다.

* 파일 원본이 CDN에 표시되지 않음
* 엔드포인트가 잘못 구성되어 CDN이 엉뚱한 위치에 보임
* 호스트가 CDN의 호스트 헤더를 거부
* 엔드포인트이 CDN 전체에 전파될 시간이 부족했음

## <a name="troubleshooting-steps"></a>문제 해결 단계
> [!IMPORTANT]
> CDN 엔드포인트을 만든 후 등록이 CDN 네트워크 전체에 전파되는 데 시간이 걸리기 때문에 엔드포인트을 즉시 사용할 수는 없습니다.
> - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
> - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
> - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 90분 이내에 전파가 완료됩니다. 
> 
> 이 문서의 단계를 완료한 후에도 여전히 404 응답이 반환되면 몇 시간 정보 기다렸다가 다시 확인한 후 지원 티켓을 열어 보세요.
> 
> 

### <a name="check-the-origin-file"></a>원본 파일 확인
먼저 캐시할 파일을 원본 서버에서 사용할 수 있고 인터넷에서 공개적으로 액세스할 수 있는지 확인합니다. 이 작업을 수행하는 가장 빠른 방법은 프라이빗 또는 Incognito(시크릿) 세션에서 브라우저를 열고 해당 파일을 직접 찾아보는 것입니다. 주소 상자에 URL을 입력하거나 붙여넣고 예상하는 파일이 나오는지 확인합니다. 예를 들어 Azure Storage 계정에 https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt에서 액세스할 수 있는 파일이 있다고 가정합니다. 이 파일의 콘텐츠를 성공적으로 로드할 수 있으면 테스트를 통과한 것입니다.

![성공했습니다.](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> 이 방법은 파일이 공개적으로 사용 가능한지 확인하는 가장 빠르고 쉬운 방법이지만, 조직의 일부 네트워크 구성에 따라 이 파일이 살제 네트워크 사용자에게만 보이는 경우에도(Azure에서 호스팅되어 있는 경우도 마찬가지) 공개적으로 사용 가능한 것으로 보일 수 있습니다. 그렇게 되지 않으려면 조직의 네트워크에 연결되지 않은 모바일 디바이스나 Azure의 가상 컴퓨터처럼 외부 브라우저를 사용하여 파일을 테스트합니다.
> 
> 

### <a name="check-the-origin-settings"></a>원본 설정 확인
파일이 인터넷에서 공개적으로 사용할 수 있는 것을 확인한 후에는 원본 설정을 확인합니다. [Azure Portal](https://portal.azure.com)에서 CDN 프로필을 찾아서 문제를 해결하려는 엔드포인트을 선택합니다. 결과 **엔드포인트** 페이지에서 원본을 선택합니다.  

![원본이 강조 표시된 엔드포인트 페이지](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**원본** 페이지가 나타납니다. 

![원본 페이지](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>원본 유형 및 호스트 이름
**원본 유형** 및 **원본 호스트 이름**의 값이 올바른지 확인합니다. 이 예제 https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt에서 URL의 호스트 이름 부분은 *cdndocdemo.blob.core.windows.net*으로 이 부분이 올바른지 확인합니다. Azure Storage, 웹앱 및 클라우드 서비스 원본은 **원본 호스트 이름** 필드에 드롭다운 목록 값을 사용하므로 잘못된 철자는 문제가 되지 않습니다. 그러나 사용자 지정 원본을 사용하는 경우 호스트 이름의 철자가 올바른지 확인합니다.

#### <a name="http-and-https-ports"></a>HTTP 및 HTTPS 포트
**HTTP** 및 **HTTPS 포트**를 확인 합니다. 대부분의 경우 80 및 443이 올바르며, 포트를 변경할 필요가 없습니다.  그러나 원본 서버가 다른 포트에서 수신하는 경우 해당 사실을 여기에 나타내야 합니다. 확실하지 않은 경우 원본 파일의 URL을 확인합니다. HTTP 및 HTTPS 사양은 포트 80 및 443을 기본값으로 사용합니다. 예제 URL https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt에는 포트가 지정되지 않으므로 기본값인 443이 사용되며 이 설정은 올바른 것입니다.  

그러나 이전에 테스트한 원본 파일의 URL이 http:\//www.contoso.com:8080/file.txt라고 가정합니다. 호스트 이름 세그먼트 끝에 있는 *:8080* 부분에 유의합니다. 이 번호는 브라우저가 포트 8080을 사용 하 여 www contoso.com의 웹 서버에 연결 하도록 지시 \. 하므로 **HTTP 포트** 필드에 *8080* 를 입력 해야 합니다. 이러한 포트 설정은 엔드포인트가 원본에서 정보를 검색할 때 사용하는 포트에만 영향을 줍니다.

> [!NOTE]
> **Akamai의 Azure CDN 표준** 엔드포인트에서는 원본에 대해 전체 TCP 포트 범위를 허용하지 않습니다.  허용되지 않는 원본 포트 목록을 보려면 [Akamai 허용된 원본 포트의 Azure CDN](/previous-versions/azure/mt757337(v=azure.100))를 참조하세요.  
> 
> 

### <a name="check-the-endpoint-settings"></a>엔드포인트 설정 검사
**엔드포인트** 페이지에서 **구성** 단추를 선택합니다.

![구성 단추가 강조 표시된 엔드포인트 페이지](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

CDN 엔드포인트 **구성** 페이지가 나타납니다.

![구성 페이지](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>프로토콜
**프로토콜**의 경우 클라이언트에서 사용하는 프로토콜이 선택되었는지 확인합니다. 클라이언트에서 사용한 것과 동일한 프로토콜이 원본 액세스에 사용되므로 이전 섹션에서 원본 포트를 올바르게 구성하는 것이 중요합니다. CDN 엔드포인트는 원본 포트에 관계없이 기본 HTTP 및 HTTPS 포트(80 및 443)에서만 수신합니다.

가상의 예 http:\//www.contoso.com:8080/file.txt로 돌아가 봅시다.  기억하시겠지만 Contoso는 HTTP 포트로 *8080*을 지정했습니다. 거기에 덧붙여 HTTPS 포트로 *44300*을 지정했다고 가정해 봅시다.  *contoso*라는 엔드포인트을 만들었다면 CDN 엔드포인트 호스트 이름은 *contoso.azureedge.net*입니다.  http:\//contoso.azureedge.net/file.txt에 대한 요청은 HTTP 요청이므로 엔드포인트이 포트 8080에서 HTTP를 사용하여 원본에서 파일을 검색합니다.  HTTPS 통한 보안 요청 https:\//contoso.azureedge.net/file.txt는 엔드포인트이 포트 44300에서 HTTPS를 사용하여 원본에서 파일을 검색하게 만듭니다.

#### <a name="origin-host-header"></a>원본 호스트 헤더
**원본 호스트 헤더** 는 각 요청과 함께 원본으로 전송되는 호스트 헤더 값입니다.  대부분의 경우 앞에서 확인한 **원본 호스트 이름** 과 같아야 합니다.  이 필드의 값이 잘못되어도 일반적으로 404 상태가 발생하지 않지만 원본이 예상하는 것이 무엇인지에 따라 다른 4xx 상태가 발생할 가능성이 있습니다.

#### <a name="origin-path"></a>원본 경로
마지막으로 **원본 경로**를 확인해야 합니다.  기본적으로 원본 경로는 비어 있습니다.  CDN에서 사용 가능하게 만들려는 원본 호스트 리소스의 범위를 좁히려는 경우에만 이 필드를 사용해야 합니다.  

예제 엔드포인트에서는 스토리지 계정의 모든 리소스를 사용하게 만들기 위해 **원본 경로**를 비워 두었습니다.  즉, https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt에 대한 요청이 결국 엔드포인트에서 */publicblob/lorem.txt*를 요청하는 cdndocdemo.core.windows.net 간의 연결이 된다는 것입니다.  마찬가지로 https:\//cdndocdemo.azureedge.net/donotcache/status.png에 대한 요청은 원본에서 */donotcache/status.png*를 요청하는 엔드포인트이 됩니다.

그러나 원본의 모든 경로에 CDN을 사용하지 않으려면 어떻게 해야 할까요?  *publicblob* 경로만 노출하고 싶은 경우를 가정해 봅시다.  **원본 경로** 필드에 */publicblob*를 입력하면 엔드포인트이 원본에 대해 만들어지는 모든 요청 앞에 */publicblob*를 삽입하게 됩니다.  즉, https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt에 대한 요청에서 이제 실제로 URL의 요청 부분인 */publicblob/lorem.txt*를 사용하여 시작 부분에 */publicblob*을 추가한다는 것입니다. 이는 원본에서 */publicblob/publicblob/lorem.txt*에 대한 요청이 됩니다.  해당 경로가 실제 파일을 확인하지 못하면 원본이 404 상태를 반환합니다.  이 예에서 lorem.txt를 검색하는 올바른 URL은 실제로 https:\//cdndocdemo.azureedge.net/lorem.txt입니다.  이 예에서는 */publicblob* 경로를 전혀 포함하지 않았습니다. 왜냐하면 URL의 요청 부분이 */lorem.txt*인데 엔드포인트이 */publicblob*을 추가하면 원본에 전달되는 요청이 */publicblob/lorem.txt*가 되기 때문입니다.

