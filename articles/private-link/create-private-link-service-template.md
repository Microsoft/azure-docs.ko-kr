---
title: Azure Private Link에서 프라이빗 링크 서비스 만들기
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 프라이빗 링크 서비스를 만듭니다.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: dce80d70af7cd711cf852a60b98ad65b6d21117f
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705251"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 프라이빗 링크 서비스 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 프라이빗 링크 서비스를 만듭니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) 또는 [Azure CLI](create-private-link-service-cli.md)를 사용하여 이 빠른 시작을 완료할 수도 있습니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정이 필요합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

이 템플릿은 프라이빗 링크 서비스를 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-privatelink-service/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): 각 가상 머신에 대해 하나의 가상 네트워크가 있습니다.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): 서비스를 호스팅하는 가상 머신을 노출하는 부하 분산 장치입니다.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): 각 가상 머신에 대해 하나씩 두 개의 네트워크 인터페이스가 있습니다.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): 서비스를 호스팅하는 가상 머신과 프라이빗 엔드포인트에 대한 연결을 테스트하는 가상 머신으로 두 개의 가상 머신이 있습니다.
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): 웹 서버를 설치하는 확장입니다.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): 서비스를 노출하는 프라이빗 링크 서비스입니다.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): 각 가상 머신에 대해 하나씩 두 개의 공용 IP 주소가 있습니다.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): 서비스에 액세스할 프라이빗 엔드포인트입니다.

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포하는 방법은 다음과 같습니다.

1. Azure에 로그인하고 템플릿을 열려면 **Azure에 배포**를 선택합니다. 템플릿은 가상 머신, 표준 부하 분산 장치, 프라이빗 링크 서비스, 프라이빗 엔드포인트, 네트워킹 및 유효성을 검사할 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. 리소스 그룹을 선택하거나 만듭니다.
3. 가상 머신 관리자 사용자 이름과 암호를 입력합니다.
4. 사용 약관 문을 읽습니다. 동의하는 경우 **위에 명시된 사용 약관에 동의함** > **구매**를 선택합니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

> [!NOTE]
> ARM 템플릿은 가상 머신 myConsumerVm<b>{uniqueid}</b> 리소스에 대한 고유한 이름을 생성합니다. **{uniqueid}** 에 대해 생성된 값으로 대체합니다.

### <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 VM _myConsumerVm{uniqueid}_ 에 연결합니다.

1.  포털의 검색 창에서 _myConsumerVm{uniqueid}_ 를 입력합니다.

2.  **연결**을 선택합니다. **가상 머신에 연결**이 열립니다.

3.  **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( _.rdp_) 파일을 만들고, 컴퓨터에 다운로드합니다.

4.  다운로드한 rdp 파일을 엽니다.

    a. 메시지가 표시되면 **연결**을 선택합니다.

    b. VM을 만들 때 지정한 사용자 이름 및 암호를 입력합니다.
    
    > [!NOTE]
    > **추가 선택 사항** > **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

5.  **확인**을 선택합니다.

6.  로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

7.  VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.

### <a name="access-the-http-service-privately-from-the-vm"></a>VM에서 개인적으로 http 서비스에 액세스

프라이빗 엔드포인트를 사용하여 VM에서 http 서비스에 연결하는 방법은 다음과 같습니다.

1.  _myConsumerVm{uniqueid}_ 의 원격 데스크톱으로 이동합니다.
2.  브라우저를 열고 프라이빗 엔드포인트 주소를 입력합니다. `http://10.0.0.5/`
3.  기본 IIS 페이지가 나타납니다.

## <a name="clean-up-resources"></a>리소스 정리

프라이빗 링크 서비스로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 프라이빗 링크 서비스와 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

[Azure Private Link](private-link-overview.md)에 대해 자세히 알아봅니다.
