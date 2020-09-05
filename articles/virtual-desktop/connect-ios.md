---
title: IOS에서 Windows 가상 데스크톱에 연결-Azure
description: IOS 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230044"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>IOS 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결

> 적용 대상: iOS 13.0 이상. IPhone, iPad 및 iPod touch와 호환 됩니다.

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-ios-2019.md)를 참조하세요.

다운로드 가능한 클라이언트를 사용 하 여 iOS 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 이 가이드에서는 iOS 클라이언트를 설정 하는 방법을 설명 합니다.

## <a name="install-the-ios-client"></a>IOS 클라이언트 설치

시작 하려면 iOS 장치에 클라이언트를 [다운로드](https://aka.ms/rdios) 하 여 설치 합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독 하 여 iOS 장치에서 액세스할 수 있는 관리 되는 리소스 목록을 가져옵니다.

피드를 구독하려면 다음을 수행합니다.

1. 연결 센터에서를 탭 한 **+** 다음 **작업 영역 추가**를 탭 합니다.
2. **피드 url 필드에** 피드 url을 입력 합니다. 피드 URL은 URL 또는 전자 메일 주소일 수 있습니다.
   - URL을 사용하는 경우 관리자가 제공한 URL을 사용합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>입니다.
   - 이메일을 사용하려면 이메일 주소를 입력하세요. 그러면 클라이언트가 이메일 주소와 연결된 URL을 검색합니다(관리자가 서버를 그렇게 구성한 경우).
   - US Gov 포털을 통해 연결 하려면를 사용 <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> 합니다.
3. **다음**을 탭합니다.
4. 메시지가 표시되면 자격 증명을 입력합니다.
   - **사용자 이름**에 리소스에 액세스할 수 있는 권한을 사용자에 게 부여 합니다.
   - **암호**에는 사용자 이름과 연결 된 암호를 지정 합니다.
   - 관리자가 인증을 구성 하는 경우 추가 요인을 제공 하 라는 메시지가 표시 될 수도 있습니다.
5. 누르기 **저장**합니다.

그러면 연결 센터에서 원격 리소스를 표시합니다.

피드를 구독하면 피드의 콘텐츠가 정기적으로 자동 업데이트됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IOS 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [ios 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) 설명서를 참조 하세요.
