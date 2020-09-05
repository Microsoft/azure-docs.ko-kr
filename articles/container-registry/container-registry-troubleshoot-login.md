---
title: 레지스트리에 로그인 문제 해결
description: Azure container registry에 로그인 할 때 발생 하는 일반적인 문제에 대 한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 8fbb96be8223001ac52db47788c31609e9b86e35
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227480"
---
# <a name="troubleshoot-registry-login"></a>레지스트리 로그인 문제 해결

이 문서는 Azure container registry에 로그인 할 때 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* `docker login`, `az acr login` 또는 둘 다를 사용 하 여 레지스트리에 로그인 할 수 없습니다.
* 레지스트리에 로그인 할 수 없으며 오류가 발생 했습니다. `unauthorized: authentication required``unauthorized: Application not registered with AAD`
* 레지스트리에 로그인 할 수 없으며 Azure CLI 오류가 발생 했습니다. `Could not connect to the registry login server`
* 이미지를 푸시 또는 끌어올 수 없으며 Docker 오류가 수신 됩니다. `unauthorized: authentication required`
* Azure Kubernetes Service, Azure DevOps 또는 다른 Azure 서비스에서 레지스트리에 액세스할 수 없습니다.
* 레지스트리에 액세스할 수 없으며 오류가 발생 합니다. `Error response from daemon: login attempt failed with status: 403 Forbidden` [레지스트리에서 네트워크 문제 해결](container-registry-troubleshoot-access.md) 을 참조 하세요.
* Azure Portal 또는 Azure CLI를 사용 하 여 레지스트리 설정을 확인 하거나 볼 수 없습니다.

## <a name="causes"></a>원인

