---
title: Azure Media Services를 사용한 비디오 파일 스트림 - .NET | Microsoft Docs
description: 이 자습서의 단계에 따라 새로운 Azure Media Services 계정을 만들고, 파일을 인코딩한 다음, Azure Media Player로 스트리밍합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, 스트림
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: ec60f775fbeb176f9442aff11117c85c5028a81f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267091"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 자습서는 Azure Media Services를 사용하여 다양한 브라우저 및 디바이스에서 비디오 스트리밍을 인코딩하고 시작하는 것이 얼마나 쉬운지 보여줍니다. 입력 내용은 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일 경로를 사용하여 지정할 수 있습니다.
이 항목의 샘플에서는 콘텐츠를 인코딩하여 HTTPS URL을 통해 액세스할 수 있게 만듭니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

자습서가 끝나면 비디오를 스트리밍할 수 있습니다.  

![비디오 재생](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)을 사용할 수 있습니다.
- [Media Services 계정 만들기](./create-account-howto.md)<br/>리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
- [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

샘플은 [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) 폴더에 있습니다.

다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) 파일을 엽니다. 값을 [API 액세스](./access-api-howto.md)에서 가져온 자격 증명으로 바꿉니다.

샘플은 다음 작업을 수행합니다.

1. **변환**을 만듭니다(먼저 지정된 변환이 있는지 확인합니다). 
2. 인코딩 **작업**의 출력으로 사용되는 출력 **자산**을 만듭니다.
3. HTTPS URL을 기반으로 하는 **작업**의 입력을 만듭니다.
4. 앞서 만든 입력 및 출력을 사용하여 인코딩 **작업**을 제출합니다.
5. 작업의 상태를 확인합니다.
6. **스트리밍 로케이터**를 만듭니다.
7. 스트리밍 URL을 빌드합니다.

샘플의 각 기능이 무엇을 하는지에 관한 설명은 코드를 검토하고 [이 소스 파일 ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 주석을 확인합니다.

## <a name="run-the-sample-app"></a>샘플 앱 실행

앱을 실행하면 다른 프로토콜을 사용하여 비디오를 재생하는 데 사용할 수 있는 URL이 표시됩니다. 

1. Ctrl+F5를 눌러서 *EncodeAndStreamFiles* 애플리케이션을 실행합니다.
2. Apple의 **HLS** 프로토콜(*manifest(format=m3u8-aapl)* 로 끝남)을 선택하고 콘솔에서 스트리밍 URL을 복사합니다.

![출력](./media/stream-files-tutorial-with-api/output.png)

샘플의 [소스 코드](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)에서 URL이 빌드된 방법을 볼 수 있습니다. 그것을 빌드하려면 스트리밍 엔드 포인트의 호스트 이름 및 스트리밍 로케이터 경로를 연결해야 합니다.  

## <a name="test-with-azure-media-player"></a>Azure Media Player로 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. 애플리케이션을 실행할 때 얻은 URL 값 중 하나를 **URL:** 상자에 붙여넣습니다. 
 
     URL을 HLS, Dash 또는 부드러운 스트리밍 형식으로 붙여넣을 수 있으며, Azure Media Player는 디바이스에서 재생하기 위한 적절한 스트리밍 프로토콜로 자동으로 전환합니다.
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 위해 만든 Media Services 및 스토리지 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>코드 검사

샘플의 각 기능이 무엇을 하는지에 관한 설명은 코드를 검토하고 [이 소스 파일 ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 주석을 확인합니다.

[파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 자습서는 고급 스트리밍 예제를 자세한 설명을 곁들여 제공합니다. 

### <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 애플리케이션으로 작업하는 경우, 스레드마다 새로운 AzureMediaServicesClient 개체를 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
