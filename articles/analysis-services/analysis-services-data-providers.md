---
title: Azure Analysis Services에 연결하는 데 필요한 클라이언트 라이브러리 | Microsoft Docs
description: 클라이언트 애플리케이션 및 도구에서 Azure Analysis Services를 연결하는 데 필요한 클라이언트 라이브러리에 대해 설명합니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 334859aecf3a5f7a5678c0af656c6837d5661dac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023516"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services에 연결하기 위한 클라이언트 라이브러리

클라이언트 라이브러리는 클라이언트 애플리케이션 및 도구에서 Analysis Services 서버에 연결하는 데 필요합니다. Microsoft 클라이언트 응용 프로그램 같은 Power BI Desktop, Excel, SQL Server Management Studio (SSMS) 및 SQL Server Data Tools (SSDT) 세 클라이언트 라이브러리를 설치 하 고 일반 응용 프로그램 업데이트와 함께 업데이트 합니다. 경우에 따라 최신 버전의 클라이언트 라이브러리를 설치 해야 합니다. 사용자 지정 클라이언트 응용 프로그램 클라이언트 라이브러리가 설치 되어 있어야 합니다.

## <a name="download-the-latest-client-libraries-windows-installer"></a>최신 클라이언트 라이브러리 다운로드(Windows Installer)  

