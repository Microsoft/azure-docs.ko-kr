---
title: 루트 또는 apex 도메인을 기존 전방 도어에 등록-Azure Portal
description: Azure Portal를 사용 하 여 기존 Front 문에 root 또는 apex 도메인을 등록 하는 방법에 대해 알아봅니다.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184609"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>프런트 도어에서 루트 또는 apex 도메인 등록
Azure 전면 도어는 CNAME 레코드를 사용 하 여 사용자 지정 도메인의 등록을 위한 도메인 소유권의 유효성을 검사 합니다. 또한 Front 도어는 프런트 도어 프로필에 연결 된 프런트 엔드 IP 주소를 노출 하지 않으므로 apex 도메인을 IP 주소에 매핑할 수 없습니다 .이를 Azure Front 도어에 등록 하는 것입니다.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어 도메인을 `contoso.com`하는 경우 `somelabel.contoso.com`에 대한 CNAME 레코드를 만들 수 있습니다. 하지만 `contoso.com` 자체에 대해서는 CNAME을 만들 수 없습니다. 이 제한은 Azure Front 도어 뒤에 부하가 분산 된 응용 프로그램을 포함 하는 응용 프로그램 소유자에 게 문제를 제공 합니다. 앞 도어 프로필을 사용 하려면 CNAME 레코드를 만들어야 하므로 apex 영역에서 전방 도어 프로필을 가리킬 수 없습니다.

이 문제는 Azure DNS에 대한 별칭 레코드를 사용 하 여 해결 됩니다. CNAME 레코드와 달리 별칭 레코드는 영역 apex에서 만들어지며 응용 프로그램 소유자는이 레코드를 사용 하 여 영역 apex 레코드를 공용 엔드포인트이 있는 프런트 도어 프로필로 가리킬 수 있습니다. 응용 프로그램 소유자는 DNS 영역 내의 다른 도메인에 사용 되는 것과 동일한 Front 도어 프로필을 가리킵니다. 예를 들어 `contoso.com` 및 `www.contoso.com`는 동일한 Front 도어 프로필을 가리킬 수 있습니다. 

Apex 또는 루트 도메인을 프런트 도어 프로필에 매핑하면 기본적으로 CNAME 평면화 또는 DNS 추적이 필요 합니다 .이는 DNS 공급자가 IP 주소에 도달할 때까지 CNAME 항목을 재귀적으로 확인 하는 메커니즘입니다. 이 기능은 전방 도어 엔드포인트에 대한 Azure DNS에서 지원 됩니다. 

> [!NOTE]
> CNAME 평면화 또는 DNS 추적을 지 원하는 다른 DNS 공급자도 있습니다. 그러나 Azure Front 도어가 고객에 게 도메인을 호스트 하는 데 Azure DNS를 사용 하는 것이 좋습니다.

Azure Portal를 사용 하 여 Front 문에 apex 도메인을 등록 하 고 SSL 종료를 위한 인증서와 연결 하 여 HTTPS를 사용 하도록 설정할 수 있습니다. Apex 도메인은 root 또는 naked 도메인도 불립니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 프런트 도어 프로필을 가리키는 별칭 레코드 만들기
> * 프런트 도어에 루트 도메인 추가
> * 루트 도메인에서 HTTPS 설정

> [!NOTE]
> 이 자습서를 사용 하려면 이미 프런트 도어 프로필을 만들어야 합니다. 시작 하기 위해 [빠른 시작: 프런트 도어 만들기](./quickstart-create-front-door.md) 또는 [HTTP에서 HTTPS로의 프런트 도어 만들기](./front-door-how-to-redirect-https.md) 와 같은 다른 자습서를 참조 하세요.

## <a name="create-an-alias-record-for-zone-apex"></a>영역 apex에 대한 별칭 레코드 만들기

1. 등록 도메인에 대한 **Azure DNS** 구성을 엽니다.
2. Apex 영역에 대한 레코드를 만들거나 편집 합니다.
3. 레코드 **유형** _으로 레코드를 선택_ 하 고 **별칭 레코드 집합**에 대해 _예_ 를 선택 합니다. **별칭 유형은** _Azure 리소스_로 설정 되어야 합니다.
4. 프런트 도어 프로필이 호스트 되는 Azure 구독을 선택 하 고 **azure 리소스** 드롭다운에서 front 도어 리소스를 선택 합니다.
5. **확인** 을 클릭 하 여 변경 내용을 제출 합니다.

    ![영역 apex에 대한 별칭 레코드](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 위의 단계에서는 프런트 도어 리소스를 가리키는 영역 apex 레코드를 만들고, 전면 도어 프로필에 도메인을 등록 하는 데 사용 되는 `afdverify.<name>.azurefd.net`에 대한 CNAME 레코드 매핑 ' afdverify ' (예: `afdverify.contosonews.com`)도 만듭니다.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Front 문에 사용자 지정 도메인 등록

1. 프런트 도어 디자이너 탭의 프런트 엔드 호스트 섹션에서 ' + ' 아이콘을 클릭 하 여 새 사용자 지정 도메인을 추가 합니다.
2. Apex 사용자 지정 호스트 이름 필드에 root 또는 도메인 이름을 입력 합니다 (예: `contosonews.com`).
3. 도메인에서 프런트 도어로의 CNAME 매핑을 확인 한 후 **추가** 를 클릭 하 여 사용자 지정 도메인을 추가 합니다.
4. **저장** 을 클릭 하 여 변경 내용을 제출 합니다.

![사용자 지정 도메인 메뉴](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS를 사용 하도록 설정

1. 추가 된 사용자 지정 도메인을 클릭 하 고 **사용자 지정 도메인 HTTPS**섹션에서 상태를 **사용**으로 변경 합니다.
2. **인증서 관리 유형을** _' 자체 인증서 사용 '_ 으로 선택 합니다.

> [!WARNING]
> Apex 또는 루트 도메인에 대해 현재 Front 도어 관리 인증서 관리 유형이 지원 되지 않습니다. Apex 또는 루트 도메인에서 HTTPS를 사용 하도록 설정 하는 데 사용할 수 있는 유일한 옵션은 Azure Key Vault에서 호스트 되는 사용자 지정 SSL 인증서를 사용 하는 것입니다.

3. 다음 단계로 진행 하기 전에 UI에 명시 된 대로 주요 자격 증명 모음에 액세스할 수 있도록 Front 도어에 올바른 권한을 설정 했는지 확인 합니다.
4. 현재 구독에서 **Key Vault 계정을** 선택한 다음 적절 한 **암호** 및 **암호 버전** 을 선택 하 여 올바른 인증서에 매핑합니다.
5. **업데이트** 를 클릭 하 여 선택 내용을 저장 한 다음 **저장**을 클릭 합니다.
6. 몇 분 후에 **새로 고침** 을 클릭 한 다음 사용자 지정 도메인을 다시 클릭 하 여 인증서 프로 비전의 진행 상황을 확인 합니다. 

> [!WARNING]
> Apex 도메인에 대한 적절 한 라우팅 규칙을 만들거나 기존 라우팅 규칙에 도메인을 추가 했는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door 작동 방식](front-door-routing-architecture.md)에 대해 알아보세요.