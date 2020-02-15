---
title: Azure Active Directory Azure Database for MySQL 사용-단일 서버
description: Azure Database for MySQL 단일 서버를 사용 하 여 인증을 위해 Azure Active Directory (Azure AD)를 설정 하는 방법에 대해 알아봅니다.
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: bb3a8c94b377fb9c9150945ec4cf5980e006dd34
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110616"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>MySQL을 사용 하 여 인증을 위해 Azure Active Directory 사용

이 문서에서는 Azure Database for MySQL를 사용 하 여 Azure Active Directory 액세스를 구성 하는 방법과 Azure AD 토큰을 사용 하 여 연결 하는 방법에 대 한 단계를 안내 합니다.

> [!IMPORTANT]
> Azure Database for MySQL에 대 한 Azure AD 인증은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리 사용자 설정

Azure ad 관리 사용자만 Azure AD 기반 인증에 대 한 사용자를 만들거나 사용 하도록 설정할 수 있습니다. Azure AD 관리 사용자를 만들려면 다음 단계를 수행 하세요.

1. Azure Portal에서 Azure AD에 대해 사용 하도록 설정 하려는 Azure Database for MySQL 인스턴스를 선택 합니다.
2. 설정 아래에서 Active Directory Admin을 선택 합니다.

![azure ad 관리자 설정][2]

3. Azure AD 관리자가 될 고객 테 넌 트의 유효한 Azure AD 사용자를 선택 합니다.

> [!IMPORTANT]
> 관리자를 설정 하면 모든 관리자 권한으로 Azure Database for MySQL 서버에 새 사용자가 추가 됩니다.

MySQL 서버당 하나의 Azure AD 관리자만 만들 수 있으며, 다른 항목을 선택 하면 서버에 대해 구성 된 기존 Azure AD 관리자가 덮어쓰여집니다.

이후 릴리스에서는 개별 사용자 대신 Azure AD 그룹에 여러 관리자를 지정 하는 것을 지원 하지만 현재는 아직 지원 되지 않습니다.

이제 관리자를 구성한 후에 로그인 할 수 있습니다.

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Azure AD를 사용 하 여 Azure Database for MySQL에 연결

다음 고급 다이어그램은 Azure Database for MySQL에서 Azure AD 인증을 사용 하는 워크플로를 요약 합니다.

![인증 흐름][1]

Azure ad가 인식 되지 않으며 MySQL에 연결할 때 사용자 이름 및 암호를 지정 하는 것만 지원 하는 mysql CLI와 같은 일반적인 MySQL 도구를 사용 하도록 Azure AD 통합을 설계 했습니다. 위의 그림에 표시 된 것 처럼 Azure AD 토큰을 암호로 전달 합니다.

현재 다음 클라이언트를 테스트 했습니다.

- MySQLWorkbench 벤치 
- Mysql CLI

또한 가장 일반적인 응용 프로그램 드라이버를 테스트 했습니다 .이 페이지의 끝 부분에서 세부 정보를 볼 수 있습니다.

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

Azure CLI 도구를 호출 하 여 1 단계에서 Azure Database for MySQL 액세스 하는 Azure AD 인증 사용자에 대 한 액세스 토큰을 가져옵니다.

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

토큰은 Azure Database for MySQL 서비스를 대상으로 하는 인증 된 사용자에 대 한 모든 정보를 인코딩하는 기본 64 문자열입니다.

> [!NOTE]
> 액세스 토큰의 유효 기간은 5 분에서 60 분 사이입니다. Azure Database for MySQL 로그인을 시작 하기 직전에 액세스 토큰을 가져오는 것이 좋습니다.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3 단계: MySQL을 사용 하 여 로그인 하기 위해 토큰을 암호로 사용

연결할 때 액세스 토큰을 MySQL 사용자 암호로 사용 해야 합니다. MySQLWorkbench 벤치와 같은 GUI 클라이언트를 사용 하는 경우 위의 메서드를 사용 하 여 토큰을 검색할 수 있습니다. 

CLI를 사용 하는 경우이 짧은 연결을 사용 하 여 연결할 수 있습니다. 

