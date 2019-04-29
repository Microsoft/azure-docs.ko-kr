---
title: Azure 파일 동기화 온-프레미스 방화벽 및 프록시 설정 | Microsoft Docs
description: Azure 파일 동기화 온-프레미스 네트워크 구성
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d9b7296a116ebd06542a53087afbd083dbd3a7eb
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766869"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 파일 동기화 프록시 및 방화벽 설정
Azure 파일 동기화는 온-프레미스 서버를 Azure Files에 연결하여, 다중 사이트 동기화 및 클라우드 계층화 기능을 사용하도록 설정합니다. 따라서 온-프레미스 서버가 인터넷에 연결되어야 합니다. IT 관리자는 서버가 Azure 클라우드 서비스에 연결하는 최상의 경로를 결정해야 합니다.

이 문서에서는 서버를 Azure 파일 동기화에 성공적이면서 안전하게 연결하기 위해 사용할 수 있는 특정 요구 사항 및 옵션에 대해 설명합니다.

> [!Important]
> Azure 파일 동기화에서는 저장소 계정에 대한 방화벽 및 가상 네트워크를 아직 지원하지 않습니다.

## <a name="overview"></a>개요
Azure 파일 동기화는 Windows Server, Azure 파일 공유 및 일부 기타 Azure 서비스 간에 오케스트레이션 서비스로 작동하여, 동기화 그룹에 설명된 대로 데이터를 동기화합니다. Azure 파일 동기화가 제대로 작동하려면 다음 Azure 서비스와 통신하도록 서버를 구성해야 합니다.

- Azure Storage
- Azure 파일 동기화
- Azure 리소스 관리자
- 인증 서비스

> [!Note]  
> Windows Server의 Azure 파일 동기화 에이전트는 클라우드 서비스에 대한 모든 요청을 시작하므로, 방화벽 관점에서 아웃바운드 트래픽만 고려하면 됩니다. <br /> Azure 파일 동기화 에이전트와 연결을 시작하는 Azure 서비스는 없습니다.

## <a name="ports"></a>포트
Azure 파일 동기화는 파일 데이터 및 메타데이터를 HTTPS를 통해서만 이동하며 아웃바운드로 포트 443이 열려 있도록 요구합니다.
따라서 모든 트래픽이 암호화됩니다.

## <a name="networks-and-special-connections-to-azure"></a>네트워크 및 Azure에 대한 특수 연결
Azure 파일 동기화 에이전트는 Azure에 대해 [ExpressRoute](../../expressroute/expressroute-introduction.md) 등과 같은 특수한 채널을 요구하지 않습니다.

Azure 파일 동기화는 Azure로 연결될 수 있는 모든 방식에 작동하며, 대역폭, 대기 시간과 같은 다양한 네트워크 특성에 맞게 자동으로 조정되고 미세 조정을 위한 관리 제어 기능을 제공합니다. 현재는 일부 기능을 사용할 수 없습니다. 특정 동작을 구성하려는 경우 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670)를 통해 알려주세요.

## <a name="proxy"></a>Proxy
Azure 파일 동기화는 앱별 및 머신 차원의 프록시 설정을 지원합니다.

**앱별 프록시 설정**을 통해 Azure 파일 동기화 트래픽에 대한 프록시를 적절하게 구성할 수 있습니다. 앱별 프록시 설정은 에이전트 버전 4.0.1.0 이상에서 지원되며 에이전트 설치 중 또는 Set-StorageSyncProxyConfiguration PowerShell cmdlet을 사용하여 구성될 수 있습니다.

앱별 프록시 설정을 구성하기 위한 PowerShell 명령입니다.
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
서버의 전체 트래픽이 프록시를 통해 라우팅되므로 **머신 수준 프록시 설정**은 Azure 파일 동기화 에이전트에 투명합니다.

머신 수준 프록시 설정을 구성하려면 다음 단계를 수행합니다. 

1. .NET 애플리케이션에 대한 프록시 설정을 구성합니다. 

   - 다음 두 파일을 편집합니다.  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - <system.net> 섹션을 machine.config 파일(<system.serviceModel> 섹션 아래)에 추가합니다.  127.0.01:8888을 프록시 서버의 IP 주소와 포트로 변경합니다. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. WinHTTP 프록시 설정을 지정합니다. 

   - 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 기존 프록시 설정을 확인합니다.   

     netsh winhttp show proxy

   - 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 프록시 설정을 지정합니다(127.0.01:8888을 프록시 서버의 IP 주소와 포트로 변경).  

     netsh winhttp set proxy 127.0.0.1:8888

