---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 55e46e058bddca717929df61b2bc766b89e0f885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485302"
---
![독립 실행형 클라우드 서비스의 가상 머신](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

가상 머신을 가상 네트워크에 둔 경우 부하 분산 및 가용성 집합에 사용할 클라우드 서비스 수를 결정할 수 있습니다. 또한 온-프레미스 네트워크와 동일한 방법으로 서브넷에서 가상 머신을 구성하고 가상 머신을 온-프레미스 네트워크에 연결할 수 있습니다. 예를 들면 다음과 같습니다.

![가상 네트워크의 가상 머신](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

가상 네트워크는 Azure에서 가상 머신을 연결하는 데 권장되는 방법입니다. 애플리케이션의 각 계층을 별도의 클라우드 서비스에 구성하는 것이 가장 좋습니다. 그러나 구독당 클라우드 서비스 수를 최대 200개의 범위 내에서 유지하기 위해 여러 애플리케이션 계층의 일부 가상 머신을 동일한 클라우드 서비스에 통합해야 할 수도 있습니다. 이 제한 및 다른 제한을 검토하려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../articles/azure-subscription-service-limits.md)을 참조하세요.

## <a name="connect-vms-in-a-virtual-network"></a>가상 네트워크에서 VM 연결
가상 네트워크에서 가상 머신을 연결하려면

1. [Azure Portal](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 가상 네트워크를 만들고 '클래식 배포'를 지정합니다.
2. 가용성 집합 및 부하 분산에 대한 디자인을 반영하기 위해 배포에 대한 클라우드 서비스의 집합을 만듭니다. Azure Portal에서 각 클라우드 서비스에 대해 **리소스 만들기 > Compute > 클라우드 서비스**를 클릭합니다.

   클라우드 서비스 정보를 작성할 때 가상 네트워크와 함께 사용한 동일한 _리소스 그룹_을 선택합니다.

3. 새 가상 머신을 만들려면 **리소스 만들기 > Compute**를 클릭하고 **추천 앱**에서 적절한 VM 이미지를 선택합니다.

   VM **기본 사항** 블레이드에서 가상 네트워크와 함께 사용한 동일한 _리소스 그룹_을 선택합니다.

   ![VNet을 사용하는 경우 VM 기본 블레이드](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. VM **설정**을 작성할 때 VM에 올바른 _클라우드 서비스_ 또는 _가상 네트워크_를 선택합니다.

   Azure는 선택에 따라 다른 항목을 선택합니다.

   ![VNet을 사용하는 경우 VM 설정 블레이드](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>독립 실행형 클라우드 서비스에서 VM 연결
독립 실행형 클라우드 서비스에서 가상 머신을 연결하려면

1. [Azure Portal](http://portal.azure.com)에서 클라우드 서비스를 만듭니다. **새로 만들기 > Compute > 클라우드 서비스**를 클릭합니다. 또는 첫 번째 가상 머신을 만들 때 배포에 대한 클라우드 서비스를 만들 수 있습니다.
2. 가상 머신을 만들 때 클라우드 서비스와 함께 사용한 동일한 리소스 그룹을 선택합니다.

   ![기존 클라우드 서비스에 가상 머신 추가](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3. VM 세부 정보를 작성할 때 첫 번째 단계에서 만든 클라우드 서비스의 이름을 선택합니다.

   ![가상 컴퓨터에 대한 클라우드 서비스 선택](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
