---
title: 'Azure AD Connect: ADConnectivityTools PowerShell 참조 | Microsoft Docs'
description: 이 문서에서는 ADConnectivityTools.psm1 PowerShell 모듈에 대한 참조 정보를 제공합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070805"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools PowerShell 참조

이 설명서에서는 Azure AD Connect에 포함된 ADConnectivityTools.psm1 PowerShell 모듈에 대해 다음과 같은 참조 정보를 제공합니다.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>개요

로컬 DNS 문제를 검색합니다.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>설명

로컬 DNS 연결 테스트를 실행합니다.
Active Directory 커넥터를 구성 하려면 사용자가 연결 하려는 포리스트와이 포리스트에 연결 된 도메인 컨트롤러에 대 한 이름 resolutionthe를 모두 포함 해야 합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

테스트할 포리스트의 이름을 지정합니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

테스트할 DC를 지정합니다.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

이 진단의 결과를 PSObject 형식으로 반환합니다.
이 도구와 수동으로 상호 작용하는 동안에는 필요하지 않습니다.

```yml
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

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>개요

지정한 포리스트가 있는지 확인합니다.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>설명

DNS 서버에서 포리스트와 연결된 IP 주소를 쿼리합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

테스트할 포리스트의 이름을 지정합니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable.
자세한 내용은 about_CommonParameters(https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>개요

AD 포리스트 기능 수준을 확인합니다.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>설명

AD 포리스트 기능 수준이 지정된 MinAdForestVersion(WindowsServer2003) 이상인지 확인합니다.
계정(도메인\사용자 이름) 및 암호를 요청할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>예제 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

대상 포리스트입니다.
기본값은 현재 로그인한 사용자의 포리스트입니다.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

대상 ForestFQDN 개체입니다.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

이 함수는 사용자에게 사용자 지정 자격 증명을 요청하지 않고 현재 컴퓨터에 로그인한 사용자의 자격 증명을 사용합니다.

```yml
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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>개요

로컬 네트워크 연결 문제를 검색합니다.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>설명

로컬 네트워크 연결 테스트를 실행합니다.

로컬 네트워킹 테스트의 경우 AAD Connect는 53(DNS), 88(Kerberos) 및 389(LDAP) 포트에서 명명된 도메인 컨트롤러와 통신할 수 있어야 합니다. 대부분의 조직에서는 자체의 DC에서 DNS를 실행하므로 이 테스트가 현재 통합되어 있습니다.
다른 DNS 서버가 지정되면 53 포트를 건너뛰어야 합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-dcs"></a>-DCs

테스트할 DC를 지정합니다.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

사용자가 AD 사이트/로그온 DC에서 제공 하는 DNS 서비스를 사용 하지 않는 경우 포트 53 확인을 건너뛰는 것이 좋습니다.
사용자가 계속 해 서 _tcp를 확인할 수 있어야 합니다.\<forestfqdn\>
Active Directory 커넥터 구성이 성공 하도록 하려면입니다.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

이 진단의 결과를 PSObject 형식으로 반환합니다.
이 도구와 수동으로 상호 작용하는 동안에는 필요하지 않습니다.

