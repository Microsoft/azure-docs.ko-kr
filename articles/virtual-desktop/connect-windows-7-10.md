---
title: Windows 가상 데스크톱 Windows 10 또는 7에 연결-Azure
description: Windows 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱에 연결 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e4f41cb23ec4a9463b983049f5f37d09d1b8c71
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226712"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows Desktop 클라이언트와 연결

> 적용 대상: Windows 7, Windows 10 및 Windows 10 IoT Enterprise

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md)를 참조하세요.

Windows 데스크톱 클라이언트를 사용 하 여 windows 7, Windows 10 및 Windows 10 IoT Enterprise를 사용 하는 장치에서 Windows 가상 데스크톱 리소스에 액세스할 수 있습니다. 클라이언트는 창 8 또는 Windows 8.1을 지원 하지 않습니다.

>[!NOTE]
>Windows 클라이언트는 Windows 가상 데스크톱 (클래식)으로 자동으로 설정 됩니다. 그러나 클라이언트가 사용자에 게 Azure Resource Manager 리소스가 있음을 감지 하면 리소스를 자동으로 추가 하거나 사용자에 게 사용 가능 여부를 알립니다.

> [!IMPORTANT]
> Windows Virtual Desktop은 RADC(RemoteApp 및 데스크톱 연결) 클라이언트 또는 원격 데스크톱 연결(MSTSC) 클라이언트를 지원하지 않습니다.

> [!IMPORTANT]
> Windows Virtual Desktop은 현재 Windows Store에서 원격 데스크톱 클라이언트를 지원하지 않습니다.

## <a name="install-the-windows-desktop-client"></a>Windows 데스크톱 클라이언트 설치

Windows 버전과 일치하는 클라이언트를 선택합니다.

- [Windows 64비트](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32비트](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

관리자 권한이 필요하지 않은 현재 사용자의 클라이언트를 설치하거나, 디바이스의 모든 사용자가 클라이언트에 액세스할 수 있도록 관리자가 클라이언트를 설치 및 구성할 수 있습니다.

설치가 완료되면 **원격 데스크톱**을 검색하여 시작 메뉴에서 클라이언트를 시작할 수 있습니다.

## <a name="subscribe-to-a-workspace"></a>작업 영역 구독

작업 영역을 구독하는 방법에는 두 가지가 있습니다. 클라이언트가 회사 또는 학교 계정에서 사용 가능한 리소스를 찾거나 클라이언트가 찾을 수 없는 경우 리소스가 있는 URL을 직접 지정할 수 있습니다. 작업 영역을 구독하면 리소스를 다음 방법 중 하나를 사용하여 시작할 수 있습니다.

- [연결 센터]로 이동하여 시작할 리소스를 두 번 클릭합니다.
- [시작] 메뉴로 이동하여 작업 영역 이름이 있는 폴더를 찾거나 검색 창에서 리소스 이름을 입력할 수도 있습니다.

### <a name="subscribe-with-a-user-account"></a>사용자 계정으로 구독

1. 클라이언트의 기본 페이지에서 **구독**을 선택 합니다.
2. 메시지가 표시되면 사용자 계정으로 로그인합니다.
3. 리소스는 연결 센터에 표시 되 고 작업 영역 별로 그룹화 됩니다.

### <a name="subscribe-with-a-url"></a>URL을 사용 하 여 구독

1. 클라이언트의 기본 페이지에서 **URL을 사용 하 여 구독**을 선택 합니다.
2. 작업 영역 URL 또는 이메일 주소를 입력합니다.
   - **작업 영역 URL**을 사용하는 경우 관리자가 제공한 URL을 사용합니다. Windows Virtual Desktop에서 리소스에 액세스하는 경우 다음 URL 중 하나를 사용할 수 있습니다.
     - Windows Virtual Desktop(클래식): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows Virtual Desktop: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
     - Windows 가상 데스크톱 (US Gov): `https://rdweb.wvd.azure.us/api/arm/feeddiscovery`
   - **전자 메일** 필드를 대신 사용 하는 경우 전자 메일 주소를 입력 합니다. 이렇게 하면 관리자가 [전자 메일 검색](/windows-server/remote/remote-desktop-services/rds-email-discovery)을 설정 하는 경우 전자 메일 주소와 연결 된 URL을 검색 하도록 클라이언트에 지시 합니다.
3. **다음**을 선택합니다.
4. 메시지가 표시되면 사용자 계정으로 로그인합니다.
5. 리소스는 연결 센터에 작업 영역 별로 그룹화 되어 표시 됩니다.

## <a name="next-steps"></a>다음 단계

Windows 데스크톱 클라이언트를 사용 하는 방법에 대 한 자세한 내용은 [Windows 데스크톱 클라이언트 시작](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)을 참조 하세요.
