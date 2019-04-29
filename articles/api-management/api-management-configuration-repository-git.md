---
title: Git를 사용하여 API Management 서비스 구성 - Azure | Microsoft Docs
description: Git을 사용하여 API Management 서비스 구성을 저장 및 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: adf4d8d5cfcef2dde8193ce1b7f2805a44e2d93d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657924"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Git을 사용하여 API Management 서비스 구성을 저장 및 구성하는 방법

각 API Management 서비스 인스턴스는 구성에 관한 정보 및 서비스 인스턴스에 대한 메타데이터를 포함하고 있는 구성 데이터베이스를 유지관리합니다. PowerShell cmdlet을 사용하거나 REST API를 호출하여 Azure Portal의 설정을 변경하면 서비스 인스턴스를 변경할 수 있습니다. 이러한 방법 이외에 다음과 같은 서비스 관리 시나리오를 통해 Git를 사용하여 서비스 인스턴스 구성을 관리할 수도 있습니다.

* 구성 버전 관리 - 서비스 구성의 서로 다른 버전 다운로드 및 저장
* 대량 구성 변경 - 로컬 저장소에 있는 서비스 구성의 여러 부분을 변경하고 단일 작업으로 변경 내용을 서버에 다시 통합
* 친숙한 Git 도구 체인 및 워크플로 - 이미 익숙해진 Git 도구 및 워크플로 사용

다음 다이어그램에서는 API Management 서비스 인스턴스를 구성하는 다양한 방법을 간략하게 보여 줍니다.

![Git 구성][api-management-git-configure]

Azure Portal, PowerShell cmdlet 또는 REST API를 사용하여 서비스를 변경할 때 다이어그램의 오른쪽과 같이 `https://{name}.management.azure-api.net` 엔드포인트를 사용하여 서비스 구성 데이터베이스를 관리합니다. 다이어그램의 왼쪽은 `https://{name}.scm.azure-api.net`에 있는 서비스에 Git 및 Git 리포지토리를 사용하여 서비스 구성을 관리할 수 있는 방법을 보여 줍니다.

다음 단계는 Git을 이용한 API Management 서비스 인스턴스 관리를 간략하게 보여 줍니다.

1. 서비스의 Git 구성에 액세스
2. Git 리포지토리에 서비스 구성 데이터베이스 저장
3. 로컬 컴퓨터에 Git 리포지토리 복제
4. 리포지토리를 로컬 컴퓨터에 풀하고 커밋한 다음 변경 내용을 리포지토리에 다시 푸시
5. 리포지토리의 변경 내용을 서비스 구성 데이터베이스에 배포

이 문서에서는 Git를 사용하도록 설정하고 이를 사용하여 서비스 구성을 관리하는 방법을 설명하며 Git 리포지토리의 파일 및 폴더에 대한 참조를 제공합니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>서비스의 Git 구성에 액세스

Git 구성 설정을 확인하고 구성하려면 **보안** 메뉴를 클릭하고 **구성 리포지토리** 탭으로 이동하면 됩니다.

![GIT 사용][api-management-enable-git]

> [!IMPORTANT]
> 명명 된 값으로 정의 되어 있지 않은 모든 암호 저장소에 저장 됩니다 하 고 사용 하지 않도록 설정 하 고 다시 Git 액세스를 사용 하도록 설정 될 때까지 기록에 유지 됩니다. 명명 된 값 정책 설명에 직접 저장할 필요가 모든 API 구성 및 정책에서 암호를 포함 하 여 상수 문자열 값을 관리 하는 안전한 장소를 제공 합니다. 자세한 내용은 [Azure API Management 정책에서 명명 된 값을 사용 하는 방법을](api-management-howto-properties.md)합니다.
>
>

REST API를 사용하여 Git 액세스를 사용 또는 사용하지 않도록 설정하는 방법은 [REST API를 사용하여 Git 액세스를 사용 또는 사용하지 않도록 설정](/rest/api/apimanagement/tenantaccess?EnableGit)을 참조하세요.

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Git 리포지토리에 서비스 구성 저장

리포지토리를 복제하기 전에 수행할 첫 번째 단계는 서비스 구성의 현재 상태를 리포지토리에 저장하는 것입니다. **리포지토리에 저장**을 클릭합니다.

확인 화면에서 원하는 대로 변경하고 **확인** 을 클릭하여 저장합니다.

