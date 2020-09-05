---
title: 시스템 요구 사항
description: Azure 원격 렌더링에 대 한 시스템 요구 사항을 나열 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 81480bea735017d3fc59e9c6cf126c2146a0c968
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798468"
---
# <a name="system-requirements"></a>시스템 요구 사항

> [!IMPORTANT]
> **Azure Remote Rendering**은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 장에서는 ARR ( *Azure Remote 렌더링* )을 사용 하기 위한 최소 시스템 요구 사항을 나열 합니다.

## <a name="development-pc"></a>개발 PC

* Windows 10 버전 1903 이상
* 최신 그래픽 드라이버.
* 선택 사항: 원격으로 렌더링 된 콘텐츠의 로컬 미리 보기 (예: Unity)를 사용 하려는 경우 [H265 하드웨어 비디오 디코더](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)를 사용 합니다.

> [!IMPORTANT]
> Windows update는 항상 최신 GPU 드라이버를 제공 하지 않습니다. 최신 드라이버는 GPU 제조업체의 웹 사이트를 참조 하세요.
>
> * [AMD 드라이버](https://www.amd.com/en/support)
> * [Intel 드라이버](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 드라이버](https://www.nvidia.com/Download/index.aspx)

아래 표는 H265 하드웨어 비디오 디코딩을 지 원하는 Gpu를 나열 합니다.

| GPU 제조업체 | 지원되는 모델 |
|-----------|:-----------|
| NVIDIA | [이 페이지의 맨 아래에](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)있는 **NVDEC 지원 매트릭스** 를 확인 합니다. GPU는 **265 4:2:0 8 비트** 열에서 예를 요구 합니다. |
| 뛰어난 | AMD의 [통합 비디오 디코더](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)버전이 6 이상인 gpu |
| Intel | Skylake 및 최신 Cpu |

올바른 H265 코덱이 설치 될 수 있지만 코덱 Dll의 보안 속성으로 인해 코덱 초기화가 실패할 수 있습니다. [문제 해결 가이드](../resources/troubleshoot.md#h265-codec-not-available) 에서는이 문제를 해결 하는 단계를 설명 합니다. DLL 문제는 Unity의 경우와 같이 데스크톱 응용 프로그램에서 서비스를 사용 하는 경우에만 발생할 수 있습니다.

## <a name="devices"></a>디바이스

Azure 원격 렌더링은 현재 **HoloLens 2** 및 Windows 데스크톱을 대상 장치로만 지원 합니다. [플랫폼 제한 사항](../reference/limits.md#platform-limitations) 섹션을 참조 하세요.

최신 버전은 대기 시간이 크게 향상 되므로 최신 HEVC 코덱을 사용 하는 것이 중요 합니다. 장치에 설치 된 버전을 확인 하려면 다음을 수행 합니다.

1. **Microsoft Store**를 시작 합니다.
1. 오른쪽 위에 있는 **"..."** 단추를 클릭 합니다.
1. **다운로드 및 업데이트를**선택 합니다.
1. **장치 제조업체에서 HEVC 비디오 확장**목록을 검색 합니다. 이 항목이 업데이트 아래에 나열 되어 있지 않으면 최신 버전이 이미 설치 되어 있는 것입니다.
1. 나열 된 코덱에 버전 **1.0.21821.0**이상이 있는지 확인 합니다.
1. **업데이트 가져오기** 단추를 클릭 하 고 설치가 완료 될 때까지 기다립니다.

## <a name="network"></a>네트워크

안정적이 고 대기 시간이 짧은 네트워크 연결은 좋은 사용자 환경을 위해 중요 합니다.

[네트워크 요구 사항](../reference/network-requirements.md)에 대 한 전용 장을 참조 하세요.

네트워크 문제 해결에 대 한 자세한 내용은 [문제 해결 가이드](../resources/troubleshoot.md#unstable-holograms)를 참조 하세요.

## <a name="software"></a>소프트웨어

다음 소프트웨어가 설치되어 있어야 합니다.

* 최신 버전의 **Visual Studio 2019** [(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [혼합 현실용 Visual Studio 도구](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). 특히, 다음 *워크로드* 설치는 필수입니다.
  * **C++를 사용한 데스크톱 개발**
  * **UWP(유니버설 Windows 플랫폼) 개발**
* **Windows SDK 10.0.18362.0** [(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(다운로드)](https://git-scm.com/downloads)
* 선택 사항: 데스크톱 PC의 서버에서 비디오 스트림을 보려면 **HEVC 비디오 확장** [(Microsoft Store 링크)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)이 필요 합니다. 스토어에서 업데이트를 확인 하 여 최신 버전이 설치 되어 있는지 확인 합니다.

## <a name="unity"></a>Unity

Unity를 사용 하 여 개발 하려면 다음을 설치 합니다.

* Unity 2019.3.1[(다운로드)](https://unity3d.com/get-unity/download)
* 다음 모듈을 Unity에 설치합니다.
  * **UWP** - 유니버설 Windows 플랫폼 빌드 지원
  * **IL2CPP** - Windows 빌드 지원(IL2CPP)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
