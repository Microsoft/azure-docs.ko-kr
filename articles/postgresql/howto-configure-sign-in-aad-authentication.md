---
title: Azure Active Directory Azure Database for PostgreSQL 사용-단일 서버
description: Azure Database for PostgreSQL 단일 서버를 사용 하 여 인증을 위해 AAD (Azure Active Directory)를 설정 하는 방법에 대해 알아봅니다.
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c929ac1c171547a4ff485fc43f0f329440f9c3b5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763643"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>PostgreSQL를 사용 하 여 인증을 위해 Azure Active Directory 사용

이 문서에서는 Azure Database for PostgreSQL를 사용 하 여 Azure Active Directory 액세스를 구성 하는 방법과 Azure AD 토큰을 사용 하 여 연결 하는 방법에 대 한 단계를 안내 합니다.

> [!IMPORTANT]
> Azure Database for PostgreSQL에 대 한 Azure AD 인증은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리 사용자 설정

Azure ad 관리 사용자만 Azure AD 기반 인증에 대 한 사용자를 만들거나 사용 하도록 설정할 수 있습니다. Azure AD 관리 사용자를 만들려면 다음 단계를 수행 하세요.

1. Azure Portal에서 Azure AD에 대해 사용 하도록 설정 하려는 Azure Database for PostgreSQL 인스턴스를 선택 합니다.
2. 설정 아래에서 Active Directory Admin을 선택 합니다.

![azure ad 관리자 설정][2]

3. Azure AD 관리자가 될 고객 테넌트의 유효한 Azure AD 사용자를 선택 합니다.

> [!IMPORTANT]
> 관리자를 설정 하면 모든 관리자 권한으로 Azure Database for PostgreSQL 서버에 새 사용자가 추가 됩니다. Azure Database for PostgreSQL의 Azure AD 관리 사용자에 게 `azure_ad_admin`역할이 있습니다.

PostgreSQL 서버 마다 하나의 Azure AD 관리자만 만들 수 있으며, 다른 서버를 선택 하면 서버에 대해 구성 된 기존 Azure AD 관리자가 덮어쓰여집니다. 개별 사용자 대신 Azure AD 그룹을 지정 하 여 여러 관리자를 가질 수 있습니다. 그러면 관리 목적으로 그룹 이름으로 로그인 됩니다.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Azure AD 사용자 만들기

Azure Database for PostgreSQL 데이터베이스에 Azure AD 사용자를 추가 하려면 연결 후 다음 단계를 수행 합니다 (연결 하는 방법에 대 한 뒷부분 섹션 참조).

1. 먼저 azure ad 사용자 `<user>@yourtenant.onmicrosoft.com`가 Azure AD 테넌트의 유효한 사용자 인지 확인 합니다.
2. Azure AD 관리 사용자로 Azure Database for PostgreSQL 인스턴스에 로그인 합니다.
3. Azure Database for PostgreSQL에서 역할 `<user>@yourtenant.onmicrosoft.com`를 만듭니다.
4. Azure_ad_user 역할의 멤버 `<user>@yourtenant.onmicrosoft.com` 만듭니다. 이는 Azure AD 사용자 에게만 제공 되어야 합니다.

**예제:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Azure AD를 통해 사용자를 인증 하는 경우 사용자에 게 Azure Database for PostgreSQL 데이터베이스 내의 개체에 액세스할 수 있는 권한이 부여 되지 않습니다. 사용자에 게 필요한 권한을 수동으로 부여 해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Azure AD 그룹 만들기

데이터베이스에 대 한 액세스를 위해 Azure AD 그룹을 사용 하도록 설정 하려면 사용자에 대해와 동일한 메커니즘을 사용 합니다. 대신 그룹 이름을 지정 합니다.

**예제:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

로그인 할 때 그룹의 구성원은 개인 액세스 토큰을 사용 하지만 사용자 이름으로 지정 된 그룹 이름으로 서명 합니다.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Azure AD를 사용 하 여 Azure Database for PostgreSQL에 연결

다음 고급 다이어그램은 Azure Database for PostgreSQL에서 Azure AD 인증을 사용 하는 워크플로를 요약 합니다.

![인증 흐름][1]

Azure ad가 인식 되지 않으며 PostgreSQL에 연결할 때 사용자 이름 및 암호를 지정 하는 것만 지 원하는 psql과 같은 일반적인 PostgreSQL 도구를 사용 하도록 Azure AD 통합을 설계 했습니다. 위의 그림에 표시 된 것 처럼 Azure AD 토큰을 암호로 전달 합니다.

현재 다음 클라이언트를 테스트 했습니다.

- psql 명령줄 (PGPASSWORD 변수를 활용 하 여 토큰 전달, 아래 참조)
- Azure Data Studio (PostgreSQL 확장 사용)
- 기타 libpq 기반 클라이언트 (예: 일반적인 응용 프로그램 프레임 워크 및 ORMs)

> [!NOTE]
> PgAdmin에서 Azure AD 토큰을 사용 하는 것은 현재 지원 되지 않습니다. 암호 (토큰은 초과)에 대 한 256 문자 제한이 하드 코드 되어 있기 때문입니다.

사용자/응용 프로그램에서 Azure AD로 인증 해야 하는 단계는 아래에 설명 되어 있습니다.

### <a name="step-1-authenticate-with-azure-ad"></a>1 단계: Azure AD를 사용 하 여 인증

