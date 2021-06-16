---
title: Azure Site Recovery에서 VMware vCenter Server 관리
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware VM의 재해 복구를 위해 VMware vCenter를 추가 및 관리하는 방법을 설명합니다.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: b7c0a8a6305f3142d170cb601ec23597a69fe5b0
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576778"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server 관리

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)의 VMware vCenter Server에 대한 관리 작업을 간략하게 설명합니다.

## <a name="verify-prerequisites-for-vcenter-server"></a>vCenter Server에 대한 필수 구성 요소 확인

VMware VM을 Azure로 재해 복구하는 동안 vCenter Server 및 VM에 대한 필수 구성 요소는 [지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines)에 나와 있습니다.

## <a name="set-up-an-account-for-automatic-discovery"></a>자동 검색용 계정 설정

온-프레미스 VMware VM에 대한 재해 복구를 설정할 때 Site Recovery에는 vCenter Server/vSphere 호스트에 대한 액세스 권한이 필요합니다. 그러면 Site Recovery 프로세스 서버가 VM을 자동으로 검색하고 필요에 따라 장애 조치(failover)를 할 수 있습니다. 기본적으로 프로세스 서버는 Site Recovery 구성 서버에서 실행됩니다. 구성 서버가 다음과 같이 vCenter Server/vSphere 호스트에 연결할 계정을 추가합니다.

1. 구성 서버에 로그인합니다.
1. 바탕 화면 바로 가기를 사용하여 구성 서버 도구(_cspsconfigtool.exe_)를 엽니다.
1. **계정 관리** 탭에서 **계정 추가** 를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 계정 세부 정보를 입력하고 **확인** 을 클릭하여 계정을 추가합니다. 계정에는 계정 권한 표에 요약된 권한이 있어야 합니다.

   > [!NOTE]
   > 계정 정보를 Site Recovery와 동기화하는 데 약 15분이 걸립니다.

### <a name="account-permissions"></a>계정 권한

|**Task** | **계정** | **권한** | **세부 정보**|
|--- | --- | --- | ---|
|**VM 검색/마이그레이션(장애 복구(failback) 제외)** | 읽기 전용 사용자 계정(최소) | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|
|**복제/장애 조치(failover)** | 읽기 전용 사용자 계정(최소) | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**복제/장애 조치(failover)/장애 복구(failback)** | 필요한 권한과 함께 역할(AzureSiteRecoveryRole)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|

## <a name="add-vmware-server-to-the-vault"></a>자격 증명 모음에 VMware 서버 추가

온-프레미스 VMware VM에 대한 재해 복구를 설정할 때 다음과 같이 VM을 검색 중인 vCenter Server/vSphere 호스트를 Site Recovery 자격 증명 모음에 추가합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버** 에서 구성 서버를 엽니다.
1. **세부 정보** 페이지에서 **vCenter** 를 클릭합니다.
1. **vCenter 추가** 에서 vSphere 호스트 또는 vCenter Server 식별 이름을 지정합니다.
1. 서버의 IP 주소 또는 FQDN을 지정합니다.
1. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다.
1. VMware vCenter 또는 vSphere ESXi 서버에 연결하는 데 사용되는 계정을 선택합니다. 그런 후 **OK** 를 클릭합니다.

## <a name="modify-credentials"></a>자격 증명 수정

필요한 경우 다음과 같이 vCenter Server/vSphere 호스트에 연결하는 데 사용되는 자격 증명을 수정할 수 있습니다.

