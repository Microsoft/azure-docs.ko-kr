---
title: 웹 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결-Azure
description: 웹 클라이언트를 사용하여 Windows Virtual Desktop에 연결하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ca5a591d168e18181a29cf1c00a7d1fead54595
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226049"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>웹 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-web-2019.md)를 참조하세요.

웹 클라이언트를 사용하면 시간이 오래 걸리는 설치 프로세스 없이 웹 브라우저에서 Windows Virtual Desktop 리소스에 액세스할 수 있습니다.

>[!NOTE]
>웹 클라이언트에서는 현재 모바일 OS를 지원하지 않습니다.

## <a name="supported-operating-systems-and-browsers"></a>지원되는 운영 체제 및 브라우저

HTML5 지원 브라우저가 제대로 작동하는 동안에는 다음 운영 체제 및 브라우저를 공식적으로 지원합니다.

| 브라우저           | 지원되는 OS                     | 메모               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 버전 11 이상 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 버전 55 이상 |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>원격 리소스 피드에 액세스

브라우저에서 <https://rdweb.wvd.microsoft.com/arm/webclient>의 Azure Resource Manager 통합 버전 Windows Virtual Desktop 웹 클라이언트로 이동한 후 사용자 계정으로 로그인합니다.

>[!NOTE]
>Azure Resource Manager 통합 하지 않고 Windows 가상 데스크톱 (클래식)을 사용 하는 경우에는에서 리소스에 <https://rdweb.wvd.microsoft.com/webclient> 대신 연결 합니다.
>
> US Gov 포털을 사용 하는 경우를 사용 <https://rdweb.wvd.azure.us/arm/webclient/index.html> 합니다.

>[!NOTE]
>Windows Virtual Desktop에 사용하려는 계정이 아닌 Azure Active Directory 계정으로 이미 로그인한 경우에는 로그아웃하거나 프라이빗 브라우저 창을 사용해야 합니다.

이제 로그인한 후에는 리소스 목록이 표시됩니다. **모든 리소스** 탭에서 일반 앱과 같이 선택하여 리소스를 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

웹 클라이언트를 사용하는 방법에 대한 자세한 내용은 [웹 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)을 참조하세요.
