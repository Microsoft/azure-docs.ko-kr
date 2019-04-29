---
title: Azure Portal을 사용하여 Azure Data Box Edge 문제 해결 | Microsoft Docs
description: Azure Data Box Edge 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: 3a8d1f93930d2b298eeb7d10a73624b9a19bcc0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756204"
---
# <a name="troubleshoot-your-azure-data-box-edge-issues"></a>Azure Data Box Edge 문제 해결 

이 문서에서는 Azure Data Box Edge에서 문제를 해결하는 방법을 설명합니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 진단 실행
> * 지원 패키지 수집
> * 로그를 사용하여 문제 해결


## <a name="run-diagnostics"></a>진단 실행

디바이스 오류를 진단하고 해결하기 위해 진단 테스트를 실행합니다. 진단 테스트를 실행하려면 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **문제 해결 > 진단 테스트**로 이동합니다. 실행할 테스트를 선택하고 **테스트 실행**을 클릭합니다. 그러면 네트워크, 디바이스, 웹 프록시, 시간 또는 클라우드 설정에서 문제가 될만한 내용을 진단하는 테스트가 시작됩니다. 디바이스에서 테스트가 실행 중이라는 알림이 표시됩니다.

    ![테스트 선택](media/data-box-edge-troubleshoot/run-diag-1.png)
 
2. 테스트가 완료된 후 결과가 표시됩니다. 

    ![테스트 결과 검토](media/data-box-edge-troubleshoot/run-diag-2.png)

    테스트가 실패하면 권장 조치에 대한 URL이 표시됩니다. URL을 클릭하면 권장 조치를 볼 수 있습니다.
 
    ![실패한 테스트에 대한 경고 살펴보기](media/data-box-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>지원 패키지 수집

로크 패키지는 Microsoft 지원에서 디바이스 문제를 해결하는 데 도움을 줄 수 있는 모든 관련 로그로 구성됩니다. 로그 패키지는 로컬 웹 UI를 통해 생성할 수 있습니다.

지원 패키지를 수집하려면 다음 단계를 수행합니다. 

1. 로컬 웹 UI에서 **문제 해결 > 지원**으로 이동합니다. **지원 패키지 만들기**를 클릭합니다. 시스템이 지원 패키지를 수집하기 시작합니다. 패키지 수집에는 몇 분 정도 걸릴 수 있습니다.

    ![사용자 추가 클릭](media/data-box-edge-troubleshoot/collect-logs-1.png)
 
2. 지원 패키지가 생성된 후에는 **지원 패키지 다운로드**를 클릭합니다. 압축된 패키지가 선택한 경로에 다운로드됩니다. 패키지의 압축을 풀면 시스템 로그 파일을 볼 수 있습니다.

    ![사용자 추가 클릭](media/data-box-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>로그를 사용하여 문제 해결

업로드 및 새로 고침 프로세스 중에 발생한 모든 오류는 해당 오류 파일에 포함됩니다.

1. 오류 파일을 보려면 공유로 이동하여 공유를 클릭하고 콘텐츠를 보십시오. 

      ![공유 콘텐츠 연결 및 확인](media/data-box-edge-troubleshoot/troubleshoot-logs-1.png)

2. _Microsoft Data Box Edge 폴더_를 클릭합니다. 이 폴더에는 두 개의 하위 폴더가 있습니다.

    - 업로드 오류에 대한 로그 파일이 있는 Upload 폴더
    - 새로 고침 중 오류를 위한 Refresh 폴더

    다음은 새로 고침에 대한 샘플 로그 파일입니다.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 이 파일에 오류가 표시되면(샘플에 강조 표시됨), 오류 코드를 적어두십시오. 이 경우에는 16001입니다. 다음 오류 참조에서 이 오류 코드에 대한 설명을 찾아봅니다.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>다음 단계

- [이 릴리스의 알려진 문제](data-box-gateway-release-notes.md)에 대해 알아봅니다.
