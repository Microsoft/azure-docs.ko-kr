---
title: Azure Analysis Services 서버에 연결 | Microsoft Docs
description: Azure의 Analysis Services 서버에서 데이터에 연결하고 가져오는 방법에 대해 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499427"
---
# <a name="connecting-to-servers"></a>서버에 연결

이 문서는 SSMS(SQL Server Management Studio)와 같은 데이터 모델링 및 관리 애플리케이션 또는 Analysis Services 프로젝트를 사용하는 Visual Studio, Microsoft Excel, Power BI Desktop 또는 사용자 지정 애플리케이션과 같은 클라이언트 보고 애플리케이션을 사용하여 서버에 연결하는 방법을 설명합니다. Azure Analysis Services에 연결에서 HTTPS를 사용합니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

[최신 클라이언트 라이브러리 가져오기](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

종류에 관계없이 모든 서버 연결에서 Analysis Services 서버에 연결하고 인터페이스하려면 업데이트된 AMO, ADOMD.NET 및 OLEDB 클라이언트 라이브러리가 필요합니다. SSMS, Visual Studio, Excel 2016 이상 및 Power BI의 경우 최신 클라이언트 라이브러리가 설치되어 있거나 월별 릴리스로 업데이트되어야 합니다. 그러나 경우에 따라 애플리케이션에 최신 버전이 없을 수 있습니다. 예를 들어 정책 지연 업데이트 또는 Microsoft 365 업데이트가 지연된 채널에 있는 경우입니다.

> [!NOTE]
> 클라이언트 라이브러리는 사용자 이름과 암호를 필요로 하는 프록시 서버를 통해 Azure Analysis Services에 연결할 수 없습니다. 

## <a name="server-name"></a>서버 이름

Azure에서 Analysis Services 서버를 만들 경우 고유한 이름 및 만들 서버 지역을 지정합니다. 연결에서 서버 이름을 지정할 경우 서버 명명 구성표는 다음과 같습니다.

```
<protocol>://<region>/<servername>
```
 프로토콜이 문자열 **asazure** 인 경우 지역은 서버를 만든 지역의 URI(예: westus.asazure.windows.net)이고 서버 이름은 지역 내 고유 서버의 이름입니다.

### <a name="get-the-server-name"></a>서버 이름 가져오기

**Azure Portal** > 서버 > **개요** > **서버 이름** 에서 전체 서버 이름을 복사합니다. 조직의 다른 사용자가 이 서버에 연결하는 경우 그들과 이 서버 이름을 공유할 수 있습니다. 서버 이름을 지정할 경우 전체 경로를 사용해야 합니다.

![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> 미국 동부 2 지역의 프로토콜은 **aspaaseastus2** 입니다.

## <a name="connection-string"></a>연결 문자열

테이블 형식 개체 모델을 사용하여 Azure Analysis Services에 연결할 경우 다음 연결 문자열 서식을 사용합니다.

###### <a name="integrated-azure-active-directory-authentication"></a>통합 Azure Active Directory 인증

사용할 수 있는 경우 통합 인증에서 Azure Active Directory 자격 증명 캐시를 선택합니다. 그렇지 않은 경우 Azure 로그인 창이 표시됩니다.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>사용자 이름 및 암호를 포함한 Azure Active Directory 인증

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows 인증(통합 보안)

현재 프로세스를 실행 중인 Windows 계정을 사용합니다.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>.odc 파일을 사용하여 연결

이전 버전의 Excel 사용자는 Office 데이터 연결(.odc) 파일을 사용하여 Azure Analysis Services 서버에 연결할 수 있습니다. 자세한 내용은 [Office 데이터 연결(.odc) 파일 만들기](analysis-services-odc.md)를 참조하세요.

## <a name="connect-as-a-linked-server-from-sql-server"></a>SQL Server에서 연결된 서버로 연결

SQL Server는 데이터 원본 공급자로 MSOLAP를 지정하여 Azure Analysis Services 리소스에 [연결된 서버](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine)로 연결할 수 있습니다. 연결된 서버 연결을 구성하기 전에 최신 [MSOLAP 클라이언트 라이브러리](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)(공급자)를 설치해야 합니다. 

Azure Analysis Services에 연결된 서버를 연결하려면 MSOLAP 공급자를 SQL Server 프로세스 외부에서 인스턴스화해야 합니다. 연결된 서버 옵션을 구성할 때 **inprocess 허용** 옵션을 **선택하지 않았는지** 확인합니다.

**inprocess허용** 을 선택하고 공급자를 SQL Server 프로세스에서 인스턴스화하면 다음 오류가 반환됩니다.

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>다음 단계

[Excel로 연결](analysis-services-connect-excel.md)    
[Power BI로 연결](analysis-services-connect-pbi.md)   
[서버 관리](analysis-services-manage.md)