---
title: 탄력적 데이터베이스 작업 설치 | Microsoft Docs
description: 탄력적 작업 기능의 설치에 대한 단계별 안내
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475765"
---
# <a name="installing-elastic-database-jobs-overview"></a>Elastic Database 작업 설치 개요

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Elastic Database 작업**](sql-database-elastic-jobs-overview.md)은 PowerShell이나 Azure Portal을 통해 설치할 수 있습니다. PowerShell 패키지를 설치한 경우에만 PowerShell API를 사용하여 작업을 만들고 관리할 수 있습니다. 또한 PowerShell API는 현재 포털보다 훨씬 더 많은 기능을 제공합니다.

이미 설치한 경우 **Elastic Database 작업** 기존 포털을 통해 **탄력적 풀**, 최신 PowerShell 미리 보기에는 기존 설치를 업그레이드 하는 스크립트를 포함 합니다. PowerShell API를 통해 노출된 새로운 기능을 활용하려면 최신 **Elastic Database 작업** 구성 요소로 설치를 업그레이드하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. 무료 평가판에 대해서는 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Azure PowerShell. [웹 플랫폼 설치 관리자](https://go.microsoft.com/fwlink/p/?linkid=320376)를 사용하여 최신 버전을 설치합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.
* [NuGet 명령줄 유틸리티](https://nuget.org/nuget.exe)는 Elastic Database 작업 패키지를 설치하는 데 사용됩니다. 자세한 내용은 https://docs.nuget.org/docs/start-here/installing-nuget을 참조하세요.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Elastic Database 작업 PowerShell 패키지 다운로드 및 가져오기
1. Microsoft Azure PowerShell 명령 창을 시작하고 NuGet 명령줄 유틸리티(nuget.exe)를 다운로드한 디렉터리로 이동합니다.
2. 다음 명령을 사용하여 **Elastic Database 작업** 패키지를 현재 디렉터리로 다운로드 및 가져옵니다.
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Elastic Database 작업** 파일은 로컬 디렉터리에서 **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x**라는 폴더에 배치됩니다. 여기서 *x.x.xxxx.x*는 버전 번호를 반영합니다. PowerShell cmdlet(필요한 클라이언트 .dll 포함)은 **tools\ElasticDatabaseJobs** 하위 디렉터리에 있고 설치, 업그레이드 및 제거하는 PowerShell 스크립트는 **tools** 하위 디렉터리에도 있습니다.
3. cd tools를 입력하여 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 폴더 아래의 tools 하위 디렉터리로 이동합니다. 예를 들면 다음과 같습니다.
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. .\InstallElasticDatabaseJobsCmdlets.ps1 스크립트를 실행하여 ElasticDatabaseJobs 디렉터리를 $Home\Documents\WindowsPowerShell\Modules에 복사합니다. 그러면 사용할 모듈도 자동으로 가져옵니다. 예를 들면 다음과 같습니다.
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>PowerShell을 사용하여 Elastic Database 작업 구성 요소 설치
1. Microsoft Azure PowerShell 명령 창을 시작하고 Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 폴더 아래의 \tools 하위 디렉터리로 이동합니다. cd \tools 입력
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. .\InstallElasticDatabaseJobs.ps1 PowerShell 스크립트를 실행하고 요청된 변수에 대한 값을 제공합니다. 이 스크립트는 [Elastic Database 작업 구성 요소 및 가격 책정](sql-database-elastic-jobs-overview.md#components-and-pricing) 에 설명된 구성 요소를 만들고 종속 구성 요소를 적절하게 사용하도록 Azure Cloud Services를 구성합니다.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

이 명령을 실행하면 **사용자 이름**과 **암호**를 묻는 창이 열립니다. 이는 Azure 자격 증명이 아니며, 새 서버에 만들려는 관리자 자격 증명이 될 사용자 이름과 암호를 입력해야 합니다.

이 샘플 호출에 제공된 매개 변수를 원하는 설정에 대해 수정할 수 있습니다. 아래에서는 각 매개 변수의 동작에 대한 자세한 정보를 제공합니다.

<table style="width:100%">
  <tr>
    <th>매개 변수</th>
    <th>설명</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>새로 만든 Azure 구성 요소를 포함하기 위해 생성된 Azure 리소스 그룹 이름을 제공합니다. 이 매개 변수의 기본값은 "__ElasticDatabaseJob"입니다. 이 값은 변경하지 않는 것이 좋습니다.</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>새로 만든 Azure 구성 요소에 사용할 Azure 위치를 제공합니다. 이 매개 변수의 기본값은 미국 중부 위치입니다.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>설치할 서비스 작업자 수를 제공합니다. 이 매개 변수의 기본값은 1입니다. 작업자 수를 늘려 서비스를 확장하고 고가용성을 제공할 수 있습니다. 서비스의 고가용성이 필요한 배포에는 "2"를 사용하는 것이 좋습니다.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>클라우드 서비스 내에서 사용할 VM 크기를 제공합니다. 이 매개 변수의 기본값은 A0입니다. 매개 변수 값... /.. / A3 작업자 역할이 각각 ExtraSmall/소규모/Medium/Large 크기를 사용 하 여 허용 됩니다. 작업자 역할 크기에 대 한 자세한 내용은 참조 하세요. <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database 작업 구성 요소 및 가격 책정</a>합니다.</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Standard 버전에 대한 컴퓨팅 크기를 제공합니다. 이 매개 변수의 기본값은 S0입니다. 매개 변수 값... /.. /.. /.. / S9/S12 Azure SQL database가 해당 계산 크기를 사용 하는 허용 됩니다. SQL Database 컴퓨팅 크기에 대한 자세한 내용은 <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database 작업 구성 요소 및 가격</a>을 참조하세요.</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>새로 만든 Azure SQL Database 서버에 대한 관리자 사용자 이름을 제공합니다. 지정되지 않은 경우 자격 증명을 요청하는 PowerShell 자격 증명 창이 열립니다.</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>새로 만든 Azure SQL Database 서버에 대한 관리자 암호를 제공합니다. 제공되지 않은 경우 자격 증명을 요청하는 PowerShell 자격 증명 창이 열립니다.</td>
</tr>
</table>

다수의 데이터베이스에 대해 병렬로 실행되는 다수의 작업을 대상으로 하는 시스템의 경우 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2와 같은 매개 변수를 지정하는 것이 좋습니다.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>PowerShell을 사용하여 기존 Elastic Database 작업 구성 요소 설치 업데이트
**Elastic Database 작업** 을 업데이트할 수 있습니다. 이 프로세스를 사용하면 제어 데이터베이스를 삭제하고 다시 만들지 않고도 이후에 서비스 코드를 업그레이드할 수 있습니다. 동일한 버전 내에서 이 프로세스를 사용하여 서비스 VM 크기 또는 서버 작업자 수를 수정할 수도 있습니다.

설치의 VM 크기를 업데이트하려면 매개 변수를 선택한 값으로 업데이트하여 다음 스크립트를 실행합니다.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>매개 변수</th>
  <th>설명</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Elastic Database 작업 구성 요소를 처음 설치할 때 사용한 Azure 리소스 그룹 이름을 식별합니다. 이 매개 변수의 기본값은 "__ElasticDatabaseJob"입니다. 이 값은 변경하지 않는 것이 좋으므로 이 매개 변수를 지정할 필요가 없습니다.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>설치할 서비스 작업자 수를 제공합니다.  이 매개 변수의 기본값은 1입니다.  작업자 수를 늘려 서비스를 확장하고 고가용성을 제공할 수 있습니다.  서비스의 고가용성이 필요한 배포에는 "2"를 사용하는 것이 좋습니다.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>클라우드 서비스 내에서 사용할 VM 크기를 제공합니다. 이 매개 변수의 기본값은 A0입니다. 매개 변수 값... /.. / A3 작업자 역할이 각각 ExtraSmall/소규모/Medium/Large 크기를 사용 하 여 허용 됩니다. 작업자 역할 크기에 대 한 자세한 내용은 참조 하세요. <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Elastic Database 작업 구성 요소 및 가격 책정</a>합니다.</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>포털을 사용하여 Elastic Database 작업 구성 요소 설치
[탄력적 풀을 만든](sql-database-elastic-pool-manage-portal.md)후에 **Elastic Database 작업** 구성 요소를 설치하여 탄력적 풀에 있는 각 데이터베이스에 대한 관리 작업을 실행할 수 있습니다. **Elastic Database 작업** PowerShell API를 사용하는 경우와 달리 포털 인터페이스는 현재 기존 풀에 대한 실행만으로 제한됩니다.

**예상 완료 시간:** 10분.

1. [Azure Portal](https://portal.azure.com/#)을 통해 탄력적 풀의 대시보드 뷰에서 **작업 만들기**를 클릭합니다.
2. 처음으로 작업을 만드는 경우 **미리 보기 약관**을 클릭하여 **Elastic Database 작업**을 설치해야 합니다.
3. 확인란을 클릭하여 약관에 동의합니다.
4. "서비스 설치" 화면에서 **작업 자격 증명**을 클릭합니다.
   
    ![서비스 설치][1]
5. 데이터베이스 관리자의 사용자 이름과 암호를 입력합니다. 설치 과정의 일부로 새 Azure SQL Database 서버가 생성됩니다. 이 새로운 서버 내에 제어 데이터베이스라는 새 데이터베이스가 생성되고 Elastic Database 작업에 필요한 메타데이터를 포함하는 데 사용됩니다. 여기서 만든 사용자 이름 및 암호는 제어 데이터베이스에 로그인하는 데 사용됩니다. 풀 내의 데이터베이스에 대한 스크립트 실행에는 별도 자격 증명이 사용됩니다.
   
    ![사용자 이름 및 암호 만들기][2]
6. 확인 단추를 클릭합니다. 몇 분 안에 새 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)에 사용자를 위한 구성 요소가 생성됩니다. 새 리소스 그룹은 아래 나온 것처럼 시작 보드에 고정됩니다. 생성이 완료되면 Elastic Database 작업(클라우드 서비스, SQL Database, Service Bus, 저장소)이 그룹 내에 모두 생성됩니다.
   
    ![시작 보드 내의 리소스 그룹][3]
7. 탄력적 데이터베이스 작업이 설치되는 동안 작업을 만들거나 관리하려고 하면 **자격 증명** 을 제출할 때 다음과 같은 메시지가 표시됩니다.
   
    ![배포가 아직 진행 중입니다.][4]

제거해야 하는 경우 리소스 그룹을 삭제합니다. [Elastic Database 작업 구성 요소를 제거하는 방법](sql-database-elastic-jobs-uninstall.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
스크립트 실행에 적절한 권한이 있는 자격 증명이 그룹의 각 데이터베이스에 만들어졌는지 확인합니다. 자세한 내용은 [SQL Database 보안](sql-database-manage-logins.md)을 참조하세요.
시작하려면 [Elastic Database 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)를 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
