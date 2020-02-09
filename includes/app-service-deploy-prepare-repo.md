---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836746"
---
## <a name="prepare-your-repository"></a>리포지토리 준비

Azure App Service Kudu 빌드 서버에서 자동 빌드를 가져오려고 하는지는 리포지토리 루트에 있는 프로젝트에 올바른 파일을 확인 합니다.

| 런타임 | 루트 디렉터리 파일 |
|-|-|
| ASP.NET(Windows만 해당) | _*.sln_, _*.csproj_ 또는 _default.aspx_ |
| ASP.NET Core | _*.sln_ 또는 _*.csproj_ |
| PHP | _index.php_ |
| Ruby(Linux만 해당) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ 또는 _package.json_(시작 스크립트 포함) |
| Python | _\*.py_, _requirements.txt_ 또는 _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ 또는 _iisstart.htm_ |
| 웹 작업 | _\<job_name > / r u n. \<확장 >_ 아래에서 _앱\_데이터/작업/continuous_ 연속 Webjob에 대한 나 _앱\_데이터/작업/triggered_ 트리거된에 대한 WebJobs 합니다. 자세한 내용은 [Kudu WebJobs 설명서](https://github.com/projectkudu/kudu/wiki/WebJobs)합니다. |
| 함수 | [Azure Functions에 대한 연속 배포](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)를 참조하세요. |

배포를 사용자 지정하려면 리포지토리 루트에 *.deployment* 파일을 포함합니다. 자세한 내용은 [배포를 사용자 지정](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) 하 고 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)합니다.

> [!NOTE]
> Visual Studio에서 개발하는 경우 [Visual Studio에서 자동으로 리포지토리를 만들도록](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) 합니다. 프로젝트 바로 Git를 사용 하 여 배포할 준비가 되었습니다.
>

