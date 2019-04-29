---
title: Microsoft Azure 기반 VM 이미지에 대한 공유 액세스 서명 URI 가져오기 | Microsoft Docs
description: VM 이미지에 대한 SAS(공유 액세스 서명) URI를 가져오는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c21fa3cf819f48dcda46f2d444ed52bc2eb9ae3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744045"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM 이미지에 대한 공유 액세스 서명 URI 가져오기

게시 프로세스 중에 SKU와 연결된 각 VHD(가상 하드 디스크)에 대한 URI(Uniform Resource Identifier)를 제공해야 합니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 이 문서에서는 각 VHD에 대한 SAS(공유 액세스 서명) URI를 생성하는 방법에 대해 설명합니다. 이 URI는 Cloud 파트너 포털의 **SKU** 탭에 입력합니다. 

VHD에 대한 SAS URI를 생성하는 경우 준수해야 하는 요구 사항은 다음과 같습니다.

- 관리되지 않는 VHD만 지원됩니다.
- `List` 및 `Read` 권한으로 충분합니다. `Write` 또는 `Delete` 액세스는 제공하지 *않습니다*.
- 액세스 기간(*만료 날짜*)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
- UTC 시간 변동을 방지하려면 시작 날짜를 현재 날짜보다 하루 전의 날짜로 설정합니다. 예를 들어, 현재 날짜가 2014년 10월 6일이면 2014년 10월 5일을 선택합니다.

## <a name="generate-the-sas-url"></a>SAS URL 생성

SAS URL은 다음 도구를 사용하여 일반적인 두 가지 방법으로 생성할 수 있습니다.

-   Microsoft Storage 탐색기 - Windows, macOS 및 Linux에서 사용할 수 있는 그래픽 도구
-   Microsoft Azure CLI - 비 Windows OS 및 자동 또는 지속적인 통합 환경에 권장됨


### <a name="azure-cli"></a>Azure CLI

다음 단계를 사용하여 Azure CLI를 통해 SAS URI를 생성합니다.

1. [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)를 다운로드하고 설치합니다.  버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다. 
2. PowerShell 파일(`.ps1` 파일 확장명)을 만들고, 다음 코드를 복사한 다음, 로컬로 저장합니다.

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. 파일을 편집하여 다음 매개 변수 값을 제공합니다.  날짜는 UTC 날짜/시간 형식(예: `10-25-2016T00:00:00Z`)으로 제공해야 합니다.
   - `<account-name>` -Azure 저장소 계정 이름
   - `<account-key>` - Azure 저장소 계정 키
   - `<vhd-name>` - VHD 이름
   - `<start-date>` - VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다. 
   - `<expiry-date>` - VHD 액세스 권한의 만료 날짜입니다.  현재 날짜보다 최소 3주 후의 날짜를 제공합니다. 
 
   다음 예제에서는 적절한 매개 변수 값을 보여 줍니다(이 문서의 작성 시점 기준).

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. 이 PowerShell 스크립트의 변경 내용을 저장합니다.
5. 이 스크립트를 관리자 권한으로 실행하여 컨테이너 수준 액세스를 위한 *SAS 연결 문자열*을 생성합니다.  다음 두 가지 기본 방법을 사용할 수 있습니다.
   - 콘솔에서 스크립트를 실행합니다.  예를 들어 Windows에서 스크립트에서 쓰기 클릭(write-click)을 수행하고, **관리자 권한으로 실행**을 선택합니다.
   - PowerShell 스크립트 편집기(예: [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise))에서 스크립트를 관리자 권한으로 실행합니다. 
     다음 예제에서는 이 편집기 내에서 생성된 SAS 연결 문자열을 보여 줍니다. 

     ![PowerShell ISE에서 SAS URI 생성](./media/publishvm_032.png)

6. 결과 SAS 연결 문자열을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다.  최종 SAS URI를 만들기 위해 연결되는 VHD 위치 정보를 추가하도록 이 문자열을 편집합니다. 
7. Azure Portal에서 새로 생성된 URI와 연결되는 VHD가 포함된 Blob Storage로 이동합니다.
8. 아래와 같이 **Blob 서비스 엔드포인트**의 URL 값을 복사합니다.

    ![Azure Portal의 Blob 서비스 엔드포인트](./media/publishvm_033.png)

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다.  다음 형식을 사용하여 전체 SAS URI를 구성합니다.

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    예를 들어 VDH의 이름이 `TestRGVM2.vhd`이면 결과 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

