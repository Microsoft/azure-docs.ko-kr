---
title: 분할/병합 서비스 배포 | Microsoft Docs
description: 분할-병합 도구를 사용하여 분할된 데이터베이스 간에 데이터를 이동합니다.
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
ms.openlocfilehash: 5aff7e93dcfaa5320be0d6f7d427abcdc88c69e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585511"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>분할-병합 서비스를 배포하여 분할된 데이터베이스 간에 데이터 이동

분할-병합 도구를 사용하면 분할된 데이터베이스 간에 데이터를 이동할 수 있습니다.  [확장된 클라우드 데이터베이스 간 데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>분할-병합 패키지 다운로드
1. [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)에서 최신 NuGet 버전을 다운로드합니다.
2. 명령 프롬프트를 열고 nuget.exe를 다운로드한 디렉터리로 이동합니다. 다운로드에는 PowerShell 명령이 포함됩니다.
3. 아래 명령을 사용하여 최신 분할/병합 패키지를 현재 디렉터리에 다운로드합니다.
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

파일은 **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**라는 디렉터리에 저장됩니다. 여기서 *x.x.xxx.x*는 버전 번호를 나타냅니다. **content\splitmerge\service** 하위 디렉터리에서 분할/병합 서비스 파일을 찾고 **content\splitmerge\powershell** 하위 디렉터리에서 분할/병합 PowerShell 스크립트 및 필요한 클라이언트 dll을 찾습니다.

## <a name="prerequisites"></a>필수 조건
1. 분할/병합 상태 데이터베이스로 사용할 Azure SQL DB 데이터베이스를 만듭니다. [Azure 포털](https://portal.azure.com)로 이동합니다. 새 **SQL Database**를 만듭니다. 데이터베이스에 이름을 지정하고 새 관리자 및 암호를 만듭니다. 나중에 사용할 수 있도록 이름과 암호를 기록합니다.
2. Azure SQL DB 서버에서 Azure 서비스의 연결을 허용하는지 확인합니다. 포털의 **방화벽 설정**에서 **Azure 서비스에 대한 액세스 허용** 설정이 **On**으로 설정되었는지 확인합니다. "저장" 아이콘을 클릭합니다.
3. 진단 출력에 사용할 Azure Storage 계정을 만듭니다.
4. 분할-병합 서비스에 사용할 Azure 클라우드 서비스를 만듭니다.

## <a name="configure-your-split-merge-service"></a>분할/병합 서비스 구성
### <a name="split-merge-service-configuration"></a>분할/병합 서비스 구성
1. 분할/병합 어셈블리를 다운로드한 폴더에서 **SplitMergeService.cspkg**와 함께 제공된 **ServiceConfiguration.Template.cscfg** 파일의 복사본을 만들고 이름을 **ServiceConfiguration.cscfg**로 바꿉니다.
2. Visual Studio와 같은 텍스트 편집기에서 인증서 지문 형식과 같은 입력의 유효성을 검사하는 **ServiceConfiguration.cscfg** 를 엽니다.
3. 새 데이터베이스를 만들거나 분할/병합 작업에 대한 상태 데이터베이스로 사용할 기존 데이터베이스를 선택하고 해당 데이터베이스의 연결 문자열을 검색합니다. 
   
   > [!IMPORTANT]
   > 지금은 상태 데이터베이스에서 라틴어 데이터 정렬(SQL\_Latin1\_General\_CP1\_CI\_AS)을 사용해야 합니다. 자세한 내용은 [Windows 데이터 정렬 이름(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188046.aspx)을 참조하세요.
   >

   Azure SQL DB를 사용할 경우 연결 문자열의 형식은 일반적으로 다음과 같습니다.
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. ElasticScaleMetadata 설정의 **SplitMergeWeb** 및 **SplitMergeWorker** 역할 섹션에서 cscfg 파일에 이 연결 문자열을 입력합니다.
5. **SplitMergeWorker** 역할의 경우, **WorkerRoleSynchronizationStorageAccountConnectionString** 설정에 대해 Azure 스토리지에 유효한 연결 문자열을 입력합니다.

### <a name="configure-security"></a>보안 구성
서비스의 보안을 구성하는 자세한 지침은 [분할-병합 보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)을 참조하세요.

이 자습서에 대한 간단한 테스트를 배포하기 위해 서비스를 작동하고 실행하는 데 필요한 최소 구성 단계가 수행됩니다. 이러한 단계에서는 단계를 실행하는 데 사용하는 컴퓨터/계정 하나만 서비스와 통신할 수 있습니다.

### <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기
새 디렉터리를 만들고 이 디렉터리에서 [Visual Studio용 개발자 명령 프롬프트](https://msdn.microsoft.com/library/ms229859.aspx) 창을 사용하여 다음 명령을 실행합니다.

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

개인 키를 보호 하기 위해 암호를 입력하라는 메시지가 표시됩니다. 강력한 암호를 입력하고 이를 확인합니다. 이후에 사용할 암호를 한 번 더 입력하라는 메시지가 표시됩니다. 마지막에 **예** 를 클릭하여 신뢰할 수 있는 인증 기관 루트 저장소로 인증서를 가져옵니다.

### <a name="create-a-pfx-file"></a>PFX 파일 만들기
makecert가 실행된 동일한 창에서 다음 명령을 실행하고, 인증서를 만드는 데 사용한 동일한 암호를 사용합니다.

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>개인 저장소로 클라이언트 인증서 가져오기
1. Windows 탐색기에서 **MyCert.pfx**를 두 번 클릭합니다.
2. **인증서 가져오기 마법사**에서 **현재 사용자**를 선택하고 **다음**을 클릭합니다.
3. 파일 경로를 확인하고 **다음**을 클릭합니다.
4. 암호를 입력하고 **확장 속성 모두 포함**을 선택된 상태로 두고 **다음**을 클릭합니다.
5. **인증서 저장소를 자동으로 선택[…]** 을 선택된 상태로 두고 **다음**을 클릭합니다.
6. **마침**, **확인**을 차례로 클릭합니다.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>클라우드 서비스에 PFX 파일 업로드
1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. **Cloud Services**를 선택합니다.
3. 분할/병합 서비스에 대해 위에서 만든 클라우드 서비스를 선택합니다.
4. 최상위 메뉴에서 **인증서** 를 클릭합니다.
5. 아래쪽 메뉴 모음에서 **업로드** 를 클릭합니다.
6. PFX 파일을 선택하고 위와 동일한 암호를 입력합니다.
7. 완료되면 목록의 새 항목에서 인증서 지문을 복사합니다.

### <a name="update-the-service-configuration-file"></a>서비스 구성 파일 업데이트
이러한 설정의 지문/값 특성에 위의 복사한 인증서 지문을 붙여넣습니다.
작업자 역할의 경우:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

웹 역할의 경우:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

프로덕션 배포의 경우 CA, 암호화, 서버 인증서 및 클라이언트 인증서에 개별 인증서를 사용해야 합니다. 이와 관련된 자세한 지침은 [보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md)을 참조하세요.

## <a name="deploy-your-service"></a>서비스 배포
1. [Azure Portal](https://portal.azure.com)로 이동
2. 이전에 만든 클라우드 서비스를 선택합니다.
3. **개요**를 클릭합니다.
4. 스테이징 환경을 선택하고 **업로드**를 클릭합니다.
5. 대화 상자에서 배포 레이블을 입력합니다. '패키지'와 '구성' 모두에 대해, '로컬에서'를 클릭하고 **SplitMergeService.cspkg** 파일 및 이전에 구성한 cscfg 파일을 선택합니다.
6. **단일 인스턴스가 포함된 역할이 하나 이상 있는 경우에도 배포** 확인란이 선택되어 있는지 확인합니다.
7. 오른쪽 아래의 눈금 단추를 눌러 배포를 시작합니다. 배포를 완료하려면 몇 분 정도 걸립니다.


## <a name="troubleshoot-the-deployment"></a>배포 문제 해결
웹 역할을 온라인 상태로 전환하지 못하면 보안 구성에 문제가 있는 것입니다. 위에서 설명한 대로 SSL이 구성되었는지 확인합니다.

작업자 역할을 온라인 상태로 전환하지 못했지만 웹 역할은 성공하면 대개 이전에 만든 상태 데이터베이스에 대한 연결 문제가 있는 것입니다.

* cscfg의 연결 문자열이 정확한지 확인합니다.
* 서버 및 데이터베이스가 존재하며, 사용자 ID 및 암호가 올바른지 확인합니다.
* Azure SQL DB의 경우 연결 문자열은 다음 형식이어야 합니다.

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* 서버 이름이 **https://** 로 시작하지 않는지 확인합니다.
* Azure SQL DB 서버에서 Azure 서비스의 연결을 허용하는지 확인합니다. 이렇게 하려면 포털에서 데이터베이스를 열고 **Azure 서비스에 대한 액세스 허용** 설정이 **On** **로 설정되었는지 확인합니다.

## <a name="test-the-service-deployment"></a>서비스 배포 테스트
### <a name="connect-with-a-web-browser"></a>웹 브라우저와 연결
분할/병합 서비스의 웹 엔드포인트를 확인합니다. 포털에서 클라우드 서비스의 **개요**로 이동한 다음, 오른쪽의 **사이트 URL**에서 찾아봅니다. 기본 보안 설정을 통해 HTTP 엔드포인트를 사용하지 않도록 설정되므로 **http://** 를 **https://** 로 바꿉니다. 이 URL에 해당하는 페이지를 브라우저에 로드합니다.

### <a name="test-with-powershell-scripts"></a>PowerShell 스크립트로 테스트
포함된 샘플 PowerShell 스크립트를 실행하여 사용자 환경 및 배포를 테스트할 수 있습니다.

포함된 스크립트 파일은 다음과 같습니다.

1. **SetupSampleSplitMergeEnvironment.ps1** - 분할/병합에 대한 테스트 데이터 계층을 설정합니다. 자세한 설명은 아래 테이블을 참조하세요.
2. **ExecuteSampleSplitMerge.ps1** - 데이터 계층에 대한 테스트 작업을 실행합니다. 자세한 설명은 아래 테이블을 참조하세요.
3. **GetMappings.ps1** – 분할된 데이터베이스 매핑의 현재 상태를 출력하는 최상위 샘플 스크립트입니다.
4. **ShardManagement.psm1** – ShardManagement API를 래핑하는 도우미 스크립트입니다.
5. **SqlDatabaseHelpers.psm1** – SQL Database 생성 및 관리를 위한 도우미 스크립트입니다.
   
   <table style="width:100%">
     <tr>
       <th>PowerShell 파일</th>
       <th>단계</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    분할된 데이터베이스 맵 관리자 데이터베이스를 만듭니다.</td>
     </tr>
     <tr>
       <td>2.    분할된 데이터베이스를 2개 만듭니다.
     </tr>
     <tr>
       <td>3.    해당 데이터베이스에 대한 분할된 데이터베이스 맵을 만듭니다. 이때 해당 데이터베이스의 기존 분할된 데이터베이스 맵은 삭제됩니다. </td>
     </tr>
     <tr>
       <td>4.    두 분할된 데이터베이스에서 작은 샘플 테이블을 만들고 둘 중 하나의 테이블을 채웁니다.</td>
     </tr>
     <tr>
       <td>5.    분할된 테이블에 대한 SchemaInfo를 선언합니다.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell 파일</th>
       <th>단계</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    첫 번째 분할된 데이터베이스에서 두 번째 분할된 데이터베이스로 데이터의 절반을 분할하는 분할 요청을 분할/병합 서비스 웹 프런트 엔드로 보냅니다.</td>
     </tr>
     <tr>
       <td>2.    분할 요청 상태를 확인하기 위해 웹 프런트 엔드를 폴링한 다음 요청이 완료될 때까지 기다립니다.</td>
     </tr>
     <tr>
       <td>3.    두 번째 분할된 데이터베이스에서 첫 번째 분할된 데이터베이스로 데이터를 다시 이동하는 병합 요청을 분할/병합 서비스 웹 프런트 엔드로 보냅니다.</td>
     </tr>
     <tr>
       <td>4.    병합 요청 상태를 확인하기 위해 웹 프런트 엔드를 폴링한 다음 요청이 완료될 때까지 기다립니다.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>PowerShell을 사용하여 배포 확인
1. 새 PowerShell 창을 열고 분할/병합 패키지를 다운로드한 디렉터리로 이동한 다음 "powershell" 디렉터리로 이동합니다.
2. Azure SQL Database 서버를 만들거나 기존 서버를 선택합니다. 이 서버에 분할된 데이터베이스 맵 관리자 및 분할된 데이터베이스가 생성됩니다.
   
   > [!NOTE]
   > SetupSampleSplitMergeEnvironment.ps1 스크립트가 기본적으로 동일한 서버에 이러한 모든 데이터베이스를 만들어 스크립트를 단순하게 유지합니다. 이 제한은 분할/병합 서비스 자체의 제한은 아닙니다.
   >
   
   분할/병합 서비스에서 데이터를 이동하고 분할된 데이터베이스 맵을 업데이트하려면 읽기/쓰기 액세스 권한이 있는 SQL 인증 로그인이 필요합니다. 분할/병합 서비스는 클라우드에서 실행되므로 현재 통합 인증을 지원하지 않습니다.
   
   Azure SQL Server가 이러한 스크립트를 실행하는 컴퓨터의 IP 주소에서 액세스할 수 있도록 구성되었는지 확인합니다. 이 설정은 Azure SQL Server/구성/허용된 IP 주소에서 찾을 수 있습니다.
3. SetupSampleSplitMergeEnvironment.ps1 스크립트를 실행하여 샘플 환경을 만듭니다.
   
   이 스크립트를 실행하면 분할된 데이터베이스 맵 관리자 데이터베이스 및 분할된 데이터베이스에서 기존의 분할된 데이터베이스 맵 관리 데이터 구조가 모두 초기화됩니다. 분할된 데이터베이스 맵 또는 분할된 데이터베이스를 다시 초기화하려는 경우에 이 스크립트를 다시 실행하는 것이 유용할 수 있습니다.
   
   샘플 명령줄:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. 현재 샘플 환경에 있는 매핑을 보려면 Getmappings.ps1 스크립트를 실행합니다.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. ExecuteSampleSplitMerge.ps1 스크립트를 실행하여 첫 번째 분할된 데이터베이스에서 두 번째 분할된 데이터베이스로 데이터의 절반을 이동하는 분할 작업을 실행한 다음 데이터를 첫 번째 분할된 데이터베이스로 다시 이동하는 병합 작업을 실행합니다. SSL을 구성하고 http 엔드포인트를 사용할 수 없도록 설정해 둔 경우에는 https:// 엔드포인트를 대신 사용해야 합니다.
   
   샘플 명령줄:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   아래 오류가 표시되면 대개 웹 엔드포인트의 인증서에 문제가 있는 것입니다. 원하는 웹 브라우저로 웹 엔드포인트에 연결하여 인증서 오류가 발생하는지 확인합니다.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   정상적으로 연결되는 경우의 출력은 다음과 같습니다.
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. 다른 데이터 형식으로도 연결해 봅니다. 이러한 모든 스크립트는 키 유형을 지정할 수 있도록 하는 선택적인 -ShardKeyType 매개 변수를 사용합니다. 기본값은 Int32지만 Int64, GUID 또는 이진 파일을 지정할 수도 있습니다.

## <a name="create-requests"></a>요청 만들기
웹 UI를 사용하거나 웹 역할을 통해 요청을 제출하는 SplitMerge.psm1 PowerShell 모듈을 가져와서 사용하여 서비스를 사용할 수 있습니다.

이 서비스는 분할된 테이블과 참조 테이블 모두에서 데이터를 이동할 수 있습니다. 분할된 테이블에는 분할 키 열과 각 분할 키에 대한 여러 행의 데이터가 있습니다. 참조 테이블은 분할되지 않으므로 모든 분할된 데이터베이스에 동일한 행 데이터를 포함합니다. 참조 테이블은 자주 변경되지 않으며 쿼리에서 분할된 테이블과 조인하는 데 사용되는 데이터에 유용합니다.

분할/병합 작업을 수행하려면 이동할 분할된 테이블 및 참조 테이블을 선언해야 합니다. **SchemaInfo** API를 사용하여 이 작업을 수행합니다. 이 API는 **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 네임스페이스에 있습니다.

1. 각 분할된 테이블에 대해 테이블의 부모 스키마 이름(선택 사항, 기본값은 “dbo”), 테이블 이름 및 분할 키가 포함된 해당 테이블의 열 이름을 설명하는 **ShardedTableInfo** 개체를 만듭니다.
2. 각 참조 테이블에 대해 테이블의 부모 스키마 이름(선택 사항, 기본값은 “dbo”) 및 테이블 이름을 설명하는 **ReferenceTableInfo** 개체를 만듭니다.
3. 새 **SchemaInfo** 개체에 위의 TableInfo 개체를 추가합니다.
4. **ShardMapManager** 개체에 대한 참조를 가져와 **GetSchemaInfoCollection**을 호출합니다.
5. **SchemaInfo**를 **SchemaInfoCollection**에 추가하고 분할된 데이터베이스 맵 이름을 입력합니다.

이러한 예는 SetupSampleSplitMergeEnvironment.ps1 스크립트에서 찾아볼 수 있습니다.

분할/병합 서비스에서 대상 데이터베이스(또는 데이터베이스의 모든 테이블에 대한 스키마)를 자동으로 생성하지는 않습니다. 대상 데이터베이스가 미리 생성되어 있어야 서비스에 요청을 보낼 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
샘플 PowerShell 스크립트를 실행할 때 아래 메시지가 표시될 수 있습니다.

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

이 오류는 SSL 인증서가 제대로 구성되지 않은 것을 의미합니다. '웹 브라우저로 연결' 섹션의 지침을 따르세요.

요청을 제출할 수 없는 경우에 다음이 나타날 수 있습니다.

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

이 경우 구성 파일, 특히 **WorkerRoleSynchronizationStorageAccountConnectionString**에 대한 설정을 확인합니다. 이 오류는 일반적으로 작업자 역할이 처음 사용할 때 메타데이터 데이터베이스를 성공적으로 초기화하지 못했을 때 나타납니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

