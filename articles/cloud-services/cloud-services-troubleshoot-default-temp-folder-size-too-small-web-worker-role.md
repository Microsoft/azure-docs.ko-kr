---
title: 역할에 대한 기본 TEMP 폴더 크기가 너무 작음 | Microsoft Docs
description: 클라우드 서비스 역할에는 TEMP 폴더에 대한 공간이 제한되어 있습니다. 이 문서에서는 공간 부족을 방지하는 방법을 몇 가지 제안합니다.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7862e4d5c4dd603dacf5784df6c4194392ebc351
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652184"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>클라우드 서비스 웹/작업자 역할에서 기본 TEMP 폴더 크기가 너무 작습니다.
클라우드 서비스 작업자 또는 웹 역할의 기본 임시 디렉터리에는 최대 100MB 크기의 공간이 있으며 특정 시점에 꽉 찰 수도 있습니다. 이 문서는 임시 디렉터리에 대한 공간 부족을 방지하는 방법을 설명합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>공간이 부족한 이유는 무엇인가요?
표준 Windows 환경 변수 TEMP 및 TMP는 애플리케이션에서 실행되는 코드에 사용할 수 있습니다. TEMP와 TMP는 모두 최대 크기가 100MB인 단일 디렉터리를 가리킵니다. 이 디렉터리에 저장된 모든 데이터는 클라우드 서비스의 수명 주기 전체에 지속되지 않습니다. 클라우드 서비스의 역할 인스턴스가 재활용되면 디렉터리가 지워집니다.

## <a name="suggestion-to-fix-the-problem"></a>문제를 해결하려는 제안
다음 대안 중 하나를 구현합니다.

* 로컬 저장소 리소스를 구성하고 TEMP 또는 TMP를 사용하는 대신 직접 액세스합니다. 애플리케이션 내에서 실행되는 코드에서 로컬 스토리지 리소스에 액세스하려면 [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) 메서드를 호출합니다.
* 로컬 저장소 리소스를 구성하고 TEMP 및 TMP 디렉터리를 카리켜서 로컬 저장소 리소스의 경로를 가리키도록 합니다. 이 수정 작업은 [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) 메서드 내에서 수행되어야 합니다.

다음 코드 예제에서는 OnStart 메서드 내에서 TEMP 및 TMP에 대한 대상 디렉터리를 수정하는 방법을 보여줍니다.

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
[Azure 웹 역할 ASP.NET 임시 폴더의 크기를 늘리는 방법](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)을 설명하는 블로그를 읽으세요.

클라우드 서비스에 대한 [문제해결 문서](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) 를 더 봅니다.

Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결하는 방법을 알아보려면 [Kevin Williamson의 블로그 시리즈](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)를 참조하세요.
