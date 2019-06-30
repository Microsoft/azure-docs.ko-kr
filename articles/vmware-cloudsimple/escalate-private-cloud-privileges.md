---
title: 사설 클라우드 권한을-CloudSimple 하 여 Azure VMware 솔루션 승격
description: 사설 클라우드에서 vCenter에서 관리 기능에 대 한 권한을 승격 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333071"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>CloudSimple 포털에서 사설 클라우드 vCenter 권한 상승 

일시적으로 사설 클라우드 vCenter에 대 한 관리 액세스에 대 한 CloudSimple 권한이 에스컬레이션할 수 있습니다.  상승 된 권한을 사용 하 여, VMware 솔루션 설치 id 원본 추가 및 사용자를 관리할 수 있습니다.

새 사용자 vCenter SSO 도메인에서 생성 하 고 vCenter에 대 한 액세스를 지정 수 있습니다.  새 사용자를 만들 때 추가할 CloudSimple 기본 제공 그룹에 vCenter 액세스에 대 한 합니다.  자세한 내용은 [CloudSimple 사설 클라우드 사용 권한 모델의 VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)합니다.

> [!CAUTION]
> 관리 구성 요소에 대 한 구성 변경 내용을 만들지 못합니다. 에스컬레이션 된 권한 상태에 있는 동안 수행 된 작업 시스템에 부정적인 영향을 줄 수 있습니다 또는 시스템을 사용할 수 없게 될 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="escalate-privileges"></a>권한 높이기

1. 액세스는 [CloudSimple 포털](access-cloudsimple-portal.md)합니다.

2. 엽니다는 **리소스** 페이지에서 권한을 상승 하려는 사설 클라우드를 선택 합니다.

3. 아래 요약 페이지의 아래쪽 근처 **vSphere 권한 변경**, 클릭 **에스컬레이션**합니다.

    ![VSphere 권한 변경](media/escalate-private-cloud-privilege.png)

4. VSphere 사용자 유형을 선택 합니다.  만 **CloudOwner@cloudsimple.local** 로컬 사용자를 에스컬레이션 될 수 있습니다.

5. 드롭다운 목록에서 에스컬레이션 시간 간격을 선택 합니다. 작업을 완료할 수 있도록 가장 짧은 기간을 선택 합니다.

6. 위험을 이해 하는 확인 하려면이 확인란을 선택 합니다.

    ![에스컬레이션 권한 대화 상자](media/escalate-private-cloud-privilege-dialog.png)

7. **확인**을 클릭합니다.

8. 단계별 프로세스는 몇 분 정도 걸릴 수 있습니다. 완료되면 **확인**을 클릭합니다.

권한 상승 시작 하 고 선택한 간격의 끝까지 유지 됩니다.  관리 작업을 수행 하 여 사설 클라우드 vcenter 서명할 수 있습니다.

> [!IMPORTANT]
> 사용자 한 명만는 에스컬레이션 된 권한 있을 수 있습니다.  다른 사용자의 권한을 에스컬레이션할 수 전에 취소 사용자 권한의로 에스컬레이션 해야 합니다.

## <a name="extend-privilege-escalation"></a>권한 상승 확장

작업을 완료 하려면 추가 시간이 필요한 경우에 권한 상승 기간을 확장할 수 있습니다.  추가 에스컬레이션 관리 작업을 완료할 수 있는 시간 간격을 선택 합니다.

1. 에 **리소스** > **사설 클라우드** CloudSimple 포털에서 권한 상승 확장 하려는 사설 클라우드를 선택 합니다.

2. 요약 탭 아래쪽 클릭 **권한 상승 확장**합니다.

    ![권한 상승 확장](media/de-escalate-private-cloud-privilege.png)

3. 드롭다운 목록에서 에스컬레이션 시간 간격을 선택 합니다. 새 에스컬레이션 종료 시간을 검토 합니다.

4. 클릭 **저장할** 간격을 확장할 수 있습니다.

## <a name="de-escalate-privileges"></a>취소 권한 상승

관리 작업에 완료 되 면 사용자의 권한을 에스컬레이션 해제 해야 합니다.  

1. 에 **리소스** > **사설 클라우드** CloudSimple 포털에서 해제 권한을 상승 하려는 사설 클라우드를 선택 합니다.

2. 클릭 **에스컬레이션 취소**합니다.

3. **확인**을 클릭합니다.

> [!IMPORTANT]
> 모든 오류를 방지 하려면 vCenter에서 로그 아웃 하 고 취소 권한 에스컬레이션 후에 다시 로그인 합니다.

## <a name="next-steps"></a>다음 단계

* [VCenter id 원본 Active Directory를 사용 하도록 설정](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 백업 솔루션을 설치 [가상 머신 워크 로드 백업](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)