---
title: Azure Site Recovery를 사용하여 VMware의 소스 환경을 Azure 복제로 설정 | Microsoft Docs
description: 이 아티클에서는 Azure Site Recovery를 사용하여 Azure에 VMware VM을 복제하도록 온-프레미스 환경을 설정하는 방법을 설명합니다.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 075f86b24e2915d9689db8097889a830bade74c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723429"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Azure 복제에 대한 VMware의 원본 환경 설정

이 아티클에서는 VMware VM을 Azure에 복제하도록 원본 온-프레미스 환경을 설정하는 방법을 설명합니다. 여기에는 복제 시나리오 선택, 온-프레미스 컴퓨터를 Site Recovery 구성 서버로 설정, 온-프레미스 VM 자동 검색을 위한 단계가 포함됩니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 작업을 이미 수행한 것으로 가정합니다.

- [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md)를 활용하여 배포 계획을 세웠습니다. 이 경우 일별 데이터 변경률에 따라 원하는 RPO(복구 지점 목표)를 충족할 수 있는 충분한 대역폭을 할당할 수 있습니다.
- [Azure Portal](https://portal.azure.com)에서 [리소스 설정](tutorial-prepare-azure.md)
- 자동 검색용 전용 계정을 포함하는 [온-프레미스 VMware 설정](vmware-azure-tutorial-prepare-on-premises.md)

## <a name="choose-your-protection-goals"></a>보호 목표 선택

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름을 선택합니다. 이 시나리오에서는 **ContosoVMVault**를 사용합니다.
2. **시작**에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화된 경우**에서 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 다음 **확인**을 선택합니다.

## <a name="set-up-the-configuration-server"></a>구성 서버 설정

OVA(Open Virtualization Application) 템플릿을 통해 구성 서버를 온-프레미스 VMware VM으로 설정할 수 있습니다. VMware VM에 설치되는 구성 요소에 대해 [자세히 알아봅니다](concepts-vmware-to-azure-architecture.md).

1. 구성 서버 배포에 대한 [필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)에 대해 알아봅니다.
2. 배포에 대한 [용량 수치를 확인](vmware-azure-deploy-configuration-server.md#capacity-planning)합니다.
3. OVA 템플릿을 [다운로드](vmware-azure-deploy-configuration-server.md#download-the-template)하고 [가져와서](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) 구성 서버를 실행하는 온-프레미스 VMware VM을 설정합니다. 템플릿을 사용 하 여 제공 된 라이선스는 평가판 라이선스 이며 180 일 동안 유효 합니다. Post이 기간 동안 고객 해야 확보 라이선스를 사용 하 여 windows 정품 인증 합니다.
4. VMware VM을 켜고 Recovery Services 자격 증명 모음에 [등록](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)합니다.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>바이러스 백신 프로그램에서 Azure Site Recovery 폴더 제외

### <a name="if-antivirus-software-is-active-on-source-machine"></a>바이러스 백신 소프트웨어가 원본 컴퓨터에서 활성 상태인 경우

원본 컴퓨터에서 바이러스 백신 소프트웨어가 활성 상태인 경우 설치 폴더를 제외해야 합니다. 원활한 복제를 위해 폴더 *C:\ProgramData\ASR\agent*를 제외합니다.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>바이러스 백신 소프트웨어가 구성 서버에서 활성 상태인 경우

원활한 복제를 수행하고 연결 문제를 방지하려면 바이러스 백신 소프트웨어에서 다음 폴더를 제외합니다.

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - ASR 서버 설치 디렉터리입니다. 예를 들면 다음과 같습니다. E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>바이러스 백신 소프트웨어가 스케일 아웃 프로세스 서버/마스터 대상에서 활성 상태인 경우

바이러스 백신 소프트웨어에서 다음 폴더를 제외합니다.

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. ASR 부하 분산된 프로세스 서버 설치 디렉터리, 예: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>다음 단계
[대상 환경 설정](./vmware-azure-set-up-target.md) 
