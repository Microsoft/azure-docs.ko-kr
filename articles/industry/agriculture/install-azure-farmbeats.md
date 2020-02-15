---
title: Azure FarmBeats 설치
description: 이 문서에서는 azure 구독에 Azure FarmBeats을 설치 하는 방법을 설명 합니다.
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 701e42caba5325df34bdbb2381389708b9b5a03f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198857"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats 설치

이 문서에서는 azure 구독에 Azure FarmBeats을 설치 하는 방법을 설명 합니다.

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 B2B(Business-to-Business) 제품입니다. 이를 통해 공급자의 농업 데이터 집합을 집계 하 고 실행 가능한 정보를 생성할 수 있습니다. Azure FarmBeats는 퓨즈 데이터 집합을 기반으로 AI (인공 지능) 또는 기계 학습 (ML) 모델을 빌드할 수 있도록 하 여이를 수행 합니다. Azure FarmBeats의 두 가지 주요 구성 요소는 다음과 같습니다.

- **Datahub**: 다양 한 공급자에 걸쳐 다양 한 농업 데이터 집합의 집계, 정규화 및 contextualization를 사용 하도록 설정 하는 API 계층입니다.

- **Accelerator**: datahub 위에 빌드되는 웹 응용 프로그램입니다. 모델 개발 및 시각화를 바로 시작 합니다. 액셀러레이터는 Azure FarmBeats Api를 사용 하 여 수집 센서 데이터를 차트로 시각화 하 고 모델 출력의 시각화를 지도로 보여 줍니다.

## <a name="general-information"></a>일반 정보

### <a name="components-installed"></a>설치 된 구성 요소

Azure FarmBeats를 설치 하는 경우 Azure 구독에 다음과 같은 리소스가 프로 비전 됩니다.

| 설치 된 Azure 리소스  | Azure FarmBeats 구성 요소  |
|---------|---------|
| Application Insights   |      Datahub & 액셀러레이터      |
| App Service     |     Datahub & 액셀러레이터     |
| App Service 계획   | Datahub & 액셀러레이터  |
| API 연결    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & 액셀러레이터      |
| Azure Batch 계정    | Datahub   |
| Azure Key Vault |  Datahub & 액셀러레이터        |
| Azure Maps 계정       |     액셀러레이터    |
| 이벤트 허브 네임 스페이스    |     Datahub      |
| 논리 앱      |  Datahub       |
| 스토리지 계정      |     Datahub & 액셀러레이터      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>발생 한 비용

Azure FarmBeats의 비용은 기본 Azure 서비스 비용의 집계입니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator)를 사용 하 여 Azure 서비스에 대 한 가격 정보를 계산할 수 있습니다. 총 설치의 실제 비용은 사용량에 따라 달라 집니다. 두 구성 요소에 대 한 안정 된 상태 비용은 다음과 같습니다.

- Datahub-일별 $10 미만
- Accelerator-하루 $2 미만

### <a name="regions-supported"></a>지원 되는 지역

현재 Azure FarmBeats는 다음 지역의 공용 클라우드 환경에서 지원 됩니다.

- 오스트레일리아 동부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽
- 동아시아
- 동남 아시아

### <a name="time-taken"></a>걸린 시간

준비 및 설치를 포함 하 여 Azure FarmBeats의 전체 설정은 한 시간 이내에 소요 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure FarmBeats의 실제 설치를 시작 하기 전에 다음 단계를 완료 해야 합니다.

### <a name="verify-permissions"></a>권한 확인

Azure FarmBeats를 설치 하려면 Azure 테 넌 트에서 다음 권한이 필요 합니다.

- 테 넌 트-AAD 앱 작성자
- 구독-소유자
- FarmBeats가 설치 되는 리소스 그룹-소유자

