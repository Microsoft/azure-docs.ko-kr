---
title: Multi-Factor Authentication 구성
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics용 SSMS에서 다단계 인증을 사용하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454375"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio 및 Azure AD에 대한 Multi-factor Authentication(MFA) 구성
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 SSMS(SQL Server Management Studio)에서 Azure AD(Azure Active Directory) MFA(다단계 인증)을 사용하는 방법을 보여 줍니다. Azure AD MFA는 SSMS 또는 SqlPackage.exe를 [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결할 때 사용할 수 있습니다. 다단계 인증에 대한 개요는 [SQL Database, SQL Managed Instance 및 Azure Synapse를 사용하여 유니버설 인증(MFA에 대한 SSMS 지원)](../database/authentication-mfa-ssms-overview.md)을 참조하세요.

> [!IMPORTANT]
> Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse의 데이터베이스는 이 문서의 나머지 부분에서 전체적으로 데이터베이스로 참조되며, 서버는 Azure SQL Database와 Azure Synapse용 데이터베이스를 호스트하는 [서버](logical-servers.md)를 참조합니다.

## <a name="configuration-steps"></a>구성 단계

1. **Azure Active Directory 구성** - 자세한 내용은 [Azure AD 디렉터리 관리](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [Azure Active Directory와 온-프레미스 ID 통합](../../active-directory/hybrid/whatis-hybrid-identity.md), [Azure AD에 고유한 도메인 이름 추가](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure는 이제 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) 및 [Windows PowerShell을 사용하여 Azure AD 관리](/previous-versions/azure/jj151815(v=azure.100))를 참조하세요.
2. **MFA 구성** - 단계별 지침은 [Azure AD Multi-Factor Authentication이란?](../../active-directory/authentication/concept-mfa-howitworks.md), [Azure SQL Database 및 Data Warehouse를 사용하여 조건부 액세스(MFA)](conditional-access-configure.md)를 참조하세요. 전체 조건부 액세스에는 프리미엄 Azure Active Directory가 필요합니다. 표준 Azure AD에서는 제한된 MFA가 제공됩니다.
3. **Azure AD 인증 구성** - 단계별 지침은 [Azure Active Directory 인증을 사용하여 SQL Database, SQL Managed Instance 또는 Azure Synapse에 연결](authentication-aad-overview.md)을 참조하세요.
4. **SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS를 [SSMS(SQL Server Management Studio) 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)에서 다운로드합니다.

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS를 통한 유니버설 인증을 사용하여 연결

다음 단계는 최신 SSMS를 사용하여 연결하는 방법을 보여 줍니다.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. 유니버설 인증을 사용하여 연결하려면 SSMS(SQL Server Management Studio)의 **서버에 연결** 대화 상자에서 **Active Directory - MFA를 지원하는 유니버설** 을 선택합니다. **Active Directory 유니버설 인증** 이 표시되면 최신 SSMS이 아닌 것입니다.

   ![SSMS의 서버에 연결 대화 상자에 있는 연결 속성 탭의 스크린샷. 데이터베이스에 연결 드롭다운에서 "MyDatabase"가 선택된 모습입니다.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Azure Active Directory 자격 증명을 사용하여 **사용자 이름** 상자를 `user_name@domain.com` 형식으로 입력합니다.

   ![서버 유형, 서버 이름, 인증 및 사용자 이름에 대한 서버에 연결 대화 상자 설정을 보여 주는 스크린샷.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. 게스트 사용자로 연결하는 경우 SSMS 18.x 이상에서 자동으로 인식하므로 게스트 사용자의 AD 도메인 이름 또는 테넌트 ID 필드를 더 이상 완료할 필요가 없습니다. 자세한 내용은 [SQL Database, SQL Managed Instance 및 Azure Synapse를 사용하여 유니버설 인증(MFA에 대한 SSMS 지원)](../database/authentication-mfa-ssms-overview.md)을 참조하세요.

   ![SSMS의 서버에 연결 대화 상자에 있는 연결 속성 탭의 스크린샷. 데이터베이스에 연결 드롭다운에서 "MyDatabase"가 선택되어 있습니다.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   그러나 SSMS 17.x 이하 버전을 사용하여 게스트 사용자로 연결하는 경우 **옵션** 을 클릭하고 **연결 속성** 대화 상자에서 **AD 도메인 이름 또는 테넌트 ID** 상자를 완료해야 합니다.

   ![SSMS의 서버에 연결 대화 상자에 있는 연결 속성 탭의 스크린샷. AD 도메인 이름 또는 테넌트 ID 속성 옵션이 입력되어 있습니다.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. **옵션** 을 선택하고 **옵션** 대화 상자에서 데이터베이스를 지정합니다. 연결된 사용자가 게스트 사용자이면(즉 joe@outlook.com) 상자에 선택 표시하고 현재 AD 도메인 이름이나 테넌트 ID를 옵션의 일부로 추가해야 합니다. [SQL Database 및 Azure Synapse Analytics를 사용하여 유니버설 인증(MFA에 대한 SSMS 지원)](../database/authentication-mfa-ssms-overview.md)을 참조하세요. 그런 다음 **연결** 을 클릭합니다.  
5. **사용자 계정 로그인** 대화 상자가 나타나면 Azure Active Directory ID의 계정 및 암호를 제공합니다. 사용자가 Azure AD와 페더레이션된 도메인에 속할 경우 암호가 필요하지 않습니다.

   ![Azure SQL Database 및 Data Warehouse의 계정에 로그인 대화 상자 스크린샷. 계정 및 암호가 입력되었습니다.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > MFA가 필요하지 않은 계정을 사용하는 유니버설 인증의 경우 이 시점에서 연결합니다. MFA가 필요한 사용자는 다음 단계를 계속 진행합니다.
   >  

6. 두 개의 MFA 설치 대화 상자가 나타날 수 있습니다. 이 일회성 작업은 MFA 관리자 설정에 따라 다르므로 선택적일 수 있습니다. MFA 사용 도메인의 경우 이 단계는 경우에 따라 미리 정의됩니다(예를 들어 도메인은 사용자에게 스마트 카드와 핀을 사용하도록 요구함).

   ![추가 보안 확인을 설정하라는 메시지가 표시된 Azure SQL Database 및 Data Warehouse의 계정에 로그인 대화 상자 스크린샷.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. 두 번째 가능한 일회성 대화 상자를 사용하여 인증 방법의 세부 정보를 선택할 수 있습니다. 가능한 옵션은 관리자가 구성합니다.

   ![인증 방법을 선택하고 구성하는 옵션이 있는 추가 보안 확인 대화 상자의 스크린샷.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory는 사용자에게 확인 정보를 보냅니다. 확인 코드를 받게되면 **확인 코드 입력** 상자에 해당 코드를 입력하고 **로그인** 을 클릭합니다.

   ![확인 코드를 입력하라는 메시지가 표시된 Azure SQL Database 및 Data Warehouse의 계정에 로그인 대화 상자 스크린샷.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

확인이 완료되면 SSMS는 일반적으로 가정된 유효한 자격 증명 및 방화벽 액세스를 연결합니다.

## <a name="next-steps"></a>다음 단계

- 다단계 인증에 대한 개요는 [SQL Database, SQL Managed Instance 및 Azure Synapse를 사용하여 유니버설 인증(MFA에 대한 SSMS 지원)](../database/authentication-mfa-ssms-overview.md)을 참조하세요.  
- 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](logins-create-manage.md)  
- 방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 서버 수준 방화벽 규칙 구성](./firewall-configure.md)을 참조하세요.  
- **Active Directory- MFA 유니버설** 인증을 사용할 때 ADAL 추적은 [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms)에서 시작할 수 있습니다. 기본적으로 꺼짐인 ADAL 추적은 **도구**, **옵션** 메뉴, **Azure Services**, **Azure Cloud**, **ADAL 출력 창 추적 수준** 을 사용하고 **보기** 메뉴의 **출력** 을 활성화하여 켤 수 있습니다. 추적은 **Azure Active Directory 옵션** 을 선택할 때 출력 창에서 사용할 수 있습니다.