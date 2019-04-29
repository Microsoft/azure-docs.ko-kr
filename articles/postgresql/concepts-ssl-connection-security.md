---
title: PostgreSQL용 Azure 데이터베이스에서 SSL 연결 구성
description: SSL 연결을 올바르게 사용하기 위해 PostgreSQL용 Azure 데이터베이스 및 연결된 애플리케이션을 구성하는 방법에 대한 지침 및 정보
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871418"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 SSL 연결 구성
PostgreSQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용해서 PostgreSQL 서비스에 클라이언트 애플리케이션을 연결하는 것을 선호합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 SSL 연결을 요구하도록 구성됩니다. 필요에 따라 클라이언트 애플리케이션이 SSL 연결을 지원하지 않을 경우 데이터베이스 서비스에 연결하기 위해 SSL을 요구하지 않도록 설정할 수 있습니다. 

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용
Azure Portal 및 CLI를 통해 프로비전된 모든 MySQL용 Azure 데이터베이스 서버의 경우 SSL 연결 적용이 기본적으로 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="configure-enforcement-of-ssl"></a>SSL 적용 구성
필요에 따라 SSL 연결 적용을 사용하지 않도록 설정할 수 있습니다. Microsoft Azure는 항상 향상된 보안을 위해 **SSL 연결 적용** 설정을 사용하는 것을 권장합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용
PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다. 

![연결 보안 - SSL 적용 사용 안 함](./media/concepts-ssl-connection-security/1-disable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용
Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>애플리케이션 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.
Drupal 및 Django와 같은 데이터베이스 서비스용 PostgreSQL을 사용하는 많은 일반적인 애플리케이션 프레임워크는 기본적으로 설치하는 동안 SSL을 사용하도록 설정하지 않습니다. SSL 연결의 사용 설정은 설치 후 또는 애플리케이션 특정 CLI 명령을 통해 수행되어야 합니다. PostgreSQL 서버에 SSL 연결이 적용되어 있고 연결된 애플리케이션이 올바르게 구성되어 있지 않은 경우 애플리케이션이 데이터베이스 서버에 연결하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 애플리케이션
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. 다음 단계를 참조하여 .cer 파일을 얻고, 인증서를 디코딩한 후 애플리케이션에 바인딩하세요.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>CA(인증 기관)에서 인증서 파일 다운로드 
PostgreSQL용 Azure 데이터베이스 서버와 함께 SSL을 통해 통신하는 데 필요한 인증서는 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)에 있습니다. 인증서 파일을 로컬로 다운로드합니다.

### <a name="install-a-cert-decoder-on-your-machine"></a>인증서 디코더를 컴퓨터에 설치 
사용할 수 있습니다 [OpenSSL](https://github.com/openssl/openssl) 데이터베이스 서버로 안전 하 게 연결할 응용 프로그램에 필요한 인증서 파일을 디코딩할 수 있습니다. OpenSSL을 설치 하는 방법에 알아보려면 참조를 [OpenSSL 설치 지침](https://github.com/openssl/openssl/blob/master/INSTALL)합니다. 


### <a name="decode-your-certificate-file"></a>인증서 파일 디코딩
다운로드한 루트 CA 파일은 암호화된 형식입니다. OpenSSL을 사용하여 인증서 파일을 디코딩합니다. 이렇게 하려면 다음 OpenSSL 명령을 실행합니다.

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>SSL 인증서 인증을 사용하여 PostgreSQL용 Azure 데이터베이스에 연결
이제 인증서를 성공적으로 디코딩했으므로 SSL을 통해 데이터베이스 서버에 안전하게 연결할 수 있습니다. 서버 인증서 확인을 허용하려면 사용자의 홈 디렉터리에 있는 파일 ~/.postgresql/root.crt에 인증서를 배치해야 합니다. (Microsoft Windows에서 이 파일의 이름은 %APPDATA%\postgresql\root.crt입니다.) 

#### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용하여 PostgreSQL 서버에 성공적으로 연결하는 방법을 보여 줍니다. 만든 `root.crt` 파일과 `sslmode=verify-ca` 또는 `sslmode=verify-full` 옵션을 사용합니다.

PostgreSQL 명령줄 인터페이스를 사용하여 다음 명령을 실행합니다.
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
성공하면 다음과 같은 출력이 표시됩니다.
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL을 위한 연결 라이브러리](concepts-connection-libraries.md)에 따라 다양한 애플리케이션 연결 옵션 검토
