---
title: 웹 사이트에서 ReportViewer 사용 | Microsoft Docs
description: 이 항목에서는 Microsoft Azure Virtual Machine에 저장된 보고서를 표시하는 Visual Studio ReportViewer 컨트롤을 사용하여 Microsoft Azure 웹 사이트를 빌드하는 방법에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580071"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Azure에서 호스트되는 웹 사이트에서 ReportViewer 사용
> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

Microsoft Azure Virtual Machine에 저장된 보고서를 표시하는 Visual Studio ReportViewer 컨트롤과 함께 Microsoft Azure 웹 사이트를 빌드할 수 있습니다. ReportViewer 컨트롤은 ASP.NET 웹 애플리케이션 템플릿을 사용하여 빌드하는 웹 애플리케이션에 있습니다.

> [!IMPORTANT]
> ASP.NET MVC 웹 애플리케이션 템플릿은 ReportViewer 컨트롤을 지원하지 않습니다.

Microsoft Azure 웹 사이트에 ReportViewer를 통합하려면 다음 작업을 완료해야 합니다.

* **추가** 
* **구성** 
* Azure에 ASP.NET 웹 애플리케이션을 **게시**

## <a name="prerequisites"></a>필수 조건
[Azure Virtual Machines의 SQL Server Business Intelligence](../classic/ps-sql-bi.md)에서 "일반 권장 사항 및 모범 사례" 섹션을 검토합니다.

