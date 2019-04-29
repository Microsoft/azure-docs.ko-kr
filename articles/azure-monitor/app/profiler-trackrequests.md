---
title: Azure Application Insights를 사용하여 요청을 추적하는 코드 작성 | Microsoft Docs
description: 요청에 대한 프로필을 받을 수 있도록 Application Insights를 사용하여 요청을 추적하는 코드를 작성합니다.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730857"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Application Insights를 사용하여 요청을 추적하는 코드 작성

성능 페이지에서 애플리케이션에 대한 프로필을 보려면 Azure Application Insights에서 애플리케이션에 대한 요청을 추적해야 합니다. Application Insights는 이미 계측된 프레임워크에 빌드된 애플리케이션에 대한 요청을 자동으로 추적할 수 있습니다. 두 가지 예는 ASP.NET 및 ASP.NET Core입니다. 

Azure 클라우드 서비스 작업자 역할 및 Service Fabric 상태 비저장 API 등의 다른 애플리케이션의 경우, 요청이 시작되고 끝나는 위치를 Application Insights에 알려주는 코드를 작성해야 합니다. 이 코드를 작성한 후 요청 원격 분석이 Application Insights로 전송됩니다. 성능 페이지에서 원격 분석을 볼 수 있으며 해당 요청에 대해 프로필이 수집됩니다. 

요청을 수동으로 추적하려면 다음을 수행합니다.

  1. 애플리케이션 수명 초기에 다음 코드를 추가합니다.  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      이 전역 계측 키 구성에 대한 자세한 내용은 [Application Insights를 통해 Service Fabric 사용](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)을 참조하세요.  

  1. 계측하려는 코드 조각의 경우 다음 예제와 같이 `StartOperation<RequestTelemetry>` **using** 문을 추가합니다.

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        다른 `StartOperation<RequestTelemetry>` 범위 내의 `StartOperation<RequestTelemetry>` 호출은 지원되지 않습니다. 대신, 중첩된 범위에서는 `StartOperation<DependencyTelemetry>`를 사용할 수 있습니다. 예를 들면 다음과 같습니다.  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
