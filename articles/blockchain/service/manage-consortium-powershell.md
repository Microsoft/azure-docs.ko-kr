---
title: Azure Blockchain 서비스 컨소시엄 구성원 관리-PowerShell
description: Azure PowerShell를 사용 하 여 Azure Blockchain Service consortium 멤버를 관리 하는 방법을 알아봅니다.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: 4eb35838394d3324f460b5e83cfbcb47b8114221
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455503"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>PowerShell을 사용 하 여 Azure Blockchain 서비스의 컨소시엄 구성원 관리

PowerShell을 사용 하 여 Azure Blockchain 서비스의 blockchain consortium 구성원을 관리할 수 있습니다. 관리자 권한이 있는 구성원은 blockchain 컨소시엄의 모든 참가자에 대해 역할을 초대, 추가, 제거 및 변경할 수 있습니다. 사용자 권한이 있는 구성원은 blockchain 컨소시엄의 모든 참가자를 보고 멤버 표시 이름을 변경할 수 있습니다.

## <a name="prerequisites"></a>선행 조건

* [Azure Portal](create-member.md)를 사용 하 여 블록 체인 멤버를 만듭니다.
* Consortia, 멤버 및 노드에 대 한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md)을 참조 하세요.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell 열기

Azure Cloud Shell은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

