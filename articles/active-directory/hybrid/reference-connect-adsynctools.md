---
title: 'Azure AD Connect: ADSyncTools PowerShell 참조 | Microsoft Docs'
description: 이 문서에서는 ADSyncTools.psm1 PowerShell 모듈에 대한 참조 정보를 제공합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 04/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f81af557242503c6380d0ff7bc1dfaed852cd908
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070686"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell 참조
이 설명서에서는 Azure AD Connect에 포함된 ADSyncTools.psm1 PowerShell 모듈에 대해 다음과 같은 참조 정보를 제공합니다.

## <a name="install-the-adsynctools-powershell-module"></a>ADSyncTools PowerShell 모듈을 설치 합니다.
ADSyncTools PowerShell 모듈을 설치 하려면 다음을 수행 합니다.

1.  관리를 사용 하 여 Windows PowerShell 열기
2.  다음을 입력 하거나 복사 하 여 붙여넣습니다. 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Enter 키를 누릅니다.
4.  모듈이 설치 되었는지 확인 하려면 다음을 입력 하거나 복사 하 여 붙여넣습니다.
    ```powershell
    Get-module AdSyncTools
    ```
5.  이제 모듈에 대 한 정보가 표시 됩니다.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>개요
AD 사용자에서 mS-Ds-ConsistencyGuid를 지웁니다.

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
대상 AD 사용자에 대한 mS-Ds-ConsistencyGuid 값을 지웁니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
설정할 AD의 대상 사용자입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-database"></a>-Database
{{데이터베이스 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{인스턴스 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{암호 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{서버 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{사용자 이름 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>개요
ConsistencyGuid 보고서를 내보냅니다.

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>설명
Import-ADSyncToolsImmutableIdMigration의 가져오기 CSV 파일에 기반한 ConsistencyGuid 보고서를 생성합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
대체 로그인 ID(메일)를 사용합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
CSV 및 LOG 파일의 출력 파일 이름입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{호스트 이름 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>개요
AD에서 사용자를 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
AD 개체(TO DO: 다중 포리스트 지원)를 반환합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
ConsistencyGuid를 설정할 AD의 대상 사용자입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>개요
AD 사용자에서 mS-Ds-ConsistencyGuid를 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
대상 AD 사용자의 mS-Ds-ConsistencyGuid 특성 값을 GUID 형식으로 반환합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
설정할 AD의 대상 사용자입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>개요
AD 사용자에서 ObjectGuid를 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
대상 AD 사용자의 ObjectGUID 특성 값을 GUID 형식으로 반환합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
설정할 AD의 대상 사용자입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>개요
AAD Connect 실행 기록을 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>설명
AAD Connect 실행 기록을 XML 형식으로 반환하는 함수입니다

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>예제 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-days"></a>-Days
{{일 수 설명 입력}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>개요
SourceAnchor 변경 오류가 있는 사용자를 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
함수는 AAD Connect 실행 기록을 쿼리 하 고 오류를 보고 하는 모든 사용자를 내보냅니다. "SourceAnchor 특성이 변경 되었습니다."

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
#Required Parameters
```

$sourcePath = 읽기-호스트 프롬프트 "파일 이름이" # "인 로그 파일 경로를 입력 합니다." \<Source_Path\> $outputPath = 읽기-호스트-프롬프트 "파일 이름" # "을 (를) 사용 하 여 출력 파일 경로 입력 \<Out_Path\>
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-sourcepath"></a>-sourcePath
{{sourcePath 설명 입력}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{outputPath 설명 입력}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>개요
AAD에서 ImmutableID를 가져옵니다.

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>설명
GUID 형식 요구 사항: MSOnline PowerShell 모듈의 ImmutableID 값이 있는 모든 Azure AD 동기화된 사용자가 포함된 파일을 생성합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-output"></a>-Output
출력 CSV 파일입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Azure AD 휴지통에서 동기화된 사용자를 가져옵니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-query"></a>-Query
{{쿼리 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{SqlConnection 설명 입력}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>개요
UserCertificate 특성에서 만료된 인증서를 제거하는 스크립트입니다.

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>설명
이 스크립트는 Active Directory 도메인의 대상 조직 구성 단위에서 모든 개체를 가져와서 개체 클래스(User/Computer)를 기준으로 필터링하고, UserCertificate 특성에 있는 만료된 인증서를 모두 삭제합니다.
기본적으로(BackupOnly 모드) 만료된 인증서만 파일에 백업하고, AD에서는 변경하지 않습니다.
-BackupOnly $false를 사용하면 이러한 개체에 대한 UserCertificate 특성에 있는 만료된 인증서가 파일로 복사된 후 AD에서 제거됩니다.
각 인증서는 별도의 파일 이름(ObjectClass_ObjectGUID_CertThumprint.cer)으로 백업됩니다. 또한 스크립트는 실제로 수행된 작업(건너뜀/내보냄/삭제됨)을 포함하여 유효하거나 만료된 인증서가 있는 모든 사용자를 표시하는 CSV 형식의 로그 파일을 만듭니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>예제 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMETERS

#### <a name="-targetou"></a>-TargetOU
AD 개체를 조회할 대상 OU입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly는 AD에서 어떤 인증서도 삭제하지 않습니다.

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
개체 클래스 필터입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>개요
간단한 설명

### <a name="syntax"></a>SYNTAX

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>설명
자세한 설명입니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{호스트 이름 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>개요
(할 일) 인증서 파일에서 AD UserCertificate 특성을 복원 합니다.

### <a name="syntax"></a>SYNTAX

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>설명
자세한 설명입니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>개요
AD 사용자에 대한 mS-Ds-ConsistencyGuid를 설정합니다.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>설명
대상 AD 사용자에 대한 mS-Ds-ConsistencyGuid 특성의 값을 설정합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
ConsistencyGuid를 설정할 AD의 대상 사용자입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId(바이트 배열, GUID, GUID 문자열 또는 Base64 문자열)입니다.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>개요
{{개요 입력}}

### <a name="syntax"></a>SYNTAX

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>설명
{{설명 입력}}

### <a name="examples"></a>예제

#### <a name="example-1"></a>예 1
```powershell
PS C:\> {{ Add example code here }}
```

{{여기에 설명 예제 추가}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{호스트 이름 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{포트 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>개요
AD 가져오기 단계에서 추적 파일을 만듭니다.

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>설명
지정된 AD 워터마크 검사점(파티션 쿠키)에서 실행되는 AAD Connect AD 가져오기의 모든 LDAP 쿼리를 추적합니다. 현재 폴더에 '. \ ADimportTrace_yyyyMMddHHmmss.log' 추적 파일을 만듭니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{ADConnectorXML 설명 입력}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
AD 커넥터 내보내기의 XML 파일입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
대상 도메인 컨트롤러입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
포리스트 루트 DN입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
추적할 AD 개체 유형 \> * = 모든 개체 유형

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
도메인 관리자로 이미 실행 중인 경우 AD 자격 증명을 제공할 필요가 없습니다.
XML 파일 대신 워터마크를 수동으로 입력합니다(예: $ADwatermark = “TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)”).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>개요
간단한 설명

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>설명
자세한 설명입니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-context"></a>-Context
Param1 도움말 설명입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Param2 도움말 설명입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Param2 도움말 설명입니다.

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Param2 도움말 설명입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>개요
사용자를 새 ConsistencyGuid(ImmutableId)로 업데이트합니다.

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>설명
사용자를 ConsistencyGuid 보고서에서 가져온 새 ConsistencyGuid(ImmutableId) 값으로 업데이트합니다. 이 함수는 WhatIf 스위치를 지원합니다. 참고: ConsistencyGuid 보고서는 탭 구분 기호를 사용하여 가져와야 합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>예제 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
작업

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
LOG 파일에 대한 출력 파일 이름입니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.
