---
title: Azure Migrate의 Collector 어플라이언스 정보 | Microsoft Docs
description: Azure Migrate의 Collector 어플라이언스에 대한 정보를 제공합니다.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 224511b9748c540f2cd48a3d8393a9c74f76ce32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679291"
---
# <a name="about-the-collector-appliance"></a>Collector 어플라이언스 정보

 이 문서에서는 Azure Migrate Collector에 대한 정보를 제공합니다.

Azure Migrate Collector는 Azure로 마이그레이션하기 전에 [Azure Migrate](migrate-overview.md) 서비스를 통해 평가를 진행하기 위해 온-프레미스 vCenter 환경을 검색하는 데 사용할 수 있는 간편한 어플라이언스입니다.  

## <a name="discovery-method"></a>검색 방법

이전에는 Collector 어플라이언스에 대해 일회성 검색과 연속 검색의 두 가지 옵션이 제공되었습니다. 일회성 검색 모델은 성능 데이터 수집을 위해 vCenter Server 통계 설정에 의존하고(통계 설정을 수준 3으로 설정해야 함), 최대가 아닌 평균 카운터를 수집하여 결과 크기가 작아지도록 하므로 이제 더 이상 사용되지 않습니다. 연속 검색 모델은 세부적인 데이터 수집을 보장하며, 최대 카운터 수집을 통해 정확한 결과 크기를 제공합니다. 작동 방식은 다음과 같습니다.

Collector 어플라이언스는 Azure Migrate 프로젝트에 지속적으로 연결되어 VM 성능 데이터를 지속적으로 수집합니다.

- Collector가 온-프레미스 환경을 지속적으로 프로파일링하여 20초마다 실시간 사용률 데이터를 수집합니다.
- 어플라이언스는 20초 샘플을 롤업하고 15분마다 데이터 요소를 하나씩 만듭니다.
- 데이터 요소를 만들기 위해 어플라이언스는 20초 샘플에서 최고 값을 선택하여 Azure로 전송합니다.
- 이 모델은 성능 데이터를 수집할 때 vCenter Server 통계 설정을 사용하지 않습니다.
- 언제든지 Collector에서 연속 프로파일링을 중지할 수 있습니다.

**빠른 평가:** 지속적인 검색 어플라이언스를 사용하면 검색이 완료되고 난 후(VM의 수에 따라 몇 시간 소요) 즉시 평가를 만들 수 있습니다. 검색을 시작할 때 성능 데이터 수집이 시작되므로 빠른 평가를 원하는 경우 평가의 크기 조정 기준으로 *온-프레미스로*를 선택해야 합니다. 성능 기반 평가의 경우 신뢰할 수 있는 크기 권장 사항을 얻으려면 검색을 시작한 후 하루 이상 대기하는 것이 좋습니다.

애플리케이션은 성능 데이터를 연속적으로 수집할 뿐이며 온-프레미스 환경의 구성 변경(예: VM 추가, 삭제, 디스크 추가 등)은 탐지하지 않습니다. 온-프레미스 환경에서 구성 변경이 있으면 다음을 통해 포털에 변경 내용을 반영할 수 있습니다.

- 항목 추가(VM, 디스크, 코어 등): 이러한 변경을 Azure Portal에 반영하려면 어플라이언스에서 검색을 중지했다가 다시 시작합니다. 이렇게 하면 Azure Migrate 프로젝트에서 변경 내용이 업데이트됩니다.

- VM 삭제: 어플라이언스 설계 방식으로 인해 검색을 중지했다 시작해도 VM 삭제가 반영되지 않습니다. 이후 검색의 데이터는 기존 검색에 추가되는 것이지 기존 검색을 덮어쓰는 것이 아니기 때문입니다. 이 경우에는 그룹에서 VM을 제거하고 평가를 다시 계산하여 포털에서 VM을 간단히 무시하면 됩니다.

> [!NOTE]
> 일회성 검색 어플라이언스는 성능 데이터 지점 가용성에 대한 vCenter Server의 통계 설정에 의존하고 평균 성능 카운터를 수집함에 따라 Azure로 마이그레이션할 VM의 크기가 부족해진다는 이유로 현재는 사용되지 않습니다.

