---
title: Windows 가상 데스크톱에 대 한 GPU 구성-Azure
description: Windows 가상 데스크톱에서 GPU 가속 렌더링 및 인코딩을 사용 하도록 설정 하는 방법입니다.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 64e8fab3ac352c906cfb63cd39f89acda4109b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719758"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 GPU (그래픽 처리 장치) 가속 구성

Windows 가상 데스크톱은 향상 된 앱 성능 및 확장성을 위해 GPU 가속 렌더링 및 인코딩을 지원 합니다. GPU 가속은 특히 그래픽이 많은 앱에 매우 중요 합니다.

이 문서의 지침에 따라 GPU에 최적화 된 Azure 가상 머신을 만들고, 호스트 풀에 추가 하 고, 렌더링 및 인코딩에 GPU 가속을 사용 하도록 구성 합니다. 이 문서에서는 Windows 가상 데스크톱 테넌트가 이미 구성 되어 있다고 가정 합니다.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU에 최적화 된 Azure 가상 컴퓨터 크기 선택

Azure는 다양 한 [GPU 최적화 가상 머신 크기](/azure/virtual-machines/windows/sizes-gpu)를 제공 합니다. 호스트 풀에 적합 한 선택은 특정 앱 워크 로드, 원하는 품질의 사용자 환경 및 비용을 비롯 한 다양 한 요인에 따라 달라 집니다. 일반적으로 더 크고 더 많은 Gpu는 지정 된 사용자 밀도에서 더 나은 사용자 환경을 제공 합니다.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>호스트 풀을 만들고, 가상 머신을 프로 비전 하 고, 앱 그룹을 구성 합니다.

선택한 크기의 VM을 사용 하 여 새 호스트 풀을 만듭니다. 지침은 [자습서: Azure Marketplace 사용 하 여 호스트 풀 만들기](/azure/virtual-desktop/create-host-pools-azure-marketplace)를 참조 하세요.

Windows 가상 데스크톱은 다음과 같은 운영 체제에서 GPU 가속 렌더링 및 인코딩을 지원 합니다.

* Windows 10 버전 1511 이상
* Windows Server 2016 이상

또한 앱 그룹을 구성 하거나 새 호스트 풀을 만들 때 자동으로 생성 되는 기본 데스크톱 앱 그룹 ("데스크톱 응용 프로그램 그룹" 이라고 함)을 사용 해야 합니다. 지침은 [자습서: Windows 가상 데스크톱에 대 한 앱 그룹 관리](/azure/virtual-desktop/manage-app-groups)를 참조 하세요.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>가상 머신에서 지원 되는 그래픽 드라이버 설치

Windows 가상 데스크톱에서 Azure N 시리즈 Vm의 GPU 기능을 활용 하려면 적절 한 그래픽 드라이버를 설치 해야 합니다. [지원 되는 운영 체제 및 드라이버](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) 의 지침에 따라 해당 하는 그래픽 공급 업체에서 수동으로 또는 Azure VM 확장을 사용 하 여 드라이버를 설치 합니다.

Azure에서 배포 된 드라이버만 Windows 가상 데스크톱에서 지원 됩니다. Additionaly NVIDIA Gpu를 사용 하는 Azure Vm의 경우 Windows 가상 데스크톱에 대해 [NVIDIA 그리드 드라이버만](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) 지원 됩니다.

드라이버를 설치한 후에는 VM을 다시 시작 해야 합니다. 위 지침의 확인 단계를 사용 하 여 그래픽 드라이버가 성공적으로 설치 되었는지 확인 합니다.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 구성

기본적으로 다중 세션 구성에서 실행 되는 앱 및 데스크톱은 CPU를 사용 하 여 렌더링 되며 사용 가능한 Gpu를 활용 하 여 렌더링 하지 않습니다. 세션 호스트에 대 한 그룹 정책를 구성 하 여 GPU 가속 렌더링을 사용 하도록 설정 합니다.

1. 로컬 관리자 권한이 있는 계정을 사용 하 여 VM의 데스크톱에 연결 합니다.
2. 시작 메뉴를 열고 "gpedit.msc"를 입력 하 여 그룹 정책 편집기를 엽니다.
3. 트리를 **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** ** > 원격 데스크톱 세션 호스트** **원격 세션 환경**으로 이동 합니다. > 
4. 정책 선택 **모든 원격 데스크톱 서비스 세션에 하드웨어 기본 그래픽 어댑터 사용** 을 선택 하 고이 정책을 **사용으로 설정** 하 여 원격 세션에서 GPU 렌더링을 사용 하도록 설정 합니다.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 구성

