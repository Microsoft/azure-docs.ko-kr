---
title: Azure Application Insights를 사용하는 OpenCensus Go 추적 | Microsoft Docs
description: OpenCensus Go 추적을 로컬 전달자 및 Application Insights와 통합하는 지침을 제공합니다.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577856"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Go(미리 보기)에서 분산 추적 수집

이제 Application Insights는 [OpenCensus](https://opencensus.io) 및 새로운 [로컬 전달자](./opencensus-local-forwarder.md)와의 통합을 통해 Go 애플리케이션의 분산 추적을 지원합니다. 이 문서에서는 Go에 대해 OpenCensus를 설정하고 추적 데이터를 Application Insights로 가져오는 프로세스를 단계별로 안내합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 필요합니다.
- Go를 설치해야 합니다. 이 문서에서는 버전 1.11 [Go 다운로드](https://golang.org/dl/)를 사용합니다.
- 지침에 따라 [로컬 전달자를 Windows 서비스로](./opencensus-local-forwarder.md) 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-application-insights-resource"></a>Application Insights 리소스 만들기

먼저 계측 키(ikey)를 생성할 Application Insights 리소스를 만들어야 합니다. 그런 다음, ikey를 사용하여 OpenCensus 계측 애플리케이션에서 Application Insights로 분산 추적을 보내도록 로컬 전달자를 구성합니다.   

1. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Application Insights 리소스 추가](./media/opencensus-Go/0001-create-resource.png)

   구성 상자가 표시되면 다음 표를 사용하여 입력 필드를 채웁니다.

    | 설정        | 값           | 설명  |
   | ------------- |:-------------|:-----|
   | **Name**      | 전역적으로 고유한 값 | 모니터링하는 응용 프로그램을 식별하는 이름입니다. |
   | **애플리케이션 유형** | 일반 | 모니터링하는 응용 프로그램의 유형입니다. |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스팅할 새 리소스 그룹의 이름입니다. |
   | **위치**: | 미국 동부 | 가까운 위치 또는 응용 프로그램이 호스팅되는 위치 근처를 선택합니다. |

2. **만들기**를 클릭합니다.

## <a name="configure-local-forwarder"></a>로컬 전달자 구성

1. **개요** > **기본 정보**를 차례로 선택하고, 애플리케이션의 **계측 키**를 복사합니다.

   ![계측 키 스크린샷](./media/opencensus-Go/0003-instrumentation-key.png)

2. `LocalForwarder.config` 파일을 편집하고 계측 키를 추가합니다. [필수 조건](./opencensus-local-forwarder.md)의 지침을 따른 경우 이 파일은 `C:\LF-WindowsServiceHost`에 있습니다.

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. 애플리케이션 **로컬 전달자** 서비스를 다시 시작합니다.

## <a name="opencensus-go-packages"></a>OpenCensus Go 패키지

1. 명령줄에서 Go용 OpenCensus 패키지를 설치합니다.

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. 다음 코드를 .go 파일에 추가하고 빌드한 후 실행합니다. 이 예제는 로컬 전달자와의 통합을 지원하는 코드를 추가하여 공식 OpenCensus 지침에서 파생된 것입니다.

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. 간단한 Go 앱이 실행되면 `http://localhost:50030`으로 이동합니다. 브라우저를 새로 고칠 때마다 텍스트 “hello world”와 함께 로컬 전달자에서 선택된 해당 범위 데이터가 생성됩니다.

4. **로컬 전달자**가 추적을 선택하는지 확인하려면 `LocalForwarder.config` 파일을 확인합니다. [필수 구성 요소](https://docs.microsoft.com/azure/application-insights/local-forwarder)의 단계를 따른 경우 `C:\LF-WindowsServiceHost`에 배치됩니다.

    로그 파일의 아래 이미지에서 내보내기를 추가한 두 번째 스크립트를 실행하기 전에 `OpenCensus input BatchesReceived`가 0인 것을 확인할 수 있습니다. 업데이트된 스크립트 실행을 시작했을 때 `BatchesReceived`가 입력한 값의 수와 동일하게 증가했습니다.
    
    ![새로운 App Insights 리소스 형식](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 시작

1. 이제 Azure Portal에서 Application Insights **개요** 페이지를 다시 열어 현재 실행 중인 애플리케이션에 대한 세부 정보를 볼 수 있습니다. **라이브 메트릭 스트림**을 선택합니다.

   ![빨간색 상자에서 라이브 메트릭 스트림이 선택된 개요 창의 스크린샷](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. 두 번째 Go 앱을 다시 실행하고 `http://localhost:50030`에 대한 브라우저를 새로 고치기 시작할 경우 로컬 전달자 서비스에서 Application Insights에 도착하는 대로 라이브 추적 데이터가 표시됩니다.

   ![성능 데이터가 표시된 라이브 메트릭 스트림의 스크린샷](./media/opencensus-go/0006-stream.png)

3. **개요** 페이지로 다시 이동하고 종속성 관계의 시각적 개체 레이아웃에 대한 **애플리케이션 맵**을 선택하고 애플리케이션 구성 요소 간의 타이밍을 호출합니다.

    ![기본 애플리케이션 맵의 스크린샷](./media/opencensus-go/0007-application-map.png)

    하나의 메서드 호출만 추적했으므로 애플리케이션 맵은 흥미롭지 않습니다. 하지만 애플리케이션은 훨씬 분산된 애플리케이션을 시각화하도록 확장될 수 있습니다.

   ![애플리케이션 맵](media/opencensus-go/application-map.png)

4. **성능 조사**를 선택하여 자세한 성능 분석을 수행하고 성능 저하의 근본 원인을 확인합니다.

    ![성능 창의 스크린샷](./media/opencensus-go/0008-performance.png)

5. **샘플**을 선택한 다음, 오른쪽 창에 표시되는 샘플 중 하나를 클릭하면 종단 간 트랜잭션 세부 정보 환경이 시작됩니다. 샘플 앱은 단일 이벤트만을 표시하지만 더 복잡한 애플리케이션을 통해 개별 이벤트의 호출 스택의 수준까지 종단 간 트랜잭션을 탐색할 수 있습니다.

     ![종단 간 트랜잭션 인터페이스의 스크린샷](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Go용 OpenCensus 추적

Go용 OpenCensus를 로컬 전달자 및 Application Insights에 통합하는 기본 사항만을 다루었습니다. [공식 OpenCensus Go 사용 지침](https://godoc.org/go.opencensus.io)에서는 더 고급 항목을 다룹니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [종단 간 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)