> [!NOTE]
> ReportViewer 컨트롤은 Visual Studio Standard Edition 이상 버전과 함께 제공됩니다. Web Developer Express Edition을 사용하려면 [MICROSOFT REPORT VIEWER 2012 런타임](https://www.microsoft.com/download/details.aspx?id=35747) 을 설치하여 ReportViewer 런타임 기능을 사용해야 합니다.
>
> 로컬 처리 모드에서 구성된 ReportViewer는 Microsoft Azure에서 지원되지 않습니다.

## <a name="adding-assemblies-to-the-deployment-package"></a>배포 패키지에 어셈블리 추가
ASP.NET 애플리케이션 온-프레미스를 호스트할 때 ReportViewer 어셈블리는 Visual Studio를 설치하는 동안 IIS 서버의 GAC(전역 어셈블리 캐시)에 일반적으로 직접 설치되고 애플리케이션에서 직접 액세스할 수 있습니다. 그러나 클라우드에서 ASP.NET 애플리케이션을 호스트할 때는 Microsoft Azure가 GAC에 어떤 것도 설치할 수 없도록 하기 때문에 ReportViewer 어셈블리가 애플리케이션에 대해 로컬로 사용 가능한지 확인해야 합니다. 프로젝트에서 ReportViewer 어셈블리에 대한 참조를 추가하여 이 작업을 수행할 수 있으며 로컬로 복사하여 구성합니다.

원격 처리 모드에서 ReportViewer 컨트롤은 다음 어셈블리를 사용합니다.

* **Microsoft.ReportViewer.WebForms.dll**: 페이지에 ReportViewer를 사용하는 데 필요한 ReportViewer 코드를 포함합니다. 프로젝트의 ASP.NET 페이지로 ReportViewer 컨트롤을 끌어 놓으면 이 어셈블리에 대한 참조가 프로젝트에 추가됩니다.
* **Microsoft.ReportViewer.Common.dll**: 런타임에 ReportViewer 컨트롤에서 사용하는 클래스를 포함합니다. 프로젝트에 자동으로 추가되지 않습니다.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Microsoft.ReportViewer.Common에 대한 참조를 추가하려면
* 프로젝트의 **참조** 노드를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택한 다음 .NET 탭에서 어셈블리를 선택하고 **확인**을 클릭합니다.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>ASP.NET 애플리케이션에서 어셈블리를 로컬로 액세스할 수 있도록 하려면
1. **참조** 폴더에서 Microsoft.ReportViewer.Common 어셈블리를 클릭하여 해당 속성을 속성 창에 표시합니다.
2. 속성 창에서 **로컬 복사** 를 True로 설정합니다.
3. Microsoft.ReportViewer.WebForms에 대해 1단계 및 2단계를 반복합니다.

### <a name="to-get-reportviewer-language-pack"></a>ReportViewer 언어 팩을 가져오려면
1. [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?LinkId=317386)에서 적절한 Microsoft Report Viewer 2012 런타임 재배포 가능 패키지를 설치합니다.
2. 드롭다운 목록에서 언어를 선택하면 페이지가 해당 다운로드 센터 페이지로 리디렉션됩니다.
3. **다운로드** 를 클릭하여 ReportViewerLP.exe의 다운로드를 시작합니다.
4. ReportViewerLP.exe를 다운로드한 후 **실행**을 클릭하여 즉시 설치하거나 **저장**을 클릭하여 컴퓨터에 저장합니다. **저장**을 클릭하는 경우 파일을 저장하는 폴더의 이름을 기억해 두세요.
5. 파일을 저장한 폴더를 찾습니다. ReportViewerLP.exe를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭한 다음 **예**를 클릭합니다.
6. ReportViewerLP.exe를 실행하면 c:\windows\assembly에서 리소스 파일 **Microsoft.ReportViewer.Webforms.Resources** 및 **Microsoft.ReportViewer.Common.Resources**를 볼 수 있습니다.

### <a name="to-configure-for-localized-reportviewer-control"></a>지역화된 ReportViewer 컨트롤을 구성하려면
1. 위에서 지정한 지침에 따라 Microsoft Report Viewer 2012 런타임 재배포 가능 패키지를 다운로드하여 설치합니다.
2. 프로젝트에 \<language\> 폴더를 만들어 관련 리소스 어셈블리 파일을 복사합니다. 복사할 리소스 어셈블리 파일은 **Microsoft.ReportViewer.Webforms.Resources.dll** 및 **Microsoft.ReportViewer.Common.Resources.dll**입니다. 리소스 어셈블리 파일을 선택하고 [속성] 창에서 **출력 디렉터리에 복사**를 “**항상 복사**”로 설정합니다.
3. 웹 프로젝트의 문화권 및 UI 문화권을 설정합니다. ASP.NET 웹 페이지의 문화권 및 UI 문화권을 설정하는 방법에 대한 자세한 내용은 [방법: ASP.NET 웹 페이지 세계화를 위해 문화권 및 UI 문화권 설정](https://go.microsoft.com/fwlink/?LinkId=237461)을 참조하세요.

## <a name="configuring-authentication-and-authorization"></a>인증 및 권한 부여 구성
ReportViewer는 적절한 자격 증명을 사용하여 보고서 서버로 인증해야 하고 원하는 보고서에 액세스하는 보고서 서버에서 자격 증명의 권한을 부여해야 합니다. 인증에 대한 자세한 내용은 [Reporting Services 보고서 뷰어 컨트롤 및 Microsoft Azure 가상 머신 기반 보고서 서버](https://msdn.microsoft.com/library/azure/dn753698.aspx)백서를 참조하세요.

## <a name="publish-the-aspnet-web-application-to-azure"></a>Azure에 ASP.NET 웹 애플리케이션 게시
Azure에 ASP.NET 웹 애플리케이션을 게시하는 방법에 대한 지침은 [방법: Visual Studio에서 Azure에 웹 애플리케이션 마이그레이션 및 게시](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) 및 [Web Apps 및 ASP.NET 시작](../../../app-service/app-service-web-get-started-dotnet.md)을 참조하세요.

> [!IMPORTANT]
> Azure 배포 프로젝트 추가 또는 Azure 클라우드 서비스 프로젝트 추가 명령이 솔루션 탐색기의 바로 가기 메뉴에 나타나지 않으면 프로젝트에 대한 대상 프레임워크를 .NET Framework 4로 변경해야 할 수 있습니다.
>
> 두 명령은 근본적으로 동일한 기능을 제공합니다. 설치한 Microsoft Azure SDK의 버전에 따라 바로 가기 메뉴에 두 명령 중 하나가 표시됩니다.
>
>

## <a name="resources"></a>리소스
[Microsoft 보고서](https://go.microsoft.com/fwlink/?LinkId=205399)

[Azure Virtual Machines의 SQL Server Business Intelligence](../classic/ps-sql-bi.md)

[PowerShell을 사용하여 기본 모드 보고서 서버로 Azure VM 만들기](../classic/ps-sql-report.md)
