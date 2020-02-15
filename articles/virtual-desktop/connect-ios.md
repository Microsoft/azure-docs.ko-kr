---
title: IOS에서 Windows 가상 데스크톱에 연결-Azure
description: IOS 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
ms.openlocfilehash: 92e11edf3b28017ac73702e3f71dfc491d3986e3
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110635"
---
# <a name="connect-with-the-ios-client"></a>iOS 클라이언트와 연결

> 적용 대상: iOS 13.0 이상. IPhone, iPad 및 iPod touch와 호환 됩니다.

다운로드 가능한 클라이언트를 사용 하 여 iOS 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 이 가이드에서는 iOS 클라이언트를 설정 하는 방법을 설명 합니다.

## <a name="install-the-ios-client"></a>IOS 클라이언트 설치

시작 하려면 iOS 장치에 클라이언트를 [다운로드](https://aka.ms/rdios) 하 여 설치 합니다.

## <a name="subscribe-to-a-feed"></a>피드 구독

관리자가 제공한 피드를 구독 하 여 iOS 장치에서 액세스할 수 있는 관리 되는 리소스 목록을 가져옵니다.

피드를 구독 하려면 다음을 수행 합니다.

1. 연결 센터에서 **+** 를 탭 한 다음 **작업 영역 추가**를 탭 합니다.
2. **피드 url 필드에** 피드 url을 입력 합니다. 피드 URL은 URL 또는 전자 메일 주소일 수 있습니다.
   - URL을 사용 하는 경우 관리자가 제공한 URL을 사용 합니다. 일반적으로 URL은 <https://rdweb.wvd.microsoft.com>입니다.
   - 전자 메일을 사용 하려면 전자 메일 주소를 입력 하세요. 이렇게 하면 관리자가 서버를 구성한 경우 전자 메일 주소와 연결 된 URL을 검색 하도록 클라이언트에 지시 합니다.
3. **다음**을 탭 합니다.
4. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.
   - **사용자 이름**에 리소스에 액세스할 수 있는 권한을 사용자에 게 부여 합니다.
   - **암호**에는 사용자 이름과 연결 된 암호를 지정 합니다.
   - 관리자가 인증을 구성 하는 경우 추가 요인을 제공 하 라는 메시지가 표시 될 수도 있습니다.
5. **저장**을 탭 합니다.

그러면 연결 센터에서 원격 리소스를 표시 해야 합니다.

피드에 가입한 후에는 피드의 콘텐츠가 정기적으로 자동 업데이트 됩니다. 관리자가 변경한 내용에 따라 리소스를 추가, 변경 또는 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IOS 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [ios 클라이언트 시작](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) 설명서를 참조 하세요.
