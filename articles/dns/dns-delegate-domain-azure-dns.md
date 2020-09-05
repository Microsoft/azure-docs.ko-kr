---
title: '자습서: 도메인 및 하위 도메인 호스트 - Azure DNS'
description: 이 문서에서는 DNS 영역을 호스팅하도록 Azure DNS를 구성하는 방법에 대해 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: 8f29a2bbe0eb392927dd111b13e2260111ddd18e
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84710136"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>자습서: Azure DNS에서 도메인 호스트

Azure DNS를 사용하여 DNS 도메인을 호스트하고 DNS 레코드를 관리할 수 있습니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

도메인 이름 등록 기관에서 contoso.net 도메인을 구입한 후 Azure DNS에서 이름이 contoso.net인 영역을 만든다고 가정해 봅시다. 여러분이 도메인 소유자이므로 등록 기관에서 도메인의 이름 서버(NS) 레코드를 구성하는 옵션을 제공합니다. 등록 기관은 NS 레코드를 .net 부모 영역에 저장합니다. 그러면 전 세계의 인터넷 사용자가 contoso.net의 DNS 레코드를 확인하려고 시도하는 경우 Azure DNS 영역의 도메인으로 리디렉션됩니다.


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * DNS 영역을 만듭니다.
> * 이름 서버 목록을 검색합니다.
> * 도메인을 위임합니다.
> * 위임이 작동하는지 확인합니다.


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure DNS에서 호스트할 수 있는 도메인 이름으로 테스트할 수 있어야 합니다. 이 도메인에 대한 전체 제어 권한이 있어야 합니다. 전체 제어 권한에는 도메인의 NS(이름 서버) 레코드를 설정하는 권한이 포함됩니다.

이 자습서에 사용되는 예제 도메인은 contoso.net이지만 사용자 고유의 도메인 이름을 사용하세요.

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

1. [Azure Portal](https://portal.azure.com/)로 이동하여 DNS 영역을 만듭니다. **DNS 영역**을 검색하여 선택합니다.

   ![DNS 영역](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. **DNS 영역 만들기**를 선택합니다.
1. **DNS 영역 만들기** 페이지에서 다음 값을 입력한 다음 **만들기**를 선택합니다.

   | **설정** | **값** | **세부 정보** |
   |---|---|---|
   |**이름**|[해당 도메인 이름] |구입한 도메인 이름입니다. 예를 들어 이 자습서에서는 contoso.net을 사용합니다.|
   |**구독**|[구독 이름]|영역을 만들 구독을 선택합니다.|
   |**리소스 그룹**|**새로 만들기:** contosoRG|리소스 그룹을 만듭니다. 리소스 그룹 이름은 선택한 구독 내에서 고유해야 합니다.<br>리소스 그룹의 위치는 DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "글로벌"이므로 표시되지 않습니다.|
   |**위치**|미국 동부||

## <a name="retrieve-name-servers"></a>이름 서버 검색

DNS 영역을 Azure DNS에 위임하려면 해당 영역에 대한 이름 서버를 알아야 합니다. Azure DNS는 영역이 만들어질 때마다 풀에서 이름 서버를 할당합니다.

1. DNS 영역을 만든 후 Azure Portal의 **즐겨찾기** 창에서 **모든 리소스**를 선택합니다. **모든 리소스** 페이지에서 DNS 영역을 선택합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 **이름 기준으로 필터링** 상자에 도메인 이름을 입력하면 애플리케이션 게이트웨이에 쉽게 액세스할 수 있습니다. 

1. DNS 영역 페이지에서 이름 서버를 검색합니다. 이 예제에서는 contoso.com 영역에 이름 서버 *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* 및 *ns4-01.azure-dns.info*가 할당되었습니다.

   ![이름 서버 목록](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS는 할당된 이름 서버에 대한 영역에 권한이 있는 NS 레코드를 자동으로 만듭니다.

## <a name="delegate-the-domain"></a>도메인 위임

DNS 영역을 만들었고 이름 서버를 확보했으니, 이제 Azure DNS 이름 서버를 사용하여 부모 도메인을 업데이트해야 합니다. 각 등록 기관에는 도메인에 대한 이름 서버 레코드를 변경하는 자체 DNS 관리 도구가 있습니다. 

1. 등록 기관의 DNS 관리 페이지에서 NS 레코드를 편집하고 NS 레코드를 Azure DNS 이름 서버로 바꿉니다.

1. Azure DNS에 도메인을 위임하는 경우 Azure DNS에서 제공하는 이름 서버를 사용해야 합니다. 도메인 이름에 상관 없이 4개의 이름 서버를 모두 사용합니다. 도메인 위임에는 같은 최상위 도메인을 도메인으로 사용하는 이름 서버가 필요하지 않습니다.

> [!NOTE]
> 각 이름 서버 주소를 복사하면 주소 끝에 마침표를 복사해야 합니다. 마침표는 정규화된 도메인 이름의 끝을 나타냅니다. 일부 등록 기관에서는 DNS 이름 끝에 마침표가 없는 경우 이를 추가합니다. DNS RFC를 준수하려면 후행 마침표를 포함합니다.

고유 영역에서 이름 서버를 사용하는 위임(*베니티 이름 서버*라고도 함)은 현재 Azure DNS에서 지원되지 않습니다.

## <a name="verify-the-delegation"></a>위임 확인

위임을 완료한 후에는 *nslookup* 같은 도구를 통해 정상적으로 작동하는지 확인하여 영역에 대한 SOA(권한 시작) 레코드를 쿼리할 수 있습니다. SOA 레코드는 영역을 만들 때 자동으로 생성됩니다. 위임을 완료한 후 정상적으로 작동하는지 확인할 수 있을 때까지 10분 이상 기다려야 할 수도 있습니다. 변경 내용이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다.

Azure DNS 이름 서버를 지정할 필요가 없습니다. 위임이 올바르게 설치되면 일반 DNS 확인 프로세스는 자동으로 이름 서버를 찾습니다.

1. 명령 프롬프트에서 다음 예제와 비슷한 nslookup 명령을 입력합니다.

   ```
   nslookup -type=SOA contoso.net
   ```

1. 응답이 다음의 nslookup 출력과 비슷하게 표시되는지 확인합니다.

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>리소스 정리

그 다음 자습서를 계속 진행하려는 분들은 **contosoRG** 리소스 그룹을 그대로 유지하시면 됩니다. 그렇지 않으면 **contosoRG** 리소스 그룹을 삭제하여 이 자습서에서 만든 리소스를 삭제하세요.

- **contosoRG** 리소스 그룹을 선택한 다음, **리소스 그룹 삭제**를 선택합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 도메인의 DNS 영역을 만들어 Azure DNS에 위임했습니다. Azure DNS 및 웹앱에 대해 자세히 알아보려면 웹앱에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)