* 사용자 환경에서 Docker가 제대로 구성 되지 않았습니다.- [솔루션](#check-docker-configuration)
* 레지스트리가 없거나 이름이 잘못 되었습니다. [솔루션](#specify-correct-registry-name)
* 레지스트리 자격 증명이 잘못 되었습니다. [솔루션](#confirm-credentials-to-access-registry)
* 자격 증명에 푸시, 끌어오기 또는 Azure Resource Manager 작업을 수행할 수 있는 권한이 없습니다.- [솔루션](#confirm-credentials-are-authorized-to-access-registry)
* 자격 증명이 만료 되었습니다. [솔루션](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>추가 진단 

[Az acr check-health](/cli/azure/acr#az-acr-check-health) 명령을 실행 하 여 레지스트리 환경의 상태에 대 한 자세한 정보를 가져오고 선택적으로 대상 레지스트리에 액세스 합니다. 예를 들어 Docker 구성 오류를 진단 하거나 로그인 문제를 Azure Active Directory 합니다. 

명령 예제는 [Azure container registry의 상태 확인](container-registry-check-health.md) 을 참조 하세요. 오류가 보고 되 면 [오류 참조](container-registry-health-error-reference.md) 및 다음 섹션에서 권장 해결 방법을 검토 합니다.

> [!NOTE]
> 일부 인증 또는 권한 부여 오류는 레지스트리 액세스를 방해 하는 방화벽 또는 네트워크 구성이 있는 경우에도 발생할 수 있습니다. [레지스트리의 네트워크 문제 해결](container-registry-troubleshoot-access.md)을 참조 하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="check-docker-configuration"></a>Docker 구성 확인

대부분의 Azure Container Registry 인증 흐름에는 로컬 Docker 설치가 필요 하므로 이미지 밀어넣기 및 끌어오기 등의 작업을 위해 레지스트리를 사용 하 여 인증할 수 있습니다. 사용자 환경에서 Docker CLI 클라이언트 및 데몬 (Docker 엔진)이 실행 중인지 확인 합니다. Docker 클라이언트 버전 18.03 이상이 필요 합니다.

관련 링크:

* [인증 개요](container-registry-authentication.md#authentication-options)
* [컨테이너 레지스트리 FAQ](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>올바른 레지스트리 이름 지정

를 사용 하는 경우 `docker login` *myregistry.azurecr.io*와 같은 레지스트리의 전체 로그인 서버 이름을 제공 합니다. 소문자만 사용 해야 합니다. 예제:

```console
docker login myregistry.azurecr.io
```

Azure Active Directory id로 [az acr login](/cli/azure/acr#az-acr-login) 을 사용 하는 경우 먼저 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli)한 후 레지스트리의 Azure 리소스 이름을 지정 합니다. 리소스 이름은 *myregistry* (도메인 접미사 제외)와 같이 레지스트리를 만들 때 제공 되는 이름입니다. 예제:

```azurecli
az acr login --name myregistry
```

관련 링크:

* [az acr login이 성공했지만 '권한 없음: 인증 필요' 오류로 인해 docker 명령이 실패함](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>레지스트리에 액세스 하기 위한 자격 증명 확인

시나리오에 사용 하는 자격 증명의 유효성을 검사 하거나 레지스트리 소유자가 제공한 자격 증명의 유효성을 검사 합니다. 가능한 몇 가지 문제는 다음과 같습니다.

* Active Directory 서비스 주체를 사용 하는 경우 Active Directory 테 넌 트에서 올바른 자격 증명을 사용 해야 합니다.
  * 사용자 이름-서비스 사용자 응용 프로그램 ID ( *클라이언트 id*라고도 함)
  * 암호 서비스 주체 암호 ( *클라이언트 암호*라고도 함)
* Azure Kubernetes Service 또는 Azure DevOps와 같은 Azure 서비스를 사용 하 여 레지스트리에 액세스 하는 경우 서비스에 대 한 레지스트리 구성을 확인 합니다.
* `az acr login` `--expose-token` Docker 디먼을 사용 하지 않고 레지스트리 로그인을 사용 하도록 설정 하는 옵션을 실행 한 경우 사용자 이름으로 인증 해야 `00000000-0000-0000-0000-000000000000` 합니다.

관련 링크:

* [인증 개요](container-registry-authentication.md#authentication-options)
* [Azure AD로 개별 로그인](container-registry-authentication.md#individual-login-with-azure-ad)
* [서비스 사용자로 로그인](container-registry-auth-service-principal.md)
* [관리 id를 사용 하 여 로그인](container-registry-authentication-managed-identity.md)
* [리포지토리 범위 토큰을 사용 하 여 로그인](container-registry-repository-scoped-permissions.md)
* [관리자 계정으로 로그인](container-registry-authentication.md#admin-account)
* [Azure AD 인증 및 권한 부여 오류 코드](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az-acr-login) 참조

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>레지스트리에 액세스할 수 있는 자격 증명 확인

`AcrPull`레지스트리에서 이미지를 끌어올 RBAC 역할 또는 이미지를 푸시하는 역할 등 자격 증명과 연결 된 레지스트리 사용 권한을 확인 합니다 `AcrPush` . 

Azure CLI를 사용 하는 포털 또는 레지스트리 관리의 레지스트리에 대 한 액세스에는 최소한 `Reader` Azure Resource Manager 작업을 수행할 역할이 필요 합니다.

사용자 또는 레지스트리 소유자는 구독에서 역할 할당을 추가 하거나 제거할 수 있는 충분 한 권한을 보유 해야 합니다.

관련 링크:

* [RBAC 역할 및 사용 권한-Azure Container Registry](container-registry-roles.md)
* [리포지토리 범위 토큰을 사용 하 여 로그인](container-registry-repository-scoped-permissions.md)
* [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-portal.md)
* [포털을 사용 하 여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)
* [새 애플리케이션 비밀 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)
* [Azure AD 인증 및 권한 부여 코드](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>자격 증명이 만료 되지 않았는지 확인 합니다.

토큰 및 Active Directory 자격 증명은 정의 된 기간 후에 만료 되어 레지스트리 액세스를 방지 합니다. 액세스를 사용 하도록 설정 하려면 자격 증명을 다시 설정 하거나 다시 생성 해야 할 수 있습니다.

* 개별 AD id, 관리 id 또는 레지스트리 로그인에 대 한 서비스 주체를 사용 하는 경우 AD 토큰은 3 시간 후에 만료 됩니다. 레지스트리에 다시 로그인 합니다.  
* 만료 된 클라이언트 암호를 사용 하는 AD 서비스 주체를 사용 하는 경우 구독 소유자 또는 계정 관리자는 자격 증명을 다시 설정 하거나 새 서비스 주체를 생성 해야 합니다.
* [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md)을 사용 하는 경우 레지스트리 소유자가 암호를 다시 설정 하거나 새 토큰을 생성 해야 할 수 있습니다.

관련 링크:

* [서비스 사용자 자격 증명 다시 설정](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [토큰 암호 다시 생성](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Azure AD로 개별 로그인](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리에서 [리소스 로그 컬렉션](container-registry-diagnostics-audit-logs.md) 을 사용 하는 경우 ContainterRegistryLoginEvents 로그를 검토 합니다. 이 로그는 들어오는 id와 IP 주소를 포함 하 여 인증 이벤트와 상태를 저장 합니다. 로그에서 [레지스트리 인증 실패](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)를 쿼리 합니다. 

관련 링크:

* [진단 평가 및 감사에 대 한 로그](container-registry-diagnostics-audit-logs.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.md)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결 하지 않으면 다음 옵션을 참조 하세요.

* 다른 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리의 네트워크 문제 해결](container-registry-troubleshoot-access.md)
  * [레지스트리 성능 문제 해결](container-registry-troubleshoot-performance.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* 제공 된 정보에 따라 [지원 티켓을 엽니다](https://azure.microsoft.com/support/create-ticket/) . 레지스트리에서 인증 오류에 대 한 빠른 진단을 실행할 수 있습니다.


