---
title: 폴링 장기 실행 작업 | Microsoft 문서
description: 이 토픽에서는 장기 실행 작업을 폴링하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 752c502268ef53d3c0575d92e75ce6a965fccd9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464983"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Azure Media Services를 사용하여 라이브 스트리밍 제공

## <a name="overview"></a>개요

Microsoft Azure Media Services는 작업(예: 채널 만들기, 시작, 중지 또는 삭제)을 시작하도록 Media Services에 요청을 보내는 API를 제공합니다. 이러한 작업은 장기 실행됩니다.

Media Services .NET SDK는 요청을 보내고 작업이 완료되기를 기다리는 API를 제공합니다(내부적으로 API는 일정 간격으로 작업 진행을 폴링함). 예를 들어 channel.Start()를 호출하면, 채널이 시작된 후 메서드가 반환됩니다. 비동기 버전: await channel.StartAsync()를 사용할 수도 있습니다(작업 기반 비동기 패턴에 대한 내용은 [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx) 참조). 작업 요청을 보낸 다음 작업이 완료될 때까지 상태에 대해 폴링하는 API를 "폴링 메서드"라고 합니다. 리치 클라이언트 애플리케이션 및/또는 상태 저장 서비스에 이 메서드 (특히 비동기 버전)를 사용하는 것이 좋습니다.

애플리케이션이 장기 실행하는 http 요청을 기다릴 수 없는 시나리오가 있으며 수동으로 작업 진행 상태를 폴링하려는 경우도 있습니다. 전형적인 예는 상태 비저장 웹 서비스와 상호작용하는 브라우저입니다. 브라우저가 채널을 만들도록 요청하면 웹 서비스는 장기 실행 작업을 시작하고 브라우저에 작업 ID를 반환합니다. 브라우저는 ID에 따라 작업 상태를 가져오도록 웹 서비스에 요청할 수 있습니다. Media Services .NET SDK는 이 시나리오에 유용한 API를 제공합니다. 이 API를 “비 폴링 메서드”라고 합니다.
“비 폴링 메서드”는 다음과 같은 명명 패턴을 사용합니다. Send*OperationName*Operation(예: SendCreateOperation). Send*OperationName*Operation 메서드는 **IOperation** 개체를 반환합니다. 반환된 개체는 작업을 추적하는 데 사용할 수 있는 정보를 포함합니다. 송신*OperationName*OperationAsync 메서드 반환 **태스크\<IOperation >** 합니다.

현재 다음 클래스에서 비 폴링 메서드를 지원합니다.  **Channel**, **StreamingEndpoint** 및 **Program**.

작업 상태에 대해 폴링하려면 **GetOperation** 메서드를 **OperationBaseCollection** 클래스에서 사용합니다. **Channel** 및 **StreamingEndpoint** 작업에 대한 작업 상태를 다음 간격으로 확인하려면, 30초를 사용합니다. **Program** 작업에 대해서는 10초를 사용합니다.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다.

## <a name="example"></a>예

다음 예제에서는 **ChannelOperations**라는 클래스를 정의합니다. 이 클래스 정의는 웹 서비스 클래스 정의 시작 지점이 될 수 있습니다. 간단히 하기 위해 다음 예제에서는 메서드의 비동기 버전을 사용합니다.

또한 이 예제에서는 클라이언트에서 이 클래스를 사용하는 방법을 보여줍니다.

### <a name="channeloperations-class-definition"></a>ChannelOperations 클래스 정의

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>The client code

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

