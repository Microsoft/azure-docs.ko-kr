---
title: 드론 이미지 가져오기
description: 이 문서에서는 파트너에서 드 론 이미지를 가져오는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 04f0c3c63d00ea49bf43f00f256266599a73d6c0
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508807"
---
# <a name="get-drone-imagery-from-drone-partners"></a>드 론 파트너에서 드 론 이미지 가져오기

이 문서에서는 드 론 이미지 파트너에서 Azure FarmBeats datahub로 orthomosaic 데이터를 가져오는 방법을 설명 합니다. Orthomosaic은 드 론에서 수집 된 데이터에서 기하학적으로 수정 되 고 연결 되어는 항공 그림 또는 이미지입니다.

현재 지원 되는 이미지 파트너는 다음과 같습니다.

  ![FarmBeats 드 론 이미지 파트너](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

드 론 이미지 데이터를 Azure FarmBeats와 통합 하면 팜에서 orthomosaic 데이터를 데이터 허브로 이동 하 여 데이터를 가져올 수 있습니다. 데이터를 사용할 수 있게 되 면 FarmBeats Accelerator에서 볼 수 있습니다. 데이터는 데이터 fusion 및 인공 지능 및 기계 학습 모델 빌드에 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

  - Azure FarmBeats를 설치 했는지 확인 합니다. FarmBeats를 설치 하는 방법에 대 한 자세한 내용은 [Azure FarmBeats 설치](install-azure-farmbeats.md)를 참조 하세요.
  - FarmBeats 시스템에서 드 론 이미지를 정의 하려는 팜을 보유 하 고 있는지 확인 합니다.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats와 드 론 이미지 통합 사용

FarmBeats와의 통합을 사용 하도록 설정 하려면 장치 공급자에 게 다음 정보를 제공 합니다.
 - API 엔드포인트
 - 테넌트 ID
 - 클라이언트 ID
 - 클라이언트 암호

다음 단계를 수행합니다.

1. 이 [스크립트](https://aka.ms/farmbeatspartnerscript)를 다운로드 하 고 로컬 드라이브에 압축을 풉니다. 두 파일은 zip 파일 내에 있습니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cloud Shell을 엽니다. 이 옵션은 포털의 오른쪽 위 모퉁이에 있는 도구 모음에서 사용할 수 있습니다.

    ![포털의 오른쪽 위 막대에서 Azure Cloud Shell 열기](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 환경이 **PowerShell**로 설정 되었는지 확인 합니다.

    ![PowerShell 설정](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell 인스턴스에서 1 단계에서 다운로드 한 두 파일을 업로드 합니다.

    ![파일 업로드](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 파일이 업로드 된 디렉터리로 이동 합니다. 기본적으로 사용자 이름 아래의 홈 디렉터리에 업로드 됩니다.
6. 다음 스크립트를 실행합니다.

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. 화면의 지시에 따라 API 끝점, 테 넌 트 ID, 클라이언트 ID, 클라이언트 암호 및 EventHub 연결 문자열의 값을 캡처합니다.

    파트너의 드 론 소프트웨어 시스템에 필요한 자격 증명을 입력 한 후에는 FarmBeats 시스템에서 모든 팜을 가져올 수 있습니다. 그런 다음 팜 세부 정보를 사용 하 여 비행 경로 계획 및 드 론 이미지 컬렉션을 수행할 수 있습니다.

    드 론 공급자 소프트웨어에서 원시 이미지를 처리 한 후 드 one 소프트웨어 시스템은 연결 되어 orthomosaic 및 기타 처리 된 이미지를 datahub에 업로드 합니다.

## <a name="view-drone-imagery"></a>드 론 이미지 보기

데이터가 FarmBeats datahub로 전송 된 후 FarmBeats Datahub Api를 사용 하 여 장면 저장소를 쿼리할 수 있습니다.

또는 **팜 세부 정보** 페이지에서 최신 드 론 이미지를 볼 수 있습니다. 이미지를 보려면 다음 단계를 수행 합니다.

1. 이미지를 업로드 한 팜을 선택 합니다. **팜** 정보 페이지가 나타납니다.
2. 최신 **전체 자릿수 맵** 섹션까지 아래로 스크롤합니다.
3. **Drone** 이미지 섹션에서 이미지를 봅니다.

    ![Drone 이미지 섹션](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>드 론 이미지 다운로드

드 론 이미지 섹션을 선택 하면 드 론 orthomosaic의 고해상도 이미지를 표시 하는 팝업이 열립니다.

![고해상도 orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>모든 드 론 맵 보기

드 론 공급자가 업로드 한 파일 및 이미지가 **Maps** 섹션에 표시 됩니다. **맵** 섹션을 선택 하 고 **팜으로**필터링 한 다음, 보고 다운로드할 적절 한 파일을 선택 합니다.

  ![Maps 섹션](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>다음 단계

FarmBeats datahub [api](rest-api-in-azure-farmbeats.md) 를 사용 하 여 드 론 이미지를 가져오는 방법에 대해 알아봅니다.