[Azure CLI 설치](/cli/azure/install-azure-cli)되어 있는지 확인 합니다.

Azure CLI 도구를 호출 하 여 Azure AD에 인증 합니다. Azure AD 사용자 ID와 암호를 제공 해야 합니다.

```
az login
```

이 명령은 Azure AD 인증 페이지에 대 한 브라우저 창을 시작 합니다.

> [!NOTE]
> Azure Cloud Shell를 사용 하 여 이러한 단계를 수행할 수도 있습니다.
> Azure Cloud Shell에서 Azure AD 액세스 토큰을 검색 하는 경우 `az login`를 명시적으로 호출 하 고 코드를 사용 하 여 별도의 창에 다시 로그인 해야 합니다. 그러면 `get-access-token` 명령의 해당 로그인이 정상적으로 작동 합니다.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2 단계: Azure AD 액세스 토큰 검색

Azure CLI 도구를 호출 하 여 1 단계에서 Azure Database for PostgreSQL 액세스 하는 Azure AD 인증 사용자에 대 한 액세스 토큰을 가져옵니다.

예 (공용 클라우드의 경우):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

위의 리소스 값은 표시 된 대로 정확 하 게 지정 해야 합니다. 다른 클라우드의 경우 다음을 사용 하 여 리소스 값을 조회할 수 있습니다.

```shell
az cloud show
```

2\.0.71 이상 버전의 경우 모든 클라우드에 대해 다음과 같은 더 편리한 버전으로 명령을 지정할 수 있습니다. Azure CLI

```shell
az account get-access-token --resource-type oss-rdbms
```

인증에 성공 하면 Azure AD에서 액세스 토큰을 반환 합니다.

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

토큰은 Azure Database for PostgreSQL 서비스를 대상으로 하는 인증 된 사용자에 대 한 모든 정보를 인코딩하는 기본 64 문자열입니다.

> [!NOTE]
> 액세스 토큰의 유효 기간은 5 분에서 60 분 사이입니다. Azure Database for PostgreSQL 로그인을 시작 하기 직전에 액세스 토큰을 가져오는 것이 좋습니다.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3 단계: PostgreSQL를 사용 하 여 로그인 하기 위해 토큰을 암호로 사용

연결할 때 액세스 토큰을 PostgreSQL 사용자 암호로 사용 해야 합니다.

`psql` 명령줄 클라이언트를 사용 하는 경우 액세스 토큰은 `psql`에서 직접 수락할 수 있는 암호 길이를 초과 하므로 `PGPASSWORD` 환경 변수를 통해 액세스 토큰을 전달 해야 합니다.

Windows 예제:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS 예:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

이제 다음과 같이 Azure Database for PostgreSQL 연결을 시작할 수 있습니다.

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

이제 Azure AD 인증을 사용 하 여 PostgreSQL 서버에 인증 됩니다.

## <a name="token-validation"></a>토큰 유효성 검사

Azure Database for PostgreSQL의 Azure AD 인증은 사용자가 PostgreSQL 서버에 존재 하는지 확인 하 고 토큰 내용의 유효성을 검사 하 여 토큰의 유효성을 검사 합니다. 다음 토큰 유효성 검사 단계를 수행 합니다.

-   토큰이 Azure AD에 의해 서명 되었으며 변조 되지 않았습니다.
-   서버와 연결 된 테넌트에 대해 Azure AD에서 토큰을 발급 했습니다.
-   토큰이 만료 되지 않았습니다.
-   토큰은 Azure Database for PostgreSQL 리소스에 대 한 것 이며 다른 Azure 리소스는 아닙니다.

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>기존 PostgreSQL 사용자를 Azure AD 기반 인증으로 마이그레이션

기존 사용자에 대해 Azure AD 인증을 사용 하도록 설정할 수 있습니다. 다음과 같은 두 가지 경우를 고려해 야 합니다.

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>사례 1: PostgreSQL username이 Azure AD 사용자 계정 이름과 일치

기존 사용자가 Azure AD 사용자 이름과 이미 일치 하는 경우를 대비 하 여 Azure AD 인증을 사용 하도록 설정 하기 위해 `azure_ad_user` 역할에 부여할 수 있습니다.

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

이제 이전에 구성 된 PostgreSQL 사용자 암호를 사용 하는 대신 Azure AD 자격 증명을 사용 하 여 로그인 할 수 있습니다.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>사례 2: PostgreSQL username이 Azure AD 사용자 계정 이름과 다릅니다.

PostgreSQL 사용자가 Azure AD에 없거나 다른 사용자 이름을 사용 하는 경우 Azure AD 그룹을 사용 하 여이 PostgreSQL 사용자로 인증할 수 있습니다. PostgreSQL 사용자와 일치 하는 이름으로 Azure AD 그룹을 만든 다음 기존 PostgreSQL 사용자에 게 역할 azure_ad_user를 부여 하 여 기존 Azure Database for PostgreSQL 사용자를 Azure AD로 마이그레이션할 수 있습니다.

```sql
GRANT azure_ad_user TO "DBReadUser";
```

여기서는 Azure AD에서 "DBReadUser" 그룹을 만들었다고 가정 합니다. 해당 그룹에 속한 사용자는 이제이 사용자로 데이터베이스에 로그인 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL 단일 서버를 사용 하 여 Azure Active Directory 인증](concepts-aad-authentication.md) 에 대 한 전체 개념 검토

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