## <a name="deploying-the-collector"></a>Collector 배포

OVF 템플릿을 사용하여 Collector 어플라이언스를 배포할 수 있습니다.

- Azure Portal의 Azure Migrate 프로젝트에서 OVF 템플릿을 다운로드합니다. 다운로드한 파일을 vCenter Server로 가져와서 Collector 어플라이언스 VM을 설정합니다.
- OVF에서 VMware가 코어 8개, 16GB RAM, 80GB 디스크 1개로 VM을 설정합니다. 운영 체제는 Windows Server 2016(64비트)입니다.
- Collector를 실행하면 Collector가 Azure Migrate에 연결할 수 있는지를 확인하기 위해 여러 가지 필수 구성 요소 확인이 실행됩니다.

- Collector를 만드는 방법에 대해 [자세히 알아보세요](tutorial-assessment-vmware.md#create-the-collector-vm).


## <a name="collector-prerequisites"></a>Collector 필수 구성 요소

Collector는 몇 가지 필수 구성 요소 확인을 통과해야 인터넷을 통해 Azure Migrate 서비스에 연결하고 검색된 데이터를 업로드할 수 있습니다.

- **Azure 클라우드 확인**: Collector는 마이그레이션하려는 Azure 클라우드를 알고 있어야 합니다.
    - Azure Government 클라우드로 마이그레이션하려는 경우에는 Azure Government를 선택합니다.
    - 상용 Azure 클라우드로 마이그레이션하려는 경우 Azure 글로벌을 선택합니다.
    - 여기에서 지정한 클라우드를 기준으로, 어플라이언스는 검색된 메타데이터를 해당 엔드포인트로 전송합니다.
- **인터넷 연결 확인**: Collector는 인터넷에 직접 연결하거나 프록시를 통해 연결할 수 있습니다.
    - 필수 구성 요소 확인에서는 [필수 및 선택적 URL](#urls-for-connectivity)에 대한 연결을 확인합니다.
    - 인터넷에 직접 연결하는 경우에는 Collector가 필수 URL에 연결할 수 있는지를 확인하는 것 외에는 별도의 작업을 수행하지 않아도 됩니다.
    - 프록시를 통해 연결하는 경우에는 아래 요구 사항을 참조하세요.
- **시간 동기화 확인**: 서비스에 대한 요청이 인증되었는지 확인하려면 Collector가 인터넷 시간 서버와 동기화 되어야 합니다.
    - 시간 유효성을 검사할 수 있도록 Collector에서 portal.azure.com URL에 연결할 수 있어야 합니다.
    - 컴퓨터가 동기화되지 않은 경우에는 Collector VM의 시계 시간을 현재 시간과 일치하도록 변경해야 합니다. 이렇게 하려면 VM에서 관리자 프롬프트를 열고 **w32tm /tz**를 실행하여 표준 시간대를 확인합니다. 그런 후에 **w32tm /resync**를 실행하여 시간을 동기화합니다.
- **Collector 서비스가 실행 중인지 확인**:  Azure Migrate Collector 서비스를 Collector VM에서 실행 중이어야 합니다.
    - 서비스는 시스템이 부팅될 때 자동으로 시작됩니다.
    - 서비스가 실행되고 있지 않으면 제어판에서 서비스를 시작합니다.
    - Collector 서비스가 vCenter Server에 연결하여 VM 메타데이터 및 성능 데이터를 수집한 다음 Azure Migrate 서비스로 전송합니다.
- **VMware PowerCLI 6.5가 설치되어 있는지 확인**: Collector VM에 VMware PowerCLI 6.5 PowerShell 모듈이 설치되어 있어야 해당 VM이 vCenter Server와 통신할 수 있습니다.
    - Collector가 모듈을 설치하는 데 필요한 URL에 액세스할 수 있으면 Collector 배포 중에 모듈이 자동으로 설치됩니다.
    - 배포 중에 Collector가 모듈을 설치할 수 없는 경우에는 모듈을 수동 설치해야 합니다.
- **vCenter Server에 대한 연결 확인**: Collector는 vCenter Server에 연결하여 VM, 해당 메타데이터 및 성능 카운터를 쿼리할 수 있어야 합니다. 연결을 위한 [필수 구성 요소를 확인](#connect-to-vcenter-server)하세요.


### <a name="connect-to-the-internet-via-a-proxy"></a>프록시를 통해 인터넷에 연결

- 프록시 서버에 인증이 필요한 경우 Collector를 설정할 때 사용자 이름과 암호를 지정할 수 있습니다.
- 프록시 서버의 IP 주소/FQDN으로 지정할 *http:\//IPaddress* 하거나 *http:\//FQDN*합니다.
- HTTP 프록시만 지원됩니다. HTTPS 기반 프록시 서버는 Collector에서 지원되지 않습니다.
- 프록시 서버가 가로채는 프록시인 경우에는 Collector VM으로 프록시 인증서를 가져와야 합니다.
  1. Collector VM에서 **시작 메뉴** > **컴퓨터 인증서 관리**로 이동합니다.
  2. 인증서 도구의 **인증서 - 로컬 컴퓨터** 아래에서 **신뢰할 수 있는 게시자** > **인증서**를 찾습니다.

      ![인증서 도구](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. 프록시 인증서를 Collector VM에 복사합니다. 네트워크 관리자에게 인증서를 받아야 할 수 있습니다.
  4. 인증서를 두 번 클릭하여 연 다음 **인증서 설치**를 클릭합니다.
  5. 인증서 가져오기 마법사 > 저장 위치에서 **로컬 컴퓨터**를 선택합니다.

     ![인증서 저장 위치](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. **모든 인증서를 다음 저장소에 저장** > **찾아보기** > **신뢰할 수 있는 게시자**를 선택하고 **마침**을 클릭하여 인증서를 가져옵니다.

     ![인증서 저장소](./media/concepts-intercepting-proxy/certificate-store.png)

  7. 인증서를 제대로 가져왔는지, 그리고 인터넷 연결 필수 구성 요소 확인이 정상적으로 작동하는지 확인합니다.


### <a name="urls-for-connectivity"></a>연결에 대한 URL

특정 URL 목록에 연결해 보는 방식으로 연결 확인의 유효성을 검사합니다.

**URL** | **세부 정보**  | **필수 구성 요소 확인**
--- | --- | ---
*.portal.azure.com | Azure 글로벌에 적용됩니다. Azure 서비스와의 연결과 시간 동기화를 확인합니다. | URL에 액세스할 수 있어야 합니다.<br/><br/> URL에 연결할 수 없으면 필수 구성 요소 확인은 실패합니다.
*.portal.azure.us | Azure Government에만 적용됩니다. Azure 서비스와의 연결과 시간 동기화를 확인합니다. | URL에 액세스할 수 있어야 합니다.<br/><br/> URL에 연결할 수 없으면 필수 구성 요소 확인은 실패합니다.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| PowerShell vCenter PowerCLI 모듈을 다운로드하는 데 사용됩니다. | Url에 대 한 액세스가 필요 합니다.<br/><br/> 필수 구성 요소 확인은 실패하지 않습니다.<br/><br/> Collector VM에서 자동 모듈 설치가 실패합니다. 인터넷에 연결 된 컴퓨터에 수동으로 모듈을 설치 하 고 다음 기기에 모듈을 복사 해야 합니다. [이 문제 해결 가이드에서 # 4 단계로 이동 하 여 자세한](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception)합니다.


### <a name="install-vmware-powercli-module-manually"></a>수동으로 VMware PowerCLI 모듈 설치

1. [이 단계](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)에 따라 모듈을 설치합니다. 이러한 단계에서는 온라인 및 오프라인 설치 방법을 모두 설명합니다.
2. Collector VM이 오프라인 상태여서 인터넷에 액세스할 수 있는 다른 컴퓨터에 모듈을 설치하려는 경우 해당 컴퓨터에서 Collector VM으로 VMware.* 파일을 복사해야 합니다.
3. 설치 후에는 필수 구성 요소 확인을 다시 시작하여 PowerCLI가 설치되었는지를 확인할 수 있습니다.

### <a name="connect-to-vcenter-server"></a>vCenter Server에 연결

Collector는 vCenter Server에 연결하여 VM 메타데이터 및 성능 카운터를 쿼리합니다. 해당 연결을 위해 필요한 사항은 다음과 같습니다.

- vCenter Server 버전 5.5, 6.0, 6.5 및 6.7만 지원됩니다.
- 검색용으로 아래에 요약되어 있는 권한이 있는 읽기 전용 계정이 필요합니다. 해당 계정을 통해 액세스 가능한 데이터 센터만 검색을 위해 액세스할 수 있습니다.
- 기본적으로는 FQDN 또는 IP 주소를 사용하여 vCenter Server에 연결합니다. vCenter Server가 다른 포트에서 수신 대기하는 경우에는 *IPAddress:Port_Number* 또는 *FQDN:Port_Number* 형식을 사용하여 해당 포트에 연결합니다.
- Collector에는 vCenter Server에 대한 네트워크 시야가 있어야 합니다.

#### <a name="account-permissions"></a>계정 권한

**계정** | **권한**
--- | ---
읽기 전용 사용자 계정(최소) | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용   


## <a name="collector-communications"></a>Collector 통신

Collector는 아래 다이어그램과 표에 요약되어 있는 방식으로 통신을 합니다.

![수집기 통신 다이어그램](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Collector 통신 대상** | **포트** | **세부 정보**
--- | --- | ---
Azure Migrate 서비스 | TCP 443 | Collector는 SSL 443을 통해 Azure Migrate 서비스와 통신합니다.
vCenter Server | TCP 443 | Collector는 vCenter Server와 통신할 수 있어야 합니다.<br/><br/> 기본적으로는 443에서 vCenter에 연결합니다.<br/><br/> vCenter Server가 다른 포트에서 수신 대기하는 경우에는 해당 포트가 Collector에서 송신 포트로 제공되어야 합니다.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>수집된 메타데이터

> [!NOTE]
> 하는 데 적합 한 크기로 응용 프로그램을 Azure로 마이그레이션할 때 수집기 어플라이언스는 Azure Migrate에서 검색 하는 메타 데이터는 Azure 적합성 분석, 응용 프로그램 종속성 분석 및 비용 계획을 수행 합니다. Microsoft는 모든 라이선스 규정 준수 감사와 관련 하 여이 데이터를 사용 하지 않습니다.

Collector 어플라이언스는 각 VM에 대해 다음 구성 메타데이터를 검색합니다. VM의 구성 데이터는 검색을 시작하고 1시간 후에 사용 가능합니다.

- vCenter Server의 VM 표시 이름
- VM의 인벤토리 경로(vCenter Server의 호스트/폴더)
- IP 주소
- MAC 주소
- 운영 체제
- 코어, 디스크, NIC 수
- 메모리 크기, 디스크 크기
- VM, 디스크 및 네트워크의 성능 카운터

### <a name="performance-counters"></a>성능 카운터

 Collector 어플라이언스는 20초 간격으로 ESXi 호스트에서 각 VM에 대해 다음 성능 카운터를 수집합니다. 이러한 카운터는 vCenter 카운터로, 용어 자체는 평균을 의미하지만 20초 샘플은 실시간 카운터입니다. VM에 대한 성능 데이터는 검색을 실행하고 2시간 후에 포털에서 사용 가능해집니다. 정확한 크기 권장 사항을 얻을 수 있도록 성능 기반 평가를 만들기 전에 최소한 하루 이상을 기다리는 것이 좋습니다. 즉각적인 만족을 원할 경우 적절한 크기의 성능 데이터를 고려하지 않는 온-프레미스 방식의 크기 조정 기준으로 평가를 만들 수 있습니다.

**카운터** |  **평가에 미치는 영향**
--- | ---
cpu.usage.average | 권장되는 VM 크기 및 비용  
mem.usage.average | 권장되는 VM 크기 및 비용  
virtualDisk.read.average | 디스크 크기, 저장소 비용 및 VM 크기 계산
virtualDisk.write.average | 디스크 크기, 저장소 비용 및 VM 크기 계산
virtualDisk.numberReadAveraged.average | 디스크 크기, 저장소 비용 및 VM 크기 계산
virtualDisk.numberWriteAveraged.average | 디스크 크기, 저장소 비용 및 VM 크기 계산
net.received.average | VM 크기 계산                          
net.transmitted.average | VM 크기 계산     

Azure Migrate에서 수집되는 VMware 카운터의 전체 목록은 아래와 같습니다.

**범주** |  **Metadata** | **vCenter 데이터 요소**
--- | --- | ---
머신 세부 정보 | VM ID | vm.Config.InstanceUuid
머신 세부 정보 | VM 이름 | vm.Config.Name
머신 세부 정보 | vCenter Server ID | VMwareClient.InstanceUuid
머신 세부 정보 |  VM 설명 |  vm.Summary.Config.Annotation
머신 세부 정보 | 라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
머신 세부 정보 | 운영 체제 유형 | vm.Summary.Config.GuestFullName
머신 세부 정보 | 운영 체제 버전 | vm.Summary.Config.GuestFullName
머신 세부 정보 | 부팅 유형 | vm.Config.Firmware
머신 세부 정보 | 코어 수 | vm.Config.Hardware.NumCPU
머신 세부 정보 | 메가바이트의 메모리 | vm.Config.Hardware.MemoryMB
머신 세부 정보 | 디스크 수 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
머신 세부 정보 | 디스크 크기 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
머신 세부 정보 | 네트워크 어댑터 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
머신 세부 정보 | CPU 사용률 | cpu.usage.average
머신 세부 정보 | 메모리 사용률 | mem.usage.average
디스크 세부 정보(디스크당) | 디스크 키 값 | disk.Key
디스크 세부 정보(디스크당) | 디스크 단위 수 | disk.UnitNumber
디스크 세부 정보(디스크당) | 디스크 컨트롤러 키 값 | disk.ControllerKey.Value
디스크 세부 정보(디스크당) | 프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 세부 정보(디스크당) | 디스크 이름 | 이 값은 disk.UnitNumber, disk.Key 및 disk.ControllerKey.Value를 사용하여 생성됨
디스크 세부 정보(디스크당) | 초당 읽기 작업 수 | virtualDisk.numberReadAveraged.average
디스크 세부 정보(디스크당) | 초당 쓰기 작업 수 | virtualDisk.numberWriteAveraged.average
디스크 세부 정보(디스크당) | 초당 읽기 처리량(메가바이트) | virtualDisk.read.average
디스크 세부 정보(디스크당) | 초당 쓰기 처리량(메가바이트) | virtualDisk.write.average
네트워크 어댑터 세부 정보(NIC당) | 네트워크 어댑터 이름 | nic.Key
네트워크 어댑터 세부 정보(NIC당) | MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
네트워크 어댑터 세부 정보(NIC당) | IPv4 주소 | vm.Guest.Net
네트워크 어댑터 세부 정보(NIC당) | IPv6 주소 | vm.Guest.Net
네트워크 어댑터 세부 정보(NIC당) | 초당 읽기 처리량(메가바이트) | net.received.average
네트워크 어댑터 세부 정보(NIC당) | 초당 쓰기 처리량(메가바이트) | net.transmitted.average
인벤토리 경로 세부 정보 | 이름 | container.GetType().Name
인벤토리 경로 세부 정보 | 자식 개체 유형 | container.ChildType
인벤토리 경로 세부 정보 | 참조 세부 정보 | container.MoRef
인벤토리 경로 세부 정보 | 전체 인벤토리 경로 | 전체 경로가 포함된 container.Name
인벤토리 경로 세부 정보 | 부모 세부 정보 | Container.Parent
인벤토리 경로 세부 정보 | 각 VM에 대한 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
인벤토리 경로 세부 정보 | 각 VM 폴더에 대한 데이터 센터 세부 정보 | ((Datacenter)container).VmFolder
인벤토리 경로 세부 정보 | 각 호스트 폴더에 대한 데이터 센터 세부 정보 | ((Datacenter)container).HostFolder
인벤토리 경로 세부 정보 | 각 호스트에 대한 클러스터 세부 정보 | ((ClusterComputeResource)container).Host)
인벤토리 경로 세부 정보 | 각 VM에 대한 호스트 세부 정보 | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Collector 어플라이언스 보호

다음 단계를 통해 Collector 어플라이언스를 보호하는 것이 좋습니다.

- 관리자 암호를 권한이 없는 당사자와 공유하거나 분실하지 않도록 주의합니다.
- 사용 중이 아니면 어플라이언스를 종료합니다.
- 보안된 네트워크에 어플라이언스를 배치합니다.
- 마이그레이션 완료되면 어플라이언스 인스턴스를 삭제합니다.
- 또한 마이그레이션 후에는 디스크 백업 파일(VMDK)을 삭제합니다. 해당 디스크에는 vCenter 자격 증명이 캐시되어 있을 수도 있기 때문입니다.

## <a name="os-license-in-the-collector-vm"></a>Collector VM의 OS 라이선스

Collector는 180일 동안 유효한 Windows Server 2012 R2 평가 라이선스와 함께 제공됩니다. Collector VM의 평가 기간이 만료되면 새 OVA를 다운로드하여 새 어플라이언스를 만드는 것이 좋습니다.

## <a name="updating-the-os-of-the-collector-vm"></a>Collector VM의 OS 업데이트

Collector 어플라이언스에는 180일 동안 사용 가능한 평가 라이선스가 포함되어 있지만 어플라이언스가 자동으로 종료되지 않도록 어플라이언스의 OS를 지속적으로 업데이트해야 합니다.

- Collector를 60일 동안 업데이트하지 않으면 컴퓨터 자동 종료가 시작됩니다.
- 검색을 실행 중인 경우에는 60일이 지났더라도 컴퓨터가 꺼지지 않으며 검색이 완료된 후 컴퓨터가 꺼집니다.
- Collector를 사용한 지 60일이 지났다면 Windows 업데이트를 실행하여 컴퓨터를 항상 업데이트된 상태로 유지하는 것이 좋습니다.

## <a name="upgrading-the-collector-appliance-version"></a>Collector 어플라이언스 버전 업그레이드

OVA를 다시 다운로드하지 않고도 Collector를 최신 버전으로 업그레이드할 수 있습니다.

1. [목록의 최신 업그레이드 패키지](concepts-collector-upgrade.md)를 다운로드합니다.
2. 다운로드한 핫픽스가 안전한지 확인하려면 관리자 권한 명령 창을 열고 다음 명령을 실행하여 Zip 파일에 대한 해시를 생성합니다. 생성된 해시는 특정 버전에 대해 언급된 해시와 일치해야 합니다.

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (예제 사용량 C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Zip 파일을 Azure Migrate 수집기 가상 머신(수집기 어플라이언스)에 복사합니다.
4. Zip 파일을 마우스 오른쪽 단추로 클릭하고 모두 압축 풀기를 선택합니다.
5. Setup.ps1 단추를 마우스 오른쪽 단추로 클릭하고 PowerShell과 함께 실행을 선택하고 화면의 지시를 따라 업데이트를 설치합니다.

## <a name="discovery-process"></a>검색 프로세스

어플라이언스를 설정한 후에는 검색을 실행할 수 있습니다. 검색 작동 방식은 다음과 같습니다.

- 범위를 기준으로 검색을 실행합니다. 지정된 vCenter 인벤토리 경로에 있는 모든 VM이 검색됩니다.
    - 범위는 한 번에 하나씩 설정합니다.
    - 범위에는 VM을 1,500개까지 포함할 수 있습니다.
    - 범위는 데이터 센터, 폴더 또는 ESXi 호스트일 수 있습니다.
- vCenter Server에 연결한 후에는 수집용 마이그레이션 프로젝트를 지정하여 연결합니다.
- VM이 검색되며 해당 메타데이터와 성능 데이터가 Azure로 전송됩니다. 이러한 작업은 수집 작업의 일부분입니다.
    - Collector 어플라이언스에는 모든 검색에서 특정 컴퓨터에 대해 동일하게 유지되는 특정 Collector ID가 지정됩니다.
    - 실행 중인 수집 작업에는 특정 세션 ID가 지정됩니다. ID는 각 수집 작업마다 변경되며 문제 해결 시에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[온-프레미스 VMware VM에 대한 평가 설정](tutorial-assessment-vmware.md)
