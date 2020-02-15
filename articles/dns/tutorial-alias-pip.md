---
title: '자습서: Azure 공용 IP 주소를 참조하는 Azure DNS 별칭 레코드 만들기'
description: 이 자습서에서는 Azure 공용 IP 주소를 참조하도록 Azure DNS 별칭 레코드를 구성하는 방법을 보여줍니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d4517314742f3ec8e9968d20745ffb697d96f324
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149935"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>자습서: Azure 공용 IP 주소를 참조하도록 별칭 레코드 구성 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 네트워크 인프라 만들기.
> * 웹 서버 가상 머신 만들기.
> * 별칭 레코드 만들기.
> * 별칭 레코드 테스트.


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
테스트할 Azure DNS에서 호스트할 수 있는 도메인 이름이 있어야 합니다. 이 도메인에 대한 전체 제어 권한이 있어야 합니다. 전체 제어 권한에는 도메인의 NS(이름 서버) 레코드를 설정하는 권한이 포함됩니다.

Azure DNS에서 도메인을 호스트하는 방법에 대한 지침은 [자습서: Azure DNS에서 도메인 호스트](dns-delegate-domain-azure-dns.md)를 참조하세요.

이 자습서에 사용되는 예제 도메인은 contoso.com이지만 사용자 고유의 도메인 이름을 사용하세요.

## <a name="create-the-network-infrastructure"></a>네트워크 인프라 만들기
먼저 웹 서버를 배치할 가상 네트워크 및 서브넷을 만듭니다.
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 위에서 **리소스 만들기**를 선택합니다. 검색 상자에 *리소스 그룹*을 입력하고 **RG-DNS-Alias-pip**라는 리소스 그룹을 만듭니다.
3. **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
4. **VNet-Servers**라는 가상 네트워크를 만들고 **RG-DNS-Alias-pip** 리소스 그룹에 배치한 후 서브넷 이름을 **SN-Web**으로 지정합니다.

## <a name="create-a-web-server-virtual-machine"></a>웹 서버 가상 머신 만들기
1. **리소스 만들기** > **Windows Server 2016 VM**을 선택합니다.
2. 이름으로 **Web-01**을 입력하고 VM을 **RG-DNS-Alias-TM** 리소스 그룹에 배치합니다. 사용자 이름 및 암호를 입력하고 **확인**을 선택합니다.
3. **크기**에 8GB RAM을 지원하는 SKU를 선택합니다.
4. **설정**에서 **VNet-Servers** 가상 네트워크 및 **SN-Web** 서브넷을 선택합니다. 공용 인바운드 포트의 경우 **HTTP** > **HTTPS** > **RDP(3389)** 를 선택한 다음, **확인**을 선택합니다.
5. **요약** 페이지에서 **만들기**를 선택합니다.

이 절차는 완료하는 데 몇 분이 걸립니다.

### <a name="install-iis"></a>IIS 설치

**Web-01**에 IIS를 설치합니다.

1. **Web-01**에 연결하고 로그인합니다.
2. **서버 관리자** 대시보드에서 **역할 및 기능 추가**를 선택합니다.
3. **다음**을 세 번 선택합니다. **서버 역할** 페이지에서 **Web Server(IIS)** 를 선택합니다.
4. **기능 추가**를 선택한 후 **다음**을 선택합니다.
5. **다음**을 네 번 클릭한 다음, **설치**를 선택합니다. 이 절차는 완료하는 데 몇 분이 걸립니다.
6. 설치가 완료된 후 **닫기**를 선택합니다.
7. 웹 브라우저를 엽니다. **localhost**로 이동하여 기본 IIS 웹 페이지가 표시되는지 확인합니다.

## <a name="create-an-alias-record"></a>별칭 레코드 만들기

공용 IP 주소를 가리키는 별칭 레코드를 만듭니다.

1. Azure DNS 영역을 선택하여 영역을 엽니다.
2. **레코드 집합**을 선택합니다.
3. **이름** 텍스트 상자에서 **web01**을 선택합니다.
4. **유형**을 **A** 레코드로 둡니다.
5. **별칭 레코드 집합** 확인란을 선택합니다.
6. **Azure 서비스 선택**을 선택한 다음, **Web-01-ip** 공용 IP 주소를 선택합니다.

## <a name="test-the-alias-record"></a>별칭 레코드 테스트

1. **RG-DNS-Alias-pip** 리소스 그룹에서 **Web-01** 가상 머신을 선택합니다. 공용 IP 주소를 기록해둡니다.
1. 웹 브라우저에서 Web01-01 가상 머신의 정규화된 도메인 이름을 찾습니다. 예를 들어 **web01.contoso.com**이 있습니다. 이제 IIS 기본 웹 페이지가 표시됩니다.
2. 웹 브라우저를 닫습니다.
3. **Web-01** 가상 머신을 중지한 다음, 다시 시작합니다.
4. 가상 머신이 다시 시작된 후 가상 머신에 대한 새 공용 IP 주소를 기록해둡니다.
5. 새 브라우저를 엽니다. Web01-01 가상 머신의 정규화된 도메인 이름을 다시 찾아봅니다. 예를 들어 **web01.contoso.com**이 있습니다.

이 절차에서는 표준 A 레코드가 아닌 공용 IP 주소 리소스를 가리키는 별칭 레코드를 사용했으므로 성공합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스가 더 이상 필요하지 않은 경우 **RG-DNS-Alias-pip** 리소스 그룹을 삭제하세요.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 공용 IP 주소를 참조하는 별칭 레코드를 만들었습니다. Azure DNS 및 웹앱에 대해 자세히 알아보려면 웹앱에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
