---
title: Azure API Management 인스턴스에 대한 사용자 지정 도메인 이름 구성 | Microsoft Docs
description: 이 항목에서는 Azure API Management 인스턴스에 대해 사용자 지정 도메인 이름을 구성하는 방법에 대해 설명합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: a771b437258046f937b97a9e37ffedbe0a17c1c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693611"
---
# <a name="configure-a-custom-domain-name"></a>사용자 지정 도메인 이름 구성 

APIM(API Management) 인스턴스를 만들 때 Azure에서는 azure-api.net의 하위 도메인에 이를 할당합니다(예: `apim-service-name.azure-api.net`). 그러나 **contoso.com** 등의 고유한 도메인 이름을 사용하여 APIM 엔드포인트를 공개할 수 있습니다. 이 자습서에서는 기존 사용자 지정 DNS 이름을 Azure API Management 인스턴스에 공개된 엔드포인트에 매핑하는 방법을 보여 줍니다.

> [!WARNING]
> 인증서 고정을 사용하여 애플리케이션의 보안을 개선하고자 하는 고객은 사용자 지정 도메인 이름 및 관리하는 인증서(기본 인증서 아님)를 사용해야 합니다. 대신 기본 인증서를 고정하는 고객은 자신이 제어하지 않는 인증서의 속성에 대한 강한 종속성을 가지게 되므로 해당 방법은 권장하지 않습니다.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ 활성 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
+ 사용자가 소유하고 있는 사용자 지정 도메인 이름입니다. 사용하려는 사용자 지정 도메인 이름은 DNS 서버에서 별도로 확보되어 호스트되어야 합니다. 이 항목은 사용자 지정 도메인 이름을 호스트하는 방법에 대해서는 설명하지 않습니다.
+ 공개 및 개인 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. 주체 또는 SAN(주체 대체 이름)이 도메인 이름과 일치해야 합니다(이렇게 하면 APIM에서 SSL을 통해 URL을 안전하게 공개할 수 있음).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure Portal을 사용하여 사용자 지정 도메인 이름 설정

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
1. **사용자 지정 도메인 및 SSL**을 선택합니다.
    
    다양 한 끝점을 사용자 지정 도메인 이름을 할당할 수 있습니다. 현재는 다음 엔드포인트를 사용할 수 있습니다. 
   + **프록시**(기본값: `<apim-service-name>.azure-api.net`), 
   + **포털**(기본값: `<apim-service-name>.portal.azure-api.net`),     
   + **관리**(기본값: `<apim-service-name>.management.azure-api.net`), 
   + **SCM**(기본값: `<apim-service-name>.scm.azure-api.net`).

     >[!NOTE]
     > 이들 중 일부 또는 모든 엔드포인트를 업데이트할 수 있습니다. 일반적으로 고객이 **프록시**(이 URL은 API Management를 통해 노출된 API를 호출하는 데 사용됨) 및 **포털**(개발자 포털 URL)을 업데이트합니다. **관리** 및 **SCM** 엔드포인트는 APIM 고객에 의해 내부적으로 사용되며 따라서 사용자 지정 도메인 이름은 자주 할당되지 않습니다.

1. 업데이트하려는 엔드포인트를 선택합니다. 
1. 오른쪽 창에서 **사용자 지정**을 클릭합니다.

   + **사용자 지정 도메인 이름**에서 사용하려는 이름을 지정합니다. 예: `api.contoso.com`. 와일드 카드 도메인 이름(예를 들어 *.domain.com)도 지원됩니다.
   + 에 **인증서**, Key Vault에서 인증서를 선택 합니다. 유효한 업로드할 수도 있습니다. PFX 파일을 제공 해당 **암호**인증서는 암호로 보호 되는 경우.

     > [!TIP]
     > Azure Key Vault를 사용 하 여 사용자 지정 도메인 SSL 인증서를 관리 하는 경우 Key Vault에 인증서 삽입 되어 있는지 확인 [으로 *인증서*](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)아니라는 *비밀*합니다. 인증서는 autorotate로 설정 되 면 최신 버전 API Management는 자동으로 선택 합니다.

1. 적용을 클릭합니다.

    >[!NOTE]
    >배포 크기에 따라, 인증서를 할당하는 과정에 15분 이상 소요될 수 있습니다. 개발자 SKU는 가동 중지되고, 기본 이상의 SKU는 가동 중지되지 않습니다.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
