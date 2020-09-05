---
title: HCX(하이브리드 클라우드 확장) 설치
description: Azure VMware 솔루션 사설 클라우드에 대해 VMware HCX (하이브리드 클라우드 확장) 솔루션 설정
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: fb8497af33b364c1d2ab475233bd2a83ef1befad
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752325"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Azure VMware 솔루션용 HCX 설치

이 문서에서는 Azure VMWare 솔루션 사설 클라우드에 대해 VMWare HCX (하이브리드 클라우드 확장) 솔루션을 설정 하는 절차를 안내 합니다. HCX를 사용 하면 VMware 워크 로드를 클라우드로, 다른 연결 된 사이트를 다양 한 기본 제공 HCX 지원 마이그레이션 유형을 통해 마이그레이션할 수 있습니다.

HCX Advanced, 기본 설치는 최대 3 개의 사이트 연결 (온-프레미스 또는 클라우드-클라우드)을 지원 합니다. 3 개 이상의 사이트 연결이 필요한 경우 고객은 현재 미리 보기로 제공 되는 지원을 통해 HCX Enterprise 추가 기능을 사용 하도록 설정할 수 있습니다. HCX Enterprise는 GA(일반 공급) 후 [추가 기능](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)도 제공합니다. 단, 고객에게 추가 요금이 발생합니다.


[시작하기 전에](#before-you-begin), [소프트웨어 버전 요구 사항](#software-version-requirements) 및 [필수 구성 요소](#prerequisites)를 철저하게 검토합니다. 

그런 다음, 다음을 수행 하는 데 필요한 모든 절차를 살펴보겠습니다.

> [!div class="checklist"]
> * 온-프레미스 HCX OVA (커넥터) 배포
> * HCX 활성화 및 구성
> * 네트워크 업링크 및 서비스 메시 구성
> * 어플라이언스 상태를 확인하여 설치 완료

설치를 완료 한 후에는이 문서의 끝에 제공 된 권장 되는 다음 단계를 따를 수 있습니다.  

## <a name="before-you-begin"></a>시작하기 전에
    
* 기본 Azure VMware 솔루션 SDDC (소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md)를 검토 합니다.
* HCX 사용자 가이드를 포함 하 여 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html) 를 검토 하 고 참조 합니다.
* Vmware 문서 [vmware HCX로 Virtual Machines 마이그레이션을](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)검토 합니다.
* 필요에 따라 [VMware HCX 배포 고려 사항을](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)검토 합니다.
* 필요에 따라 HCX의 VMware vSphere [블로그 시리즈](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)와 같은 HCX의 관련 VMware 자료 검토 
* Azure VMware 솔루션 지원 채널을 통해 Azure VMware 솔루션 HCX Enterprise 활성화를 요청 합니다.

Azure VMware 솔루션 사설 클라우드 HCX 솔루션을 사용 하기 위해 준비 하는 경우 계산 및 저장소 리소스에 대 한 워크 로드의 크기를 조정 하는 것이 필수 계획 단계입니다. 초기 사설 클라우드 환경 계획의 일부로 크기 조정 단계를 처리 합니다. 

또한 Azure Migrate 포털 ()에서 Azure VMware 솔루션 평가를 완료 하 여 워크 로드의 크기를 지정할 수 있습니다 https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) .

## <a name="software-version-requirements"></a>소프트웨어 버전 요구 사항

인프라 구성 요소는 필요한 최소 버전을 실행해야 합니다. 
                                                         
| 구성 요소 유형    | 원본 환경 요구 사항    | 대상 환경 요구 사항   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>5.5 U1 또는 이전 버전을 사용 하는 경우 HCX 작업에 독립 실행형 HCX 사용자 인터페이스를 사용 합니다.  | 6.0 U2 이상   |
| ESXi   | 5.0    | ESXi 6.0 이상   |
| NSX    | 원본에서 논리 스위치의 HCX 네트워크 확장의 경우: NSXv 6.2 + 또는 NSX-T 2.4 +   | NSXv 6.2+ 또는 NSX-T 2.4+<br/><br/>HCX 근접 라우팅의 경우: NSXv 6.4 + (NSX에서 근접 라우팅이 지원 되지 않음) |
| vCloud Director   | 필요하지 않음 - 원본 사이트에서 vCloud Director와의 상호 운용성 없음 | 대상 환경을 vCloud Director와 통합 하는 경우 최소값은 9.1.0.2입니다.  |

