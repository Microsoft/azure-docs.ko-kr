---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성
description: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687977"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성

이 문서에서는 서버 그룹의 보안과 관련 된 다양 한 측면에 대해 설명 합니다.
- 미사용 암호화
- 사용자 관리
   - 일반적인 큐브 뷰
   - _Postgres_ 관리 사용자의 암호 변경
- 감사

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>미사용 암호화
데이터베이스를 저장 하는 디스크를 암호화 하거나 데이터베이스 함수를 사용 하 여 삽입 또는 업데이트 하는 데이터를 암호화 하 여 미사용 암호화를 구현할 수 있습니다.

### <a name="hardware-linux-host-volume-encryption"></a>하드웨어: Linux 호스트 볼륨 암호화
시스템 데이터 암호화를 구현 하 여 Azure Arc에서 사용 하는 디스크에 상주 하는 데이터를 보호 Data Services 설정 합니다. 이 항목에 대 한 자세한 내용은 다음 항목을 참조 하세요.
- 일반적으로 Linux에서 [저장소 데이터 암호화](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) 
- LUKS `cryptsetup` encrypt 명령 (Linux)을 사용 하는 디스크 암호화 명령 (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) 특히, Azure Arc 사용 Data Services은 사용자가 제공 하는 물리적 인프라에서 실행 되므로 인프라 보안을 담당 하 고 있습니다.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>소프트웨어: `pgcrypto` 서버 그룹에서 PostgreSQL 확장 사용
Azure Arc 설치를 호스트 하는 데 사용 되는 디스크를 암호화 하는 것 외에도, Azure Arc enabled PostgreSQL Hyperscale 서버 그룹을 구성 하 여 응용 프로그램이 데이터베이스의 데이터를 암호화 하는 데 사용할 수 있는 메커니즘을 노출할 수 있습니다. `pgcrypto`확장은 `contrib` Postgres 확장의 일부 이며 Azure Arc Enabled PostgreSQL hyperscale 서버 그룹에서 사용할 수 있습니다. 여기에서 확장에 대 한 세부 정보를 찾을 수 있습니다 `pgcrypto` . [](https://www.postgresql.org/docs/current/pgcrypto.html)
요약 하자면, 다음 명령을 사용 하 여 확장을 사용 하도록 설정 하 고 만든 다음 사용 합니다.


#### <a name="create-the-pgcrypto-extension"></a>확장 만들기 `pgcrypto`
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 표준 PostgreSQL 쿼리를 실행 합니다.
```console
CREATE EXTENSION pgcrypto;
```

> 연결 방법에 대 한 자세한 내용은 [여기](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) 에서 찾을 수 있습니다.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>서버 그룹에서 사용할 준비가 된 확장 목록을 확인 하십시오.
`pgcrypto`서버 그룹에서 사용할 수 있는 확장을 나열 하 여 확장을 사용할 준비가 되었는지 확인할 수 있습니다.
선택한 클라이언트 도구를 사용 하 여 서버 그룹에 연결 하 고 표준 PostgreSQL 쿼리를 실행 합니다.
```console
select * from pg_extension;
```
`pgcrypto`위에 표시 된 명령을 사용 하 여 사용 하도록 설정 하 고 만들었는지 확인 해야 합니다.

#### <a name="use-the-pgcrypto-extension"></a>확장 사용 `pgcrypto`
이제에서 제공 하는 함수를 사용 하도록 응용 프로그램 코드를 조정할 수 있습니다 `pgcrypto` .
- 일반 해시 함수
- 암호 해시 함수
- PGP 암호화 함수
- 원시 암호화 함수
- 임의 데이터 함수

예를 들어 해시 값을 생성 합니다. 명령 실행:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

는 다음 해시를 반환 합니다.

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

또는 예를 들면 다음과 같습니다.

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

는 다음 해시를 반환 합니다.

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

또는 예를 들어 암호와 같은 암호화 된 데이터를 저장 합니다.

내 응용 프로그램이 다음 표에 있는 암호를 저장 한다고 가정해 보겠습니다.

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

사용자를 만들 때 암호를 암호화 합니다.

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

이제 암호가 암호화 된 것을 볼 수 있습니다.

```console
select * from mysecrets;
```

출력:

- USERid: 1
- 사용자 이름: 나
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

응용 프로그램에 연결 하 고 암호를 전달 하면 테이블에서 조회 되 `mysecrets` 고, 응용 프로그램에 제공 된 암호와 테이블에 저장 된 암호 사이에 일치 하는 항목이 있으면 사용자 이름이 반환 됩니다. 예를 들면 다음과 같습니다.

- 잘못 된 암호를 전달 합니다.
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   출력 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 올바른 암호를 전달 합니다.

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   출력:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

이 작은 예제는 Azure Arc의 미사용 데이터 (암호화 된 데이터 저장)에서 Postgres 확장을 사용 하 여 PostgreSQL Hyperscale을 암호화 하 `pgcrypto` 고 응용 프로그램에서 `pgcrypto` 이 암호화 된 데이터를 조작 하는 데 제공 하는 함수를 사용할 수 있다는 것을 보여 줍니다.

## <a name="user-management"></a>사용자 관리
### <a name="general-perspectives"></a>일반적인 큐브 뷰
표준 Postgres 방법을 사용 하 여 사용자 또는 역할을 만들 수 있습니다. 그러나 이 작업을 수행하는 경우 코디네이터 역할에서만 이러한 아티팩트를 사용할 수 있습니다. 미리 보기 중에 이러한 사용자/역할은 아직 코디네이터 노드 외부 및 서버 그룹의 작업자 노드에 배포된 데이터에 액세스할 수 없습니다. 그 이유는 미리 보기에서 사용자 정의가 작업자 노드에 복제되지 않기 때문입니다.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>_Postgres_ 관리 사용자의 암호 변경
Azure Arc enabled PostgreSQL Hyperscale에는 서버 그룹을 만들 때 암호를 설정 하는 표준 Postgres 관리 사용자 _Postgres_ 가 제공 됩니다.
암호를 변경 하는 명령의 일반적인 형식은 다음과 같습니다.
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

여기서 `--admin-password` 는 AZDATA_PASSWORD **session** 환경 변수에 값이 존재 하는 것과 관련 된 부울입니다.
AZDATA_PASSWORD **세션** 환경 변수가 존재 하 고 값을 포함 하는 경우 위의 명령을 실행 하면 postgres 사용자의 암호가이 환경 변수의 값으로 설정 됩니다.

AZDATA_PASSWORD **세션** 환경 변수가 존재 하지만 값이 없거나 AZDATA_PASSWORD **session** 환경 변수가 없는 경우 위의 명령을 실행 하면 사용자에 게 암호를 대화형으로 입력 하 라는 메시지가 표시 됩니다.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>대화형 방식으로 postgres 관리 사용자의 암호 변경

1. AZDATA_PASSWORD **session** 환경 변수를 삭제 하거나 해당 값을 삭제 합니다.
2. 명령 실행:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   예
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   암호를 입력 하 고 확인 하 라는 메시지가 표시 됩니다.
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   암호를 업데이트 하는 동안 명령의 출력에 다음이 표시 됩니다.
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>AZDATA_PASSWORD **세션** 환경 변수를 사용 하 여 postgres 관리 사용자의 암호를 변경 합니다.
1. AZDATA_PASSWORD **session** 환경 변수의 값을 암호를 원하는 값으로 설정 합니다.
2. 다음 명령을 실행 합니다.
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   예
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   암호를 업데이트 하는 동안 명령의 출력에 다음이 표시 됩니다.
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> AZDATA_PASSWORD 세션의 환경 변수가 있는지 확인 하려면 다음을 실행 합니다.
> - Linux 클라이언트에서 다음을 수행 합니다.
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - PowerShell을 사용 하는 Windows 클라이언트:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>감사

감사 시나리오의 경우 `pgaudit` Postgres의 확장을 사용 하도록 서버 그룹을 구성 하십시오. 자세한 `pgaudit` 내용은 [ `pgAudit` GitHub 프로젝트](https://github.com/pgaudit/pgaudit/blob/master/README.md)를 참조 하세요. `pgaudit`서버 그룹에서 확장을 사용 하도록 설정 하려면 [PostgreSQL 확장 사용](using-extensions-in-postgresql-hyperscale-server-group.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계
- [ `pgcrypto` 확장](https://www.postgresql.org/docs/current/pgcrypto.html) 참조
- [PostgreSQL 확장 사용](using-extensions-in-postgresql-hyperscale-server-group.md) 을 참조 하세요.