**예 (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

"일반 텍스트-플러그 인 사용" 설정에 유의 하세요. 다른 클라이언트와 유사한 구성을 사용 하 여 해시가 아닌 서버에 토큰이 전송 되도록 해야 합니다.

이제 Azure AD 인증을 사용 하 여 MySQL 서버에 인증 됩니다.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 Azure AD 사용자 만들기

Azure Database for MySQL 데이터베이스에 Azure AD 사용자를 추가 하려면 연결 후 다음 단계를 수행 합니다 (연결 하는 방법에 대 한 뒷부분 섹션 참조).

1. 먼저 azure ad 사용자 `<user>@yourtenant.onmicrosoft.com`가 Azure AD 테 넌 트의 유효한 사용자 인지 확인 합니다.
2. Azure AD 관리 사용자로 Azure Database for MySQL 인스턴스에 로그인 합니다.
3. Azure Database for MySQL에서 사용자 `<user>@yourtenant.onmicrosoft.com`를 만듭니다.

**예:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

32 자를 초과 하는 사용자 이름의 경우 연결할 때 사용 하는 대신 별칭을 사용 하는 것이 좋습니다. 

예제:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Azure AD를 통해 사용자를 인증 하는 경우 사용자에 게 Azure Database for MySQL 데이터베이스 내의 개체에 액세스할 수 있는 권한이 부여 되지 않습니다. 사용자에 게 필요한 권한을 수동으로 부여 해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 Azure AD 그룹 만들기

데이터베이스에 대 한 액세스를 위해 Azure AD 그룹을 사용 하도록 설정 하려면 사용자에 대해와 동일한 메커니즘을 사용 합니다. 대신 그룹 이름을 지정 합니다.

**예:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

로그인 할 때 그룹의 구성원은 개인 액세스 토큰을 사용 하지만 사용자 이름으로 지정 된 그룹 이름으로 서명 합니다.

## <a name="token-validation"></a>토큰 유효성 검사

Azure Database for MySQL의 Azure AD 인증은 사용자가 MySQL 서버에 존재 하는지 확인 하 고 토큰 내용의 유효성을 검사 하 여 토큰의 유효성을 검사 합니다. 다음 토큰 유효성 검사 단계를 수행 합니다.

-   토큰이 Azure AD에 의해 서명 되었으며 변조 되지 않았습니다.
-   서버와 연결 된 테 넌 트에 대해 Azure AD에서 토큰을 발급 했습니다.
-   토큰이 만료 되지 않았습니다.
-   토큰은 Azure Database for MySQL 리소스에 대 한 것 이며 다른 Azure 리소스는 아닙니다.

## <a name="compatibility-with-application-drivers"></a>응용 프로그램 드라이버와의 호환성

대부분의 드라이버가 지원 되지만 암호를 일반 텍스트로 보내기 위한 설정을 사용 해야 하므로 토큰을 수정 하지 않고 보냅니다.

* C/C++
  * 작업: 지원 됨
  * mysql-커넥터-c + +: 지원 됨
* Java
  * 커넥터/J (mysql-커넥터-java): 지원 됨, `useSSL` 설정을 활용 해야 함
* Python
  * 커넥터/Python: 지원 됨
* Ruby
  * mysql2: 지원 됨
* .NET
  * mysql-커넥터-net: 지원 됨, mysql_clear_password 플러그 인을 추가 해야 합니다.
  * mysql-net/MySqlConnector: 지원 됨
* Node.js
  * mysqljs: 지원 되지 않음 (패치 없이 일반 텍스트에서 토큰을 전송 하지 않음)
  * 노드-mysql2: 지원 됨
* Perl
  * DBD:: mysql: 지원 됨
  * Net:: MySQL: 지원 되지 않음
* Go
  * go-sql 드라이버: 지원 됨, 연결 문자열에 `?tls=true&allowCleartextPasswords=true` 추가

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL 단일 서버를 사용 하 여 Azure Active Directory 인증](concepts-azure-ad-authentication.md) 에 대 한 전체 개념 검토

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
