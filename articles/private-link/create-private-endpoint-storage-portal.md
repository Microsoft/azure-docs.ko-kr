---
title: Azure 개인 엔드포인트을 사용 하 여 전용 저장소 계정에 연결
description: 개인 엔드포인트을 사용 하 여 Azure에서 저장소 계정에 개인적으로 연결 하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: bb1913d77616869c889c464a41e8166b3a88b03c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028879"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Azure 개인 엔드포인트을 사용 하 여 전용 저장소 계정에 연결
Azure 개인 엔드포인트은 Azure의 개인 링크에 대 한 기본 빌딩 블록입니다. Vm (가상 머신)과 같은 Azure 리소스가 개인 링크 리소스와 개인적으로 통신할 수 있도록 합니다.

이 빠른 시작에서는 Azure Portal를 사용 하 여 개인 엔드포인트이 있는 저장소 계정인 Azure virtual network에서 VM을 만드는 방법에 대해 알아봅니다. 그런 다음 VM에서 저장소 계정에 안전 하 게 액세스할 수 있습니다.

> [!NOTE]
> 프라이빗 엔드포인트는 동일한 서브넷에 있는 서비스 엔드포인트와 함께 허용되지는 않습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기
이 섹션에서는 개인 링크 리소스 (이 예제에서는 저장소 계정)에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 개인 링크 리소스에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | *MyVirtualNetwork*를 입력합니다. |
    | 주소 공간 | *10.1.0.0/16*을 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | 리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **WestCentralUS**를 선택합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.1.0.0/24*를 입력합니다. |
    |||
1. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.


### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면 왼쪽 상단에서 **리소스 만들기** > **계산** > **가상 컴퓨터**를 선택 합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **없음**을 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |
    |||

1. **다음: 디스크**를 선택 합니다.

1. **가상 머신 만들기-디스크**에서 기본값을 그대로 두고 **다음: 네트워킹**을 선택 합니다.

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

## <a name="create-your-private-endpoint"></a>개인 엔드포인트 만들기
이 섹션에서는 개인 엔드포인트을 사용 하 여 개인 저장소 계정을 만듭니다. 

1. Azure Portal 화면 왼쪽 상단에서 **리소스 만들기** > **저장소** > **저장소 계정**을 선택 합니다.

1. **저장소 계정 만들기-기본 사항**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | 스토리지 계정 이름  | *Mystorageaccount*를 입력 합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 성능 중심| 기본값인 **표준**을 그대로 둡니다. |
    | 계정 종류 | 기본 **저장소 (범용 v2)** 를 그대로 둡니다. |
    | 복제 | **읽기 액세스 지역 중복 저장소 (RA-GRS)** 를 선택 합니다. |
    |||
  
3. **다음: 네트워킹**을 선택 합니다.
4. **저장소 계정 만들기-네트워킹**, 연결 방법에서 **개인 엔드포인트**을 선택 합니다.
5. **저장소 계정 만들기-네트워킹**에서 **개인 엔드포인트 추가**를 선택 합니다. 
6. **개인 엔드포인트 만들기**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    |위치|**WestCentralUS**를 선택합니다.|
    |이름|*myPrivateEndpoint*를 입력합니다.  |
    |저장소 하위 리소스|기본 **Blob**을 그대로 둡니다. |
    | **네트워킹** |  |
    | 가상 네트워크  | 리소스 그룹 *Myresourcegroup*에서 *MyVirtualNetwork* 을 선택 합니다. |
    | 서브넷 | *Mysubnet*을 선택 합니다. |
    | **프라이빗 DNS 통합**|  |
    | 프라이빗 DNS 영역과 통합  | 기본값 **예**를 그대로 둡니다. |
    | 프라이빗 DNS 영역  | 기본값 **(New) privatelink.blob.core.windows.net**을 그대로 둡니다. |
    |||
7. **확인**을 선택합니다. 
8. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 
10. 방금 만든 저장소 계정 리소스를 찾습니다.
11. 왼쪽 콘텐츠 메뉴에서 **액세스 키** 를 선택 합니다.
12. Key1에 대 한 연결 문자열에서 **복사** 를 선택 합니다.
 
## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. *downloaded.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-storage-account-privately-from-the-vm"></a>VM에서 개인적으로 저장소 계정 액세스

이 섹션에서는 개인 엔드포인트을 사용 하 여 저장소 계정에 비공개로 연결 합니다.

1. *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.
2. `nslookup mystorageaccount.blob.core.windows.net`를 입력 하면 다음과 같은 메시지가 표시 됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)를 설치합니다.
4. 마우스 오른쪽 단추를 클릭 하 여 **저장소 계정** 을 선택 합니다.
5. **Azure storage에 연결을**선택 합니다.
6. **연결 문자열 사용을**선택 합니다.
7. **다음**을 선택합니다.
8. 이전에 복사한 정보를 붙여넣어 연결 문자열을 입력 합니다.
9. **다음**을 선택합니다.
10. **연결**을 선택합니다.
11. Mystorageaccount에서 Blob 컨테이너 찾아보기 
12. 생략할 폴더를 만들거나 *mystorageaccount*에 파일을 업로드 합니다. 
13. *Myvm*에 대 한 원격 데스크톱 연결을 닫습니다. 

저장소 계정에 액세스 하기 위한 추가 옵션:
- Microsoft Azure Storage 탐색기는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해 주는 Microsoft의 독립 실행형 무료 앱입니다. 응용 프로그램을 설치 하 여 전용 저장소 계정 콘텐츠를 찾아볼 수 있습니다. 
 
- AzCopy 유틸리티는 Azure storage에 대 한 고성능 스크립트 가능 데이터 전송을 위한 또 다른 옵션입니다. AzCopy를 사용하여 Blob, File 및 Table Storage 간에 데이터를 전송합니다. 


## <a name="clean-up-resources"></a>리소스 정리 
개인 엔드포인트, 저장소 계정 및 VM을 사용 하 여 작업을 완료 하면 리소스 그룹 및 여기에 포함 된 모든 리소스를 삭제 합니다. 
1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력하고 검색 결과에서 *myResourceGroup*을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 
3. **리소스 그룹 이름 입력**에 대해 *myResourceGroup*을 입력하고 **삭제**를 선택합니다. 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 가상 네트워크와 저장소 계정 및 개인 엔드포인트에서 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결 하 고 개인 링크를 사용 하 여 저장소 계정에 안전 하 게 전달 했습니다. 개인 엔드포인트에 대 한 자세한 내용은 [Azure 개인 엔드포인트 이란?](private-endpoint-overview.md)을 참조 하세요.