## <a name="prerequisites"></a>사전 요구 사항

* Express 경로 Global Reach 온-프레미스와 Azure VMware 솔루션 SDDC Express 경로 간에 구성 되어야 합니다.

* 온-프레미스와 Azure VMware 솔루션 SDDC 간에 필요한 모든 포트가 열려 있어야 합니다 ( [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)참조).

* 온-프레미스에서 HCX Manager에 대해 하나의 IP 주소, 상호 연결 (IX) 및 네트워크 확장 (NE) 어플라이언스에 대해 최소 두 개의 IP 주소

* 온-프레미스 HCX IX 및 NE 어플라이언스는 vCenter 및 ESXi 인프라에 도달할 수 있어야 합니다.

* WAN 상호 연결 어플라이언스를 배포하려면 Azure Portal에서 SDDC 배포에 사용되는 /22 CIDR 네트워크 주소 블록 외에도 HCX에는 /29 블록이 필요합니다. 이러한 요구 사항을 네트워크 계획에 맞게 지정 해야 합니다.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>VMware HCX OVA 온-프레미스 배포

1. Azure VMware 솔루션 SDDC vCenter에 로그인 하 고 **Hcx**를 선택 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/avs-vsphere-client.png" alt-text="Azure VMware 솔루션 SDDC vCenter에 로그인 하 고 HCX를 선택 합니다.":::

1. **관리**에서 **시스템 업데이트** 를 선택 하 고 **다운로드 요청 링크** 를 선택 하 여 VMware hcx ova 파일을 다운로드 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/administration-updates.png" alt-text="관리에서 시스템 업데이트를 선택 하 고 다운로드 요청 링크를 선택 하 여 VMware HCX OVA 파일을 다운로드 합니다.":::

1. 그런 다음 온-프레미스 vCenter로 이동 하 여 온-프레미스 vCenter에 배포할 위치 템플릿을 선택 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="그런 다음 온-프레미스 vCenter로 이동 하 여 온-프레미스 vCenter에 배포할 위치 템플릿을 선택 합니다.":::

1. 이름 및 위치를 선택한 다음, HCX를 배포해야 하는 리소스/클러스터를 선택합니다. 그런 다음, 세부 정보 및 필요한 리소스를 검토합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" 이름 및 위치를 선택한 다음 HCX를 배포 해야 하는 리소스/클러스터를 선택 합니다. 그런 다음 세부 정보 및 필요한 리소스를 검토 합니다.":::

1. 사용 조건을 검토하고 동의하는 경우 필요한 스토리지 및 네트워크를 선택합니다. 그런 후 **다음**을 선택합니다.

1. **템플릿사용자 지정**에서 필요한 모든 정보를 입력합니다. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="템플릿 사용자 지정에서 필요한 모든 정보를 입력 합니다.":::

1. **다음**을 선택하고, 구성을 확인한 후, **마침**을 선택하여 HCX OVA를 배포합니다.

## <a name="activate-hcx"></a>HCX 활성화

설치 후에 다음 단계를 수행합니다.

1. 에서 온-프레미스 HCX manager에 로그온 하 `https://HCXManagerIP:9443` 고 사용자 이름 및 암호를 사용 하 여 로그인 합니다. 

   > [!IMPORTANT]
   > `9443`HCX 관리자 IP 주소를 사용 하 여 포트 번호를 포함 해야 합니다.