[AAD 응용 프로그램](#create-an-aad-application) 단계를 만들려면 처음 두 가지 권한이 필요 합니다. 필요한 경우 적절 한 권한을 가진 사용자를 가져와 AAD 응용 프로그램을 만들 수 있습니다.

Marketplace에서 FarmBeats install을 실행 하는 사람은 FarmBeats가 설치 되는 리소스 그룹의 소유자 여야 합니다. 구독 소유자의 경우이는 리소스 그룹을 만들 때 자동으로 발생 합니다. 다른 사용자의 경우 리소스 그룹을 미리 만들고 리소스 그룹의 소유자가 되도록 구독 소유자에 게 요청 하세요.

[역할 기반 access control](https://docs.microsoft.com/azure/role-based-access-control/check-access)에 대 한 지침에 따라 Azure Portal에서 액세스 권한을 확인할 수 있습니다.

### <a name="decide-subscription-and-region"></a>구독 및 지역 결정

Azure FarmBeats를 설치 하려는 Azure 구독 ID 및 지역이 필요 합니다. [지원 되는 지역](#regions-supported) 섹션에 나열 된 지역 중 하나를 선택 합니다.

**Azure 구독 ID** 와 **azure 지역을**적어 둡니다.

### <a name="create-an-aad-application"></a>AAD 응용 프로그램 만들기

Azure FarmBeats에는 응용 프로그램을 만들고 등록 하는 Azure Active Directory 필요 합니다. AAD 생성 스크립트를 성공적으로 실행 하려면 다음 권한이 필요 합니다.

- 테 넌 트-AAD 앱 작성자
- 구독-소유자

PowerShell 환경을 사용 하 여 Cloud Shell 인스턴스에서 다음 단계를 실행 합니다. 첫 번째 사용자에 게 구독을 선택 하 고 저장소 계정을 만들도록 요청 하는 메시지가 표시 됩니다. 지시에 따라 설치를 완료 합니다.

1. [AAD 앱 생성기 스크립트](https://aka.ms/FarmBeatsAADScript) 다운로드

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 기본적으로이 파일은 홈 디렉터리에 다운로드 됩니다. 디렉터리로 이동 합니다.

    ```azurepowershell-interactive
        cd
    ```

3. AAD 스크립트 실행

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 스크립트는 다음 세 가지 입력을 요청 합니다.

    - FarmBeats 웹 사이트 이름: FarmBeats 웹 응용 프로그램에 대 한 고유한 URL 접두사입니다. 접두사가 이미 사용 되는 경우 스크립트는 오류를 발생 합니다. FarmBeats 배포는 설치 되 면 https://\<FarmBeats에서 액세스할 수 있습니다.-웹 사이트-이름 >. n e t 및 swagger Api는 https://\<FarmBeats >-api.azurewebsites.net

    - Azure 로그인 ID: FarmBeats의 관리자로 추가 하려는 사용자의 Azure 로그인 ID를 제공 합니다. 그러면이 사용자는 FarmBeats 웹 응용 프로그램에 액세스 하는 액세스 권한을 다른 사용자에 게 부여할 수 있습니다. 로그인 ID는 일반적으로 john.doe@domain.com형식입니다. Azure UPN도 지원 됩니다.

    - 구독 ID: Azure FarmBeats을 설치 하려는 구독의 ID입니다.

5. AAD 스크립트는 실행 하는 데 2 분 정도 걸리며 동일한 디렉터리의 json 파일 뿐만 아니라 화면에서 값을 출력 합니다. 다른 사용자가 스크립트를 실행 한 경우이 출력을 사용자와 공유 하도록 요청 하세요.

### <a name="create-sentinel-account"></a>센티널 계정 만들기

Azure FarmBeats 설치 프로그램을 사용 하 여 팜에 대 한 유럽 우주 에이전시의 [센티널-2](https://scihub.copernicus.eu/) 위성 업무에서 위성 이미지를 가져올 수 있습니다. 이 설정을 구성 하려면 센티널 계정이 필요 합니다.

센티널을 사용 하 여 무료 계정을 만들려면 다음 단계를 따르세요.

1. 공식 [등록](https://aka.ms/SentinelRegistration) 페이지로 이동 합니다.
2. 필요한 세부 정보 (이름, 성, 사용자 이름, 암호 및 전자 메일 ID)를 입력 하 고 양식을 완성 합니다.
3. 확인 링크는 등록 된 전자 메일 ID로 전송 됩니다. 전자 메일에 제공 된 링크를 선택 하 고 확인을 완료 합니다.

등록 프로세스가 완료 되었습니다. 확인이 완료 되 면 **센티널 사용자 이름** 및 **센티널 암호**를 기록해 둡니다.

## <a name="install"></a>설치

이제 FarmBeats을 설치할 준비가 되었습니다. 설치를 시작 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인합니다. 오른쪽 위 모서리에서 계정을 선택 하 고 Azure FarmBeats를 설치 하려는 Azure AD 테 넌 트로 전환 합니다.

2. 포털 내의 Azure Marketplace로 이동 하 고 Marketplace에서 **Azure FarmBeats** 를 검색 합니다.

3. Azure FarmBeats의 개요가 표시 된 새 창이 표시 됩니다. **만들기**를 선택합니다.

4. 새 창이 나타납니다. Azure FarmBeats를 설치 하려는 올바른 구독, 리소스 그룹 및 위치를 선택 하 여 등록 프로세스를 완료 합니다.

5. **FarmBeats 서비스 경고** 섹션에서 Azure FarmBeats 관련 된 모든 서비스 경고를 받을 전자 메일 주소를 제공 합니다. 페이지 아래쪽에서 **다음** 을 선택 하 여 **종속성** 탭으로 이동 합니다.

    ![기본 탭](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. [Aad 스크립트](#create-an-aad-application) 의 출력에서 aad 응용 프로그램 섹션의 입력으로 개별 항목을 복사 합니다.

7. 센티널 계정 섹션에서 [센티널 계정](#create-sentinel-account) 사용자 이름 및 암호를 입력 합니다. **다음** 을 선택 하 여 **검토 + 만들기** 탭으로 이동 합니다.

    ![종속성 탭](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 입력 한 세부 정보의 유효성이 검사 되 면 **확인**을 선택 합니다. 사용 약관 페이지가 나타납니다. 용어를 검토 하 고 **만들기** 를 선택 하 여 설치를 시작 합니다. 설치 진행 상황을 따를 수 있는 페이지로 리디렉션됩니다.

설치가 완료 되 면 설치를 확인 하 고 설치 중에 제공한 웹 사이트 이름으로 이동 하 여 FarmBeats 포털을 사용 하기 시작할 수 있습니다. https://\<FarmBeats-name >. 팜을 만들 수 있는 옵션이 포함 된 FarmBeats 사용자 인터페이스가 표시 되어야 합니다.

**Datahub** 는 Https://\<FarmBeats > (영문)에서 찾을 수 있습니다. 여기에서 다양 한 FarmBeats API 개체를 보고 Api에 대 한 REST 작업을 수행 합니다.

## <a name="upgrade"></a>업그레이드

FarmBeats를 최신 버전으로 업그레이드 하려면 PowerShell 환경을 사용 하 여 Cloud Shell 인스턴스에서 다음 단계를 실행 합니다. 사용자가 FarmBeats가 설치 된 구독의 소유자 여야 합니다.

첫 번째 사용자에 게 구독을 선택 하 고 저장소 계정을 만들도록 요청 하는 메시지가 표시 됩니다. 지시에 따라 설치를 완료 합니다.

1. [업그레이드 스크립트](https://aka.ms/FarmBeatsUpgradeScript) 다운로드

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 기본적으로이 파일은 홈 디렉터리에 다운로드 됩니다. 디렉터리로 이동 합니다.

    ```azurepowershell-interactive
        cd
    ```

3. 업그레이드 스크립트 실행

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

입력. json 파일의 경로는 선택 사항입니다. 지정 하지 않으면 스크립트에서 필요한 모든 입력을 요청 합니다. 업그레이드는 30 분 정도 완료 되어야 합니다.

## <a name="uninstall"></a>제거

Azure FarmBeats Datahub 또는 Accelerator를 제거 하려면 다음 단계를 완료 합니다.

1. Azure Portal에 로그인 하 고 이러한 구성 요소가 설치 된 **리소스 그룹을 삭제** 합니다.

2. Azure Active Directory로 이동 하 여 Azure FarmBeats 설치에 연결 된 **AZURE AD 응용 프로그램 & 삭제** 합니다.

## <a name="next-steps"></a>다음 단계

Azure 구독에 Azure FarmBeats을 설치 하는 방법을 배웠습니다. 이제 Azure FarmBeats 인스턴스에 [사용자를 추가](manage-users-in-azure-farmbeats.md#manage-users) 하는 방법에 대해 알아봅니다.
