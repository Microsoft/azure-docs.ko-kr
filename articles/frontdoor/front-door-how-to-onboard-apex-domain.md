---
title: 온 보 딩 루트 또는 루트 도메인을 Azure portal을 사용 하는 기존 프런트 도어
description: 에 대해 알아봅니다 어떻게 등록 하려면 Azure portal을 사용 하는 기존 프런트 도어 루트 또는 루트 도메인을.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 464c38b0ece274d2ea7df89ab9fd7c0a60dc4b58
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332815"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>등록 하면 첫 번째 관문에서 루트 또는 루트 도메인
Azure 프런트 도어 CNAME 레코드를 사용 하 여 온 보 딩을 사용자 지정 도메인에 대 한 도메인 소유권을 확인 합니다. 또한 첫 번째 관문에서 프런트 도어 프로필과 연결 된 프런트 엔드 IP 주소를 노출 하지 않습니다 하 고 따라서 매핑할 수 없습니다 루트 도메인을 IP 주소로 인 경우 의도 온 보 딩이 Azure 프런트 도어를.

DNS 프로토콜은 영역 루트에서 CNAME 레코드가 할당되는 것을 방지합니다. 예를 들어, 도메인이 `contoso.com`;에 대 한 CNAME 레코드를 만들 수 있습니다 `somelabel.contoso.com`;에 대 한 CNAME을 만들 수 없습니다 있지만 `contoso.com` 자체입니다. 이 제한은 부하 분산 된 응용 프로그램 Azure 프런트 도어 뒤에 있는 응용 프로그램 소유자에 대 한 문제를 표시 합니다. 첫 번째 관문 프로필을 사용 하 여 CNAME 레코드를 만들 필요를 하므로 영역 루트의 첫 번째 관문 프로필을 가리키도록 수는 없습니다.

Azure DNS에서 별칭 레코드를 사용 하 여이 문제는 해결 됩니다. CNAME 레코드를 달리 별칭 레코드 영역 apex에 만들어지고 응용 프로그램 소유자가 해당 영역의 apex 레코드에 공개 끝점이 포함 된 첫 번째 관문 프로필을 가리키도록에 사용할 수 있습니다. 응용 프로그램 소유자가 해당 DNS 영역 내에서 다른 도메인에 사용 되는 동일한 프런트 도어 프로필을 가리킵니다. 예를 들어 `contoso.com` 고 `www.contoso.com` 동일한 첫 번째 관문 프로필을 가리킬 수 있습니다. 

기본적으로 첫 번째 관문 프로필에 루트 또는 루트 도메인을 매핑하는 CNAME을 평면화 하거나 DNS를 추적 하는 메커니즘을 설정 하는 DNS에서 공급자를 재귀적으로 확인 하는 CNAME 항목이 IP 주소에 도달할 때까지는 필요 합니다. 이 기능은 프런트 도어 끝점에 대 한 Azure DNS에서 지원 됩니다. 

> [!NOTE]
> 하지만 다른 DNS 공급자도 평면화 하는 CNAME을 지 원하는 기능은 DNS 추적 Azure 프런트 도어 권장 고객을 위해 Azure DNS를 사용 하 여 해당 도메인을 호스트 하는 것에 대 한 합니다.

에 첫 번째 관문에서 루트 도메인을 등록 하려면 Azure portal을 사용 하 고에 SSL 종료를 위한 인증서를 사용 하 여 연결 하 여 HTTPS를 사용할 수 있습니다. 루트 도메인은 또한 루트 또는 naked 도메인 이라고 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 첫 번째 관문 프로필을 가리키는 별칭 레코드 만들기
> * 첫 번째 관문을 루트 도메인을 추가 합니다.
> * 루트 도메인에서 HTTPS를 설정 합니다.

> [!NOTE]
> 이 자습서에서는 이미 있다고 프런트 도어 프로필을 만드는데 합니다. 와 같은 다른 자습서를 참조 하세요. [빠른 시작: 만들 프런트 도어](./quickstart-create-front-door.md) 또는 [HTTP 및 HTTPS 간의 리디렉션으로 프런트 도어 만들기](./front-door-how-to-redirect-https.md) 시작 하려면.

## <a name="create-an-alias-record-for-zone-apex"></a>영역 apex에 대 한 별칭 레코드 만들기

1. 오픈 **Azure DNS** 도메인 등록을 구성 합니다.
2. 만들거나 영역 apex에 대 한 레코드를 편집 합니다.
3. 레코드 선택 **형식** 으로 _A_ 기록 하 고 선택한 _예_ 에 대 한 **별칭 레코드 집합**합니다. **별칭 형식** 로 설정 해야 _Azure 리소스_합니다.
4. 첫 번째 관문 프로필 호스팅되는 Azure 구독을 선택 하 고 첫 번째 관문에서 리소스를 선택 하 여 **Azure 리소스** 드롭다운 합니다.
5. 클릭 **확인** 여 변경 내용을 제출 합니다.

    ![영역 apex에 대 한 별칭 레코드](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 위의 단계 프런트 도어 리소스 및 CNAME 레코드 매핑 'afdverify'를 가리키는 영역 apex 레코드를 만듭니다 (예제- `afdverify.contosonews.com`)를 `afdverify.<name>.azurefd.net` 되는 첫 번째 관문 프로필의 도메인을 온 보 딩에 사용 합니다.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>등록 하면 첫 번째 관문에서 사용자 지정 도메인

1. 첫 번째 관문 디자이너 탭 클릭 ' +' 아이콘 프런트 엔드 호스트에 새 사용자 지정 도메인을 추가 하려면 섹션입니다.
2. 사용자 지정 호스트 이름 필드에서 루트 또는 루트 도메인 이름을 입력 예제 `contosonews.com`합니다.
3. 클릭 하 여 첫 번째 관문에서 도메인 CNAME 매핑 유효성이 검사 되 면 **추가** 사용자 지정 도메인을 추가 합니다.
4. 클릭 **저장할** 변경 내용을 제출 합니다.

![사용자 지정 도메인 메뉴](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS를 사용 하도록 설정

1. 추가 된 사용자 지정 도메인에 섹션에서 클릭 **사용자 지정 도메인 HTTPS**, 상태를 변경 **Enabled**합니다.
2. 선택 합니다 **인증서 관리 유형** 하 _나만의 인증서 사용'_ 합니다.

> [!WARNING]
> 프런트 도어 관리 되는 인증서 관리 유형에 루트 또는 루트 도메인에 대 한 현재 지원 되지 않습니다. 첫 번째 관문에 대 한 루트 또는 루트 도메인에서 HTTPS를 활성화할 수 있는 유일한 옵션에는 Azure Key Vault에 호스팅된 사용자 고유의 사용자 지정 SSL 인증서를 사용 합니다.

3. 설정한 다음 단계를 진행 하기 전에 ui에서 설명 했 듯이 자격 증명 모음 키에 액세스 하는 출발점이 올바른 권한을 확인 합니다.
4. 선택는 **Key Vault 계정** 현재 구독에서 적절 한 선택 **비밀** 하 고 **비밀 버전** 올바른 인증서에 매핑할.
5. 클릭할 **업데이트** 선택 항목을 저장 한 다음 클릭 **저장**합니다.
6. 클릭 **새로 고침** 후 몇 분이 고 사용자 지정 도메인 인증서 프로 비전의 진행 상태를 다시 클릭 합니다. 

> [!WARNING]
> 루트 도메인에 대 한 적절 한 라우팅 규칙을 생성 하거나 기존 라우팅 규칙에 도메인 추가 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.