3. 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 Storage 동기화 에이전트 서비스를 다시 시작합니다. 

      net stop filesyncsvc

      참고: Storage 동기화 에이전트(filesyncsvc) 서비스는 중지되면 자동으로 시작됩니다.

## <a name="firewall"></a>방화벽
이전 섹션에서 설명한 것처럼 포트 443을 아웃바운드로 열어 두어야 합니다. 데이터 센터, 분기 또는 지역의 정책에 따라, 이 포트를 통한 트래픽을 특정 도메인으로 추가로 제한하는 것이 바람직하거나 필요할 수 있습니다.

다음 표에서는 통신에 필요한 도메인에 대해 설명합니다.

| 서비스 | 공용 클라우드 끝점 | Azure Government 엔드포인트 | 사용 현황 |
|---------|----------------|---------------|------------------------------|
| **Azure 리소스 관리자** | https://management.azure.com | https://management.usgovcloudapi.net | 초기 서버 등록 호출을 포함하는 모든 사용자 호출(예: PowerShell)은 이 URL로 이동되거나 이 URL을 통해 이동됩니다. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Azure Resource Manager 호출은 인증된 사용자가 수행해야 합니다. 성공하기 위해 이 URL이 사용자 인증에 사용됩니다. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Azure 파일 동기화 배포의 일부로, 구독의 Azure Active Directory에 서비스 주체가 만들어집니다. 이 URL이 해당 작업에 사용됩니다. 이 보안 주체는 Azure 파일 동기화 서비스에 대한 최소한의 권한 집합을 위임하는 데 사용됩니다. Azure 파일 동기화의 초기 설정을 수행하는 사용자는 구독 소유자 권한이 있는 인증된 사용자여야 합니다. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | 서버는 파일을 다운로드할 때 저장소 계정의 Azure 파일 공유와 직접 소통하면서 데이터 이동을 보다 효율적으로 수행합니다. 서버에는 대상으로 지정된 파일 공유 액세스만 허용하는 SAS 키가 있습니다. |
| **Azure 파일 동기화** | &ast;.one.microsoft.com | &ast;.afs.azure.us | 초기 서버 등록 후 서버는 해당 지역에서 Azure 파일 동기화 서비스 인스턴스에 대한 지역별 URL을 수신합니다. 서버는 이 URL을 사용하여 동기화를 처리하는 인스턴스와 직접 효율적으로 통신할 수 있습니다. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Azure 파일 동기화 에이전트가 설치되면 PKI URL을 사용하여 Azure 파일 동기화 서비스 및 Azure 파일 공유와 통신하는 데 필요한 중간 인증서를 다운로드합니다. OCSP URL은 인증서의 상태를 확인하는 데 사용됩니다. |

> [!Important]
> &ast;.one.microsoft.com에 대한 트래픽을 허용할 때 서버에서 동기화 서비스 이외의 위치로 트래픽이 전달될 수 있습니다. 하위 도메인에서 더 많은 Microsoft 서비스를 사용할 수 있습니다.

&ast;.one.microsoft.com이 너무 광범위한 경우 Azure File Sync 인스턴스의 명시적인 지역별 인스턴스로의 통신만 허용하여 서버 통신을 제한할 수 있습니다. 선택할 인스턴스는 서버를 배포 및 등록한 저장소 동기화 서비스의 지역에 따라 다릅니다. 이 지역을 아래 표에서는 "기본 엔드포인트 URL"이라고 부릅니다.

BCDR(비즈니스 연속성 및 재해 복구)을 위해 GRS(지역 중복 저장소) 저장소 계정에서 Azure 파일 공유를 지정했을 수도 있습니다. 이 경우 Azure 파일 공유는 지속적인 지역 정전 시 쌍을 이루는 지역에 장애 조치(failover)됩니다. Azure 파일 동기화는 동일한 지역 쌍을 저장소로 사용합니다. 따라서 GRS 저장소 계정을 사용하는 경우 서버가 Azure 파일 동기화의 쌍을 이루는 지역과 통신할 수 있도록 추가 URL을 설정해야 합니다. 아래 표에서는 이것을 "쌍을 이루는 지역"이라고 부릅니다. 마찬가지로 Traffic Manager 프로필 URL도 사용하도록 설정해야 합니다. 이렇게 하면 장애 조치 시 네트워크 트래픽을 쌍을 이루는 지역으로 원활하게 다시 라우팅할 수 있으며, 이것을 아래 표에서는 "검색 URL"이라고 부릅니다.

