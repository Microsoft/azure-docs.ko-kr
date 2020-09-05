---
title: Azure virtual machines에 SAP HANA 설치 | Microsoft Docs '
description: Azure Vm에 SAP HANA를 설치 하기 위한 가이드
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: f2a62cb08fcce6597f02c080231f5e1808794054
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509963"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Azure virtual machines에 SAP HANA 설치
## <a name="introduction"></a>소개
이 가이드는 Azure virtual machines에서 HANA를 배포 하는 데 적합 한 리소스를 가리키도록 도와줍니다. 이 가이드에서는 Azure VM에 SAP HANA를 설치 하기 전에 확인 해야 하는 설명서 리소스를 안내 합니다. 따라서 올바른 단계를 수행 하 여 Azure Vm에서 지원 되는 SAP HANA 구성으로 종료할 수 있습니다.  

> [!NOTE]
> 이 가이드에서는 Azure VM에 SAP HANA를 배포하는 방법에 대해 설명합니다. HANA large instances에 SAP HANA를 배포 하는 방법에 대 한 자세한 내용은 [Azure에서 SAP HANA (Large instances)를 설치 및 구성 하는 방법](./hana-installation.md)을 참조 하세요.
 
## <a name="prerequisites"></a>사전 요구 사항
또한이 가이드에서는 다음에 대해 잘 알고 있다고 가정 합니다.
* SAP HANA, SAP NetWeaver 및 온-프레미스에 설치하는 방법
* Azure에서 SAP HANA 및 SAP 응용 프로그램 인스턴스를 설치 하 고 작동 하는 방법입니다.
* 에 설명 된 개념 및 절차는 다음과 같습니다.
   * Azure에서 SAP 배포 계획-Azure Virtual Network 계획 및 Azure Storage 사용을 포함 합니다. [Azure Virtual Machines의 SAP NetWeaver-계획 및 구현 가이드](./planning-guide.md) 를 참조 하세요.
   * Azure에 VM을 배포하는 배포 원칙 및 방법 - [SAP 용 Azure Virtual Machines 배포를](./deployment-guide.md) 참조 하세요.
   * [Azure virtual machines의 SAP HANA 고가용성](./sap-hana-availability-overview.md) 에 설명 된 SAP HANA에 대 한 고가용성 개념

## <a name="step-by-step-before-deploying"></a>배포 전 단계별 실행
이 섹션에서는 Azure 가상 컴퓨터에서 SAP HANA 설치를 시작 하기 전에 수행 해야 하는 다른 단계가 나열 되어 있습니다. 순서를 열거 하 고 열거 된 후에 다음을 수행 해야 합니다.

