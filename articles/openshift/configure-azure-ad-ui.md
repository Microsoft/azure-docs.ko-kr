---
title: Azure Red Hat OpenShift 실행 OpenShift 4-Azure Portal 및 OpenShift 웹 콘솔을 사용 하 여 Azure Active Directory 인증 구성
description: Azure Portal 및 OpenShift 웹 콘솔을 사용 하 여 OpenShift 4를 실행 하는 Azure Red Hat OpenShift 클러스터에 대 한 Azure Active Directory 인증을 구성 하는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 10a7dc662993327b71d43c27f44d22166a3f3611
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590322"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Red Hat OpenShift 4 클러스터에 대 한 Azure Active Directory 인증 구성 (포털)

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

클러스터의 **OAuth 콜백 URL** 을 생성 하 고 적어 둡니다. 사용자의 클러스터 이름으로 **aro-rg** 를 리소스 그룹의 이름 및 **aro-cluster** 로 바꾸어야 합니다.

> [!NOTE]
> `AAD`Oauth 콜백 URL의 섹션은 나중에 설정할 oauth id 공급자 이름과 일치 해야 합니다.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>인증을 위한 Azure Active Directory 응용 프로그램 만들기

Azure Portal에 로그인 하 여 [앱 등록 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)로 이동한 다음 **새 등록** 을 클릭 하 여 새 응용 프로그램을 만듭니다.

응용 프로그램의 이름 (예: **azuread-auth)** 을 제공 하 고 이전에 검색 한 OAUTH 콜백 URL의 값을 사용 하 여 **리디렉션 URI** 를 입력 합니다.

![새 애플리케이션 등록](media/aro4-ad-registerapp.png)

**인증서 & 암호** 로 이동 하 고 **새 클라이언트 암호** 를 클릭 한 다음 세부 정보를 입력 합니다. 이후 단계에서 사용할 때 키 값을 기록해 둡니다. 다시 검색할 수 없습니다.

![비밀 만들기](media/aro4-ad-clientsecret.png)

**개요** 로 이동 하 여 **응용 프로그램 (클라이언트) id** 및 **디렉터리 (테 넌 트) id**를 기록 합니다. 이후 단계에서 필요 합니다.

![응용 프로그램 (클라이언트) 및 디렉터리 (테 넌 트) Id 검색](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>선택적 클레임 구성

응용 프로그램 개발자는 Azure AD 응용 프로그램에서 [선택적 클레임](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) 을 사용 하 여 응용 프로그램으로 전송 되는 토큰에서 원하는 클레임을 지정할 수 있습니다.

선택적 클레임을 사용하여 다음을 수행할 수 있습니다.

* 애플리케이션에 대한 토큰에 포함할 추가 클레임을 선택합니다.
* Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
* 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

`email` `upn` `upn` Azure Active Directory에서 반환 하는 ID 토큰의 일부로를 추가 하 여 클레임을 사용 하 고로 대체 하도록 openshift를 구성 합니다.

**토큰 구성 (미리 보기)** 으로 이동 하 고 **선택적 클레임 추가**를 클릭 합니다. **ID** 를 선택 하 고 **전자 메일** 및 **upn** 클레임을 확인 합니다.

![비밀 만들기](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>클러스터에 사용자 및 그룹 할당 (선택 사항)

기본적으로 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션은 성공적으로 인증한 테넌트의 모든 사용자에게 제공됩니다. Azure AD를 사용하면 테넌트 관리자 및 개발자는 앱을 테넌트의 특정 사용자 집합 또는 보안 그룹으로 제한할 수 있습니다.

Azure Active Directory 설명서의 지침에 따라 [사용자 및 그룹을 앱에 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration)합니다.

## <a name="configure-openshift-openid-authentication"></a>OpenShift Openid connect 인증 구성

`kubeadmin`자격 증명을 검색 합니다. 다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

다음 예제 출력에서는 암호가 `kubeadminPassword`에 있는 것을 보여줍니다.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

다음 명령을 실행하여 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 형식의 클러스터 콘솔 URL을 찾을 수 있습니다.

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

브라우저에서 콘솔 URL을 시작하고 `kubeadmin` 자격 증명을 사용하여 로그인합니다.

**관리**로 이동 하 여 **클러스터 설정**을 클릭 한 다음 **전역 구성** 탭을 선택 합니다. 스크롤하여 **OAuth**를 선택 합니다.

아래로 스크롤하여 **Id 공급자** 아래에서 **추가** 를 선택 하 고 **openid connect 연결**을 선택 합니다.
![Id 공급자 드롭다운에서 Openid connect Connect를 선택 합니다.](media/aro4-oauth-idpdrop.png)

이름을 **AAD**로, **클라이언트 Id** 를 **응용 프로그램 id** 로, **클라이언트 암호**를 입력 합니다. **발급자 URL** 의 `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 형식은 다음과 같습니다. 자리 표시자를 이전에 검색 한 테 넌 트 ID로 바꿉니다.

![OAuth 정보 입력](media/aro4-oauth-idp-1.png)

**클레임** 섹션까지 아래로 스크롤하고 **upn** 클레임의 값을 사용 하도록 **기본 설정 된 사용자 이름을** 업데이트 합니다.

![클레임 정보 입력](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory를 통해 로그인 확인

이제 OpenShift 웹 콘솔을 로그 아웃 하 고 다시 로그인을 시도 하면 **AAD**로 로그인 할 수 있는 새로운 옵션이 제공 됩니다. 몇 분 정도 기다려야 할 수 있습니다.

![Azure Active Directory 옵션이 있는 로그인 화면](media/aro4-login-2.png)