1. **라이선스**에서 **HCX 고급 키**를 입력합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="라이선스에서 HCX 고급 키를 입력 합니다.":::
    
    > [!NOTE]
    > HCX Manager는 인터넷 액세스가 가능하거나 프록시가 구성되어 있어야 합니다.

1. **Vcenter**에서 필요한 경우 vcenter 정보를 편집 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="VCenter에서 필요한 경우 vCenter 정보를 편집 합니다.":::

1. **데이터 센터 위치**에서 필요한 경우 데이터 센터 위치를 편집합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="데이터 센터 위치에서 필요한 경우 데이터 센터 위치를 편집 합니다.":::

## <a name="configure-hcx"></a>HCX 구성 

1. 온-프레미스 vCenter에 로그인 하 고 **홈**에서 **hcx**를 선택 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="온-프레미스 vCenter에 로그인 하 고 홈에서 HCX를 선택 합니다.":::

1. **인프라**에서 **사이트 쌍**을 선택  >  **하 여 사이트 페어링을 추가**합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="인프라에서 사이트 페어링 > 선택 하 여 사이트 페어링을 추가 합니다.":::

1. 원격 HCX URL 또는 IP 주소, Azure VMware Solution cloudadmin 사용자 이름 및 암호를 입력 한 다음 **연결**을 선택 합니다.

   시스템에서 연결된 사이트가 표시됩니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="시스템에서 연결된 사이트가 표시됩니다.":::

1. **인프라**에서 상호 연결 **Interconnect**  >  **다중 사이트 서비스 메시**  >  **네트워크**프로필  >  **네트워크 프로필 만들기**를 선택 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="인프라 아래에서 상호 연결 > 다중 사이트 서비스 메시 > 네트워크 프로필 > 네트워크 프로필 만들기를 선택 합니다.":::

1. 새 네트워크 프로필에 대해 HCX IX 및 NE IP 주소 범위를 입력 합니다. IX 및 NE 어플라이언스에는 최소 두 개의 IP 주소가 필요 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="새 네트워크 프로필에 대해 HCX IX 및 NE IP 주소 범위를 입력 합니다. IX 및 NE 어플라이언스에는 최소 두 개의 IP 주소가 필요 합니다.":::
  
   > [!NOTE]
   > 네트워크 확장 어플라이언스(HCX-NE)에는 DVS(분산 가상 스위치)와 일 대 일 관계가 있습니다.  

1. 이제 **컴퓨팅 프로필** > **컴퓨팅 프로필 만들기**를 선택합니다.

1. 컴퓨팅 프로필 이름을 입력하고 **계속**을 선택합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="계산 프로필 이름을 입력 하 고 계속을 선택 합니다.":::

1. 사용할 서비스 (예: 마이그레이션, 네트워크 확장 또는 재해 복구)를 선택 하 고 **계속**을 선택 합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="사용할 서비스 (예: 마이그레이션, 네트워크 확장 또는 재해 복구)를 선택 하 고 계속을 선택 합니다.":::

1. **서비스 리소스 선택**에서 선택한 HCX 서비스를 사용하도록 설정해야 하는 서비스 리소스를 하나 이상 선택합니다. **계속**을 선택합니다.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="서비스 리소스 선택에서 선택한 HCX 서비스를 사용 하도록 설정 해야 하는 서비스 리소스를 하나 이상 선택 합니다. 계속을 선택 합니다.":::
  
   > [!NOTE]
   > HCX를 사용하여 마이그레이션을 위해 대상으로 지정하는 원본 VM의 특정 클러스터를 선택합니다.

1. **Datastore**, **계속**을 차례로 선택합니다. 
      
   HCX 상호 연결 어플라이언스를 배포하기 위한 각 컴퓨팅 및 스토리지 리소스를 선택합니다. 여러 리소스를 선택하는 경우 HCX는 해당 용량을 모두 사용할 때까지 선택된 첫 번째 리소스를 사용합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="HCX Interconnect 어플라이언스를 배포 하기 위한 각 계산 및 저장소 리소스를 선택 합니다. 여러 리소스를 선택 하는 경우 HCX는 용량을 모두 사용할 때까지 선택 된 첫 번째 리소스를 사용 합니다.":::

