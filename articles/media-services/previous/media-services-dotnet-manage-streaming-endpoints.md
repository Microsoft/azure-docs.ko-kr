---
title: .NET SDK를 사용하여 스트리밍 엔드포인트를 관리합니다. | Microsoft Docs
description: 이 문서에서는 Azure Portal를 사용 하 여 스트리밍 끝점을 관리 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: c0eceff6ba371cbdd01805a38c56dcc09f0887f9
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266009"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>.NET SDK를 사용하여 스트리밍 엔드포인트 관리

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

>[!NOTE]
>[개요](media-services-streaming-endpoints-overview.md) 문서를 검토해야 합니다. 또한 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)도 살펴보세요.

이 문서의 코드는 Azure Media Services .NET SDK를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

- 기본 스트리밍 엔드포인트를 살펴봅니다.
- 새 스트리밍 엔드포인트를 만들거나 추가합니다.

    다른 CDN 및 직접 액세스를 사용하려는 경우 여러 스트리밍 엔드포인트를 배치하는 것이 좋습니다.

    > [!NOTE]
    > 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.
    
- 스트리밍 엔드포인트를 업데이트합니다.
    
    Update() 함수를 호출해야 합니다.

- 스트리밍 엔드포인트를 삭제합니다.

    >[!NOTE]
    >기본 스트리밍 엔드포인트는 삭제할 수 없습니다.

스트리밍 엔드포인트의 크기를 조정하는 방법에 대한 자세한 내용은 [이 문서](media-services-portal-scale-streaming-endpoints.md)를 참조하세요.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

개발 환경을 설정 하 고 [.net을 사용한 Media Services 개발](media-services-dotnet-how-to-use.md)에 설명 된 대로 연결 정보를 사용 하 여 app.config 파일을 채웁니다. 

## <a name="add-code-that-manages-streaming-endpoints"></a>스트리밍 엔드포인트를 관리하는 코드 추가
    
Program.cs의 코드를 다음 코드로 바꿉니다.

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
    class Program
    {
        // Read values from the App.config file.

        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
