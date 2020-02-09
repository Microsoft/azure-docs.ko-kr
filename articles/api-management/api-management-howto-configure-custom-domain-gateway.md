---
title: 자체 호스팅 Azure API Management gateway에 대한 사용자 지정 도메인 이름 구성 | Microsoft Docs
description: 이 항목에서는 자체 호스팅 Azure API Management gateway에 대한 사용자 지정 도메인 이름을 구성 하는 단계에 대해 설명 합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513810"
---
# <a name="configure-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성

[자체 호스팅 Azure API Management gateway](self-hosted-gateway-overview.md) 를 프로 비전 할 때 호스트 이름이 할당 되지 않으며 해당 IP 주소에서 참조 해야 합니다. 이 문서에서는 자체 호스팅 게이트웨이의 기존 사용자 지정 DNS 이름 (호스트 이름이 라고도 함)을 매핑하는 방법을 보여 줍니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능은 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층 에서만 추가 비용 없이 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한 됩니다.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

-   활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management 인스턴스입니다. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
- 자체 호스팅 게이트웨이입니다. 자세한 내용은 [자체 호스팅 게이트웨이를 프로 비전 하는 방법](api-management-howto-provision-self-hosted-gateway.md) 을 참조 하세요.
-   사용자 또는 조직에서 소유 하 고 있는 사용자 지정 도메인 이름입니다. 이 항목에서는 사용자 지정 도메인 이름을 지정 하는 방법에 대한 지침을 제공 하지 않습니다.
-   사용자 지정 도메인 이름을 자체 호스팅 게이트웨이의 IP 주소에 매핑하는 DNS 서버에서 호스트 되는 DNS 레코드입니다. 이 항목에서는 DNS 레코드를 호스트 하는 방법에 대한 지침을 제공 하지 않습니다.
-   공개 및 프라이빗 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. 주체 또는 SAN (주체 대체 이름)은 도메인 이름과 일치 해야 합니다. 이렇게 하면 API Management 인스턴스가 SSL을 통해 Url을 안전 하 게 노출할 수 있습니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management 서비스에 사용자 지정 도메인 인증서 추가

1. **보안**에서 **인증서** 를 선택 합니다.
2. **+추가**를 선택합니다.
3. 인증서의 리소스 이름을 **Id** 필드에 입력 합니다.
4. 인증서를 포함 하는 파일을 선택 합니다. PFX)를 선택 하 여 **인증서** 필드 또는 옆에 있는 폴더 아이콘을 선택 합니다.
5. **암호** 필드에 인증서의 암호를 입력 합니다.
6. **만들기** 를 선택 하 여 API Management 서비스에 인증서를 추가 합니다.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Azure Portal를 사용 하 여 자체 호스팅 게이트웨이의 사용자 지정 도메인 이름 설정

1. **설정**에서 **게이트웨이** 를 선택 합니다.
2. 도메인 이름을 구성 하려는 자체 호스팅 게이트웨이를 선택 합니다.
3. **설정**에서 **호스트 이름** 을 선택 합니다.
4. **+ 추가** 를 선택 합니다.
5. **이름** 필드에 호스트 이름에 대한 리소스 이름을 입력 합니다.
6. **호스트** 이름 필드에 도메인 이름을 입력 합니다.
7. **인증서** 드롭다운에서 인증서를 선택 합니다.
8. 이 게이트웨이를 통해 노출 되는 Api가 클라이언트 인증서 인증을 사용 하는 경우 **클라이언트 인증서 협상** 확인란을 선택 합니다.
    > [!WARNING]
    > 이 설정은 게이트웨이에 대해 구성 된 모든 도메인 이름에 의해 공유 됩니다.
9. **추가** 를 선택 하 여 선택한 자체 호스팅 게이트웨이에 사용자 지정 도메인 이름을 할당 합니다.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