1. 구성 서버에 로그인합니다.
1. 바탕 화면 바로 가기를 사용하여 구성 서버 도구(_cspsconfigtool.exe_)를 엽니다.
1. **계정 관리** 탭에서 **계정 추가** 를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 새 계정의 세부 정보를 입력하고 **확인** 을 클릭합니다. 계정에는 [계정 권한](#account-permissions) 테이블에 나열된 사용 권한이 필요합니다.
1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버** 에서 구성 서버를 엽니다.
1. **세부 정보** 에서 **서버 새로 고침** 을 클릭합니다.
1. 서버 새로 고침 작업이 완료되면 vCenter Server를 선택합니다.
1. **요약** 에서 **vCenter Server/vSphere 호스트 계정** 에 새로 추가된 계정을 선택하고 **저장** 을 클릭합니다.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter Server 삭제

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버** 에서 구성 서버를 엽니다.
1. **세부 정보** 페이지에서 vCenter 서버를 선택합니다.
1. **삭제** 단추를 클릭합니다.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>IP 주소 및 포트 수정

VCenter Server의 IP 주소 또는 서버와 Site Recovery 간에 통신하는 데 사용되는 포트를 수정할 수 있습니다. 기본적으로 Site Recovery는 포트 443을 통해 vCenter Server/vSphere 호스트 정보에 액세스합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버** 에서 vCenter Server가 추가된 구성 서버를 클릭합니다.
1. **vCenter Server** 에서 수정할 vCenter Server를 클릭합니다.
1. **요약** 에서 IP 주소와 포트를 업데이트하고 변경 내용을 저장합니다.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 변경 내용을 적용하려면 15분 정도 기다리거나 [구성 서버를 새로 고치세요](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>새 서버로 모든 VM 마이그레이션

새 vCenter Server를 사용하도록 모든 VM을 마이그레이션하려면 vCenter Server에 할당된 IP 주소만 업데이트해야 합니다. 중복 항목이 생길 수 있으므로 다른 VMware 계정을 추가하지 마세요. 주소를 다음과 같이 업데이트합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버** 에서 vCenter Server가 추가된 구성 서버를 클릭합니다.
1. **VCenter Servers** 섹션에서 마이그레이션할 vCenter Server를 클릭합니다.
1. **요약** 에서 새 vCenter Server IP 주소를 업데이트하고 변경 내용을 저장합니다.
1. IP 주소가 업데이트되는 즉시 Site Recovery는 새 vCenter Server에서 VM 검색 정보를 수신하기 시작합니다. 진행 중인 복제 작업에는 영향을 주지 않습니다.

## <a name="migrate-a-few-vms-to-a-new-server"></a>몇 개의 VM을 새 서버로 마이그레이션

복제하는 VM 중 일부를 새 vCenter Server 마이그레이션하려면 다음을 수행합니다.

1. 구성 서버에 새 vCenter Server를 [추가](#add-vmware-server-to-the-vault)합니다.
1. 새 서버로 이동할 VM에 대해 [복제를 사용하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)합니다.
1. VMware에서 VM을 새 vCenter Server로 마이그레이션합니다.
1. 새 vCenter Server를 선택하여 마이그레이션된 VM에 대한 [복제를 다시 사용하도록 설정](vmware-azure-tutorial.md#enable-replication)합니다.

## <a name="migrate-most-vms-to-a-new-server"></a>새 서버로 대부분의 VM 마이그레이션

새 vCenter Server로 마이그레이션할 VM의 수가 원래 vCenter Server에 남아 있는 VM의 수보다 많은 경우 다음을 수행합니다.

1. 구성 서버 설정의 vCenter Server에 할당된 [IP 주소](#modify-the-ip-address-and-port)를 새 vCenter Server의 주소로 업데이트합니다.
1. 이전 서버에 남아 있는 몇 개의 VM에 대해 [복제를 사용하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)합니다.
1. [이전 vCenter Server](#add-vmware-server-to-the-vault)와 해당 IP 주소를 구성 서버에 추가합니다.
1. 이전 서버에 남아 있는 VM에 대한 [복제를 다시 사용하도록 설정](vmware-azure-tutorial.md#enable-replication)합니다.

## <a name="next-steps"></a>다음 단계

문제가 있는 경우 [vCenter Server 검색 오류 문제 해결](vmware-azure-troubleshoot-vcenter-discovery-failures.md)을 참조하세요.