1. **네트워크 프로필**에서 만든 관리 네트워크 프로필을 선택하고 **계속**을 선택합니다.  
      
   vCenter 및 ESXi 호스트의 관리 인터페이스에 연결할 수 있는 네트워크 프로필을 선택합니다. 이러한 네트워크 프로필을 아직 정의하지 않은 경우 여기에서 만들 수 있습니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="VCenter 및 ESXi 호스트의 관리 인터페이스에 연결할 수 있는 네트워크 프로필을 선택 합니다. 이러한 네트워크 프로필을 아직 정의 하지 않은 경우 여기에서 만들 수 있습니다.":::

1. **네트워크 업링크**를 선택하고 **계속**을 선택합니다.
      
   다음 중 하나에 해당 하는 네트워크 프로필을 하나 이상 선택 합니다.  
   * 원격 사이트의 상호 연결 어플라이언스를 이 네트워크를 통해 연결할 수 있습니다.  
   * 원격 측 어플라이언스를 이 네트워크를 통해 로컬 상호 작용 어플라이언스에 연결할 수 있습니다.  
    
   여러 사이트에서 공유되지 않는 지점 간 네트워크(예: Direct Connect)를 사용하는 경우 컴퓨팅 프로필은 여러 사이트에서 공유되므로 이 단계를 건너뛸 수 있습니다. 이러한 경우, 상호 연결 서비스 메시를 만드는 동안 업링크 네트워크 프로필을 재정의하고 지정할 수 있습니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="네트워크 업링크를 선택 하 고 계속을 선택 합니다.":::

1. **vMotion Network Profile**을 선택하고 **계속**을 선택합니다.
      
   ESXi 호스트의 vMotion 인터페이스에 연결할 수 있는 네트워크 프로필을 선택합니다. 이러한 네트워크 프로필을 아직 정의하지 않은 경우 여기에서 만들 수 있습니다. vMotion 네트워크가 없는 경우 **관리 네트워크 프로필**을 선택합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="VMotion 네트워크 프로필을 선택 하 고 계속을 선택 합니다.":::

1. **Vsphere 복제 네트워크 프로필 선택**에서 네트워크 프로필 ESXi 호스트의 vsphere 복제 인터페이스를 선택한 다음 **계속**을 선택 합니다.
      
   대부분의 경우 이 프로필은 관리 네트워크 프로필과 동일합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="VSphere 복제 네트워크 프로필 선택에서 네트워크 프로필 ESXi 호스트의 vSphere 복제 인터페이스를 선택한 다음 계속을 선택 합니다.":::

1. **네트워크 확장에 대 한 분산 스위치 선택**에서 통합 되 고 연결 되는 vm이 네트워크에 연결 된 DVS를 선택 합니다.  **계속**을 선택합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="네트워크 확장에 대 한 분산 스위치 선택에서 통합 되 고 연결 되는 Vm이 네트워크에 연결 된 DVS를 선택 합니다.  계속을 선택 합니다.":::

1. 연결 규칙을 검토 하 고 **계속**을 선택 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="연결 규칙을 검토 하 고 계속을 선택 합니다.":::

1.  **마침**을 선택하여 컴퓨팅 프로필을 만듭니다.

## <a name="configure-network-uplink"></a>네트워크 업링크 구성

이제 네트워크 업링크에 대 한 Azure VMware 솔루션 SDDC에서 네트워크 프로필 변경을 구성 합니다.

1. SDDC NSX-T에 로그인 하 여 새 논리 스위치를 만들거나 온-프레미스와 Azure VMware Solution SDDC 간의 네트워크 업링크에 사용할 수 있는 기존 논리 스위치를 사용 합니다.