```yml
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

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>개요

지정된 포리스트 및 해당 연결된 도메인 컨트롤러에 연결할 수 있는지 확인합니다.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>설명

"ping" 테스트(컴퓨터에서 네트워크 및/또는 인터넷을 통해 대상 컴퓨터에 연결할 수 있는지 여부)를 실행합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

테스트할 포리스트의 이름을 지정합니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

테스트할 DC를 지정합니다.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>개요

가져온 포리스트 FQDN의 도메인에 연결할 수 있는지 확인합니다.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>설명

DomainGuid 및 DomainDN을 검색하려고 시도하여 가져온 포리스트 FQDN의 모든 도메인에 연결할 수 있는지 확인합니다.
계정(도메인\사용자 이름) 및 암호를 요청할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>예제 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

대상 포리스트입니다.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

대상 ForestFQDN 개체입니다.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

이 함수는 사용자에게 사용자 지정 자격 증명을 요청하지 않고 현재 컴퓨터에 로그인한 사용자의 자격 증명을 사용합니다.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>개요

사용자에게 엔터프라이즈 관리자 자격 증명이 있는지 확인합니다.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>설명

제공된 사용자에게 엔터프라이즈 관리자 자격 증명이 있는지 검색합니다.
계정(도메인\사용자 이름) 및 암호를 요청할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>예제 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

이 함수는 사용자에게 사용자 지정 자격 증명을 요청하지 않고 현재 컴퓨터에 로그인한 사용자의 자격 증명을 사용합니다.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>개요

계정과 암호의 조합에서 DomainFQDN을 검색합니다.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>설명

제공된 자격 증명에서 domainFQDN 개체를 가져오려고 시도합니다.
domainFQDN이 유효한 경우 사용자의 선택에 따라 DomainFQDNName 또는 RootDomainName이 반환됩니다.
계정(도메인\사용자 이름) 및 암호를 요청할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>예제 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

검색할 데이터의 원하는 종류입니다.
현재 "DomainFQDNName" 또는 "RootDomainName"으로 제한됩니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

이 함수는 사용자에게 사용자 지정 자격 증명을 요청하지 않고 현재 컴퓨터에 로그인한 사용자의 자격 증명을 사용합니다.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Start-NetworkConnectivityDiagnosisTools 함수에서 사용하는 보조 매개 변수입니다.

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>개요

계정과 암호의 조합에서 ForestFQDN을 검색합니다.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>설명

제공된 자격 증명에서 ForestFQDN을 가져오려고 시도합니다.
계정(도메인\사용자 이름) 및 암호를 요청할 수 있습니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>예제 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

대상 포리스트입니다. 기본값은 현재 로그인한 사용자의 도메인입니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

이 함수는 사용자에게 사용자 지정 자격 증명을 요청하지 않고 현재 컴퓨터에 로그인한 사용자의 자격 증명을 사용합니다.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>개요

Main 함수입니다.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>설명

AD 자격 증명이 유효한지 확인하는 사용 가능한 모든 메커니즘을 실행합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

대상 포리스트입니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

사용자 지정 설치의 경우 사용자가 AADConnect 마법사의 AD 포리스트 계정 창에서 "새 AD 계정 만들기"를 선택한 경우 플래그는 $True입니다.
사용자가 "기존 AD 계정 사용"을 선택한 경우 $False입니다.
기본 설치의 경우 기본 설치에 대한 이 변수의 값은 $True여야 합니다.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

사용자의 자격 증명이 요청될 때 사용자 이름 필드를 미리 채우는 매개 변수입니다.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>개요

네트워크 연결 테스트를 위한 Main 함수입니다.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>설명

로컬 네트워크 연결 테스트를 실행합니다.

### <a name="examples"></a>예제

#### <a name="example-1"></a>예제 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>예제 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

테스트할 포리스트 이름을 지정합니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Credentials

테스트를 실행하는 사용자의 사용자 이름과 암호입니다.
Azure AD Connect 마법사를 실행하는 데 필요한 것과 동일한 수준의 권한이 필요합니다.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

이 함수의 출력이 포함될 로그 파일의 위치를 지정합니다.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

테스트할 DC를 지정합니다.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

이 함수의 용도에 대한 메시지를 표시할 수 있는 플래그입니다.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

이 진단의 결과를 PSObject 형식으로 반환합니다.
이 도구와 수동으로 상호 작용하는 동안에는 지정할 필요가 없습니다.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

사용자가 입력한 자격 증명이 유효한지 여부를 나타냅니다.
이 도구와 수동으로 상호 작용하는 동안에는 지정할 필요가 없습니다.

```yml
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
