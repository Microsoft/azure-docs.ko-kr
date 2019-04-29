---
title: Azure Site Recovery를 사용하여 Azure로 VMware 재해 복구를 위한 구성 서버 요구 사항 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware 재해 복구를 위해 구성 서버를 배포할 때 지원 및 요구 사항에 대해 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 399dcd744819cf4cb5d9f5f9636967c34e186a0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60920992"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위한 구성 서버 요구 사항

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다.

- 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다.
- 구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

## <a name="configuration-server-deployment"></a>구성 서버 배포

VMware VM을 Azure로 재해 복구하기 위해 구성 서버를 VMware VM으로 배포합니다.

- Site Recovery는 사용자가 Azure Portal에서 다운로드하고 vCenter Server로 가져와서 구성 서버 VM을 설정하는 OVA 템플릿을 제공합니다.
- OVA 템플릿을 사용하여 구성 서버를 배포하면 VM이 이 문서에 나열된 요구 사항을 자동으로 준수합니다.
- OVA 템플릿을 사용하여 구성 서버를 설정하는 것이 좋습니다. 그러나 VMware VM에 대한 재해 복구를 설정 중이고 OVA 템플릿을 사용할 수 없는 경우에는 [제공된 지침](physical-azure-set-up-source.md)을 사용하여 구성 서버를 배포할 수 있습니다.
- 온-프레미스 물리적 머신을 Azure로 재해 복구하기 위한 구성 서버를 배포하는 경우 [이 문서](physical-azure-set-up-source.md)의 지침을 따르세요. 


## <a name="hardware-requirements"></a>하드웨어 요구 사항

**구성 요소** | **요구 사항** 
--- | ---
CPU 코어 | 8 
RAM | 16GB
디스크 수 | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 
사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB

## <a name="software-requirements"></a>소프트웨어 요구 사항

**구성 요소** | **요구 사항** 
--- | ---
운영 체제 | Windows Server 2012 R2 <br> Windows Server 2016
운영 체제 로케일 | 미국 영어(en-us)
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V 
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음 <br>- [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용 

## <a name="network-requirements"></a>네트워크 요구 사항

**구성 요소** | **요구 사항** 
--- | --- 
IP 주소 유형 | 공용 
인터넷 액세스 | 서버에서 이러한 URL에 액세스해야 합니다(직접 또는 프록시를 통해). <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF에는 다음 URL에 대한 액세스 권한도 필요합니다. <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송) 
NIC 유형 | VMXNET3(구성 서버가 VMware VM인 경우)

## <a name="required-software"></a>필수 소프트웨어

**구성 요소** | **요구 사항** 
--- | ---
VMware vSphere PowerCLI | 구성 서버가 VMware VM에서 실행되는 경우 [PowerCLI 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)을 설치해야 합니다.
MYSQL | MySQL을 설치해야 합니다. 수동으로 설치할 수도 있고 Site Recovery를 통해 설치할 수도 있습니다.

## <a name="sizing-and-capacity-requirements"></a>크기 및 용량 요구 사항

다음 표에서 구성 서버에 대한 용량 요구 사항을 보여 줍니다. 여러 VMware VM을 복제하는 경우 [용량 계획 고려 사항](site-recovery-plan-capacity-vmware.md)을 검토하고, VMWare 복제용 [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) 도구를 실행해야 합니다. 다음을 참조하세요. 

**구성 요소** | **요구 사항** 
--- | ---

| **CPU** | **메모리** | **캐시 디스크** | **데이터 변경률** | **복제된 컴퓨터** |
| --- | --- | --- | --- | --- |
| vCPU 8대<br/><br/> 2개 소켓 * 4코어 \@ 2.5GHz | 16GB | 300GB | 500GB 이하 | 머신 100대 미만 |
| vCPU 12대<br/><br/> 2개 소켓 * 6코어 \@ 2.5GHz | 18GB | 600GB | 500GB-1TB | 컴퓨터 100-150대 |
| vCPU 16대<br/><br/> 2개 소켓 * 8코어 \@ 2.5GHz | 32GB | 1TB | 1-2TB | 머신 150-200대 | 



## <a name="next-steps"></a>다음 단계
Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
