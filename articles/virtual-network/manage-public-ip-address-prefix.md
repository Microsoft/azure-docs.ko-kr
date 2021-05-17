---
title: Azure 공용 IP 주소 접두사 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 공용 IP 주소 접두사 및 이를 생성, 변경 또는 삭제하는 방법에 대해 알아봅니다. 추가 정보를 찾을 수 있는 위치를 참조하세요.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783434"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기, 변경 또는 삭제

공용 IP 주소 접두사 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소 접두사는 사용자가 지정하는 공용 IP 주소 수에 기반을 둔 연속 주소 범위입니다. 주소는 구독에 할당됩니다. 공용 IP 주소 리소스를 만들 때 접두사에서 고정 공용 IP 주소를 만들고 주소를 가상 머신, 부하 분산 장치 또는 기타 리소스에 연결하여 인터넷 연결을 지원할 수 있습니다. 공용 IP 주소 접두사에 익숙하지 않다면 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.41 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

공용 IP 주소 접두사에는 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses)을 참조하세요.

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

1. 포털의 왼쪽 위 모서리에서 **+ 리소스 만들기** 를 선택합니다.
2. *마켓플레이스 검색* 상자에 *공용 IP 접두사* 를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사** 를 선택합니다.
3. **공용 IP 주소 접두사** 아래에서 **만들기** 를 선택합니다.
4. **공용 IP 주소 접두사 만들기** 아래에서 다음 설정의 값을 입력하거나 선택한 다음, 만들기 **를 선택합니다**:

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |구독|예|공용 IP 주소를 연결하려는 리소스와 동일한 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)에 있어야 합니다.|
   |리소스 그룹|예|공용 IP 주소를 연결하려는 리소스와 동일하거나 다른 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에 있을 수 있습니다.|
   |이름|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |지역|예|범위에서 주소를 할당할 공용 IP 주소와 동일한 [지역](https://azure.microsoft.com/regions)에 있어야 합니다.|
   |접두사 크기|예| 필요한 접두사의 크기입니다. /28 또는 16개의 IP 주소가 기본값입니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>가용성 영역을 사용하는 지역에서는 PowerShell 또는 CLI 명령을 사용하여 공용 IP 주소 접두사를 영역 비 영역, 특정 영역과 연결되도록 만들거나 영역 중복성을 사용할 수 있습니다.  API 버전 2020-08-01 이상에서는 영역 매개 변수가 제공되지 않은 경우 영역 공용 IP 주소 접두사가 생성되지 않습니다. 2020-08-01 보다 오래된 API 버전의 경우 영역 중복 공용 IP 주소 접두사가 생성됩니다. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기
접두사를 만들었으면 접두사로 고정 IP 주소를 만들어야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소 접두사* 를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사** 를 선택합니다.
2. 공용 IP를 만들려는 접두사를 선택합니다.
3. 해당 항목이 검색 결과에 표시되면 선택하고 개요 섹션에서 **+IP 주소 추가** 를 클릭합니다.
4. **공용 IP 주소 만들기** 아래에서 다음 설정의 값을 입력하거나 선택합니다. 접두사는 Standard SKU, IPv4 및 고정 주소용이므로 다음과 같은 정보만 제공해야 합니다.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |속성|예|공용 IP 주소의 이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |유휴 제한 시간(분)|예|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. |
   |DNS 이름 레이블|예|이름을 만드는 Azure 지역 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 *location.cloudapp.azure.com*(여기서 location은 선택한 위치임)과 같은 기본 서브넷을 추가합니다. 자세한 내용은 [Azure 공용 IP 주소와 Azure DNS 사용](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|

또는 아래의 CLI 및 PS 명령을 public-ip-prefix(CLI) 및 PublicIpPrefix(PS) 매개 변수와 함께 사용하여 공용 IP 주소 리소스를 생성할 수 있습니다. 

|도구|명령|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>접두사 보기 또는 삭제

1. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *공용 IP 주소 접두사* 를 입력합니다. 검색 결과에 표시된 **공용 IP 주소 접두사** 를 선택합니다.
2. 목록에서 보거나, 설정을 변경하거나, 삭제하려는 공용 IP 주소 접두사의 이름을 선택합니다.
3. 공용 IP 주소 접두사에 대해 수행하려는 작업(보기/삭제/변경)에 따라 다음 옵션 중 하나를 수행합니다.
   - **보기**: **개요** 섹션에서는 공용 IP 주소 접두사의 주요 설정을 보여줍니다(예: 접두사).
   - **삭제**: 공용 IP 주소 접두사를 삭제하려면 **개요** 섹션에서 **삭제** 를 선택합니다. 접두사 내 주소가 공용 IP 주소 리소스에 연결되어 있으면 공용 IP 주소 리소스를 먼저 삭제해야 합니다. [공용 IP 주소 삭제](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list): 공용 IP 주소를 나열함, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show): 설정을 표시함, [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update): 업데이트함, [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete): 삭제함|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix): 공용 IP 주소 개체를 검색하고 해당 설정을 확인함, [Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix): 설정을 업데이트함, [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix): 삭제함|

## <a name="permissions"></a>사용 권한

공용 IP 주소 접두사에 대한 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할 또는 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 계정이 할당되어야 합니다.

| 작업                                                            | 속성                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 공용 IP 주소 접두사 읽기                                |
| Microsoft.Network/publicIPPrefixes/write                          | 공용 IP 주소 접두사 만들기 또는 업데이트                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 공용 IP 주소 접두사 삭제                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | 접두사로 공용 IP 주소 만들기 |

## <a name="next-steps"></a>다음 단계

- [공용 IP 접두사](public-ip-address-prefix.md)를 사용하는 경우의 시나리오 및 이점에 대해 알아보기
