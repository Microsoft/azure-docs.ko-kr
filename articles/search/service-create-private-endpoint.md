---
title: 보안 연결을 위한 개인 끝점 만들기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에 대 한 보안 연결을 위해 가상 네트워크에서 개인 끝점 설정
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0cfa7b63d1ce9dd4d9b40cd0eedac247f9c56437
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935758"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 보안 연결을 위한 개인 끝점 만들기

이 문서에서는 Azure Portal를 사용 하 여 인터넷을 통해 액세스할 수 없는 새 Azure Cognitive Search 서비스 인스턴스를 만듭니다. 다음으로 동일한 가상 네트워크에 Azure 가상 머신을 구성 하 고이를 사용 하 여 개인 끝점을 통해 검색 서비스에 액세스 합니다.

개인 끝점은 [Azure 개인 링크](../private-link/private-link-overview.md)에서 별도 서비스로 제공 됩니다. 비용에 대 한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조 하세요.

> [!Important]
> Azure Portal 또는 [관리 REST API 버전 2020-03-13](/rest/api/searchmanagement/)을 사용 하 여 Azure Cognitive Search에 대 한 개인 끝점 지원을 구성할 수 있습니다. 서비스 끝점이 개인 인 경우 일부 포털 기능이 사용 하지 않도록 설정 됩니다. 서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서 및 기술 정의와 같은 서비스의 다양 한 구성 요소 및 인덱스에 대 한 포털 액세스는 보안상의 이유로 제한 됩니다.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>보안 액세스에 개인 끝점을 사용 하는 이유

Azure Cognitive Search에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 은 가상 네트워크의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전 하 게 액세스할 수 있도록 합니다. 개인 끝점은 검색 서비스에 대 한 [가상 네트워크 주소 공간의](../virtual-network/private-ip-addresses.md) IP 주소를 사용 합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크 및 Microsoft 백본 네트워크의 개인 링크를 순회 하 여 공용 인터넷에서 노출 되는 것을 제거 합니다. 개인 링크를 지 원하는 다른 PaaS 서비스 목록은 제품 설명서의 [가용성 섹션](../private-link/private-link-overview.md#availability) 을 확인 하세요.

검색 서비스에 대 한 개인 끝점을 사용 하면 다음을 수행할 수 있습니다.

- 검색 서비스에 대 한 공용 끝점의 모든 연결을 차단 합니다.
- 가상 네트워크에서 데이터의 반출을 차단할 수 있도록 하 여 가상 네트워크에 대 한 보안을 강화 합니다.
- 개인 피어 링을 사용 하 여 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [연결할 expressroutes](../expressroute/expressroute-locations.md) 를 사용 하 여 가상 네트워크에 연결 하는 온-프레미스 네트워크에서 검색 서비스에 안전 하 게 연결 합니다.

## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 검색 서비스의 개인 끝점에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

1. Azure Portal 홈 탭에서 **리소스 만들기**  >  **네트워킹**  >  **가상 네트워크**를 선택 합니다.

1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 구독 | 구독 선택|
    | 리소스 그룹 | **새로 만들기**를 선택 하 고 *myresourcegroup*을 입력 한 다음 **확인** 을 선택 합니다. |
    | 속성 | *MyVirtualNetwork* 입력 |
    | 지역 | 원하는 지역 선택 |
    |||

1. 나머지 설정에 대 한 기본값을 그대로 둡니다. **검토 + 만들기** 및 **만들기** 를 차례로 클릭 합니다.

## <a name="create-a-search-service-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 검색 서비스 만들기

이 섹션에서는 개인 끝점을 사용 하 여 새 Azure Cognitive Search 서비스를 만듭니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**  >  **웹**  >  **Azure Cognitive Search**를 선택 합니다.

1. **새 Search Service-기본 사항**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | URL | 고유한 이름을 입력합니다. |
    | 위치 | 원하는 지역을 선택 합니다. |
    | 가격 책정 계층 | **가격 책정 계층 변경** 을 선택 하 고 원하는 서비스 계층을 선택 합니다. **무료** 계층에서는 지원 되지 않습니다. **Basic** 이상 이어야 합니다.) |
    |||
  
1. **다음: 규모**를 선택 합니다.

1. 값을 기본값으로 그대로 두고 **다음: 네트워킹**을 선택 합니다.

1. **새 Search Service-네트워킹**에서 **끝점 연결 (데이터)** 에 대해 **개인** 을 선택 합니다.

1. **새 Search Service-네트워킹**의 **개인 끝점**에서 **+ 추가** 를 선택 합니다. 