1. 온-프레미스에서 Azure VMware 솔루션 SDDC 통신에 사용할 수 있는 Azure VMware Solution SDDC에서 HCX 업링크에 대 한 네트워크 프로필을 만듭니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="온-프레미스에서 Azure VMware 솔루션 SDDC 통신에 사용할 수 있는 Azure VMware Solution SDDC에서 HCX 업링크에 대 한 네트워크 프로필을 만듭니다.":::

1. 네트워크 프로필의 이름을 입력하고 필요한 L2 네트워크 확장을 기준으로 4-5개 이상의 사용 가능한 IP 주소를 입력합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="네트워크 프로필의 이름을 입력하고 필요한 L2 네트워크 확장을 기준으로 4-5개 이상의 사용 가능한 IP 주소를 입력합니다.":::

1. **만들기** 를 선택 하 여 Azure VMWARE 솔루션 SDDC 구성을 완료 합니다.

## <a name="configure-service-mesh"></a>서비스 메시 구성

이제 온-프레미스와 Azure VMware 솔루션 SDDC 간에 서비스 메시를 구성 합니다.

1. Azure VMware 솔루션 SDDC vCenter에 로그인 하 고 **Hcx**를 선택 합니다.

2. **인프라**에서 **Interconnect**  >  **service 메시**  >  **Create service 메시** 를 선택 하 여 이전 단계에서 만든 네트워크 및 계산 프로필을 구성 합니다.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="인프라에서 Interconnect > Service 메시를 선택 하 > 서비스 메시를 만들어 이전 단계에서 만든 네트워크 및 계산 프로필을 구성 합니다.":::

3. 페어링된 사이트를 선택 하 여 하이브리드 기능을 사용 하도록 설정 하 고 **계속**을 선택 합니다.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="페어링된 사이트를 선택 하 여 하이브리드 기능을 사용 하도록 설정 하 고 계속을 선택 합니다.":::

4. 원본 및 원격 계산 프로필을 선택 하 여 하이브리드 services를 사용 하도록 설정 하 고 **계속**을 선택 합니다.
      
   선택 항목은 Vm에서 HCX 서비스를 사용할 수 있는 리소스를 정의 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="선택 항목은 Vm에서 HCX 서비스를 사용할 수 있는 리소스를 정의 합니다.":::

5. 사용할 서비스를 선택 하 고 **계속**을 선택 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="사용할 서비스를 선택 하 고 계속을 선택 합니다.":::

6. **고급 구성 - 업링크 네트워크 프로필 재정의**에서 **계속**을 선택합니다.  
      
   업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크에 연결 하는 데 사용 됩니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크에 연결 하는 데 사용 됩니다.":::

7. **네트워크 확장 어플라이언스 Scale Out 구성을**선택 합니다. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="네트워크 확장 어플라이언스 Scale Out 구성을 선택 합니다.":::

8. DVS 스위치 수에 해당하는 어플라이언스 수를 입력합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="DVS 스위치 수에 해당하는 어플라이언스 수를 입력합니다.":::

9. **계속 건너뛰려면 계속** 을 선택 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="계속 건너뛰려면 계속을 선택 합니다.":::

10. 토폴로지 미리 보기를 검토하고 **계속**을 선택합니다. 

11. 이 서비스 메시에 사용할 사용자에 게 친숙 한 이름을 입력 하 고 **마침** 을 선택 하 여 완료 합니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="전체 서비스 메시":::

   서비스 메시가 배포되고 구성됩니다.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="배포된 서비스 메시":::

## <a name="check-appliance-status"></a>어플라이언스 상태 확인
어플라이언스의 상태를 확인하려면 **상호 연결** > **어플라이언스**를 선택합니다. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="어플라이언스의 상태를 확인 합니다.":::

## <a name="next-steps"></a>다음 단계

어플라이언스 상호 연결 **터널 상태가** **UP** 이 고 녹색이 면 hcx를 사용 하 여 Azure VMware 솔루션 vm을 마이그레이션하고 보호할 준비가 된 것입니다. VMware 기술 설명서에서 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html) 및 [VMware HCX로 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)을 참조하세요.