원격 데스크톱은 원격 데스크톱 클라이언트에 전송 하기 위해 앱 및 데스크톱 (GPU 또는 CPU를 사용 하 여 렌더링)에 의해 렌더링 된 모든 그래픽을 인코딩합니다. 기본적으로 원격 데스크톱은이 인코딩에 대해 사용 가능한 Gpu를 활용 하지 않습니다. 세션 호스트에 대 한 그룹 정책를 구성 하 여 GPU 가속 프레임 인코딩을 사용 하도록 설정 합니다. 위의 단계를 계속 합니다.

1. **원격 데스크톱 연결에 대해 정책 우선 444 순위** 를 지정 하 고,이 정책을 **사용** 으로 설정 하 여 원격 세션에서 h.264/avc 444 코덱을 강제로 적용 합니다.
2. 정책을 선택 하 고 **원격 데스크톱 연결에 대해 h.264/avc 하드웨어 인코딩을 구성** 하 고이 정책을 **사용** 으로 설정 하 여 원격 세션에서 AVC/H. 264에 대해 하드웨어 인코딩을 사용 하도록 설정 합니다.

    >[!NOTE]
    >Windows Server 2016에서는 **AVC 하드웨어 인코딩 선호** 을 **항상 시도**로 설정 합니다.

3. 그룹 정책을 편집 했으므로 이제 그룹 정책 업데이트를 강제로 적용 합니다. 명령 프롬프트를 열고 다음을 입력 합니다.

    ```batch
    gpupdate.exe /force
    ```

4. 원격 데스크톱 세션에서 로그 아웃 합니다.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 확인

앱이 렌더링에 GPU를 사용 하는지 확인 하려면 다음 중 하나를 수행 합니다.

* NVIDIA GPU를 사용 하는 Azure Vm의 경우 [드라이버 설치 확인](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) 에 설명 된 대로 `nvidia-smi` 유틸리티를 사용 하 여 앱을 실행할 때 GPU 사용률을 확인 합니다.
* 지원 되는 운영 체제 버전에서 작업 관리자를 사용 하 여 GPU 사용률을 확인할 수 있습니다. "성능" 탭에서 GPU를 선택 하 여 앱에서 GPU를 활용 하는지 확인 합니다.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 확인

원격 데스크톱이 GPU 가속 인코딩을 사용 하는지 확인 하려면 다음을 수행 합니다.

1. Windows 가상 데스크톱 클라이언트를 사용 하 여 VM의 데스크톱에 연결 합니다.
2. 이벤트 뷰어를 시작 하 고 다음 노드로 이동 합니다. **응용 프로그램 및 서비스 로그** > **Microsoft** > **Windows** > **remotedesktopservices-rdpcorets** > **운영**
3. GPU 가속 인코딩이 사용 되는지 확인 하려면 이벤트 ID 170을 찾습니다. "AVC 하드웨어 인코더 사용: 1"이 표시 되 면 GPU 인코딩이 사용 됩니다.
4. AVC 444 모드가 사용 되는지 확인 하려면 이벤트 ID 162를 찾습니다. "AVC 사용 가능: 1 초기 프로필: 2048"이 표시 되 면 AVC 444이 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이러한 지침에는 단일 세션 호스트 VM에서 GPU 가속을 사용 하 여 실행 해야 합니다. 더 큰 호스트 풀에서 GPU 가속을 사용 하기 위한 몇 가지 추가 고려 사항:

* [Vm 확장](/azure/virtual-machines/extensions/overview) 을 사용 하 여 여러 vm에서 드라이버 설치 및 업데이트를 간소화 하는 것이 좋습니다. NVIDIA gpu를 사용 하는 Vm에 [NVIDIA Gpu 드라이버 확장](/azure/virtual-machines/extensions/hpccompute-gpu-windows) 을 사용 하 고 amd gpu를 사용 하는 vm에 대해 Amd Gpu 드라이버 확장 (출시 예정)을 사용 합니다.
* 여러 Vm에서 그룹 정책 구성을 간소화 하기 위해 Active Directory 그룹 정책를 사용 하는 것이 좋습니다. Active Directory 도메인에 그룹 정책를 배포 하는 방법에 대 한 자세한 내용은 [그룹 정책 개체 작업](https://go.microsoft.com/fwlink/p/?LinkId=620889)을 참조 하세요.
