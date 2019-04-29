---
title: Azure Site Recovery를 사용하여 Azure로 VMware VM의 재해 복구를 위한 VMware vCenter 서버 관리 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware VM의 재해 복구를 위해 VMware vCenter를 추가 및 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600364"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter 서버 관리

이 문서는 VMware vCenter에서 수행될 수 있는 다양한 Site Recovery 작업을 설명합니다. 시작하기 전에 [필수 구성 요소](vmware-physical-azure-support-matrix.md#replicated-machines)를 확인합니다.


## <a name="set-up-an-account-for-automatic-discovery"></a>자동 검색용 계정 설정

Site Recovery는 가상 머신 자동 검색 및 가상 머신의 장애 조치와 장애 복구를 위해 프로세스 서버의 VMware에 대한 액세스 권한이 필요합니다. 다음과 같이 액세스용 계정을 만듭니다.

1. 구성 서버 컴퓨터에 로그온합니다.
2. 바탕 화면 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
3. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. 계정 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다.  계정에는 다음 표에 요약된 권한이 있어야 합니다. 

계정 정보가 Site Recovery 서비스와 동기화되는 데 약 15분 정도가 걸립니다.

### <a name="account-permissions"></a>계정 권한

|**Task** | **계정** | **권한** | **세부 정보**|
|--- | --- | --- | ---|
|**자동 검색/마이그레이션(장애 복구(failback) 없이)** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|
|**복제/장애 조치(failover)** | 최소한 읽기 전용 사용자 필요| 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**복제/장애 조치(failover)/장애 복구(failback)** | 필요한 권한과 함께 역할(AzureSiteRecoveryRole)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅숏 -&gt; 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|


## <a name="add-vmware-server-to-the-vault"></a>자격 증명 모음에 VMware 서버 추가

1. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
2. **세부 정보** 페이지에서 **+vCenter**를 클릭합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>자격 증명 수정

다음과 같이 vCenter 서버 또는 ESXi 호스트에 연결하는 데 사용되는 자격 증명을 수정합니다.

1. 구성 서버에 로그인하고, 바탕 화면에서 cspsconfigtool.exe를 시작합니다.
2. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 새 계정의 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다. 계정에는 [위](#account-permissions)에 나열된 권한이 있어야 합니다.
4. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
5. **세부 정보** 페이지에서 **서버 새로 고침**을 클릭합니다.
6. 서버 새로 고침 작업이 완료되면 vCenter Server를 선택하여 vCenter **요약** 페이지를 엽니다.
7. **vCenter 서버/vSphere 호스트 계정** 필드에서 새로 추가된 계정을 선택하고 **저장**을 클릭합니다.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter 서버 삭제

1. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
2. **세부 정보** 페이지에서 vCenter 서버를 선택합니다.
3. **삭제** 단추를 클릭합니다.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>VCenter IP 주소 및 포트를 수정 합니다.

1. Azure 포털에 로그인합니다.
2. 이동할 **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**합니다.
3. 구성 서버는 vCenter에서 클릭에 할당 됩니다.
4. 에 **vCenter 서버** 섹션 수정 하려는 vCenter에서을 클릭 합니다.
5. VCenter 요약 페이지에서 IP 주소 및 포트의 각 필드에 vCenter 업데이트 하 고 변경 내용을 저장 합니다.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. 15 분 동안 기다린 후에 변경 사항이 유효 하거나 [구성 서버를 새로 고칠](vmware-azure-manage-configuration-server.md#refresh-configuration-server)합니다.

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>모든 보호 된 가상 머신을 새 vCenter로 마이그레이션

새 vCenter에 모든 가상 컴퓨터를 마이그레이션하려면 다른 vCenter 계정을 추가 하지 마세요. 항목이 중복이 발생할 수 있습니다. 새 vCenter의 IP 주소로 업데이트 합니다.

1. Azure 포털에 로그인합니다.
2. 이동할 **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**합니다.
3. 구성 서버 이전 vCenter에서 클릭에 할당 됩니다.
4. 에 **vCenter 서버** 섹션에서 마이그레이션할 하려는 vCenter에서을 클릭 합니다.
5. VCenter 요약 페이지에서 필드에 새 vCenter의 IP 주소를 업데이트할 **vCenter server/vSphere 호스트 이름 또는 IP 주소**합니다. 변경 내용을 저장합니다.

IP 주소 업데이트 되는 즉시 새 vCenter에서 가상 컴퓨터의 검색 정보를 받는 Site Recovery 구성 요소 시작 됩니다. 진행 중인 복제 작업을 영향을 주지 않습니다.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>새 vCenter로 몇 개의 보호 된 가상 컴퓨터 마이그레이션

> [!NOTE]
> 이 섹션에서는 마이그레이션하는 경우 보호 된 가상 컴퓨터의 몇 가지 새 vCenter에 적용 됩니다. 새 vCenter에서 새 가상 컴퓨터를 보호 하려는 경우 [구성 서버에 새 vCenter 세부 정보를 추가](#add-vmware-server-to-the-vault) 로 시작 하 고  **[보호 사용](vmware-azure-tutorial.md#enable-replication)** 합니다.

에 새 vCenter를 몇 개의 가상 컴퓨터를 이동 합니다.

1. [구성 서버에서 새 vCenter 정보를 추가할](#add-vmware-server-to-the-vault)합니다.
2. [가상 머신의 복제를 사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 마이그레이션할 계획입니다.
3. 새 vCenter에 선택한 가상 머신의 마이그레이션을 완료 합니다.
4. 이제에서 마이그레이션된 가상 컴퓨터를 보호 [보호를 사용 하도록 설정 하면 새 vCenter를 선택 하면](vmware-azure-tutorial.md#enable-replication)합니다.

> [!TIP]
> 마이그레이션되는 가상 머신의 숫자가 **더 높은** 이전 vcenter에서 유지 하는 가상 컴퓨터 수가 수가 여기에 제공 된 지침을 사용 하 여 새 vCenter의 IP 주소를 업데이트 합니다. 이전 vcenter에서 유지 되는 몇 가지 가상 컴퓨터용 [복제 사용 안 함](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [구성 서버에 새 vCenter 세부 정보를 추가](#add-vmware-server-to-the-vault)를 시작 하 고  **[보호 사용](vmware-azure-tutorial.md#enable-replication)** 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1. 보호 된 가상 컴퓨터는 하나의 ESXi 호스트에서 다른 호스트로 이동 되 면 하는 경우 영향을 받나요 복제?

    아니요, 진행 중인 복제에 영향 하지 않습니다. 그러나 [충분 한 권한이 있는 마스터 대상 서버를 배포 해야 합니다.](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. 포트 번호는 무엇을 간의 통신에 사용 하는 vCenter 및 기타 Site Recovery 구성 요소?

    기본 포트는 443입니다. 구성 서버는이 포트를 통해 vCenter/vSphere 호스트 정보에 액세스 합니다. 이 정보를 업데이트 하려면 클릭 [여기](#modify-the-vcenter-ip-address-and-port)합니다.
