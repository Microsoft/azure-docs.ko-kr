---
title: 보안 웹 응용 프로그램 개발 | Microsoft Docs
description: 이 간단한 샘플 앱은 Azure에서 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선 하는 보안 모범 사례를 구현 합니다.
keywords: 수치
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 640900458eccc36afe58cb148ffd7b94b43be879
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934908"
---
# <a name="develop-a-secure-web-app"></a>보안 웹 앱 개발

이 샘플은 Azure에서 앱을 개발 하기 위한 보안 리소스에 대 한 링크가 포함 된 웹 페이지를 표시 하는 간단한 Python 앱입니다. 앱은 Azure에서 앱을 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선 하는 데 도움이 될 수 있는 보안 모범 사례를 구현 합니다.

응용 프로그램 구성 요소가 올바르게 구성 되었는지 확인 하려면이 문서에서 설명 하는 단계를 순서 대로 수행 해야 합니다. 데이터베이스, Azure App Service Azure Key Vault 인스턴스 및 Azure 애플리케이션 게이트웨이 인스턴스는 서로 종속 됩니다.

배포 스크립트는 인프라를 설정 합니다. 배포 스크립트를 실행 한 후 구성 요소와 서비스를 함께 연결 하려면 Azure Portal에서 몇 가지 수동 구성을 수행 해야 합니다.

샘플 앱은 응용 프로그램에서 보안 조치를 구현 하려는 Azure에서 응용 프로그램을 개발 하는 초보자를 목표로 합니다.

이 앱을 개발 하 고 배포 하는 방법에 대 한 자세한 내용은 다음과 같습니다.