[Shell.azure.com/powershell](https://shell.azure.com/powershell)으로 이동 하 여 별도의 브라우저 탭에서 Cloud Shell를 열 수도 있습니다. **복사** 를 선택 하 여 코드 블록을 복사 하 고 Cloud Shell에 붙여넣은 다음 **Enter 키** 를 선택 하 여 실행 합니다.

## <a name="install-the-powershell-module"></a>PowerShell 모듈을 설치 합니다.

PowerShell 갤러리에서 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 패키지를 설치 합니다.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>정보 기본 설정 지정

정보 기본 설정 변수를 설정 하 여 cmdlet을 실행할 때 자세한 정보를 얻을 수 있습니다. 기본적으로 *$InformationPreference* 는 *SilentlyContinue*로 설정 됩니다.

Cmdlet에서 더 자세한 정보를 보려면 다음과 같이 PowerShell에서 기본 설정을 지정 합니다.

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 연결 설정

컨소시엄 멤버를 관리 하려면 Blockchain 서비스 구성원 엔드포인트에 대 한 Web3 연결을 설정 합니다. 이 스크립트를 사용 하 여 컨소시엄 관리 cmdlet을 호출 하는 전역 변수를 설정할 수 있습니다.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

멤버 *계정 암호\>\<* 멤버를 만들 때 사용한 구성원 계정 암호로 바꿉니다.

Azure Portal에서 다른 값을 찾습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 기본 Blockchain 서비스 멤버 **개요** 페이지로 이동 합니다.

    ![멤버 개요](./media/manage-consortium-powershell/member-overview.png)

    *\<구성원 계정\>* 및 *\<rootcontract address\>* 를 포털의 값으로 바꿉니다.

1. 엔드포인트 주소에 대해 **트랜잭션 노드**를 선택 하 고 **기본 트랜잭션 노드**를 선택 합니다. 기본 노드의 이름은 blockchain 멤버와 동일 합니다.
1. **연결 문자열**을 선택합니다.

    ![연결 문자열](./media/manage-consortium-powershell/connection-strings.png)

    *\<엔드포인트 주소\>* 를 **https (액세스 키 1)** 또는 **https (액세스 키 2)** 의 값으로 바꿉니다.

## <a name="manage-the-network-and-smart-contracts"></a>네트워크 및 스마트 계약 관리

네트워크 및 스마트 계약 cmdlet을 사용 하 여 컨소시엄 관리를 담당 하는 블록 체인 엔드포인트의 스마트 계약에 대 한 연결을 설정 합니다.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

이 cmdlet을 사용 하 여 컨소시엄 관리의 스마트 계약에 연결할 수 있습니다. 이러한 계약은 컨소시엄 내에서 구성원을 관리 하 고 적용 하는 데 사용 됩니다.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| RootContractAddress | 컨소시엄 관리 스마트 계약의 루트 계약 주소 | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

이 cmdlet을 사용 하 여 원격 노드의 관리 계정에 대 한 정보를 저장 하는 개체를 만들 수 있습니다.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blockchain 구성원 계정 주소 | 예 |
| ManagedAccountPassword | 계정 주소 암호 | 예 |

#### <a name="example"></a>예

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

이 cmdlet을 사용 하 여 트랜잭션 노드의 RPC 엔드포인트에 대 한 연결을 설정할 수 있습니다.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blockchain 구성원 엔드포인트 주소 | 예 |

#### <a name="example"></a>예

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>컨소시엄 구성원 관리

컨소시엄 구성원 관리 cmdlet을 사용 하 여 컨소시엄 내에서 멤버를 관리 합니다. 사용 가능한 작업은 consortium 역할에 따라 다릅니다.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

이 cmdlet을 사용 하 여 컨소시엄의 멤버 세부 정보 또는 목록 멤버를 가져올 수 있습니다.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | 세부 정보를 검색할 Blockchain 서비스 멤버의 이름입니다. 이름을 입력 하면 멤버의 세부 정보를 반환 합니다. 이름을 생략 하면 모든 컨소시엄 멤버 목록이 반환 됩니다. | 아니오 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>예제 출력

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

이 cmdlet을 사용 하 여 blockchain 멤버를 제거할 수 있습니다.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | 제거할 멤버 이름 | 예 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Account | Web3Account에서 가져온 개체를 가져옵니다. Web3Account | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 및 $MemberAccount 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

이 cmdlet을 사용 하 여 표시 이름 및 consortium 역할을 비롯 한 blockchain 멤버 특성을 설정할 수 있습니다.

컨소시엄 관리자는 모든 구성원에 대해 **DisplayName** 및 **Role** 을 설정할 수 있습니다. 사용자 역할의 consortium 구성원은 자신의 구성원 표시 이름만 변경할 수 있습니다.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| 이름 | Blockchain 멤버의 이름 | 예 |
| DisplayName | 새 표시 이름 | 아니오 |
| AccountAddress | 계정 주소 | 아니오 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Account | Web3Account에서 가져온 개체를 가져옵니다. Web3Account | 예 |
| Web3Client |  Web3Connection에서 가져온 Web3Client 개체| 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 및 $MemberAccount 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>컨소시엄 회원 초대 관리

컨소시엄 구성원 초대 관리 cmdlet을 사용 하 여 컨소시엄 회원 초대를 관리 합니다. 사용 가능한 작업은 consortium 역할에 따라 다릅니다.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

이 cmdlet을 사용 하 여 새 멤버를 컨소시엄에 초대 합니다.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 구성원의 Azure 구독 ID | 예 |
| 역할 | 컨소시엄 역할입니다. 값은 관리자 또는 사용자 일 수 있습니다. 관리자는 consortium 관리자 역할입니다. 사용자는 consortium 구성원 역할입니다. | 예 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Account | Web3Account에서 가져온 개체를 가져옵니다. Web3Account | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 및 $MemberAccount 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

이 cmdlet을 사용 하 여 컨소시엄 회원의 초대 상태를 검색 하거나 나열할 수 있습니다.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 멤버의 Azure 구독 ID입니다. 구독 ID를 제공 하는 경우 구독 ID의 초대 정보를 반환 합니다. 구독 ID를 생략 하면 모든 멤버 초대 목록이 반환 됩니다. | 아니오 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>예제 출력

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

이 cmdlet을 사용 하 여 컨소시엄 회원의 초대를 해지할 수 있습니다.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 해지할 멤버의 Azure 구독 ID | 예 |
| 멤버 | Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Account | Web3Account에서 가져온 개체를 가져옵니다. Web3Account | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 및 $MemberAccount 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

이 cmdlet을 사용 하 여 기존 초대에 대 한 **역할** 을 설정 합니다. 컨소시엄 관리자만 초대를 변경할 수 있습니다.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 매개 변수 | 설명 | 필수 |
|-----------|-------------|:--------:|
| SubscriptionId | 초대할 구성원의 Azure 구독 ID | 예 |
| 역할 | 초대에 대 한 새 컨소시엄 역할입니다. 값은 **사용자** 또는 **관리자**일 수 있습니다. | 예 |
| 멤버 |  Import-ConsortiumManagementContracts에서 얻은 Members 개체 | 예 |
| Web3Account | Web3Account에서 가져온 개체를 가져옵니다. Web3Account | 예 |
| Web3Client | Web3Connection에서 가져온 Web3Client 개체 | 예 |

#### <a name="example"></a>예

[Web3 연결](#establish-a-web3-connection) 을 설정 하 여 $ContractConnection 및 $MemberAccount 변수를 설정 합니다.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>다음 단계

Consortia, 멤버 및 노드에 대 한 자세한 내용은 [Azure Blockchain Service 컨소시엄](consortium.md) 을 참조 하세요.
