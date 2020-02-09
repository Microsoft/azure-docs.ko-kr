---
title: 구성 및 관리 문제 FAQ
titleSuffix: Azure Cloud Services
description: 이 문서는 Microsoft Azure Cloud Services의 구성 및 관리에 대한 질문과 대답을 나열합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 4ca8c18d448297cc2d2e2249a1dbbe04ecccd092
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019420"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services의 구성 및 관리 문제: FAQ(질문과 대답)

이 문서는 [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 구성 및 관리 문제에 대한 질문과 대답을 포함합니다. 크기 정보는 [Cloud Services VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**인증서**

- [내 클라우드 서비스 SSL 인증서의 인증서 체인이 완료되지 않은 이유는 무엇인가요?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- ["확장을 위한 Windows Azure Tools 암호화 인증서"의 목적은 무엇인가요?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- ["RDP"하지 않고 인스턴스에서 CSR(인증서 서명 요청)을 생성하려면 어떻게 할까요?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [내 클라우드 서비스 관리 인증서가 만료 됩니다. 갱신 하는 방법](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [기본 SSL 인증서(.pfx) 및 중간 인증서(.p7b) 설치를 자동화하려면 어떻게 해야 하나요?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- ["MachineKey용 Microsoft Azure 서비스 관리" 인증서의 목적은 무엇인가요?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**모니터링 및 로깅**

- [애플리케이션 관리 및 모니터링에 도움이 될 수 있는 Azure Portal의 예정된 클라우드 서비스 기능은 무엇인가요?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [IIS에서 로그 디렉터리에 작성을 중지하는 이유는 무엇인가요?](#why-does-iis-stop-writing-to-the-log-directory)
- [Cloud Services에 대해 WAD 로깅을 활성화하려면 어떻게 할까요?](#how-do-i-enable-wad-logging-for-cloud-services)

**네트워크 구성**

- [Azure Load Balancer에 유휴 시간 제한을 설정하려면 어떻게 할까요?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [클라우드 서비스에 고정 IP 주소를 연결하려면 어떻게 할까요?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Azure 기본 IPS/IDS와 DDoS에서 제공하는 기능이란?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Cloud Services VM에서 HTTP/2를 사용하려면 어떻게 해야 하나요?](#how-to-enable-http2-on-cloud-services-vm)

**권한**

- [Microsoft 내부 엔지니어는 권한이 없어도 클라우드 서비스 인스턴스에 RDP가 가능한가요?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [RDP 파일을 사용 하 여 클라우드 서비스 VM에 대한 원격 데스크톱을 만들 수 없습니다. 다음 오류가 발생 했습니다. 인증 오류가 발생 했습니다 (코드: 0x80004005).](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**크기 조정**

- [X 인스턴스 이상 확장할 수 없습니다.](#i-cannot-scale-beyond-x-instances)
- [메모리 메트릭을 기반으로 자동 크기 조정을 구성하려면 어떻게 해야 하나요?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**일반**

- ["nosniff"를 내 웹 사이트에 추가하려면 어떻게 할까요?](#how-do-i-add-nosniff-to-my-website)
- [웹 역할에 IIS를 사용자 지정하려면 어떻게 할까요?](#how-do-i-customize-iis-for-a-web-role)
- [클라우드 서비스에 대한 할당량 제한은 무엇인가요?](#what-is-the-quota-limit-for-my-cloud-service)
- [클라우드 서비스 VM의 드라이브에 사용 가능한 디스크 공간이 거의 표시되지 않는 이유는 무엇인가요?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [내 Cloud Services에 대한 맬웨어 방지 확장을 자동화된 방식으로 추가하려면 어떻게 해야 하나요?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Cloud Services에 대한 SNI(서버 이름 표시)를 사용하도록 설정하려면 어떻게 해야 하나요?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [내 Azure Cloud Service에 태그를 추가하려면 어떻게 해야 하나요?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure Portal에는 클라우드 서비스의 SDK 버전이 표시 되지 않습니다. 어떻게 받을 수 있나요?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [클라우드 서비스를 몇 달 동안 종료 하려고 합니다. IP 주소를 잃지 않고 클라우드 서비스의 청구 비용을 줄이는 방법](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>인증서

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>내 클라우드 서비스 SSL 인증서의 인증서 체인이 완료되지 않은 이유는 무엇인가요?
    
고객은 리프 인증서가 아닌 전체 인증서 체인(리프 인증서, 중간 인증서 및 루트 인증서)을 설치하는 것이 좋습니다. 방금 리프 인증서를 설치한 경우 Windows CTL을 탐색하여 인증서 체인을 빌드하기 위해 Windows를 사용합니다. Windows가 인증서의 유효성을 검사할 때 Azure 또는 Windows 업데이트에서 일시적인 네트워크 또는 DNS 문제가 발생하는 경우 인증서는 잘못된 것으로 간주될 수 있습니다. 전체 인증서 체인을 설치하여 이 문제를 방지할 수 있습니다. [체인된 SSL 인증서를 설치하는 방법](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/)의 블로그는 이 작업을 수행하는 방법을 보여줍니다.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>"확장을 위한 Windows Azure Tools 암호화 인증서"의 목적은 무엇인가요?

이러한 인증서는 클라우드 서비스에 확장명을 추가할 때마다 자동으로 생성됩니다. 가장 일반적으로 WAD 확장 또는 RDP 확장이지만 맬웨어 방지 또는 로그 수집기 확장과 같은 다른 항목일 수도 있습니다. 이러한 인증서는 확장을 위해 프라이빗 구성을 암호화하고 암호 해독하는 경우에만 사용됩니다. 만료 날짜를 확인하지 않으므로 인증서가 만료되는지는 중요하지 않습니다. 

이러한 인증서를 무시할 수 있습니다. 인증서를 정리하려면 모두 삭제할 수 있습니다. Azure에서 사용 중인 인증서를 삭제하려면 오류가 throw됩니다.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>"RDP"하지 않고 인스턴스에서 CSR(인증서 서명 요청)을 생성하려면 어떻게 할까요?

다음 지침 문서를 참조하세요.

[WAWS(Windows Azure 웹 사이트)를 사용하기 위해 인증서 가져오기](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR은 텍스트 파일일 뿐입니다. 인증서를 궁극적으로 사용하는 컴퓨터에서 만들어서는 안됩니다. 이 문서는 App Service을 위해 작성 되었지만 CSR 만들기는 일반적 이며 Cloud Services에도 적용 됩니다.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>내 클라우드 서비스 관리 인증서가 곧 만료됩니다. 갱신하려면 어떻게 해야 하나요?

다음 PowerShell 명령을 사용하여 관리 인증서를 갱신할 수 있습니다.

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile**은 Azure Portal의 **구독** > **관리 인증서**에 새 관리 인증서를 만듭니다. 새 인증서의 이름은 "구독이름]-[현재날짜]-자격증명" 형식입니다.

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>기본 SSL 인증서(.pfx) 및 중간 인증서(.p7b) 설치를 자동화하려면 어떻게 해야 하나요?

시작 스크립트(일괄 처리/cmd/PowerShell)를 사용하여 이 작업을 자동화하고 서비스 정의 파일에 해당 시작 스크립트를 등록할 수 있습니다. 시작 스크립트의 동일한 디렉터리에 있는 프로젝트 폴더에 시작 스크립트와 인증서(.p7b 파일)를 모두 추가합니다.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>"MachineKey용 Microsoft Azure 서비스 관리" 인증서의 목적은 무엇인가요?

이 인증서는 Azure Web Roles에서 컴퓨터 키를 암호화하기 위해 사용됩니다. 자세히 알아보려면 [이 권고](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731)를 확인 하세요.

자세한 내용은 다음 문서를 참조하세요.
- [클라우드 서비스에 대한 시작 작업 구성 및 실행 방법](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [일반적인 클라우드 서비스 시작 작업](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>모니터링 및 로깅

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>애플리케이션 관리 및 모니터링에 도움이 될 수 있는 Azure Portal의 예정된 클라우드 서비스 기능은 무엇인가요?

RDP(Remote Desktop Protocol)에 대한 새 인증서를 생성하는 기능이 곧 제공됩니다. 또는 다음 스크립트를 실행할 수 있습니다.

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Csdef 및 cscfg 업로드 위치에 대해 Blob나 로컬을 선택할 수 있는 기능이 곧 제공됩니다. [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)를 사용하여 각 위치 값을 설정할 수 있습니다.

인스턴스 수준에서 메트릭을 모니터링할 수 있습니다. 추가 모니터링 기능은 [클라우드 서비스를 모니터링하는 방법](cloud-services-how-to-monitor.md)에서 제공합니다.

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS에서 로그 디렉터리에 작성을 중지하는 이유는 무엇인가요?
로그 디렉터리에 작성할 로컬 스토리지 할당량을 모두 사용했습니다. 이 문제를 해결 하기 위해 다음 세 가지 중 하나를 수행할 수 있습니다.
* IIS에 진단을 사용하도록 설정하고 진단을 주기적으로 Blob Storage에 이동합니다.
* 로깅 디렉터리에서 로그 파일을 수동으로 제거합니다.
* 로컬 리소스에 대한 할당량 제한을 늘립니다.

자세한 내용은 다음 문서를 참조하세요.
* [Azure Storage에서 진단 데이터 저장 및 보기](cloud-services-dotnet-diagnostics-storage.md)
* [클라우드 서비스에서 IIS 로그 작성 중지](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Cloud Services에 대해 WAD 로깅을 활성화하려면 어떻게 할까요?
다음 옵션을 통해 WAD(Windows Azure Diagnostics) 로깅을 활성화할 수 있습니다.
1. [Visual Studio에서 활성화](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [.NET 코드를 통해 사용](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Powershell을 통해 활성화](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Cloud Services의 현재 WAD 설정을 가져오기 위해 [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd를 사용하거나 "Cloud Services --> 확장" 블레이드에서 포털을 통해 볼 수 있습니다.


## <a name="network-configuration"></a>네트워크 구성

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer에 유휴 시간 제한을 설정하려면 어떻게 할까요?
다음과 같이 서비스 정의(csdef) 파일에서 시간 제한을 지정할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
자세한 정보는 [새로 만들기: Azure Load Balancer에 구성 가능한 유휴 시간 제한](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)을 참조하세요.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>클라우드 서비스에 고정 IP 주소를 연결하려면 어떻게 할까요?
고정 IP 주소를 설정하려면 예약된 IP 만들어야 합니다. 이 예약된 IP는 새 클라우드 서비스 또는 기존 배포에 연결할 수 있습니다. 자세한 내용은 다음과 같은 문서를 참조하세요.
* [예약된 IP 주소를 만드는 방법](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [기존 클라우드 서비스의 IP 주소 예약](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [예약된 IP를 새 클라우드 서비스에 연결](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [실행 중인 배포에 예약된 IP 연결](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [서비스 구성 파일을 사용하여 클라우드 서비스에 예약된 IP 연결](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure 기본 IPS/IDS와 DDoS에서 제공하는 기능이란?
Azure에는 위협으로부터 보호하기 위해 데이터 센터 실제 서버에 있는 IP/ID가 있습니다. 또한 고객은 웹 애플리케이션 방화벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템(IDS/IPS) 등과 같은 타사 보안 솔루션을 배포할 수 있습니다. 자세한 내용은 [데이터 및 자산 보호 및 글로벌 보안 표준 준수](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)를 참조하세요.

Microsoft에서는 서버, 네트워크 및 애플리케이션을 지속적으로 모니터링하여 위협을 감지합니다. Azure의 다면적 위협 관리 접근 방식은 침입 감지, DDoS(Distributed Denial-of-Service) 공격 방지, 침투 테스트, 동작 분석, 이상 감지 및 Machine Learning을 사용하여 방어를 지속적으로 강화하고 위험을 줄입니다. Azure용 Microsoft 맬웨어 방지 프로그램은 Azure Cloud Services 및 가상 머신을 보호합니다. 웹 애플리케이션 방화벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템(IDS/IPS) 등과 같은 타사 보안 솔루션을 배포하는 옵션이 있습니다.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Cloud Services VM에서 HTTP/2를 사용하려면 어떻게 해야 하나요?

Windows 10 및 Windows Server 2016은 클라이언트와 서버 쪽 모두에서 HTTP/2에 대한 지원이 함께 제공됩니다. TLS 확장을 통해 HTTP/2를 협상하는 TLS로 클라이언트(브라우저)가 IIS 서버에 연결되어 있는 경우 서버 쪽에서 변경을 수행할 필요가 없습니다. TLS를 통해 HTTP/2의 사용을 지정하는 h2-14 헤더가 기본적으로 전송되기 때문입니다. 반면에 HTTP/2로 업그레이드하도록 클라이언트를 업그레이드 헤더로 전송 중인 경우 서버 쪽에서 아래 변경을 수행하여 업그레이드 작업이 HTTP/2 연결을 끝냈는지 확인해야 합니다. 

1. regedit.exe를 실행합니다.
2. 레지스트리 키(HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters)를 찾습니다.
3. **DuoEnabled**라는 새 DWORD 값을 만듭니다.
4. 해당 값을 1로 설정합니다.
5. 서버를 다시 시작합니다.
6. **기본 웹 사이트**로 이동하여 **바인딩**에서 방금 만든 자체 서명된 인증서와 새 TLS 바인딩을 만듭니다. 

자세한 내용은 다음을 참조하세요.

- [IIS에서 HTTP/2](https://blogs.iis.net/davidso/http2)
- [동영상: Windows 10에서 HTTP/2: 브라우저, 앱 및 웹 서버](https://channel9.msdn.com/Events/Build/2015/3-88)
         

이러한 단계는 시작 작업을 통해 자동화할 수 있으므로 새로운 PaaS 인스턴스가 만들어질 때마다 시스템 레지스트리에 위의 변경 내용을 수행할 수 있습니다. 자세한 내용은 [클라우드 서비스에 대한 시작 작업 구성 및 실행 방법](cloud-services-startup-tasks.md)을 참조하세요.

 
이 작업이 끝난 후에 다음 방법 중 하나를 사용하여 HTTP/2가 사용하도록 설정되어 있는지 여부를 확인할 수 있습니다.

- IIS 로그의 프로토콜 버전을 사용하도록 설정하고 IIS 로그를 확인합니다. 로그에 HTTP/2가 표시됩니다. 
- Internet Explorer 또는 Microsoft Edge에서 F12 개발자 도구를 사용 하도록 설정 하 고 네트워크 탭으로 전환 하 여 프로토콜을 확인 합니다. 

자세한 내용은 [IIS에서 HTTP/2](https://blogs.iis.net/davidso/http2)를 참조하세요.

## <a name="permissions"></a>권한

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Cloud Services에 역할 기반 액세스를 구현하려면 어떻게 할까요?
Cloud Services는 Azure Resource Manager 기반 서비스가 아니므로 RBAC(역할 기반 액세스 제어) 모델을 지원하지 않습니다.

[Azure의 다양한 역할 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)를 참조하세요.

## <a name="remote-desktop"></a>원격 데스크톱

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Microsoft 내부 엔지니어는 권한이 없어도 클라우드 서비스 인스턴스에 RDP가 가능한가요?
Microsoft에서는 소유자 또는 지정된 사용자의 서면 승인(전자 메일 또는 기타 서면 통신) 없이는 내부 엔지니어가 클라우드 서비스에 RDP하는 것을 허용하지 않는 엄격한 프로세스를 따릅니다.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>RDP 파일을 사용하여 클라우드 서비스 VM에 RDP할 수 없습니다. 인증 오류(코드: 0x80004005)가 발생합니다.

Azure Active Directory에 조인된 컴퓨터에서 RDP 파일을 사용하는 경우 이 오류가 발생할 수 있습니다. 이 문제를 해결하려면 다음 단계를 따릅니다.

1. 다운로드한 RDP 파일을 마우스 오른쪽 단추로 클릭한 다음 **편집**을 선택합니다.
2. 사용자 이름 앞에 "&#92;"을 접두사로 추가합니다. 예를 들어 **username** 대신 **.\username**을 사용합니다.

## <a name="scaling"></a>크기 조정

### <a name="i-cannot-scale-beyond-x-instances"></a>X 인스턴스 이상 확장할 수 없습니다.
사용자의 Azure 구독은 사용할 수 있는 코어 수를 제한합니다. 사용 가능한 모든 코어를 사용하는 경우 크기 조정은 작동하지 않습니다. 예를 들어 100개의 코어 제한이 있으면 클라우드 서비스에 대한 100개의 A1 크기 가상 머신 인스턴스나 50개의 A2 크기 가상 머신 인스턴스를 생성할 수 있습니다.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>메모리 메트릭을 기반으로 자동 크기 조정을 구성하려면 어떻게 해야 하나요?

Cloud Services를 위한 메모리 메트릭 기반의 자동 크기 조정은 현재 지원되지 않습니다. 

이 문제를 해결하려면 Application Insights를 사용하세요. 자동 크기 조정은 Application Insights를 메트릭 소스로 지원하며 "메모리" 같은 게스트 메트릭에 따라 역할 인스턴스 수를 확장할 수 있습니다.  클라우드 서비스 프로젝트 패키지 파일(*.cspkg)에서 Application Insights를 구성하고 서비스에서 Azure Diagnostics 확장을 사용하도록 설정하여 이 기능을 구현해야 합니다.

Application Insights를 통해 사용자 지정 메트릭을 사용하여 Cloud Services에서 자동 크기 조정을 구성하는 방법에 대한 자세한 내용은 [Azure에서 사용자 지정 메트릭을 기준으로 자동 크기 조정 시작](../azure-monitor/platform/autoscale-custom-metric.md)을 참조하세요.

Azure Diagnostics를 Cloud Services용 Application Insights와 통합하는 방법에 대한 자세한 내용은 [Application Insights에 클라우드 서비스, Virtual Machine 또는 Service Fabric 데이터 보내기](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)를 참조하세요.

Cloud Services용 Application Insights를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Cloud Services용 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)를 참조하세요.

Cloud Services용 Azure Diagnostics 로깅을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Cloud Services 및 가상 머신에 대한 진단 설정](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)을 참조하세요.

## <a name="generic"></a>일반

### <a name="how-do-i-add-nosniff-to-my-website"></a>"nosniff"를 내 웹 사이트에 추가하려면 어떻게 할까요?
클라이언트가 MIME 형식을 스니핑하지 않도록 하려면 *web.config* 파일에 설정을 추가합니다.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

IIS에서 설정으로도 추가할 수 있습니다. [일반적인 시작 작업](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) 문서의 다음 명령을 사용합니다.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>웹 역할에 IIS를 사용자 지정하려면 어떻게 할까요?
[일반적인 시작 작업](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) 문서의 IIS 시작 스크립트를 사용합니다.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>클라우드 서비스에 대한 할당량 제한은 무엇인가요?
[서비스 특정 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)을 참조하세요.

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>클라우드 서비스 VM의 드라이브에 사용 가능한 디스크 공간이 거의 표시되지 않는 이유는 무엇인가요?
이 동작은 정상적이며 애플리케이션에 문제가 발생하지 않아야 합니다. 저널은 Azure PaaS VM에서 %approot% 드라이브에 설정되며 이 기능은 기본적으로 파일이 차지하는 공간의 두 배를 사용합니다. 그러나 이를 사소하게 만드는 몇 가지 사항이 있습니다.

% Approot% 드라이브 크기는 \<크기인 .cspkg + max journal size + > 사용 가능한 공간의 여백 또는 1.5 GB 중 더 큰 값으로 계산 됩니다. VM의 크기는 이 계산과 관련이 없습니다. (VM 크기는 임시 C: 드라이브의 크기에 영향을 줍니다.) 

%approot% 드라이브에 작성하도록 지원되지 않습니다. Azure VM에 작성하는 경우 임시 LocalStorage 리소스에서 수행해야 합니다(또는 Blob Storage, Azure Files 등과 같은 다른 옵션). 따라서 %approot% 폴더에서 사용 가능한 공간의 크기는 의미가 없습니다. 애플리케이션을 %approot% 드라이브에 작성하는지 확실하지 않은 경우 몇 일 동안 서비스를 실행한 다음, "이전" 및 "이후" 크기를 비교할 수 있습니다. 

Azure에서는 %approot% 드라이브에 아무 것도 작성하지 않습니다. .cspkg에서 VHD가 생성되고 Azure VM에 탑재되면 이 드라이브에 작성될 수 있는 유일한 항목은 사용자의 애플리케이션입니다. 

저널 설정을 구성할 수 없으므로 끌 수 없습니다.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>내 Cloud Services에 대한 맬웨어 방지 확장을 자동화된 방식으로 추가하려면 어떻게 해야 하나요?

시작 작업에서 PowerShell 스크립트를 사용하여 맬웨어 방지 확장을 사용하도록 설정할 수 있습니다. 다음 문서에 나온 단계를 따라 구현합니다. 
 
- [PowerShell 시작 작업 만들기](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

맬웨어 방지 배포 시나리오 및 포털에서 활성화하는 방법에 대한 자세한 내용은 [맬웨어 방지 배포 시나리오](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)를 참조하세요.

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Cloud Services에 대한 SNI(서버 이름 표시)를 사용하도록 설정하려면 어떻게 해야 하나요?

다음 방법 중 하나를 사용하여 Cloud Services에서 SNI를 사용하도록 설정할 수 있습니다.

**방법 1: PowerShell 사용**

아래와 같이 클라우드 서비스 역할 인스턴스에 대한 시작 작업에서 PowerShell cmdlet **New-WebBinding**을 사용하여 SNI 바인딩을 구성할 수 있습니다.
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
[여기](https://technet.microsoft.com/library/ee790567.aspx)에 설명된 대로 $sslFlags는 다음과 같은 값 중 하나일 수 있습니다.

|값|의미|
------|------
|0|SNI 없음|
|1|SNI 사용|
|2|중앙 인증서 저장소를 사용하는 비 SNI 바인딩|
|3|중앙 인증서 저장소를 사용하는 SNI 바인딩|
 
**방법 2: 코드 사용**

이 [블로그 게시물](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)에 설명된 대로 역할 시작의 코드를 통해 SNI 바인딩을 구성할 수 있습니다.

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
위의 방법 중 하나를 사용하여, SNI 바인딩이 적용되기 위해서는 시작 작업을 사용하거나 코드를 통해 특정 호스트 이름에 대한 해당 인증서(*.pfx)를 역할 인스턴스에 먼저 설치해야 합니다.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>내 Azure Cloud Service에 태그를 추가하려면 어떻게 해야 하나요? 

Cloud Service는 클래식 리소스입니다. Azure Resource Manager를 통해 만든 리소스만 태그를 지원합니다. 클래식 서비스와 같은 클래식 리소스에는 태그를 적용할 수 없습니다. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure Portal에 내 클라우드 서비스의 SDK 버전이 표시되지 않습니다. 표시하려면 어떻게 해야 하나요?

Azure Portal에서 이 기능을 사용할 수 있도록 현재 작업을 진행 중입니다. 현재는 PowerShell 명령을 사용하여 SDK 버전을 확인할 수 있습니다.

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>클라우드 서비스를 몇 달 동안 종료하려고 합니다. IP 주소를 잃지 않고 클라우드 서비스 비용을 절감하려면 어떻게 해야 하나요?

이미 클라우드 서비스가 배포된 경우 Compute 및 Storage 사용량에 따라 대금이 청구됩니다. 따라서 Azure VM을 종료하더라도 Storage 요금이 계속 청구됩니다. 

다음과 같은 방법으로 서비스의 IP 주소를 잃지 않고 요금을 줄일 수 있습니다.

1. 배포된 서비스를 삭제하기 전에 [IP 주소를 예약](../virtual-network/virtual-networks-reserved-public-ip.md)합니다.  이 IP 주소에 대한 요금만 청구됩니다. IP 주소 요금 청구에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요.
2. 배포된 서비스를 삭제합니다. xxx.cloudapp.net은 나중에 사용할 수 있도록 삭제하지 마세요.
3. 구독에서 예약한 것과 동일한 예약 IP를 사용하여 클라우드 서비스를 다시 배포하려는 경우 [Cloud Services 및 Virtual Machines에 대해 예약된 IP 주소](https://azure.microsoft.com/blog/reserved-ip-addresses/)를 참조하세요.

