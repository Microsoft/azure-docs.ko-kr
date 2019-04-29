---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600392"
---
**vCenter 추가**에서 vSphere 호스트 또는 vCenter Server에 대한 식별 이름을 지정하고 서버의 IP 주소 또는 FQDN을 지정합니다. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다. VMware vCenter 또는 vSphere ESXi 서버에 연결할 계정을 선택합니다. **확인**을 클릭합니다.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > VMware vCenter 서버 또는 vCenter 또는 호스트 서버에 대 한 관리자 권한이 있는지 확인 하지 않습니다는 계정으로 VMware vSphere 호스트를 추가 하는 경우 계정에이 권한이 설정 되어 있습니다. 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 머신 및 vSphere Distributed Switch 합니다. 또한 VMware vCenter 서버에 저장소 보기 권한을 사용하도록 설정되어야 합니다.