게시하려는 SKU의 각 VHD에 대해 이러한 단계를 반복합니다.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

다음 단계를 사용하여 따라 Microsoft Azure Storage 탐색기를 통해 SAS URI를 생성합니다.

1. [Microsoft Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 다운로드하고 설치합니다.
2. 탐색기를 열고, 왼쪽 메뉴 모음에서 **계정 추가** 아이콘을 클릭합니다.  **Azure Storage에 연결** 대화 상자가 표시됩니다.
3. **Azure 계정 추가**를 선택하고 **로그인**을 클릭합니다.  Azure 계정에 로그인하는 데 필요한 단계를 계속 수행합니다.
4. 왼쪽의 **탐색기** 창에서 **저장소 계정**으로 이동하고, 이 노드를 펼칩니다.
5. VHD를 마우스 오른쪽 단추로 클릭하고, 상황에 맞는 메뉴에서 **공유 액세스 서명 가져오기**를 선택합니다. 

    ![Azure 탐색기에서 SAS 항목 가져오기](./media/publishvm_034.png)

6. **공유 액세스 서명** 대화 상자가 표시됩니다. 다음 필드에 대한 값을 입력합니다.
   - **시작 시간** - VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
   - **만료 시간** - VHD 액세스 권한의 만료 날짜입니다.  현재 날짜보다 최소 3주 후의 날짜를 제공합니다.
   - **권한** - `Read` 및 `List` 권한을 선택합니다. 

     ![Azure 탐색기의 SAS 대화 상자](./media/publishvm_035.png)

7. **만들기**를 클릭하여 이 VHD에 연결되는 SAS URI를 만듭니다.  이제 대화 상자에는 이 작업에 대한 세부 정보가 표시됩니다. 
8. **URL** 값을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 

    ![Azure 탐색기에서 SAS URI 만들기](./media/publishvm_036.png)

    이 생성된 SAS URL은 컨테이너 수준 액세스를 위한 것입니다.  구체적으로 만들려면 연결된 VHD 이름을 추가해야 합니다.

9. 텍스트 파일을 편집합니다. SAS URL의 `vhds` 문자열 뒤에 VHD 이름을 삽입합니다(선행 슬래시 포함).  최종 SAS URI는 다음과 같은 형식이어야 합니다.

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    예를 들어 VDH의 이름이 `TestRGVM2.vhd`이면 결과 SAS URI는 다음과 같습니다.

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

게시하려는 SKU의 각 VHD에 대해 이러한 단계를 반복합니다.


## <a name="verify-the-sas-uri"></a>SAS URI 확인

다음 검사 목록을 사용하여 생성된 각 SAS URI를 검토하고 확인합니다.  다음 사항을 확인합니다.
- URI의 형식은 `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`입니다.
- URI에는 ".vhd" 파일 이름 확장명이 있는 VHD 이미지 파일 이름이 포함됩니다.
- URI 중간에 `sp=rl`이 나타납니다. 이 문자열은 `Read` 및 `List` 액세스 권한이 지정되었음을 나타냅니다.
- 이후에는 `sr=c`도 나타납니다. 이 문자열은 컨테이너 수준 액세스 권한이 지정되었음을 나타냅니다.
- 연결된 Blob을 다운로드하려면 URI를 복사하여 브라우저에 붙여넣습니다.  (다운로드가 완료되기 전에 작업을 취소할 수 있습니다.)


## <a name="next-steps"></a>다음 단계

SAS URI를 생성하는 데 어려움이 있는 경우 [일반적인 SAS URL 문제](./cpp-common-sas-url-issues.md)를 참조하세요.  그렇지 않으면 나중에 사용할 수 있도록 안전한 위치에 SAS URI를 저장합니다. 이는 Cloud 파트너 포털에서 [VM 제안](./cpp-publish-offer.md)을 게시하는 데 필요합니다.