1. 일부 가능한 배포 시나리오는 Azure에서 지원 되지 않습니다. 따라서 SAP HANA 배포와 관련 하 여 [Azure 가상 머신에서 지원 되는 시나리오에서 SAP 워크 로드](./sap-planning-supported-configurations.md) 문서를 확인 해야 합니다. 시나리오가 나열 되지 않은 경우에는 테스트 되지 않은 것으로 가정 하 고, 결과적으로는 지원 되지 않는 것으로 가정 해야 합니다.
2. SAP HANA 배포에 대 한 메모리 요구 사항을 대략적으로 이해 하 고 있다고 가정 하 고, 피팅 Azure VM을 찾아야 합니다. Sap [support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 설명 된 대로 sap NetWeaver에 대해 인증 된 모든 vm은 SAP HANA 인증 되지 않습니다. SAP HANA 인증 된 Azure Vm에 대 한 진위는 웹 사이트 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)입니다. **S** 로 시작 하는 단위는 [HANA Large Instances](./hana-overview-architecture.md) 단위 이며 Azure vm이 아닙니다.
3. 서로 다른 Azure VM 유형은 SUSE Linux 또는 Red Hat Linux에 대해 서로 다른 최소 운영 체제 릴리스를 포함 합니다. 웹 사이트 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서이 단위에 대 한 자세한 데이터를 얻으려면 SAP HANA 인증 단위 목록에서 항목을 클릭 해야 합니다. 지원 되는 HANA 워크 로드 외에도 SAP HANA에 대해 해당 단위에서 지원 되는 OS 릴리스가 나열 됩니다.
4. 운영 체제 릴리스에서는 특정 최소 커널 릴리스를 고려해 야 합니다. 이러한 최소 릴리스는 이러한 SAP 지원 정보에 설명 되어 있습니다.
    - [SAP support note #2814271 SAP HANA 백업이 체크섬 오류로 Azure에서 실패 함](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP support note #2753418 타이머 대체로 인 한 잠재적 성능 저하](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP support note #2791572 Azure에서 Hyper-v에 대 한 VDSO 지원이 누락 되어 성능 저하가 발생 합니다.](https://launchpad.support.sap.com/#/notes/2791572)
4. 선택한 가상 컴퓨터 유형에 대해 지원 되는 OS 릴리스를 기반으로 원하는 SAP HANA 릴리스가 해당 운영 체제 릴리스부터 지원 되는지 여부를 확인 해야 합니다. 다른 운영 체제 릴리스를 사용 하는 SAP HANA 릴리스의 지원 매트릭스에 대 한 [#2235581 SAP 지원 정보](https://launchpad.support.sap.com/#/notes/2235581) 를 참조 하세요.
5. Azure VM 유형, 운영 체제 릴리스 및 SAP HANA 릴리스의 유효한 조합을 발견 했을 수 있으므로 SAP 제품 가용성 매트릭스를 확인 해야 합니다. SAP 가용성 매트릭스에서 SAP HANA 데이터베이스에 대해 실행 하려는 SAP 제품이 지원 되는지 여부를 확인할 수 있습니다.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>단계별 VM 배포 및 게스트 OS 고려 사항
이 단계에서는 HANA를 설치 하는 VM을 배포 하는 단계를 수행 하 고 궁극적으로 설치 후 선택한 운영 체제를 최적화 해야 합니다.

1. Azure 갤러리에서 기본 이미지를 선택 합니다. SAP HANA에 대 한 사용자 고유의 운영 체제 이미지를 빌드하려면 성공적인 SAP HANA 설치에 필요한 모든 패키지를 알고 있어야 합니다. 그렇지 않으면 SAP 용 SUSE 및 Red Hat 이미지를 사용 하거나 Azure 이미지 갤러리에서 SAP HANA 하는 것이 좋습니다. 이러한 이미지에는 성공적인 HANA 설치에 필요한 패키지가 포함 됩니다. 운영 체제 공급자의 지원 계약에 따라 사용자 고유의 라이선스를 가져올 이미지를 선택 해야 합니다. 또는 지원이 포함 된 OS 이미지를 선택 합니다.
2. 사용자 고유의 라이선스를 제공 해야 하는 게스트 OS 이미지를 선택한 경우 최신 패치를 다운로드 하 고 적용할 수 있도록 구독에 OS 이미지를 등록 해야 합니다. 이 단계에서는 공용 인터넷 액세스가 필요 합니다. Azure에서 SMT 서버와 같은의 전용 인스턴스를 설정 하지 않는 한
3. VM의 네트워크 구성을 결정 합니다. [Azure의 인프라 구성 및 작업 SAP HANA](./hana-vm-operations.md)문서에서 자세한 내용을 볼 수 있습니다. Azure에서 가상 네트워크 카드에 할당할 수 있는 네트워크 처리량 할당량이 없다는 점에 유의 하세요. 따라서 다른 vNICs를 통해 트래픽을 전달 하는 유일한 목적은 보안 고려 사항에 따라 달라 집니다. 여러 vNICs를 통한 트래픽 라우팅의 복잡성과 보안 측면에서 적용 되는 요구 사항 간에 지원 되는 손상을 찾을 수 있습니다.
3. VM을 배포 하 고 등록 한 후 운영 체제에 최신 패치를 적용 합니다. 사용자 고유의 구독으로 등록 됩니다. 또는 운영 체제 지원을 포함 하는 이미지를 선택한 경우 VM은 이미 패치에 액세스할 수 있어야 합니다. 
4. SAP HANA에 필요한 튜닝을 적용 합니다. 이러한 튜닝은 이러한 SAP 지원 정보에 나와 있습니다.

    - [SAP support note #2694118-Azure의 Red Hat Enterprise Linux HA 추가 기능](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP support note #1984787-SUSE LINUX Enterprise Server 12: 설치 참고 사항](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP support note #2578899-SUSE Linux Enterprise Server 15: 설치 참고](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP support note #2002167-Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문) 
    -  [SAP support note #2772999-Red Hat Enterprise Linux 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP support note #2777782-SAP HANA DB: RHEL 8에 권장 되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP support note #2455582-Linux: GCC 6.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP support note #2382421-HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)

1. SAP HANA에 대 한 Azure storage 유형을 선택 합니다. 이 단계에서는 SAP HANA 설치용 저장소 레이아웃을 결정 해야 합니다. 연결 된 Azure 디스크 또는 기본 Azure NFS 공유를 사용 하 게 됩니다. 사용할 수 있는 azure storage 유형 또는 지원 되는 Azure storage 유형은 [azure virtual machine 저장소 구성 SAP HANA](./hana-vm-operations-storage.md)에 설명 되어 있습니다. 시작 지점으로 문서화 된 구성을 수행 합니다. 비프로덕션 시스템의 경우 낮은 처리량 또는 IOPS를 구성할 수 있습니다. 프로덕션 용도로 더 많은 처리량 및 IOPS를 구성 해야 할 수 있습니다.
2. M 시리즈 또는 Mv2 시리즈 Vm을 사용 하는 경우 DBMS 트랜잭션 로그 또는 다시 실행 로그를 포함 하는 볼륨에 대해 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md) 를 구성 했는지 확인 합니다. 문서화 된 쓰기 가속기에 대 한 제한 사항을 알고 있어야 합니다.
2. 배포 된 VM에서 [Azure 가속화 된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 을 사용 하도록 설정 했는지 확인 합니다.

> [!NOTE]
> 다른 sap-튜닝 프로필의 명령이 나 메모에 설명 된 것과 같은 모든 명령이 Azure에서 성공적으로 실행 되는 것은 아닙니다. 기본 Azure 호스트 하드웨어의 전원 모드를 조작할 수 없기 때문에 Vm의 전원 모드를 조작 하는 명령은 일반적으로 오류와 함께 반환 됩니다.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure virtual machines에 특정 한 단계별 준비
Azure의 세부 사항 중 하나는 SAP 호스트 에이전트에 대 한 모니터링 데이터를 제공 하는 Azure VM 확장의 설치입니다. 이 모니터링 확장의 설치에 대 한 세부 정보는 다음에 설명 되어 있습니다.

-  [Sap Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) 는 Azure에서 Linux vm을 사용한 sap 고급 모니터링에 대해 설명 합니다. 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) 는 LINUX의 SAPOSCOL에 대 한 정보를 설명 합니다. 
-  [Sap Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) MICROSOFT AZURE의 sap에 대 한 주요 모니터링 메트릭에 대해 설명 합니다.
-  [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 설치
Azure 가상 컴퓨터를 배포 하 고 등록 및 구성 된 운영 체제를 사용 하 여 SAP 설치에 따라 SAP HANA를 설치할 수 있습니다. 이 설명서를 시작 하는 것이 좋습니다 .이 SAP 웹 사이트 [HANA 리소스](https://www.sap.com/products/hana/implementation/resources.html) 부터 시작 하세요.

Azure Premium Storage 또는 Ultra disk의 직접 연결 된 디스크를 사용 하는 SAP HANA 스케일 아웃 구성의 경우 [azure의 인프라 구성 및 작업 문서 SAP HANA](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out) 문서에서 구체적인 내용을 읽으십시오.


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 백업에 대 한 추가 리소스
Azure Vm에서 SAP HANA 데이터베이스를 백업 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure Virtual Machines에서 SAP HANA에 대 한 백업 가이드](./sap-hana-backup-guide.md)
* [파일 수준의 SAP HANA Azure Backup](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>다음 단계
설명서를 참조 하세요.

- [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md)
- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)