| 클라우드  | 지역 | 기본 엔드포인트 URL | 쌍을 이루는 지역 | 검색 URL |
|--------|--------|----------------------|---------------|---------------|
| 공용 |오스트레일리아 동부 | https://kailani-aue.one.microsoft.com | 오스트레일리아 남동부 | https://kailani-aue.one.microsoft.com |
| 공용 |오스트레일리아 남동부 | https://kailani-aus.one.microsoft.com | 오스트레일리아 동부 | https://tm-kailani-aus.one.microsoft.com |
| 공용 | 캐나다 중부 | https://kailani-cac.one.microsoft.com | 캐나다 동부 | https://tm-kailani-cac.one.microsoft.com |
| 공용 | 캐나다 동부 | https://kailani-cae.one.microsoft.com | 캐나다 중부 | https://tm-kailani.cae.one.microsoft.com |
| 공용 | 미국 중부 | https://kailani-cus.one.microsoft.com | 미국 동부 2 | https://tm-kailani-cus.one.microsoft.com |
| 공용 | 동아시아 | https://kailani11.one.microsoft.com | 동남아시아 | https://tm-kailani11.one.microsoft.com |
| 공용 | 미국 동부 | https://kailani1.one.microsoft.com | 미국 서부 | https://tm-kailani1.one.microsoft.com |
| 공용 | 미국 동부 2 | https://kailani-ess.one.microsoft.com | 미국 중부 | https://tm-kailani-ess.one.microsoft.com |
| 공용 | 유럽 북부 | https://kailani7.one.microsoft.com | 서유럽 | https://tm-kailani7.one.microsoft.com |
| 공용 | 동남아시아 | https://kailani10.one.microsoft.com | 동아시아 | https://tm-kailani10.one.microsoft.com |
| 공용 | 영국 남부 | https://kailani-uks.one.microsoft.com | 영국 서부 | https://tm-kailani-uks.one.microsoft.com |
| 공용 | 영국 서부 | https://kailani-ukw.one.microsoft.com | 영국 남부 | https://tm-kailani-ukw.one.microsoft.com |
| 공용 | 서유럽 | https://kailani6.one.microsoft.com | 유럽 북부 | https://tm-kailani6.one.microsoft.com |
| 공용 | 미국 서부 | https://kailani.one.microsoft.com | 미국 동부 | https://tm-kailani.one.microsoft.com |
| 정부 | 미국 정부 애리조나 | https://usgovarizona01.afs.azure.us | 미국 정부 텍사스 | https://tm-usgovarizona01.afs.azure.us |
| 정부 | 미국 정부 텍사스 | https://usgovtexas01.afs.azure.us | 미국 정부 애리조나 | https://tm-usgovtexas01.afs.azure.us |

- LRS(로컬 중복 저장소) 또는 ZRS(영역 중복 저장소) 저장소 계정을 사용하는 경우 "기본 엔드포인트 URL" 아래에 나열된 URL을 사용하도록 설정하기만 하면 됩니다.

- GRS(전역 중복 저장소) 저장소 계정을 사용하는 경우 세 URL을 사용하도록 설정합니다.

**예제:** `"West US"`에 저장소 동기화 서비스를 배포하고 서버를 서비스에 등록합니다. 여기서 서버가 통신할 수 있는 URL은 다음과 같습니다.

> - https://kailani.one.microsoft.com(기본 엔드포인트: 미국 서부)
> - https://kailani1.one.microsoft.com(쌍을 이루는 장애 조치(failover) 지역: 미국 동부)
> - https://tm-kailani.one.microsoft.com (주 지역의 검색 URL)

## <a name="summary-and-risk-limitation"></a>요약 및 위험 제한
이 문서의 앞부분에 나오는 목록에는 Azure 파일 동기화가 현재 통신하는 URL이 포함되어 있습니다. 방화벽은 이러한 도메인의 아웃바운드 트래픽을 허용할 수 있어야 합니다. Microsoft는 이 목록을 업데이트 상태로 유지하려고 합니다.

방화벽 규칙을 제한하는 도메인을 설정하는 것도 보안을 강화하는 방법이 될 수 있습니다. 이러한 방화벽 구성을 사용하는 경우 시간이 지남에 따라 URL이 추가되고 변경될 수 있다는 점에 유의해야 합니다. 이 문서를 정기적으로 확인하세요.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
- [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