|다운로드  |제품 버전  | 
|---------|---------|
|[MSOLAP(amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.15.26    |
|[MSOLAP(x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.15.26      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.17.1.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.17.1.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO 및 ADOMD(NuGet 패키지)

AMO(Analysis Services Management Objects) 및 ADOMD 클라이언트 라이브러리는 [NuGet.org](https://www.nuget.org/)에서 설치 가능한 패키지 형태로 제공됩니다. Windows Installer를 사용하는 대신 NuGet 참조로 마이그레이션하는 것이 좋습니다. 

|패키지  | 제품 버전  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.17.1     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.17.1      |

NuGet 패키지 어셈블리 AssemblyVersion은 유의적 버전 MAJOR.MINOR.PATCH를 따릅니다. NuGet 참조는 GAC에 다른 버전이 있더라도(MSI 설치의 결과) 필요한 버전을 로드합니다. 패치는 각 릴리스에 대해 증가합니다. AMO 및 ADOMD 버전은 동기 상태로 유지됩니다.

## <a name="understanding-client-libraries"></a>클라이언트 라이브러리 이해

Analysis Services는 데이터 공급자로도 알려진 세 가지 클라이언트 라이브러리를 활용합니다. ADOMD.NET 및 AMO(Analysis Services Management Objects)는 관리되는 클라이언트 라이브러리입니다. Analysis Services OLE DB 공급자(MSOLAP DLL)는 네이티브 클라이언트 라이브러리입니다. 일반적으로 세 가지 모두 동시에 설치됩니다. **Azure Analysis Services에는 세 라이브러리 모두의 최신 버전이 필요합니다**. 

Power BI Desktop 및 Excel과 같은 Microsoft 클라이언트 애플리케이션은 이러한 세 가지 클라이언트 라이브러리를 모두 설치하고 새 버전이 사용 가능해지면 업데이트합니다. 업데이트의 버전 또는 빈도에 따라 일부 클라이언트 라이브러리가 Azure Analysis Services에서 요구하는 최신 버전이 아닐 수도 있습니다. 사용자 지정 애플리케이션, 또는 AsCmd, TOM, ADOMD.NET과 같은 다른 인터페이스에도 마찬가지입니다. 이러한 애플리케이션에서는 라이브러리를 수동으로 또는 프로그래밍 방식으로 설치해야 합니다. 수동 설치를 위한 클라이언트 라이브러리는 SQL Server 기능 팩에 배포 가능 패키지로 포함되어 있습니다. 그러나 이러한 클라이언트 라이브러리는 SQL Server 버전에 연결되며 최신 버전이 아닐 수도 있습니다.  

클라이언트 연결을 위한 클라이언트 라이브러리는 Azure Analysis Services 서버에서 데이터 원본에 연결하는 데 필요한 데이터 공급자와는 다릅니다. 데이터 원본 연결에 대한 자세한 내용은 [데이터 원본 연결](analysis-services-datasource.md)을 참조하세요.

## <a name="client-library-types"></a>클라이언트 라이브러리 형식

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider(MSOLAP) 

 Analysis Services OLE DB Provider(MSOLAP)는 Analysis Services 데이터베이스 연결용 네이티브 클라이언트 라이브러리입니다. 이 라이브러리는 ADOMD.NET과 AMO 둘 다에서 데이터 공급자에 연결 요청을 위임하는 방식으로 간접적으로 사용됩니다. 애플리케이션 코드에서 직접 OLE DB Provider를 호출할 수도 있습니다.  
  
 Analysis Services OLE DB Provider는 Analysis Services 데이터베이스에 액세스하는 데 사용되는 대부분의 도구와 애플리케이션에 의해 자동으로 설치됩니다. Analysis Services 데이터에 액세스하는 데 사용되는 컴퓨터에 설치되어야 합니다.  
  
 OLE DB 공급자는 종종 연결 문자열에 지정됩니다. Analysis Services 연결 문자열은 다른 명명법을 사용하여 OLE DB Provider: MSOLAP.\<version>.dll을 나타냅니다.

### <a name="amo"></a>AMO  

 AMO는 서버 관리 및 데이터 정의에 사용되는 관리되는 클라이언트 라이브러리입니다. 도구 및 클라이언트 애플리케이션에 의해 설치되고 사용됩니다. 예를 들어 SSMS(SQL Server Management Studio)는 AMO를 사용하여 Analysis Services에 연결합니다. AMO를 사용하는 연결은 일반적으로 최소로 유지되며 `"data source=\<servername>"`으로 구성됩니다. 연결이 설정된 후에 API를 사용하여 데이터베이스 컬렉션 및 주요 개체에 대해 작업을 수행합니다. SSDT와 SSMS는 둘 다 AMO를 사용하여 Analysis Services 인스턴스에 연결합니다.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET은 Analysis Services 데이터를 쿼리하는 데 사용되는 관리되는 데이터 클라이언트 라이브러리입니다. 도구 및 클라이언트 애플리케이션에 의해 설치되고 사용됩니다. 
  
 데이터베이스에 연결하는 경우 세 라이브러리에 대한 연결 문자열 속성이 모두 비슷합니다. [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString)을 사용하여 ADOMD.NET에 대해 정의한 거의 모든 연결 문자열이 AMO 및 Analysis Services OLE DB Provider(MSOLAP)에도 작동합니다. 자세한 내용은 [연결 문자열 속성&#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services)를 참조하세요.  

  
##  <a name="bkmk_LibUpdate"></a> 클라이언트 라이브러리 버전을 확인하는 방법   
  
### <a name="oleddb-msolap"></a>OLEDDB(MSOLAP)  
  
1.  `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`로 이동합니다. 둘 이상의 폴더가 있는 경우 더 높은 숫자를 선택합니다.
  
2.  마우스 오른쪽 단추로 **msolap.dll** > **Properties** > **Details**를 클릭합니다. 파일 이름이 msolap140.dll이면 최신 버전보다 오래된 것이므로 업그레이드해야 합니다.
    
    ![클라이언트 라이브러리 세부 정보](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`로 이동합니다. 둘 이상의 폴더가 있는 경우 더 높은 숫자를 선택합니다.
2. 마우스 오른쪽 단추로 **Microsoft.AnalysisServices** > **속성** > **세부 정보**를 클릭합니다.  

### <a name="adomd"></a>ADOMD

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`로 이동합니다. 둘 이상의 폴더가 있는 경우 더 높은 숫자를 선택합니다.
2. 마우스 오른쪽 단추로 **Microsoft.AnalysisServices.AdomdClient** > **속성** > **세부 정보**를 클릭합니다.  


## <a name="next-steps"></a>다음 단계
[Excel로 연결](analysis-services-connect-excel.md)    
[Power BI와 연결](analysis-services-connect-pbi.md)