- Azure Key Vault 인스턴스를 만들고 저장소에서 암호를 저장 하 고 검색 합니다.
- Azure Database for PostgreSQL를 배포 하 고, 보안 암호를 설정 하 고, 액세스 권한을 부여 합니다.
- Linux 용 Azure Web Apps에서 알파인 Linux 컨테이너를 실행 하 고 Azure 리소스에 대해 관리 id를 사용 하도록 설정 합니다.
- [OWASP Top 10 규칙 집합](https://coreruleset.org/)을 사용 하는 방화벽을 사용 하 여 Azure 애플리케이션 게이트웨이 인스턴스를 만들고 구성 합니다.
- Azure 서비스를 사용 하 여 전송 중인 데이터와 미사용 데이터의 암호화를 사용 하도록 설정 합니다.

이 앱을 개발 하 고 배포한 후 설명 된 구성 및 보안 측정값과 함께 다음 샘플 웹 앱을 설정 합니다.

![샘플 웹 앱](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>아키텍처
앱은 3 개의 계층을 포함 하는 일반적인 n 계층 응용 프로그램입니다. 모니터링 및 비밀 관리 구성 요소가 통합 된 프런트 엔드, 백 엔드 및 데이터베이스 계층이 다음과 같이 표시 됩니다.

![앱 아키텍처](./media/secure-web-app/architecture.png)

아키텍처는 다음 구성 요소로 구성 됩니다.

- [Azure Application Gateway](../../application-gateway/index.yml). 응용 프로그램 아키텍처에 대 한 게이트웨이 및 방화벽을 제공 합니다.
- [Linux의 Azure Web Apps](../../app-service/containers/app-service-linux-intro.md). Linux 환경에서 Python 앱을 실행 하기 위한 컨테이너 런타임을 제공 합니다.
- [Azure Key Vault](../../key-vault/index.yml) 앱의 암호를 저장 및 암호화 하 고 그에 대 한 액세스 정책 생성을 관리 합니다.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). 앱 데이터를 안전 하 게 저장 합니다.
- [Azure Security Center](../../security-center/index.yml) 및 [Azure 애플리케이션 정보](../../azure-monitor/app/app-insights-overview.md) 앱의 작동에 대 한 모니터링 및 경고를 제공 합니다.

## <a name="threat-model"></a>위협 모델
위협 모델링은 비즈니스 및 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 한 다음 적절 한 완화 계획이 적용 되도록 하는 프로세스입니다.

이 샘플에서는 [Microsoft Threat Modeling Tool](threat-modeling-tool.md) 를 사용 하 여 보안 샘플 앱에 대 한 위협 모델링을 구현 했습니다. 구성 요소와 데이터 흐름을 다이어그램으로 작성 하 여 개발 프로세스 초기에 문제 및 위협을 식별할 수 있습니다. 그러면 나중에 시간과 비용이 절약 됩니다.

샘플 앱에 대 한 위협 모델은 다음과 같습니다.

![위협 모델](./media/secure-web-app/threat-model.png)

위협 모델링 도구가 생성 하는 몇 가지 샘플 위협과 잠재적 취약성은 다음 스크린샷에 나와 있습니다. 위협 모델은 노출 된 공격 노출 영역에 대 한 개요를 제공 하 고 개발자에 게 문제를 완화 하는 방법을 고려 하는 메시지를 표시 합니다.

![위협 모델 출력](./media/secure-web-app/threat-model-output.png)

예를 들어, 이전 위협 모델 출력의 SQL 삽입은 사용자 입력을 데이터 하 고 Azure Database for PostgreSQL의 저장 된 함수를 사용 하 여 완화할 수 있습니다. 이렇게 완화 하면 데이터 읽기 및 쓰기 중에 쿼리를 임의로 실행할 수 없습니다.

개발자는 위협 모델 출력의 각 위협을 완화 하 여 시스템의 전반적인 보안을 향상 시킵니다.

## <a name="deployment"></a>배포
다음 옵션을 사용 하 여 Linux를 Azure App Service에서 실행할 수 있습니다.

- 지원 기술 (Python, Ruby, PHP, Java, node.js, .NET Core)을 사용 하 여 만든 Azure의 미리 작성 된 Microsoft 컨테이너 목록에서 컨테이너를 선택 합니다.
- 사용자가 작성 한 컨테이너를 사용 합니다. 이미지의 원본으로 사용자 고유의 컨테이너 레지스트리를 선택 하 고 HTTP를 지 원하는 사용 가능한 여러 기술에 대해 빌드합니다.

이 예제에서는 App Service에 webapp를 배포 하 고 리소스를 만드는 배포 스크립트를 실행 합니다.

앱은 아래에 표시 된 다양 한 배포 모델을 사용할 수 있습니다.

![배포 데이터 흐름 다이어그램](./media/secure-web-app/deployment.png)

Azure에 앱을 배포 하는 방법에는 다음을 비롯 한 여러 가지가 있습니다.

- Azure 리소스 관리자 템플릿
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

이 응용 프로그램 사용:

- 컨테이너 이미지를 만들고 빌드하기 위한 [Docker](https://docs.docker.com/) 입니다.
- 배포에 대 한 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 입니다.
- 컨테이너 레지스트리 인 [Docker 허브](https://hub.docker.com/) .

## <a name="security-considerations"></a>보안 고려 사항

### <a name="network"></a>네트워크
샘플 앱은 네트워크에서 들어오고 나가는 전송 중인 데이터에 대해 종단 간 SSL 암호화를 사용 합니다. 게이트웨이는 자체 서명 된 인증서로 구성 됩니다.
> [!IMPORTANT]
> 이 데모에서는 자체 서명 된 인증서를 사용 합니다. 프로덕션 환경에서는 확인 된 CA (인증 기관)에서 인증서를 가져와야 합니다.

또한 응용 프로그램 방화벽은 네트워크 트래픽에 악의적인 트래픽이 감지 될 때 들어오는 트래픽과 경고 관리자를 검사 합니다.
Application Gateway는 위협 모델에서 발견 된 DDoS 및 SQL 삽입 위협에 대 한 가능성을 완화 합니다.

### <a name="identity"></a>클레임
포털에 로그인 하기 위해 샘플 앱은 리소스에 대 한 액세스 권한을 할당 받은 Azure Active Directory (Azure AD) 관리자에 대해 Multi-factor Authentication을 사용 합니다.
샘플 앱은 관리 되는 id를 사용 하 여 Azure Key Vault에서 비밀을 읽고 검색할 수 있는 권한을 얻고, 앱이 비밀을 읽을 수 있는 자격 증명 및 토큰을 하드 코드 하지 않아도 되도록 합니다. 관리 id를 사용 하는 경우 Azure AD는 앱이 암호를 읽고 수정 하는 데 필요한 서비스 주체를 자동으로 만듭니다.

Azure 리소스 및 MFA에 대 한 관리 되는 id를 사용 하면 악의적 사용자가 권한을 획득 하 고 시스템에서 해당 권한을 에스컬레이션 하기가 더 어려워집니다. 위협 모델에서이 위협을 지적 했습니다.
앱은 oauth를 사용 하 여 OAuth 응용 프로그램에 등록 된 사용자가 앱에 로그인 할 수 있도록 합니다.

### <a name="storage"></a>스토리지
PostgreSQL 데이터베이스의 데이터는 Azure Database for PostgreSQL에 의해 자동으로 암호화 됩니다. 이 데이터베이스는 배포 된 App Service 웹 앱만 올바른 인증 자격 증명을 사용 하 여 데이터베이스 리소스에 액세스할 수 있도록 App Service IP 주소에 권한을 부여 합니다.

### <a name="logging-and-auditing"></a>로깅 및 감사
앱은 Application Insights를 사용 하 여 로깅을 구현 하 여 발생 하는 메트릭, 로그 및 예외를 추적 합니다. 이 로깅은 응용 프로그램의 상태를 개발자 및 운영 팀 멤버에 게 알리기 위해 충분 한 앱 메타 데이터를 제공 합니다. 또한 보안 인시던트의 경우 역 추적에 충분 한 데이터를 제공 합니다.

## <a name="cost-considerations"></a>비용 고려 사항
Azure 계정이 아직 없는 경우 무료 계정을 만들 수 있습니다. 무료 [계정 페이지로](https://azure.microsoft.com/free/) 이동 하 여 시작 하 고, 무료 Azure 계정으로 수행할 수 있는 작업을 확인 하 고, 12 개월 동안 무료 인 제품을 알아보세요.

보안 기능을 사용 하 여 샘플 앱에 리소스를 배포 하려면 일부 프리미엄 기능에 대해 비용을 지불 해야 합니다. 앱 크기를 조정 하 고 Azure에서 제공 하는 무료 계층 및 평가판을 응용 프로그램 요구 사항에 맞게 업그레이드 해야 하는 경우 비용이 늘어날 수 있습니다. Azure [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 비용을 예측 합니다.

## <a name="deploy-the-solution"></a>솔루션 배포
### <a name="prerequisites"></a>필수 구성 요소
응용 프로그램을 실행 하려면 다음과 같은 도구를 설치 해야 합니다.

- 응용 프로그램 코드를 수정 하 고 볼 수 있는 코드 편집기입니다. [Visual Studio Code](https://code.visualstudio.com/) 는 오픈 소스 옵션입니다.
- 개발 컴퓨터에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) 합니다.
- 시스템에서 [Git](https://git-scm.com/) 를 설정 합니다. Git은 소스 코드를 로컬에서 복제 하는 데 사용 됩니다.
- 사용자에 게 친숙 한 방식으로 JSON을 쿼리 하는 UNIX 도구인 [jq](https://stedolan.github.io/jq/)입니다.

샘플 앱의 리소스를 배포 하려면 Azure 구독이 필요 합니다. Azure 구독이 없는 경우 [무료 계정을 만들어](https://azure.microsoft.com/free/) 샘플 앱을 테스트할 수 있습니다.

이러한 도구를 설치 하 고 나면 Azure에 앱을 배포할 준비가 되었습니다.

### <a name="environment-setup"></a>환경 설정
배포 스크립트를 실행 하 여 환경 및 구독을 설정 합니다.

1. 소스 코드 리포지토리를 복제 하려면 다음 Git 명령을 사용 합니다.

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. 디렉터리로 이동 하려면 다음 명령을 사용 합니다.

   ```
   cd tutorial-project/scripts
   ```

3. Scripts 폴더에는 사용 중인 플랫폼 (Windows 또는 Linux)과 관련 된 파일이 있습니다. Azure CLI 이미 설치 되어 있으면 명령 프롬프트에서 다음 CLI 명령을 실행 하 여 Azure 계정에 로그인 합니다.

   ``` azurecli
   az login
   ```

브라우저가 열리고 자격 증명을 사용 하 여 로그인 됩니다. 로그인 한 후 명령 프롬프트에서 리소스 배포를 시작할 수 있습니다.

배포 스크립트 `deploy-powershell.ps1` 및 `deploy-bash.sh` 는 전체 응용 프로그램을 배포 하는 코드를 포함 합니다.
솔루션을 배포 하려면 다음을 수행 합니다.

1. PowerShell을 사용 하는 경우 지역 `deploy-powershell.ps1` 및 리소스 그룹 `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` 이름을 적절 한 Azure 지역으로 바꾸고 리소스 그룹의 이름을 입력 하 여 파일을 실행 합니다.
2. Linux에서를 입력 `deploy-bash.sh` `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`하 여 파일을 실행 하는 경우 다음을 입력 하 여 파일 실행 파일을 실행 해야 할 수 있습니다.`chmod +x deploy-bash.sh`

다음 예에서는 주요 구성 요소의 조각을 소개 합니다. 배포 파일을 실행 하 여 예제를 개별적으로 또는 나머지 구성 요소와 함께 배포할 수 있습니다.

### <a name="implementation-guidance"></a>구현 지침
배포 스크립트는 네 단계로 나눌 수 있는 하나의 스크립트입니다. 각 단계에서 [아키텍처 다이어그램](#architecture)에 있는 Azure 리소스를 배포 하 고 구성 합니다.

네 가지 단계는 다음과 같습니다.

- Azure Key Vault를 배포 합니다.
- Azure Database for PostgreSQL를 배포 합니다.
- Linux에서 Azure Web Apps를 배포 합니다.
- 웹 응용 프로그램 방화벽을 사용 하 여 Application Gateway를 배포 합니다.

각 단계는 이전에 배포 된 리소스의 구성을 사용 하 여 이전 단계를 기반으로 합니다.

구현 단계를 완료 하려면 [필수 구성 요소](#prerequisites)아래에 나열 된 도구를 설치 했는지 확인 합니다.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault 배포
이 섹션에서는 암호 및 인증서를 저장 하는 데 사용 되는 Azure Key Vault 인스턴스를 만들고 배포 합니다.

배포를 완료 한 후 Azure에 Azure Key Vault 인스턴스를 배포 했습니다.

Azure CLI를 사용 하 여 Azure Key Vault을 배포 하려면 다음을 수행 합니다.

1. Azure Key Vault에 대 한 변수를 선언 합니다.
2. Azure Key Vault 공급자를 등록 합니다.
3. 인스턴스에 대 한 리소스 그룹을 만듭니다.
4. 3 단계에서 만든 리소스 그룹에 Azure Key Vault 인스턴스를 만듭니다.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
Key Vault를 사용 하 여 리소스에 액세스 하는 앱에서 Azure 리소스에 대 한 관리 되는 id를 사용 하는 것이 가장 좋습니다. Key Vault에 대 한 액세스 키가 코드 또는 구성에 저장 되지 않을 경우 보안 상태가 향상 됩니다.

#### <a name="deploy-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 배포
Azure Database for PostgreSQL는 다음과 같은 방식으로 작동 합니다. 먼저 데이터베이스 서버를 만든 다음 스키마와 데이터를 저장할 데이터베이스를 만듭니다.

배포를 완료 한 후에는 Azure에서 PostgreSQL 서버 및 데이터베이스를 실행 합니다.

Azure CLI를 사용 하 여 Azure Database for PostgreSQL을 배포 하려면 다음을 수행 합니다.

1. Azure CLI 및 Azure 구독 설정을 사용 하 여 터미널을 엽니다.
2. 데이터베이스에 액세스 하는 데 사용 되는 보안 사용자 이름 및 암호 조합을 생성 합니다. (이러한 항목을 사용 하는 앱에 대 한 Azure Key Vault에 저장 되어야 합니다.)
3. PostgreSQL 서버 인스턴스를 만듭니다.
4. 3 단계에서 만든 서버 인스턴스에 데이터베이스를 만듭니다.
5. PostgreSQL 인스턴스에서 PostgreSQL 스크립트를 실행 합니다.

아래 코드는 위의 KeyVault 배포 단계에서 Azure KeyVault에 저장 된 PGUSERNAME 및 PGUSERNAME 암호를 사용 합니다.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

데이터베이스를 배포한 후에는 Azure Key Vault에 자격 증명과 연결 문자열을 저장 해야 합니다.
Scripts 폴더 `functions.sql` 에는 저장 된 함수를 실행할 때 저장 된 함수를 만드는 PL/pgsql 코드를 포함 하는 파일이 있습니다. 이 파일을 실행 하면 입력이 매개 변수화 SQL 삽입이 제한 됩니다.

PostgreSQL는 데이터베이스에 연결 하는 `psql` 데 사용 되는 라는 도구와 함께 제공 됩니다. 를 실행 `functions.sql`하려면 로컬 컴퓨터에서 Azure Database for PostgreSQL 인스턴스에 연결 하 고 여기에서 실행 해야 합니다. Psql 도구 설치는 각 운영 체제에 PostgreSQL의 기본 설치에 포함 되어 있습니다.
자세한 내용은 [psql 설명서](https://www.postgresql.org/docs/9.3/app-psql.html)를 참조 하세요.

Azure Cloud Shell에도 `psql` 도구가 포함 됩니다. Cloud Shell 아이콘을 선택 하 여 Azure Portal에서 직접 Cloud Shell를 사용할 수 있습니다.

PostgreSQL 인스턴스에 대 한 원격 액세스를 사용 하도록 설정 하려면 PostgreSQL에서 IP 주소에 대 한 권한을 부여 해야 합니다.
**연결 보안** 탭으로 이동 하 여 **클라이언트 IP 추가**를 선택 하 고 새 설정을 저장 하 여이 액세스를 사용 하도록 설정 합니다.

![클라이언트 IP 권한 부여](./media/secure-web-app/add-client-ip-postgres.png)

로컬 psql 도구 대신 Cloud Shell를 사용 하는 경우 **Azure 서비스에 대 한 액세스 허용** 을 선택 하 고 해당 값을 **켜기** 로 변경 하 여 Cloud Shell 액세스를 허용 합니다.

그런 다음 Azure Portal에 있는 PostgreSQL 인스턴스의 **연결** 문자열 탭에서 연결 문자열 매개 변수를 사용 하 여 아래 psql 명령을 실행 하 여 인스턴스에 연결 합니다.
빈 중괄호를 데이터베이스의 연결 문자열 블레이드의 매개 변수로 바꾸고 암호는 Azure Key Vault 합니다.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

데이터베이스에 연결 되어 있는지 확인 한 후 다음 PL/pgSQL 스크립트를 실행 합니다. 이 스크립트는 데이터베이스에 데이터를 삽입 하는 데 사용 되는 저장 함수를 만듭니다.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


PostgreSQL에 대 한 SSL 및 CA (인증 기관) 확인을 설정 하는 방법에 대 한 자세한 내용은 [Azure Database for PostgreSQL에서 ssl 연결 구성](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security)을 참조 하세요.

컨테이너에 루트 인증서가 포함 되어 있습니다. 인증서를 얻기 위해 수행 하는 단계는 다음과 같습니다.

1. 인증 [기관](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)에서 인증서 파일을 다운로드 합니다.
2. [OpenSSL를 컴퓨터에 다운로드 하 여 설치](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security)합니다.
3. 인증서 파일 디코딩:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

PostgreSQL에 대 한 SSL 보안을 구성 하는 방법에 대 한 자세한 내용은 [Ssl 연결 보안 구성](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security)을 참조 하세요.

#### <a name="deploy-azure-web-apps-on-linux"></a>Linux에서 Azure Web Apps 배포
Azure는 Python, Ruby, C#및 Java와 같이 널리 사용 되는 언어에 대해 미리 작성 된 컨테이너 및 이미지 집합을 제공 하므로 Azure App Service 위에 Linux 서비스를 쉽게 빌드할 수 있습니다. 또한 Azure는 거의 모든 프로그래밍 언어를 Azure App Service 플랫폼에서 실행할 수 있는 사용자 지정 컨테이너를 지원 합니다.

배포 되는 앱은 최신 Ubuntu Linux 배포에서 실행 되는 간단한 Python 앱입니다. 자격 증명 관리 및 데이터 저장소에 대 한 이전 섹션에서 만든 Azure Key Vault 및 PostgreSQL 인스턴스에 연결 합니다.

다음 Docker 파일은 앱의 루트 폴더에 제공 됩니다.

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

위의 Dockerfile은 Azure Container Registry `mcr.microsoft.com/samples/basic-linux-app`에서 호스팅되는 컨테이너를 빌드하는 데 사용 됩니다.

코드는 다음과 같습니다.

1. App Service 인스턴스의 변수와 이름을 선언 합니다.
2. App Service 계획에 대 한 리소스 그룹을 만듭니다.
3. Linux 컨테이너 인스턴스에서 Azure Web Apps를 프로 비전 합니다.
4. 웹 앱 컨테이너에 대 한 로깅을 사용 하도록 설정 합니다.
5. 컨테이너의 앱 설정에서 일부 앱 구성을 설정 합니다.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

이 스크립트는 코드 또는 구성에서 암호를 하드 코딩 하지 않고 Azure Key Vault와 상호 작용 하는 데 MSI에서 사용할 수 있는 App Service 인스턴스에 대해 할당 된 id를 만듭니다.

포털의 Azure Key Vault 인스턴스로 이동 하 여 액세스 정책 탭에서 할당 된 id에 권한을 부여 합니다. **새 액세스 정책 추가**를 선택 합니다. **보안 주체 선택**에서 만든 App Service 인스턴스의 이름과 비슷한 응용 프로그램 이름을 검색 합니다.
응용 프로그램에 연결 된 서비스 사용자가 표시 되어야 합니다. 다음 스크린샷에 표시 된 것 처럼 it를 선택 하 여 액세스 정책 저장 페이지를 선택 합니다.

응용 프로그램은 키를 검색 하기만 하면 되므로 암호 옵션에서 **Get** 권한을 선택 하 여 액세스를 허용 하는 동시에 액세스를 허용 합니다.

![Key Vault 액세스 정책](./media/secure-web-app/kv-access-policy.png)

*Key Vault 액세스 정책 만들기*

액세스 정책을 저장 한 다음 **액세스** 정책 탭에서 새 변경 내용을 저장 하 여 정책을 업데이트 합니다.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>웹 응용 프로그램 방화벽을 사용 하 여 Application Gateway 배포
웹 앱에서는 서비스를 인터넷의 외부 세계에 직접 노출 하지 않는 것이 좋습니다.
부하 분산 및 방화벽 규칙은 들어오는 트래픽에 대 한 보안을 강화 하 고 제어 하 고 관리 하는 데 도움을 줍니다.

Application Gateway 인스턴스를 배포 하려면:

1. 응용 프로그램 게이트웨이를 저장할 리소스 그룹을 만듭니다.
2. 게이트웨이에 연결할 가상 네트워크를 프로 비전 합니다.
3. 가상 네트워크에서 게이트웨이에 대 한 서브넷을 만듭니다.
4. 공용 IP 주소를 프로 비전 합니다.
5. Application gateway를 프로 비전 합니다.
6. 게이트웨이에서 웹 응용 프로그램 방화벽을 사용 하도록 설정 합니다.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

이전 스크립트:

1. Azure에서 자체 서명 된 새 인증서를 만듭니다.
2. 자체 서명 된 인증서를 b a s e 64로 인코딩된 파일로 다운로드 합니다.
3. 자체 서명 된 인증서의 암호를 생성 합니다.
4. 인증서를 암호로 서명 된 PFX 파일로 내보냅니다.
5. Azure Key Vault에 인증서의 암호를 저장 합니다.

이 섹션에서는 응용 프로그램 게이트웨이를 배포 합니다.

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

배포를 완료 한 후에는 웹 응용 프로그램 방화벽이 설정 된 응용 프로그램 게이트웨이를 갖게 됩니다.

게이트웨이 인스턴스는 HTTPS에 대해 443 포트를 노출 합니다. 이 구성은 포트 443에서 HTTPS를 통해서만 앱에 액세스할 수 있도록 합니다.

사용 하지 않는 포트를 차단 하 고 공격 노출 영역 노출을 제한 하는 것이 보안 모범 사례입니다.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service 인스턴스에 네트워크 보안 그룹 추가

App Service 인스턴스를 가상 네트워크와 통합할 수 있습니다. 이러한 통합을 통해 앱의 들어오고 나가는 트래픽을 관리 하는 네트워크 보안 그룹 정책을 사용 하 여 구성할 수 있습니다.

1. 이 기능을 사용 하도록 설정 하려면 Azure 앱 서비스 인스턴스 블레이드의 **설정**에서 **네트워킹**을 선택 합니다. 오른쪽 창의 **VNet 통합**아래에서 **구성 하려면 여기를 클릭**하세요 .를 선택 합니다.

   ![새 가상 네트워크 통합](./media/secure-web-app/app-vnet-menu.png)

    *App Service에 대 한 새 가상 네트워크 통합*
1. 다음 페이지에서 **VNET 추가 (미리 보기)** 를 선택 합니다.

1. 다음 메뉴에서로 `hello-vnet`시작 하는 배포에서 만든 가상 네트워크를 선택 합니다. 새 서브넷을 만들거나 기존 서브넷을 선택할 수 있습니다.
   이 경우 새 서브넷을 만듭니다. **주소 범위** 를 **10.0.3.0/24** 로 설정 하 고 서브넷의이름을 서브넷으로 설정 합니다.

   ![가상 네트워크 구성 App Service](./media/secure-web-app/app-vnet-config.png)

    *App Service에 대 한 가상 네트워크 구성*

가상 네트워크 통합을 사용 하도록 설정 했으므로 응용 프로그램에 네트워크 보안 그룹을 추가할 수 있습니다.

1. 검색 상자를 사용 하 여 **네트워크 보안 그룹**을 검색 합니다. 결과에서 **네트워크 보안 그룹** 을 선택 합니다.

    ![네트워크 보안 그룹 검색](./media/secure-web-app/nsg-search-menu.png)

    *네트워크 보안 그룹 검색*

2. 다음 메뉴에서 **추가**를 선택 합니다. NSG의 **이름과** 이를 찾을 **리소스 그룹** 을 입력 합니다. 이 NSG는 application gateway의 서브넷에 적용 됩니다.

    ![NSG 만들기](./media/secure-web-app/nsg-create-new.png)

    *NSG 만들기*

3. NSG를 만든 후 선택 합니다. 블레이드의 **설정**에서 **인바운드 보안 규칙**을 선택 합니다. 443 포트를 통해 응용 프로그램 게이트웨이로 들어오는 연결을 허용 하도록 이러한 설정을 구성 합니다.

   ![NSG 구성](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 구성*

4. 게이트웨이 NSG에 대 한 아웃 바운드 규칙에서 서비스 태그 `AppService`를 대상으로 하는 규칙을 만들어 App Service 인스턴스에 대 한 아웃 바운드 연결을 허용 하는 규칙을 추가 합니다.

   ![NSG에 대 한 아웃 바운드 규칙 추가](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG에 대 한 아웃 바운드 규칙 추가*

    게이트웨이가 아웃 바운드 규칙을 가상 네트워크로 보낼 수 있도록 다른 아웃 바운드 규칙을 추가 합니다.

   ![다른 아웃 바운드 규칙 추가](./media/secure-web-app/nsg-outbound-vnet.png)

    *다른 아웃 바운드 규칙 추가*

5. NSG의 서브넷 블레이드에서 **연결**을 선택 하 고, 배포에서 만든 가상 네트워크를 선택 하 고, **gw**이라는 게이트웨이 서브넷을 선택 합니다. NSG가 서브넷에 적용 됩니다.

6. 이전 단계에서와 같이 App Service 인스턴스에 대 한 다른 NSG를 만듭니다. 이름을 지정 합니다. 응용 프로그램 게이트웨이 NSG와 마찬가지로 포트 443에 대 한 인바운드 규칙을 추가 합니다.

   이 앱의 경우를 포함 하지 않는 App Service Environment 인스턴스에 App Service 인스턴스가 배포 되어 있는 경우 App Service NSG의 인바운드 보안 그룹에서 포트 454-455를 열어 Azure Service Health 프로브를 허용 하는 인바운드 규칙을 추가할 수 있습니다. 구성:

   ![Azure Service Health 프로브에 대 한 규칙 추가](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure Service Health 프로브에 대 한 규칙 추가 (App Service Environment에만 해당)*

7. 아웃 바운드 보안 규칙에서 App Service 인스턴스가 PostgreSQL 데이터베이스와 통신할 수 있도록 하는 새로운 아웃 바운드 보안 규칙을 만듭니다. 다음과 같이 구성 합니다.

   ![아웃 바운드 PostgreSQL 연결을 허용 하는 규칙](./media/secure-web-app/nsg-outbound-postgresql.png)

   *아웃 바운드 PostgreSQL 연결을 허용 하는 규칙 추가*

공격 노출 영역을 제한 하려면 응용 프로그램 게이트웨이만 응용 프로그램에 액세스할 수 있도록 App Service 네트워크 설정을 수정 합니다.
이렇게 하려면 App Service 네트워크 탭으로 이동 하 고, **IP 제한** 탭을 선택 하 고, 응용 프로그램 게이트웨이의 ip만 서비스에 직접 액세스 하도록 허용 하는 허용 규칙을 만듭니다.

해당 개요 페이지에서 게이트웨이의 IP 주소를 검색할 수 있습니다. **Ip 주소 CIDR** 탭에서 ip 주소를 형식 `<GATEWAY_IP_ADDRESS>/32`으로 입력 합니다.

![게이트웨이만 허용](./media/secure-web-app/app-allow-gw-only.png)

*게이트웨이 IP만 App Service에 액세스할 수 있도록 허용*


#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth 구현

샘플 웹 앱 페이지에 배포 된 Azure 문서는 보호가 필요할 수 있는 앱의 리소스입니다. Azure AD (Azure Active Directory)를 사용 하 여 다양 한 인증 흐름을 사용 하 여 웹, 데스크톱 및 모바일 앱에 대 한 인증을 구현할 수 있습니다.
앱은 **Microsoft와 로그인**을 사용 하 여 앱에서 단일 테넌트 Azure AD 사용자의 목록에 추가 된 사용자의 프로필을 읽을 수 있도록 합니다.

Azure Portal에서 필요한 자격 증명을 사용 하도록 앱을 구성 합니다.

1. **Azure Active Directory**를 선택 하거나 검색 상자를 사용 하 여 검색 합니다.

2. **새 등록**선택:

   ![등록 만들기](./media/secure-web-app/ad-auth-create.png)

   *Azure AD 앱 등록 만들기*

3. 다음 페이지에서 앱 이름을 입력 합니다. **지원 되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택 합니다.
    **URI 리디렉션**에서 앱이 실행 될 기본 도메인을 입력 하 고 토큰 엔드포인트을 사용 하 여 하나를 추가 합니다. 예: *GATEWAY_HASH*. cloudapp.net/token.

   ![Azure AD 앱 등록 구성](./media/secure-web-app/ad-auth-type.png)

   *Azure AD 앱 등록 구성*

4. 등록 된 앱과 해당 정보를 표시 하는 화면이 표시 됩니다. Azure Key Vault 인스턴스에이 정보를 추가 해야 합니다.
   1. 응용 프로그램 (클라이언트) ID를 복사 하 고 Key Vault에로 `CLIENTID`저장 합니다.
   2. 이전 단계에서 입력 한 리디렉션 URI를 복사 하 고으로 `REDIRECTURI`저장 합니다.
   3. Microsoftonline.com 형식의 Azure AD 기본 디렉터리 이름을 복사 하 고 Key Vault에로 `TENANT`저장 합니다.
   4. 이전에 만든 Azure AD 앱의 **인증서 & 암호** 탭으로 이동 하 고 다음 스크린샷에 표시 된 것 처럼 **새 클라이언트 암호**를 선택 합니다. 만료 날짜를 설정 하 고 생성 된 값을 복사한 다음 Key Vault `CLIENTSECRET`에 저장 합니다.

      ![Azure AD 인증 암호](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 인증 암호*

   5. 명령줄/온라인 도구를 사용 하 여 안전한 임의 비밀 키를 생성 합니다. 로 `FLASKSECRETKEY`Key Vault에 저장 합니다. 응용 프로그램 프레임 워크는이 키를 사용 하 여 세션을 만듭니다.
        비밀 키를 생성 하는 방법을 알아보려면 [Flask 세션](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)을 참조 하세요.

5. 로그인을 구성한 후에는 Azure AD 링크에 사용자를 추가 하 여 리소스에 로그인 할 수 있도록 해야 합니다. 추가 하려면 Azure AD의 **사용자** 탭으로 이동 하 여 **모든 사용자**를 선택한 다음 **새 사용자** 또는 **새 게스트 사용자**를 선택 합니다. 테스트를 위해 게스트 사용자를 추가 하 고 사용자를 디렉터리에 초대할 수 있습니다. 또는 앱이 실행 되는 도메인의 유효성을 검사 한 경우 새 사용자를 추가할 수 있습니다. 이 예제에서는 Azure AD 테넌트에 등록 된 사용자만 액세스 하도록 등록할 수 있습니다. 다중 테넌트 로그인 액세스에 대 한 자세한 내용은 설명서를 참조 하세요.

   ![기본 도메인에 사용자 추가](./media/secure-web-app/ad-auth-add-user.png)

   *기본 Azure Active Directory 도메인에 사용자 추가*

Azure AD 구성 및 비밀을 Key Vault에 추가 하면 Azure OAuth 인증을 사용 하 여 사용자를 앱에 인증할 수 있습니다.
앱 코드에서이는 ADAL (Azure Active Directory 인증 라이브러리)에 의해 처리 됩니다.

비밀을 Key Vault 하 고 응용 프로그램이 암호 및 데이터베이스에 액세스할 수 있게 되 면 게이트웨이의 응용 프로그램 URL (https://GATEWAY_HASH.cloudapp.net) 를 통해 응용 프로그램 서비스에 연결할 수 있습니다.

Azure AD에 로그인 할 때 "사용자가 로그인 하려는 디렉터리에 등록 되지 않았습니다." 라는 오류 메시지가 표시 되 면 사용자를 추가 해야 합니다. 사용자를 추가 하려면 Azure AD의 **사용자** 탭으로 이동 하 고 세부 정보를 입력 하 여 사용자를 수동으로 추가 하거나 **게스트 초대** 블레이드에서 azure ad에 게스트 사용자로 전자 메일 주소를 입력 하 여 사용자를 초대 합니다.

#### <a name="deploy-application-insights"></a>Application Insights 배포
앱이 배포 되 고 작동 되었으므로 로깅 및 추적 데이터 컬렉션과 함께 앱 내에서 발생 하는 오류를 처리 해야 합니다.
로깅 및 추적 데이터 컬렉션은 앱에서 발생 하는 감사 이벤트에 대 한 뷰를 제공 합니다.

Application Insights은 사용자 또는 시스템에서 생성할 수 있는 로그를 수집 하는 서비스입니다.

Application Insights 인스턴스를 만들려면 다음을 수행 합니다.

1. Azure Portal의 검색 상자를 사용 하 여 **Application Insights** 를 검색 합니다.
2. **Application Insights**를 선택합니다. 여기에 표시 된 세부 정보를 제공 하 여 인스턴스를 만듭니다.

   ![Application Insights 인스턴스 만들기](./media/secure-web-app/app-insights-data.png)

배포가 완료 되 면 Application Insights 인스턴스가 있습니다.

Application Insights 인스턴스를 만든 후 응용 프로그램에서 클라우드로 로그를 보낼 수 있는 계측 키를 인식 하도록 해야 합니다. 이렇게 하려면 Application Insights 키를 검색 하 고 Azure에서 Application Insights에 대해 제공 하는 응용 프로그램 라이브러리 내에서 사용 합니다. 모범 사례는 키와 암호를 안전 하 게 유지 하기 위해 Azure Key Vault에 저장 하는 것입니다.

기본 샘플 응용 프로그램의 경우 Application Insights 인스턴스를 만든 후 앱에서 로그를 클라우드로 보낼 수 있도록 하는 계측 키를 인식 하도록 해야 합니다.
Key Vault에서 `APPINSIGHTSKEY` 비밀을 설정 하 고 해당 값을 계측 키로 설정 합니다. 이렇게 하면 앱에서 로그 및 메트릭을 Application Insights로 보낼 수 있습니다.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory Multi-factor Authentication 구현
관리자는 포털의 구독 계정이 보호 되는지 확인 해야 합니다. 구독은 만든 리소스를 관리 하기 때문에 공격에 취약 합니다. 구독을 보호 하려면 구독의 **Azure Active Directory** 탭에서 multi-factor Authentication을 사용 하도록 설정 합니다.

Azure AD는 특정 조건에 맞는 사용자 또는 사용자 그룹에 적용 되는 정책에 따라 작동 합니다.
Azure는 관리자가 포털에 로그인 하는 데 2 단계 인증이 필요 하도록 지정 하는 기본 정책을 만듭니다.
이 정책을 사용 하도록 설정 하면 로그 아웃 하 고 Azure Portal에 다시 로그인 하 라는 메시지가 표시 될 수 있습니다.

관리자 로그인에 대해 MFA를 사용 하도록 설정 하려면:

1. Azure Portal에서 **Azure Active Directory** 탭으로 이동 합니다.
2. 보안 범주 아래에서 조건부 액세스를 선택 합니다. 이 화면이 표시 됩니다.

   ![조건부 액세스-정책](./media/secure-web-app/ad-mfa-conditional-add.png)

새 정책을 만들 수 없는 경우:

1. **MFA** 탭으로 이동 합니다.
2. 무료 평가판을 구독 하려면 Azure AD Premium **무료 평가판** 링크를 선택 합니다.

   ![무료 평가판 Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

조건부 액세스 화면으로 돌아갑니다.

1. 새 정책 탭을 선택 합니다.
2. 정책 이름을 입력합니다.
3. MFA를 사용 하도록 설정할 사용자 또는 그룹을 선택 합니다.
4. **액세스 제어**에서 **허용** 탭을 선택한 후 **다단계 인증 필요** 및 기타 설정을 선택 합니다.

   ![MFA 요구](./media/secure-web-app/ad-mfa-conditional-add.png)

화면 위쪽의 확인란을 선택 하 여 정책을 사용 하도록 설정 하거나 **조건부 액세스** 탭에서이 작업을 수행할 수 있습니다. 이 정책을 사용 하도록 설정 하면 사용자가 포털에 로그인 하려면 MFA가 필요 합니다.

모든 Azure 관리자에 대해 MFA를 요구 하는 기준 정책이 있습니다. 포털에서 즉시 사용 하도록 설정할 수 있습니다. 이 정책을 사용 하도록 설정 하면 현재 세션이 무효화 되 고 다시 로그인 되도록 할 수 있습니다.

기준 정책을 사용 하지 않는 경우:
1.  **관리자 용 MFA 필요**를 선택 합니다.
2.  **즉시 정책 사용**을 선택 합니다.

   ![즉시 정책 사용을 선택 합니다.](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure 센티널을 사용 하 여 앱 및 리소스 모니터링

응용 프로그램이 커지면 리소스에서 수신 된 모든 보안 신호 및 메트릭을 집계 하 고 작업 지향 방식으로 유용 하 게 만드는 것이 어려워집니다.

Azure 센티널은 데이터를 수집 하 고, 가능한 위협의 유형을 검색 하 고, 보안 인시던트에 대 한 가시성을 제공 하도록 설계 되었습니다.
수동 작업을 대기 하는 동안 Azure 센티널은 미리 작성 된 플레이 북을 사용 하 여 경고 및 인시던트 관리 프로세스를 시작할 수 있습니다.

샘플 앱은 Azure 센티널에서 모니터링할 수 있는 몇 가지 리소스로 구성 되어 있습니다.
Azure 센티널을 설정 하려면 먼저 다양 한 리소스에서 수집 된 모든 데이터를 저장 하는 Log Analytics 작업 영역을 만들어야 합니다.

이 작업 영역을 만들려면 다음을 수행 합니다.

1. Azure Portal 검색 상자에서 **Log Analytics**를 검색 합니다. **Log Analytics 작업 영역**을 선택합니다.

   ![Log Analytics 작업 영역 검색](./media/secure-web-app/sentinel-log-analytics.png)

    *Log Analytics 작업 영역 검색*

2. 다음 페이지에서 **추가** 를 선택 하 고 작업 영역에 대 한 이름, 리소스 그룹 및 위치를 제공 합니다.
   ![Log Analytics 작업 영역 만들기](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Log Analytics 작업 영역 만들기*

3. 검색 상자를 사용 하 여 **Azure 센티널**을 검색 합니다.

   ![Azure 센티널 검색](./media/secure-web-app/sentinel-add.png)

    *Azure 센티널 검색*

4. **추가** 를 선택한 다음 이전에 만든 Log Analytics 작업 영역을 선택 합니다.

   ![Log Analytics 작업 영역 추가](./media/secure-web-app/sentinel-workspace-add.png)

    *Log Analytics 작업 영역 추가*

5. **Azure 센티널-데이터 커넥터** 페이지의 **구성**에서 **데이터 커넥터**를 선택 합니다. Azure 센티널의 분석을 위해 Log Analytics storage 인스턴스에 연결할 수 있는 Azure 서비스 배열이 표시 됩니다.

   ![Log Analytics 데이터 커넥터](./media/secure-web-app/sentinel-connectors.png)

    *Azure 센티널에 데이터 커넥터 추가*

   예를 들어 응용 프로그램 게이트웨이를 연결 하려면 다음 단계를 수행 합니다.

   1. Azure 애플리케이션 게이트웨이 인스턴스 블레이드를 엽니다.
   2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
   3. **진단 설정 추가**를 선택 합니다.

      ![Application Gateway 진단 추가](./media/secure-web-app/sentinel-gateway-connector.png)

      *Application Gateway 진단 추가*

   4. **진단 설정** 페이지에서 사용자가 만든 Log Analytics 작업 영역을 선택 하 고 수집 하 여 Azure 센티널로 보낼 모든 메트릭을 선택 합니다. **저장**을 선택합니다.

        ![Azure 센티널 커넥터 설정](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure 센티널 커넥터 설정*

  리소스의 메트릭은 Azure 센티널에 있으므로 쿼리 및 조사할 수 있습니다.

   Azure Key Vault, 공용 IP 주소, Azure Database for PostgreSQL, 계정에서 진단 로그를 지 원하는 모든 서비스에 대 한 진단 설정에 동일한 메트릭을 추가 합니다.

메트릭을 설정한 후 Azure 센티널에는 분석할 데이터가 있습니다.

## <a name="evaluate-and-verify"></a>평가 및 확인
아키텍처를 개발 하 고 배포한 후에는 코드와 배포 된 서비스가 보안 표준을 충족 하는지 확인 해야 합니다. 다음은 소프트웨어를 확인 하는 데 사용할 수 있는 몇 가지 단계입니다.

- 정적 코드 분석
- 취약성 검색
- 응용 프로그램 종속성의 취약점 찾기 및 수정

보안 개발의 모범 사례에 대 한 기본 구성 요소는 다음과 같습니다.

### <a name="static-code-analysis"></a>정적 코드 분석
샘플 앱의 경우 정적 분석 도구를 사용 하 여 확인 하려면 taint 확인 및 데이터 흐름 분석과 같은 기술을 사용 하 여 앱 코드에서 취약성을 검색 해야 합니다. Python 정적 분석 도구를 통해 앱을 안전 하 게 보호할 수 있습니다.

**Lint**

Python lint 라이브러리인 PyFlakes는 다음과 같이 앱에서 데드 코드 및 사용 되지 않는 함수를 제거 하는 데 도움이 됩니다.

![PyFlakes](./media/secure-web-app/pyflakes.png)

Lint는 런타임 중에 코드를 정리 하 고 오류를 줄일 수 있는 힌트와 가능한 변경 내용을 제공 합니다.

**PyLint**

PyLint은이 프로젝트에 대 한 대부분의 값을 제공 합니다. 코드 표준 검사, 오류 검사 및 리팩터링 팁을 수행 하 여 서버에서 실행 되는 코드가 안전한 지 확인 합니다. PyLint를 사용 하 여 코드를 업데이트 하면 다음 이미지에 표시 된 것 처럼 버그를 제거 하 고 PyLint 등급을 높일 수 있습니다.

![PyLint 전](./media/secure-web-app/before-pylint.png)

*PyLint 전*

Lint 도구에서 발견 한 코드 오류 중 일부를 해결 한 후에는 코드에서 오류가 발생 하는 것이 더 확실 하지 않습니다. 오류를 수정 하면 코드를 프로덕션 환경에 배포할 때 발생할 수 있는 보안 위험을 크게 줄일 수 있습니다.

![Pylint 후](./media/secure-web-app/after-pylint.png)

*PyLint 후*

### <a name="vulnerability-scanning"></a>취약성 검색
[OWASP의 ZAP](https://www.zaproxy.org/) 도구는 샘플 앱에서 취약성을 확인 하는 데 사용할 수 있는 오픈 소스 웹 응용 프로그램 취약성 스캐너입니다. 샘플 앱에서 도구를 실행 하면 가능한 몇 가지 오류 및 공격 벡터가 표시 됩니다.

![ZAP 도구](./media/secure-web-app/zap-tool.png)

*ZAP 도구*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>앱 종속성의 취약점 찾기 및 수정
응용 프로그램 종속성을 찾아 수정 하려면 [OWASP의 종속성 검사](https://www.owasp.org/index.php/OWASP_Dependency_Check)를 사용 하면 됩니다.

안전은 종속성을 확인 하는 유사한 응용 프로그램입니다. [GitHub](https://github.com/pyupio/safety)에서 찾을 수 있습니다. 안전 검사는 잘 알려진 취약성 데이터베이스에서 발견 되었습니다.

![안전성](./media/secure-web-app/pysafety.png)

*안전성*

## <a name="next-steps"></a>다음 단계
다음 문서는 보안 응용 프로그램을 디자인, 개발 및 배포 하는 데 도움이 될 수 있습니다.

- [디자인](secure-design.md)
- [개발](secure-develop.md)
- [배포](secure-deploy.md)