1. **개인 끝점 만들기**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | 위치 | **미국 서부**를 선택합니다.|
    | 속성 | *myPrivateEndpoint*를 입력합니다.  |
    | 대상 하위 리소스 | 기본 **Searchservice**를 그대로 둡니다. |
    | **네트워킹** |  |
    | 가상 네트워크  | 리소스 그룹 *Myresourcegroup*에서 *MyVirtualNetwork* 을 선택 합니다. |
    | 서브넷 | *mySubnet*을 선택합니다. |
    | **프라이빗 DNS 통합** |  |
    | 프라이빗 DNS 영역과 통합  | 기본값 **예**를 그대로 둡니다. |
    | 프라이빗 DNS 영역  | 기본값인 * * (New) privatelink.search.windows.net * *를 그대로 둡니다. |
    |||

1. **확인**을 선택합니다. 

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 

1. 새 서비스를 프로 비전 하는 작업이 완료 되 면 방금 만든 리소스를 찾습니다.

1. 왼쪽 콘텐츠 메뉴에서 **키** 를 선택 합니다.

1. 나중에 서비스에 연결할 때 **기본 관리자 키** 를 복사 합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**  >  **계산**  >  **가상 머신**를 선택 합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **미국 서 부** 또는 사용 중인 모든 지역을 선택 합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본 선택 된 **포트 허용**을 그대로 둡니다. |
    | 인바운드 포트 선택 | 기본 **RDP (3389)** 를 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |
    |||

1. 완료되면 **다음: 디스크**를 선택합니다.

1. **가상 머신 만들기 - 디스크**에서 기본값을 그대로 두고 **다음: 네트워킹**을 선택합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **MyVirtualNetwork**를 그대로 둡니다.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24**를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    ||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 


## <a name="connect-to-the-vm"></a>VM에 연결

를 다운로드 하 고 다음과 같이 VM *Myvm* 에 연결 합니다.

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  


## <a name="test-connections"></a>연결 테스트

이 섹션에서는 검색 서비스에 대 한 개인 네트워크 액세스를 확인 하 고 개인 끝점을 사용 하 여에 개인적으로 연결 합니다.

검색 서비스 끝점이 개인 인 경우 일부 포털 기능이 사용 하지 않도록 설정 됩니다. 서비스 수준 설정을 보고 관리할 수 있지만 인덱스, 인덱서 및 기술 정의와 같은 서비스의 다양 한 다른 구성 요소에 대 한 포털 액세스는 보안상의 이유로 제한 됩니다.

1. *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.

1. ' Nslookup [검색 서비스 이름]. windows. n e t. net '을 입력 합니다.

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. VM에서 검색 서비스에 연결 하 여 인덱스를 만듭니다. 이 [빠른](search-get-started-postman.md) 시작을 수행 하 여 REST API를 사용 하는 postman의 서비스에서 새 검색 인덱스를 만들 수 있습니다. Postman의 요청을 설정 하려면 검색 서비스 끝점 (https://[search service name]. 검색. w i n d)과 이전 단계에서 복사한 관리 api 키가 필요 합니다.

1. VM에서 빠른 시작을 완료 하는 것은 서비스가 완전히 작동 하는지 확인 하는 것입니다.

1. *Myvm*에 대 한 원격 데스크톱 연결을 닫습니다. 

1. 공용 끝점에서 서비스에 액세스할 수 없는지 확인 하려면 로컬 워크스테이션에서 Postman을 열고 빠른 시작에서 처음 몇 가지 작업을 시도 합니다. 원격 서버가 존재 하지 않는다는 오류가 표시 되 면 검색 서비스에 대 한 개인 끝점을 성공적으로 구성 합니다.

## <a name="clean-up-resources"></a>리소스 정리 
개인 끝점, 검색 서비스 및 VM을 사용 하 여 작업을 완료 하면 리소스 그룹 및 포함 된 모든 리소스를 삭제 합니다.
1.  *myResourceGroup*   포털 맨 위에 있는 **검색** 상자에 myresourcegroup을 입력 하 고 검색 결과에서 *myresourcegroup*을 선택   합니다. 
1. **리소스 그룹 삭제**를 선택합니다. 
1.  *myResourceGroup*   **리소스 그룹 이름 입력** 에 myresourcegroup을 입력 하 고 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 가상 네트워크에서 VM을 만들고 개인 끝점을 사용 하 여 검색 서비스를 만들었습니다. 인터넷에서 VM에 연결 하 고 개인 링크를 사용 하 여 검색 서비스에 안전 하 게 전달 했습니다. 개인 끝점에 대 한 자세한 내용은 [Azure 개인 끝점 이란?](../private-link/private-endpoint-overview.md)을 참조 하세요.