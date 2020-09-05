---
title: VM 이미지에 대 한 SAS URI 가져오기-Azure Marketplace
description: Azure Marketplace에서 VHD(가상 하드 디스크)에 대한 SAS(공유 액세스 서명) URI를 생성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144525"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>VM 이미지에 대 한 SAS URI 가져오기

게시 프로세스가 진행 되는 동안 요금제와 연결 된 각 VHD (이전에는 Sku 라고 함)에 대 한 SAS (공유 액세스 서명) URI를 제공 해야 합니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 파트너 센터의 **계획** 탭에서이 URI를 입력 합니다.

Vhd에 대 한 SAS Uri 생성에는 다음과 같은 요구 사항이 있습니다.

- 관리 되지 않는 Vhd만 지원 합니다.
- 목록 및 읽기 권한만 필요 합니다. 쓰기 또는 삭제 권한을 제공 하지 마세요.
- 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
- UTC 시간 변경을 방지하려면 시작 날짜를 현재 날짜 전으로 설정합니다. 예를 들어 현재 날짜가 2020 년 6 월 16 일 경우 6/15/2020를 선택 합니다.

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소(URL)를 만드는 데 사용되는 일반적인 두 가지 도구는 다음과 같습니다.

1. **Microsoft Storage Explorer** – Windows, macOS 및 Linux에서 사용할 수 있는 그래픽 도구
2. **Microsoft Azure CLI** – 비 Windows 운영 체제 및 자동 또는 지속적인 통합 환경에 권장됨

### <a name="using-tool-1-microsoft-storage-explorer"></a>도구 1 사용: Microsoft Storage 탐색기

1. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 다운로드하고 설치합니다.
2. 탐색기를 열고 왼쪽 메뉴에서 **계정 추가**를 선택 합니다.
3. **Azure Storage에 연결** 대화 상자에서 **azure 계정 추가** 를 선택 하 고 azure 계정에 로그인 합니다.
4. 왼쪽 탐색기 창에서 **저장소 계정** 노드를 확장 합니다.
5. VHD를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다.
6. **공유 액세스 서명** 대화 상자에서 다음 필드를 완료 합니다.

    1. 시작 시간 – VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    2. 만료 시간 – VHD 액세스 권한의 만료 날짜입니다. 현재 날짜보다 최소 3주 후의 날짜를 제공합니다.
    3. 권한 – 읽기 및 목록 사용 권한을 선택합니다.
    4. 컨테이너 수준 – 컨테이너 수준 공유 액세스 서명 URI 생성 확인란을 선택합니다.

    ![공유 액세스 서명 대화 상자](media/vm/create-sas-uri-storage-explorer.png)

7. 이 VHD에 연결되는 SAS URI를 만들려면 **만들기**를 선택합니다. 대화 상자가 새로 고쳐지고 이 작업에 대한 세부 정보가 표시됩니다.

8. URI를 복사하고, 안전한 위치에 텍스트 파일로 저장합니다.

    ![URI를 복사 하 고 있습니다.](media/vm/create-sas-uri-shared-access-signature-details.png)

    이 생성된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 이를 특정 하 게 만들려면 텍스트 파일을 편집 하 여 VHD 이름을 추가 합니다.

9. SAS URI의 VHD 문자열 뒤에 VHD 이름을 삽입합니다(앞의 슬래시 포함). 최종 SAS URI는 다음과 같습니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. 게시할 계획의 각 VHD에 대해 이 단계를 반복합니다.

### <a name="using-tool-2-azure-cli"></a>도구 2 사용: Azure CLI

1. [MICROSOFT AZURE CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)I를 다운로드 하 여 설치 합니다. 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일(.ps1 파일 확장명)을 만들고, 다음 코드를 복사한 다음, 로컬로 저장합니다.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 파일을 편집하여 다음 매개 변수 값을 사용합니다. UTC 날짜/시간 형식으로 날짜를 입력 합니다 (예: 2020-04-01T00:00:00Z).

    - 계정-이름 – Azure storage 계정 이름입니다.
    - 계정-키 – Azure storage 계정 키입니다.
    - vhd-이름 – VHD 이름입니다.
    - 시작-날짜 – VHD 액세스를 위한 사용 권한 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    - 만료 날짜-VHD 액세스를 위한 사용 권한 만료 날짜입니다. 현재 날짜 이후 최소 3주 후의 날짜를 제공합니다.

    다음은이 문서를 작성할 당시에 적절 한 매개 변수 값의 예입니다.

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. 변경 내용을 저장합니다.
2. 다음 방법 중 하나를 사용하여 이 스크립트를 관리자 권한으로 실행해 컨테이너 수준 액세스를 위한 SAS 연결 문자열을 만듭니다.

    - 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.
    - [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)와 같은 PowerShell 스크립트 편집기에서 스크립트를 실행합니다. 이 화면은 이 편집기 내에서 SAS 연결 문자열을 만드는 것을 보여 줍니다.

    [![PowerShell 편집기 내에서 SAS 연결 문자열 만들기](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. SAS 연결 문자열을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 문자열을 편집하여 최종 SAS URI를 만들기 위한 VHD 위치 정보를 추가합니다.
7. Azure Portal에서 새 URI와 연결된 VHD를 포함하는 BLOB 스토리지로 이동합니다.
8. Bblob 서비스 끝점의 URL을 복사 합니다.

    ![Blob service 끝점의 URL을 복사 하는 중입니다.](media/vm/create-sas-uri-blob-endpoint.png)

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용하여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>SAS URI 확인

파트너 센터에 게시 하기 전에 SAS URI를 확인 하 여 요청에 대 한 SAS URI 사후 전송과 관련 된 문제를 방지 합니다. 이 프로세스는 선택 사항 이지만 권장 됩니다.

- URI에는 파일 이름 확장명을 포함 하 여 VHD 이미지 파일 이름이 포함 됩니다 `.vhd` .
- `Sp=rl`이 URI의 중간에 표시됩니다. 이 문자열은 읽기 및 나열 액세스를 지정 합니다.
- `sr=c`이 표시되면 컨테이너 수준 액세스가 지정된 것입니다.
- Blob을 테스트 다운로드하려면 URI를 브라우저에 복사하여 붙여넣습니다(다운로드를 완료하기 전에 작업을 취소할 수 있음).

## <a name="next-step"></a>다음 단계

- [Azure Virtual Machine 제품 만들기](azure-vm-create-offer.md)를 참조 하세요.
