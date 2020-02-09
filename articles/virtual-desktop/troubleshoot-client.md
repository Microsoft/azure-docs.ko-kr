---
title: 원격 데스크톱 클라이언트 Windows 가상 데스크톱 문제 해결-Azure
description: Windows 가상 데스크톱 테넌트 환경에서 클라이언트 연결을 설정할 때 발생 하는 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bcf22ec1eaf05f5dda6396cca017fa21fd6ddbf5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968189"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>원격 데스크톱 클라이언트 문제 해결

이 문서에서는 원격 데스크톱 클라이언트와 관련 된 일반적인 문제 및 해결 방법에 대해 설명 합니다.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 또는 Windows 10 용 원격 데스크톱 클라이언트가 응답을 중지 하거나 열 수 없습니다.

다음 PowerShell cmdlet을 사용 하 여 대역 외 (OOB) 클라이언트 레지스트리를 정리 합니다.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

**%AppData%\RdClientRadc** 으로 이동 하 여 모든 콘텐츠를 삭제 합니다.

Windows 7 및 Windows 10 용 원격 데스크톱 클라이언트를 제거 하 고 다시 설치 합니다.

## <a name="web-client-wont-open"></a>웹 클라이언트가 열리지 않음

먼저 브라우저에서 다른 웹 사이트를 열어 인터넷 연결을 테스트 합니다. 예를 들면 [www.bing.com](https://www.bing.com)입니다.

**Nslookup** 을 사용 하 여 DNS에서 FQDN을 확인할 수 있는지 확인 합니다.

```cmd
nslookup rdweb.wvd.microsoft.com
```

Windows 7 또는 Windows 10 용 원격 데스크톱 클라이언트와 같은 다른 클라이언트를 사용 하 여 연결을 시도 하 고 웹 클라이언트를 열 수 있는지 확인 합니다.

### <a name="opening-another-site-fails"></a>다른 사이트를 열지 못했습니다.

이 문제는 일반적으로 네트워크 연결 문제 또는 네트워크 중단으로 인해 발생 합니다. 네트워크 지원에 문의 하는 것이 좋습니다.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup에서 이름을 확인할 수 없습니다.

이 문제는 일반적으로 네트워크 연결 문제 또는 네트워크 중단으로 인해 발생 합니다. 네트워크 지원에 문의 하는 것이 좋습니다.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>클라이언트는 연결할 수 없지만 네트워크의 다른 클라이언트는 연결할 수 있습니다.

웹 클라이언트를 사용 하는 동안 브라우저가 작동을 시작 하거나 작동을 중지 하는 경우 다음 지침에 따라 문제를 해결 합니다.

1. 브라우저를 다시 시작합니다.
2. 브라우저 쿠키를 지웁니다. [Internet Explorer에서 쿠키 파일을 삭제 하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)참조 하세요.
3. 브라우저 캐시를 지웁니다. [브라우저에 대 한 브라우저 캐시 지우기를](https://binged.it/2RKyfdU)참조 하세요.
4. 비공개 모드에서 브라우저를 엽니다.

## <a name="web-client-stops-responding-or-disconnects"></a>웹 클라이언트가 응답을 중지 하거나 연결을 끊습니다.

다른 브라우저 또는 클라이언트를 사용 하 여 연결 해 보세요.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>다른 브라우저 및 클라이언트도 작동 하지 않거나 열리지 않습니다.

브라우저를 전환한 후에도 문제가 계속 되 면 브라우저와 관련 된 문제가 발생할 수 있지만 네트워크에 문제가 있을 수 있습니다. 네트워크 지원에 문의 하는 것이 좋습니다.

## <a name="web-client-keeps-prompting-for-credentials"></a>웹 클라이언트는 자격 증명을 묻는 메시지를 표시 합니다.

웹 클라이언트에서 자격 증명을 입력 하 라는 메시지가 표시 되 면 다음 지침을 따르세요.

1. 웹 클라이언트 URL이 올바른지 확인 합니다.
2. 사용 중인 자격 증명이 URL에 연결 된 Windows 가상 데스크톱 환경에 대 한 것인지 확인 합니다.
3. 브라우저 쿠키를 지웁니다. 자세한 내용은 [Internet Explorer에서 쿠키 파일을 삭제 하는 방법](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)을 참조 하세요.
4. 브라우저 캐시를 지웁니다. 자세한 내용은 [브라우저의 브라우저 캐시 지우기](https://binged.it/2RKyfdU)를 참조 하세요.
5. 비공개 모드에서 브라우저를 엽니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 문제 해결 자습서를 진행 하려면 [자습서: 템플릿 배포 리소스 관리자 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조 하세요.