---
title: Azure Analysis Services 관리 | Microsoft Docs
description: Azure에서 Analysis Services 서버를 관리하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065200"
---
# <a name="manage-analysis-services"></a>Analysis Services 관리
Azure에 Analysis Services 서버를 만들었으면 즉시 또는 조만간에 수행해야 하는 몇 가지 운영 및 관리 작업이 있습니다. 예를 들어 데이터 새로 고침 처리를 실행하거나, 서버의 모델에 대한 액세스 권한이 있는 사용자를 제어하거나, 서버의 상태를 모니터링합니다. 일부 관리 작업은 Azure 포털에서만, 일부 다른 작업은 SSMS(SQL Server Management Studio)에서만, 일부 작업은 둘 중 하나에서 수행할 수 있습니다.

## <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com/)은 서버를 생성 및 삭제하고, 서버 리소스를 모니터링하고, 크기를 변경하고, 서버에 액세스할 수 있는 사용자를 관리할 수 있는 포털입니다.  몇 가지 문제가 발생하면 지원 요청을 제출할 수도 있습니다.

![Azure에서 서버 이름 가져오기](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure에서 서버를 연결하는 것은 조직에서 서버 인스턴스를 연결하는 것과 같습니다. SSMS에서는 데이터 처리와 같이 동일한 작업을 많이 수행하거나 처리 스크립트를 만들고 역할을 관리하고 PowerShell을 사용할 수 있습니다.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS 다운로드 및 설치
모든 최신 기능과 Azure Analysis Services 서버에 연결할 때 가장 원활한 환경을 얻으려면 최신 버전의 SSMS를 사용하고 있어야 합니다. 

[SQL Server Management Studio를 다운로드합니다](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>SSMS로 연결
 SSMS를 사용할 때 처음으로 서버에 연결하기 전에 Analysis Services 관리자 그룹에 사용자 이름이 포함되어 있는지 확인합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [서버 관리자 및 데이터베이스 사용자](#server-administrators-and-database-users)를 참조하세요.

1. 먼저 서버 이름을 가져온 후에 연결할 수 있습니다. **Azure 포털** > 서버 > **개요** > **서버 이름**에서 서버 이름을 복사합니다.
   
    ![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS > **개체 탐색기**에서 **연결** > **Analysis Services**를 클릭합니다.
3. **서버에 연결** 대화 상자에서 서버 이름을 붙여넣은 다음 **인증**에서 다음 인증 유형 중 하나를 선택합니다.   
    > [!NOTE]
    > 인증 유형, **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택하는 것이 좋습니다.

    > [!NOTE]
    > Microsoft 계정, Live ID, Yahoo, Gmail 등으로 로그인하는 경우 암호 필드를 비워 둡니다. 연결을 클릭하면 암호를 묻는 메시지가 표시됩니다.

    **Windows 인증** - Windows 도메인\사용자 이름 및 암호 자격 증명을 사용합니다.

    **Active Directory 암호 인증** - 조직의 계정을 사용합니다. 예를 들어 비도메인 가입 컴퓨터에서 연결하는 경우에 이 옵션을 사용합니다.

    **Active Directory - MFA 지원을 통한 유니버슬 인증** - [비대화형 또는 다단계 인증](../sql-database/sql-database-ssms-mfa-authentication.md)을 사용합니다. 
   
    ![SSMS에서 연결](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>서버 관리자 및 데이터베이스 사용자
Azure Analysis Services에서는 두 가지 유형의 사용자, 서버 관리자 및 데이터베이스 사용자가 있습니다. 두 가지 유형의 사용자 모두 Azure Active Directory에 포함되어야 하며 조직 전자 메일 주소 또는 UPN으로 지정해야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요.


## <a name="troubleshooting-connection-problems"></a>연결 문제 해결
SSMS를 사용하여 연결할 때 문제가 발생하는 경우 로그인 캐시를 지워야 할 수 있습니다. 아무것도 디스크로 캐시되지 않습니다. 캐시를 지우려면 연결 프로세스를 닫았다가 다시 시작합니다. 

## <a name="next-steps"></a>다음 단계
새 서버에 테이블 형식 모델을 아직 배포하지 않았으면 지금이야말로 좋은 기회입니다. 자세한 내용은 [Azure Analysis Services에 배포](analysis-services-deploy.md)를 참조하세요.

서버에 모델을 배포한 경우에는 클라이언트 또는 브라우저를 통해 연결할 준비가 된 것입니다. 자세한 내용은 [Azure Analysis Services 서버에서 데이터 가져오기](analysis-services-connect.md)를 참조하세요.

