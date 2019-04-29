---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485281"
---
*사용자 지정* 가상 머신란 간단하게 말해서 **Marketplace**의 **기능을 갖춘 앱**을 사용하여 만드는 가상 머신을 의미합니다. 이러한 가상 머신을 만드는 이유는 사용자를 대신하여 대부분의 작업을 수행하기 때문입니다. 하지만 여전히 다음과 같은 항목이 포함된 구성을 선택할 수 있습니다.

* 가상 네트워크에 가상 머신 연결
* 맬웨어 방지 등, Azure Virtual Machine Agent 및 Azure Virtual Machine Extensions 설치
* 기존 클라우드 서비스에 가상 컴퓨터 추가
* 기존 Storage 계정에 가상 머신 추가
* 가상 머신을 가용성 집합에 추가

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> 가상 네트워크를 사용하는 가상 머신을 만들려면 가상 머신을 만들 때 가상 네트워크를 지정해야 합니다.
> * 가상 네트워크를 사용할 때 얻을 수 있는 두 가지 이점은 가상 머신에 직접 연결한다는 점과 프레미스 간 연결을 설정한다는 점입니다.
> * 가상 머신을 만드는 경우에만 가상 네트워크에 가입하도록 가상 머신을 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.
>
>

## <a name="to-create-the-virtual-machine"></a>가상 컴퓨터를 만들려면

<!-- Update_Description: update meta properties -->