몇 분 후에 구성이 저장되며, 마지막 구성 변경 및 서비스 구성과 리포지토리 간 마지막 동기화의 날짜 및 시간을 비롯하여 리포지토리의 구성 상태가 표시됩니다.

구성이 리포지토리에 저장된 후 해당 구성을 복제할 수 있습니다.

REST API를 사용하여 이 작업을 수행하는 방법은 [REST API를 사용하여 구성 스냅숏 커밋](/rest/api/apimanagement/tenantaccess?CommitSnapshot)을 참조하세요.

## <a name="to-clone-the-repository-to-your-local-machine"></a>로컬 컴퓨터에 리포지토리 복제

리포지토리를 복제하려면 리포지토리에 대한 URL, 사용자 이름 및 암호가 필요합니다. 사용자 이름 및 기타 자격 증명을 가져오려면 페이지의 맨 위 가까이에 있는 **액세스 자격 증명**을 클릭합니다.

암호를 생성하려면 먼저 **만료**가 원하는 만료 날짜 및 시간으로 설정되었는지 확인한 다음, **생성**을 클릭합니다.

> [!IMPORTANT]
> 이 암호를 기록해 둡니다. 이 페이지를 떠나면 암호가 다시 표시되지 않습니다.
>

다음 예제에서는 [Windows용 Git](https://www.git-scm.com/downloads) 에서 Git Bash 도구를 사용하지만 현재 친숙한 아무 Git나 사용할 수 있습니다.

원하는 폴더에서 Git 도구를 열고 Azure Portal에서 제공한 다음 명령을 실행하여 Git 리포지토리를 로컬 컴퓨터에 복제합니다.

```
git clone https://{name}.scm.azure-api.net/
```

메시지가 표시되면 사용자 이름 및 암호를 제공합니다.

오류가 발생하면 다음 예제와 같이 `git clone` 명령을 사용자 이름 및 암호를 포함하도록 수정해 보십시오.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

그래도 오류가 발생하면 명령의 암호 부분에 대해 URL 인코딩을 시도해 보십시오. 이렇게 하는 한 가지 빠른 방법은 Visual Studio를 열고 **직접 실행 창**에서 다음 명령을 실행하는 것입니다. **직접 실행 창**을 열려면 Visual Studio에서 솔루션 또는 프로젝트를 열고(또는 비어 있는 새 콘솔 애플리케이션을 만들고) **디버그** 메뉴에서 **창**, **직접 실행**을 선택합니다.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

사용자 이름 및 리포지토리 위치와 함께 인코딩된 암호를 사용하여 Git 명령을 생성합니다.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

리포지토리가 복제된 후 로컬 파일 시스템에서 이를 보고 작업할 수 있습니다. 자세한 내용은 [로컬 Git 리포지토리의 파일 및 폴더 구조 참조](#file-and-folder-structure-reference-of-local-git-repository)를 참조하세요.

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>최근 서비스 인스턴스 구성으로 로컬 리포지토리를 업데이트하려면

Azure Portal에서 또는 REST API를 사용하여 API Management 서비스 인스턴스를 변경하는 경우 변경 내용을 리포지토리에 저장해야 로컬 리포지토리를 최신 변경 내용으로 업데이트할 수 있습니다. 이 작업을 수행하려면 Azure Portal의 **구성 리포지토리** 탭에서 **리포지토리에 구성 저장**을 클릭한 다음, 로컬 리포지토리에서 다음 명령을 실행합니다.

```
git pull
```

`git pull` 을 실행하기 전에 현재 로컬 리포지토리에 대한 폴더에 있는지 확인하십시오. `git clone` 명령을 방금 완료한 경우 다음과 같은 명령을 실행하여 디렉터리를 리포지토리로 변경해야 합니다.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>로컬 리포지토리의 변경 내용을 서버 리포지토리에 푸시하려면
로컬 리포지토리의 변경 내용을 서버 리포지토리에 푸시하려면 변경 내용을 커밋한 다음 이를 서버 리포지토리에 게시해야 합니다. 변경 내용을 커밋하려면 Git 명령 도구를 열고 로컬 리포지토리의 디렉터리로 전환한 후 다음 명령을 실행합니다.

```
git add --all
git commit -m "Description of your changes"
```

모든 커밋을 서버에 푸시하려면 다음 명령을 실행합니다.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>서비스 구성 변경 내용을 API Management 서비스 인스턴스에 배포하려면

로컬 변경 내용이 커밋되고 서버 리포지토리에 푸시된 후 이를 API Management 서비스 인스턴스에 배포할 수 있습니다.

REST API를 사용하여 이 작업을 수행하는 방법은 [REST API를 사용하여 구성 데이터베이스에 Git 변경 내용 배포](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration)를 참조하세요.

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>로컬 Git 리포지토리의 파일 및 폴더 구조 참조

로컬 Git 리포지토리의 파일 및 폴더에는 서비스 인스턴스에 관한 구성 정보가 포함되어 있습니다.

| 항목 | 설명 |
| --- | --- |
| 루트 api 관리 폴더 |서비스 인스턴스에 대한 최상의 구성 포함 |
| apis 폴더 |서비스 인스턴스의 apis에 대한 구성 포함 |
| groups 폴더 |서비스 인스턴스의 그룹에 대한 구성 포함 |
| policies 폴더 |서비스 인스턴스의 정책 포함 |
| portalStyles 폴더 |서비스 인스턴스의 개발자 포털 사용자 지정에 대한 구성 포함 |
| products 폴더 |서비스 인스턴스의 제품에 대한 구성 포함 |
| templates 폴더 |서비스 인스턴스의 전자 메일 템플릿에 대한 구성 포함 |

각 폴더는 하나 이상의 파일 및 하나 이상의 폴더, 예를 들어 각 API, 제품 또는 그룹에 대한 폴더를 포함할 수 있습니다. 각 폴더 내의 파일은 폴더 이름에 의해 설명된 엔터티 유형에 대해 적용됩니다.

| 파일 형식 | 목적 |
| --- | --- |
| json : |해당 엔터티에 관한 구성 정보 |
| html |대개 개발자 포털에 표시되는 엔터티에 관한 설명 |
| Xml |정책 설명 |
| css |개발자 포털 사용자 지정에 대한 스타일 시트 |

이러한 파일은 로컬 파일 시스템에서 생성, 삭제, 편집 및 관리할 수 있으며 변경 내용을 API Management 서비스 인스턴스에 다시 배포할 수 있습니다.

> [!NOTE]
> 다음 엔터티는 Git 리포지토리에 포함되지 않으며 Git를 사용하여 구성할 수 없습니다.
>
> * [사용자](https://docs.microsoft.com/en-us/rest/api/apimanagement/user)
> * [구독](https://docs.microsoft.com/en-us/rest/api/apimanagement/subscription)
> * [명명 된 값](https://docs.microsoft.com/en-us/rest/api/apimanagement/property)
> * 스타일 이외의 개발자 포털 엔터티
>

### <a name="root-api-management-folder"></a>루트 api 관리 폴더
루트 `api-management` 폴더에는 다음과 같은 형식의 서비스 인스턴스에 관한 최상위 정보를 포함하고 있는 `configuration.json` 파일이 포함되어 있습니다.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

처음 네 설정(`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled` 및 `UserRegistrationTermsConsentRequired`)은 **보안** 섹션의 **ID** 탭에 있는 다음과 같은 설정에 매핑됩니다.

| Id 설정 | 매핑 대상 |
| --- | --- |
| RegistrationEnabled |**사용자 이름 및 암호** ID 공급자의 현재 상태 |
| UserRegistrationTerms |**사용자 등록 시 사용 약관** 텍스트 상자 |
| UserRegistrationTermsEnabled |**등록 페이지에 사용 약관 표시** 확인란 |
| UserRegistrationTermsConsentRequired |**동의 필요** 확인란 |
| RequireUserSigninEnabled |**로그인 페이지로 익명 사용자 리디렉션** 확인란 |

처음 네 설정(`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled` 및 `DelegationValidationKey`)은 **보안** 섹션의 **위임** 탭에 있는 다음과 같은 설정에 매핑됩니다.

| 위임 설정 | 매핑 대상 |
| --- | --- |
| DelegationEnabled |**로그인 및 등록 위임** 확인란 |
| DelegationUrl |**위임 엔드포인트 URL** 텍스트 상자 |
| DelegatedSubscriptionEnabled |**제품 구독 위임** 확인란 |
| DelegationValidationKey |**유효성 검사 키 위임** 텍스트 상자 |

마지막 설정 `$ref-policy`은 서비스 인스턴스에 대한 전역 정책 설명 파일에 매핑됩니다.

### <a name="apis-folder"></a>apis 폴더
`apis` 폴더에는 다음 항목을 포함한 서비스 인스턴스의 각 API에 대한 폴더가 포함되어 있습니다.

* `apis\<api name>\configuration.json` - API에 대한 구성이며 백 엔드 서비스 URL 및 작업에 관한 정보를 포함하고 있습니다. 이는 `application/json` 형식의 `export=true`을 사용한 [특정 API 가져오기](https://docs.microsoft.com/rest/api/apimanagement/apis/get)를 호출하려는 경우 반환되는 것과 같은 정보입니다.
* `apis\<api name>\api.description.html` - API에 대한 설명이며 [API 엔터티](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property)의 `description` 속성에 해당합니다.
* `apis\<api name>\operations\` - 이 폴더는 작업을 API에 매핑하는 `<operation name>.description.html` 파일을 포함하고 있습니다. 각 파일은 REST API에서 [작업 엔터티](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties)의 `description` 속성에 매핑되는 API의 단일 작업에 대한 설명을 포함하고 있습니다.

### <a name="groups-folder"></a>groups 폴더
`groups` 폴더는 서비스 인스턴스에 정의된 각 그룹에 대한 폴더를 포함하고 있습니다.

* `groups\<group name>\configuration.json` - 그룹에 대한 구성입니다. 이는 [특정 그룹 가져오기](https://docs.microsoft.com/rest/api/apimanagement/group/get) 를 호출하려는 경우 반환되는 것과 같은 정보입니다.
* `groups\<group name>\description.html` - 그룹에 대한 설명이며 [그룹 엔터티](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)의 `description` 속성에 해당합니다.

### <a name="policies-folder"></a>policies 폴더
`policies` 폴더에는 서비스 인스턴스에 대한 정책 설명이 포함되어 있습니다.

* `policies\global.xml` -서비스 인스턴스에 대 한 전역 범위에 정의된 정책을 포함하고 있습니다.
* `policies\apis\<api name>\` -API 범위에 정책을 정의 한 경우 해당 정책이 이 폴더에 포함되어 있습니다.
* `policies\apis\<api name>\<operation name>\` 폴더 - 작업 범위에 정책을 정의한 경우 해당 정책은 각 작업에 대한 정책 설명에 매핑되는 이 폴더의 `<operation name>.xml` 파일에 포함되어 있습니다.
* `policies\products\` - 제품 범위에 정책을 정의한 경우 해당 정책은 각 제품에 대한 정책 설명에 매핑되는 이 폴더의 `<product name>.xml` 파일에 포함되어 있습니다.

### <a name="portalstyles-folder"></a>portalStyles 폴더
`portalStyles` 폴더에는 서비스 인스턴스에 대한 개발자 포털 사용자 지정에 대한 구성 및 스타일 시트가 포함되어 있습니다.

* `portalStyles\configuration.json` - 개발자 포털에서 사용하는 스타일 시트의 이름이 포함되어 있습니다.
* `portalStyles\<style name>.css` - 각 `<style name>.css` 파일에는 개발자 포털에 대한 스타일(기본적으로 `Preview.css` 및 `Production.css`)이 포함되어 있습니다.

### <a name="products-folder"></a>products 폴더
`products` 폴더는 서비스 인스턴스에 정의된 각 제품에 대한 폴더를 포함하고 있습니다.

* `products\<product name>\configuration.json` - 제품에 대한 구성입니다. 이는 [특정 제품 가져오기](https://docs.microsoft.com/rest/api/apimanagement/product/get) 를 호출하려는 경우 반환되는 것과 같은 정보입니다.
* `products\<product name>\product.description.html` - 제품에 대한 설명이며 REST API에서 [제품 엔터티](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity)의 `description` 속성에 해당합니다.

### <a name="templates"></a>템플릿
`templates` 폴더에는 서비스 인스턴스의 [전자 메일 템플릿](api-management-howto-configure-notifications.md) 에 대한 구성이 포함되어 있습니다.

* `<template name>\configuration.json` - 전자 메일 템플릿에 대한 구성입니다.
* `<template name>\body.html` - 전자 메일 템플릿의 본문입니다.

## <a name="next-steps"></a>다음 단계
서비스 인스턴스를 관리하는 다른 방법에 대한 자세한 내용은 다음을 참조하세요.

* 다음 PowerShell cmdlet을 사용하여 서비스 인스턴스 관리
  * [서비스 배포 PowerShell cmdlet 참조](https://docs.microsoft.com/powershell/module/wds)
  * [서비스 관리 PowerShell cmdlet 참조](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* REST API를 사용하여 서비스 인스턴스 관리
  * [API Management REST API 참조](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




