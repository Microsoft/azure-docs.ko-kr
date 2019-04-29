---
title: 입력 유효성 검사 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 0803ade7613480621a0cd87f9944ee5f55bf432c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586308"
---
# <a name="security-frame-input-validation--mitigations"></a>보안 프레임: 입력 유효성 검사 | 완화 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **웹 애플리케이션** | <ul><li>[신뢰할 수 없는 스타일시트를 사용하는 모든 변환에 대해 XSLT 스크립팅을 사용하지 않도록 설정](#disable-xslt)</li><li>[사용자가 제어할 수 있는 콘텐츠를 포함할 수 있는 각 페이지가 자동 MIME 스니핑에서 옵트아웃하는지 확인](#out-sniffing)</li><li>[XML 엔터티 확인 확정 또는 사용 중지](#xml-resolution)</li><li>[http.sys를 활용하는 애플리케이션에서 URL 정형화 확인 수행](#app-verification)</li><li>[사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인](#controls-users)</li><li>[웹 애플리케이션에서 데이터 액세스를 위해 형식이 안전한 매개 변수를 사용하는지 확인](#typesafe)</li><li>[별도의 모델 바인딩 클래스 또는 바인딩 필터 목록을 사용하여 MVC 대량 할당 취약성 방지](#binding-mvc)</li><li>[렌더링하기 전에 신뢰할 수 없는 웹 출력 인코딩](#rendering)</li><li>[모든 문자열 형식 모델 속성에 대한 입력 유효성 검사 및 필터링 수행](#typemodel)</li><li>[모든 문자를 허용하는 양식 필드(예: 서식 있는 텍스트 편집기)에서 삭제 적용](#richtext)</li><li>[기본 제공 인코딩이 없는 싱크에 DOM 요소 할당 금지](#inbuilt-encode)</li><li>[애플리케이션 내의 모든 리디렉션이 폐쇄되어 있거나 안전하게 수행되는지 확인](#redirect-safe)</li><li>[컨트롤러 메서드에서 허용하는 모든 문자열 형식 매개 변수에 대한 입력 유효성 검사 구현](#string-method)</li><li>[정규식 처리에 대한 시간 제한 상한값을 설정하여 잘못된 정규식으로 인한 DoS 방지](#dos-expression)</li><li>[Razor 보기에서 Html.Raw 사용 방지](#html-razor)</li></ul> | 
| **데이터베이스** | <ul><li>[저장 프로시저에서 동적 쿼리 사용 금지](#stored-proc)</li></ul> |
| **앱 API** | <ul><li>[모델 유효성 검사가 Web API 메서드에서 수행되는지 확인](#validation-api)</li><li>[Web API 메서드에서 허용하는 모든 문자열 형식 매개 변수에 대한 입력 유효성 검사 구현](#string-api)</li><li>[Web API에서 데이터 액세스를 위해 형식이 안전한 매개 변수를 사용하는지 확인](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Azure Cosmos DB에 매개 변수가 있는 SQL 쿼리 사용](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF - 스키마 바인딩을 통한 입력 유효성 검사](#schema-binding)</li><li>[WCF - 매개 변수 검사기를 통한 입력 유효성 검사](#parameters)</li></ul> |

## <a id="disable-xslt"></a>신뢰할 수 없는 스타일시트를 사용하는 모든 변환에 대해 XSLT 스크립팅을 사용하지 않도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [XSLT 보안](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript 속성](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **단계** | XSLT는 `<msxml:script>` 요소를 사용하여 스타일시트 내에서 스크립팅을 지원합니다. 이렇게 하면 사용자 지정 함수를 XSLT 변환에 사용할 수 있습니다. 스크립트는 변환을 수행하는 프로세스의 컨텍스트에서 실행됩니다. 신뢰할 수 없는 코드가 신뢰할 수 없는 환경에서 실행되지 않도록 하려면 XSLT 스크립트를 사용하지 않도록 설정해야 합니다. *.NET을 사용하는 경우:* XSLT 스크립팅은 기본적으로 사용하지 않도록 설정되지만 `XsltSettings.EnableScript` 속성을 통해 명시적으로 사용하도록 설정되지 않았음을 확인해야 합니다.|

### <a name="example"></a>예 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>예
MSXML 6.0을 사용하는 경우 XSLT 스크립팅은 기본적으로 비활성화됩니다. 그러나 AllowXsltScript XML DOM 개체 속성을 통해 명시적으로 사용하지 않도록 설정되지 않았는지 확인해야 합니다. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>예
MSXML 5 이하를 사용하는 경우 XSLT 스크립팅은 기본적으로 활성화되며, 명시적으로 사용하지 않도록 설정해야 합니다. AllowXsltScript XML DOM 개체 속성을 false로 설정합니다. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>사용자가 제어할 수 있는 콘텐츠를 포함할 수 있는 각 페이지가 자동 MIME 스니핑에서 옵트아웃하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IE8 보안 5부 - 포괄적 보호](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)(영문)  |
| **단계** | <p>사용자가 제어할 수 있는 콘텐츠를 포함할 수 있는 각 페이지에 대해 `X-Content-Type-Options:nosniff` HTTP 헤더를 사용해야 합니다. 이 요구 사항을 준수하려면 사용자가 제어할 수 있는 콘텐츠를 포함할 수 있는 페이지에 대해서만 페이지별로 필수 헤더를 설정하거나 애플리케이션의 모든 페이지에 대해 전역으로 필수 헤더를 설정할 수 있습니다.</p><p>웹 서버에서 제공되는 파일의 각 형식에는 콘텐츠의 특성(즉 이미지, 텍스트, 애플리케이션 등)을 설명하는 [MIME 형식](https://en.wikipedia.org/wiki/Mime_type)(*콘텐츠 형식*이라고도 함)이 연결되어 있습니다.</p><p>X-Content-Type-Options 헤더는 개발자가 콘텐츠를 MIME 스니핑하지 않아야 한다고 지정할 수 있는 HTTP 헤더입니다. 이 헤더는 MIME 스니핑 공격을 완화하도록 설계되었습니다. IE8(Internet Explorer 8)에는 이 헤더에 대한 지원이 추가되었습니다.</p><p>IE8 사용자만 X-Content-Type-Options의 이점을 얻을 수 있습니다. 이전 버전의 Internet Explorer에서는 현재 X-Content-Type-Options 헤더를 사용하지 않습니다.</p><p>Internet Explorer 8 이상은 MIME 스니핑 옵트아웃 기능을 구현하는 유일한 주요 브라우저입니다. 다른 주요 브라우저(Firefox, Safari, Chrome)에서 비슷한 기능을 구현하는 경우 이 권장 사항은 해당 브라우저의 구문도 포함하도록 업데이트됩니다.</p>|

### <a name="example"></a>예
애플리케이션의 모든 페이지에 대해 필수 헤더를 전역으로 사용하도록 설정하려면 다음 중 하나를 수행할 수 있습니다. 

* IIS(인터넷 정보 서비스) 7에서 애플리케이션을 호스팅하는 경우 web.config 파일에 헤더를 추가합니다. 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* 글로벌 Application\_BeginRequest를 통해 헤더를 추가합니다. 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* 사용자 지정 HTTP 모듈을 구현합니다. 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* 개별 응답에 추가하여 특정 페이지에 대해서만 필수 헤더를 사용하도록 설정할 수 있습니다. 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>XML 엔터티 확인 확정 또는 사용 중지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [XML 엔터티 확장](https://capec.mitre.org/data/definitions/197.html)(영문), [XML DoS(서비스 거부) 공격 및 방어](https://msdn.microsoft.com/magazine/ee335713.aspx)(영문), [MSXML 보안 개요](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [MSXML 코드 보안에 대한 모범 사례](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [NSXMLParserDelegate 프로토콜 참조](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)(영문), [외부 참조 확인](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **단계**| <p>널리 사용되지는 않지만 XML 파서가 문서 자체 또는 외부 원본에서 정의된 값을 사용하여 매크로 엔터티를 확장할 수 있도록 하는 XML 기능이 있습니다. 예를 들어 문서에서 "Microsoft" 값이 있는 "companyname" 엔터티를 정의하여 "&companyname;" 텍스트가 문서에 나타날 때마다 자동으로 Microsoft 텍스트로 바꿀 수 있습니다. 또는 문서에서 외부 웹 서비스를 참조하는 "MSFTStock" 엔터티를 정의하여 Microsoft 주식의 현재 가치를 가져올 수 있습니다.</p><p>그러면 언제든지 "&MSFTStock;"이 문서에 나타나면 자동으로 현재 주가로 바뀝니다. 그러나 이 기능을 악용하여 DoS(서비스 거부) 조건을 만들 수 있습니다. 공격자는 시스템에서 사용 가능한 모든 메모리를 사용 하는 지 수 확장 XML 폭탄을 만들려면 여러 엔터티를 중첩할 수 있습니다. </p><p>또는 무한한 양의 데이터를 다시 스트림하거나 단순히 스레드를 중단하는 외부 참조를 만들 수 있습니다. 결과적으로 애플리케이션에서 내부 및/또는 외부 XML 엔터티 확인을 사용하지 않는 경우 모든 팀이 해당 확인을 완전히 사용하지 않도록 설정하거나, 이 기능이 절대적으로 필요한 경우 애플리케이션에서 엔터티 분석을 위해 소비할 수 있는 메모리와 시간의 양을 수동으로 제한해야 합니다. 애플리케이션에서 엔터티 확인이 필요하지 않은 경우 엔터티 확인을 사용하지 않도록 설정합니다. </p>|

### <a name="example"></a>예
.NET Framework 코드의 경우 다음 방법을 사용할 수 있습니다.

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
`XmlReaderSettings`에서 `ProhibitDtd`의 기본값은 true이지만 `XmlTextReader`에서는 false입니다. XmlReaderSettings를 사용하는 경우 명시적으로 ProhibitDtd를 true로 설정할 필요는 없지만 보안을 위해 수행하는 것이 좋습니다. 또한 XmlDocument 클래스는 기본적으로 엔터티 확인을 허용합니다. 

### <a name="example"></a>예
XmlDocuments에 대한 엔터티 확인을 사용하지 않도록 설정하려면, Load 메서드의 `XmlDocument.Load(XmlReader)` 오버로드를 사용하고 다음 코드와 같이 XmlReader 인수에 적절한 속성을 설정하여 확인을 사용하지 않도록 설정합니다. 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>예
애플리케이션에서 엔터티 확인을 사용하지 않도록 설정할 수 없는 경우 애플리케이션의 필요에 따라 XmlReaderSettings.MaxCharactersFromEntities 속성을 적절한 값으로 설정합니다. 이 속성은 잠재적인 지수 확장 DoS 공격의 영향을 제한합니다. 다음 코드에서는 이 방법의 예제를 제공합니다. 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>예
인라인 엔터티는 확인해야 하지만 외부 엔터티는 확인할 필요가 없는 경우 XmlReaderSettings.XmlResolver 속성을 null로 설정합니다. 예를 들면 다음과 같습니다. 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
MSXML6에서 ProhibitDTD는 기본적으로 true(DTD 처리 비활성화)로 설정됩니다. Apple OSX/iOS 코드의 경우 NSXMLParser 및 libXML2의 두 가지 XML 파서를 사용할 수 있습니다. 

## <a id="app-verification"></a>http.sys를 활용하는 애플리케이션에서 URL 정형화 확인 수행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>http.sys를 사용하는 모든 애플리케이션은 다음 지침을 따라야 합니다.</p><ul><li>URL 길이를 16,384자(ASCII 또는 유니코드) 이하로 제한합니다. 이 값은 기본 IIS(인터넷 정보 서비스) 6 설정에 따른 절대 최대 URL 길이입니다. Websites에서는 가능한 경우 이 길이보다 더 짧도록 노력해야 합니다.</li><li>.NET FX의 정규화 규칙을 활용할 수 있으므로 표준 .NET Framework 파일 I/O 클래스(예: FileStream)를 사용합니다.</li><li>알려진 파일 이름의 허용 목록을 명시적으로 작성합니다.</li><li>UrlScan 거부를 제공하지 않는 것으로 알려진 파일 형식(exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, printer, ini, pol, dat 파일)을 명시적으로 거부합니다.</li><li>다음 예외를 catch합니다.<ul><li>System.ArgumentException(디바이스 이름의 경우)</li><li>System.NotSupportedException(데이터 스트림의 경우)</li><li>System.IO.FileNotFoundException(잘못된 이스케이프 문자를 사용한 파일 이름의 경우)</li><li>System.IO.DirectoryNotFoundException(잘못된 이스케이프 문자를 사용한 디렉터리의 경우)</li></ul></li><li>Win32 파일 I/O API를 호출하면 *안됩니다*. 잘못된 URL인 경우 사용자에게 400 오류를 정상적으로 반환하고 실제 오류를 기록합니다.</li></ul>|

## <a id="controls-users"></a>사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [무제한 파일 업로드](https://www.owasp.org/index.php/Unrestricted_File_Upload)(영문), [파일 서명 테이블](https://www.garykessler.net/library/file_sigs.html)(영문) |
| **단계** | <p>업로드된 파일은 애플리케이션에 상당한 위험을 초래합니다.</p><p>많은 공격의 첫 번째 단계는 공격 대상 시스템에 일부 코드를 가져오는 것입니다. 그런 다음 공격에서 해당 코드를 실행하도록 하는 방법을 찾아야만 합니다. 파일 업로드를 사용하면 공격자가 첫 번째 단계를 수행할 수 있습니다. 무제한 파일 업로드의 결과는 전체 시스템 감염, 오버로드된 파일 시스템 또는 데이터베이스, 백 엔드 시스템에 대한 전달 공격 및 단순 변조를 포함하여 다양할 수 있습니다.</p><p>애플리케이션에서 업로드된 파일로 수행하는 작업과 특히 파일이 저장되는 위치에 따라 달라집니다. 파일 업로드에 대한 서버 쪽 유효성 검사는 없습니다. 파일 업로드 기능에 대해 다음과 같은 보안 제어를 구현해야 합니다.</p><ul><li>파일 확장명 검사(허용되는 파일 형식의 유효한 집합만)</li><li>최대 파일 크기 제한</li><li>파일을 웹 루트에 업로드하면 안됩니다. 비시스템 드라이브의 디렉터리에 업로드해야 합니다.</li><li>파일 덮어쓰기를 방지하기 위해 업로드된 파일 이름에 임의성이 있도록 명명 규칙을 따라야 합니다.</li><li>디스크에 쓰기 전에 파일에 대해 바이러스 백신 검사를 수행해야 합니다.</li><li>악의적 문자에 대해 파일 이름 및 다른 메타데이터(예: 파일 경로)의 유효성을 검사해야 합니다.</li><li>사용자가 위장된 파일을 업로드하지 못하도록 파일 형식 서명을 확인해야 합니다(예: 확장명을 txt로 변경하여 exe 파일 업로드).</li></ul>| 

### <a name="example"></a>예
파일 형식 서명 유효성 검사와 관련된 마지막 지점에 대한 자세한 내용은 아래 클래스를 참조하세요. 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>웹 애플리케이션에서 데이터 액세스를 위해 형식이 안전한 매개 변수를 사용하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>매개 변수 컬렉션을 사용하는 경우 SQL은 입력을 실행 코드가 아닌 리터럴 값으로 처리합니다. 매개 변수 컬렉션을 사용하여 입력 데이터에 형식 및 길이 제약 조건을 적용할 수 있습니다. 범위를 벗어난 값은 예외를 트리거합니다. 형식이 안전한 SQL 매개 변수를 사용하지 않으면 공격자가 필터링되지 않은 입력에 포함된 삽입 공격을 실행할 수 있습니다.</p><p>필터링되지 않은 입력에서 발생할 수 있는 SQL 삽입 공격을 방지하려면 SQL 쿼리를 구성할 때 형식이 안전한 매개 변수를 사용합니다. 저장 프로시저 및 동적 SQL 문과 함께 형식이 안전한 매개 변수를 사용할 수 있습니다. 매개 변수는 데이터베이스에서 리터럴 값으로 처리되며 실행 코드로는 처리되지 않습니다. 또한 매개 변수의 형식 및 길이에 대해서도 검사합니다.</p>|

### <a name="example"></a>예 
다음 코드에서는 저장 프로시저를 호출할 때 형식이 안전한 매개 변수를 SqlParameterCollection에 사용하는 방법을 보여 줍니다. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
이전의 코드 예제에서 입력 값은 11자를 초과할 수 없습니다. 데이터가 매개 변수에 정의된 형식 또는 길이를 따르지 않으면 SqlParameter 클래스에서 예외가 발생합니다. 

## <a id="binding-mvc"></a>별도의 모델 바인딩 클래스 또는 바인딩 필터 목록을 사용하여 MVC 대량 할당 취약성 방지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [메타데이터 특성](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [공개 키 보안 취약성 및 완화](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)(영문), [ASP.NET MVC의 대량 할당에 대한 완벽한 가이드](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx)(영문), [MVC를 사용하여 EF 시작](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)(영문) |
| **단계** | <ul><li>**과도한 게시 취약성은 언제 찾아야 합니까? -** 사용자 입력에서 모델 클래스를 바인딩하는 모든 위치에서 과도한 게시 취약성이 발생할 수 있습니다. MVC와 같은 프레임워크는 POCO(Plain Old CLR Object)를 포함하여 사용자 지정 .NET 클래스의 사용자 데이터를 나타낼 수 있습니다. MVC는 자동으로 이러한 모델 클래스를 요청의 데이터로 채우고, 사용자 입력을 처리하는 데 편리한 표현을 제공합니다. 사용자가 설정하지 않아야 하는 속성이 이러한 클래스에 포함되어 있으면, 사용자가 애플리케이션에서 전혀 의도하지 않은 데이터를 제어할 수 있는 과도한 게시 공격으로 인해 애플리케이션이 취약하게 될 수 있습니다. MVC 모델 바인딩과 마찬가지로 개체/관계형 매퍼(예: Entity Framework)와 같은 데이터베이스 액세스 기술도 종종 POCO 개체를 사용하여 데이터베이스 데이터를 나타낼 수 있습니다. 이러한 데이터 모델 클래스는 MVC에서 사용자 입력을 처리할 때와 마찬가지로 데이터베이스 데이터를 처리할 때도 동일한 편리성을 제공합니다. MVC와 데이터베이스는 모두 POCO 개체와 같은 유사한 모델을 지원하기 때문에 두 가지 용도로 동일한 클래스를 쉽게 다시 사용할 수 있습니다. 이러한 사례는 중요한 부분의 분리를 유지하는 데 실패하며, 의도하지 않은 속성이 모델 바인딩에 노출되어 과도한 게시 공격을 허용하는 일반적인 영역 중 하나입니다.</li><li>**필터링되지 않은 데이터베이스 모델 클래스를 MVC 작업에 대한 매개 변수로 사용하지 않아야 하는 이유는 무엇입니까? -** MVC 모델 바인딩은 해당 클래스의 모든 항목을 바인딩하기 때문입니다. 데이터가 보기에 표시되지 않더라도 악의적인 사용자는 이 데이터를 포함한 HTTP 요청을 보낼 수 있으며, 작업에서 사용자 입력을 위해 허용해야 하는 데이터의 셰이프가 데이터베이스 클래스라고 지시하므로 MVC에서 해당 데이터를 기꺼이 바인딩합니다.</li><li>**모델 바인딩에 사용되는 셰이프에 주의해야 하는 이유는 무엇입니까? -** 지나치게 광범위한 모델로 ASP.NET MVC 모델 바인딩을 사용하면 애플리케이션이 과도한 게시 공격에 노출됩니다. 과도한 게시로 인해 공격자는 항목에 대한 가격이나 계정의 보안 권한을 무시하는 등 개발자가 의도한 것 이상으로 애플리케이션 데이터를 변경할 수 있습니다. 애플리케이션은 작업별 바인딩 모델(또는 특정의 허용되는 속성 필터 목록)을 사용하여 신뢰할 수 없는 입력에서 모델 바인딩을 통해 허용할 수 있는 명시적 계약을 제공해야 합니다.</li><li>**코드를 복제하는 별도의 바인딩 모델이 있습니까? -** 아니요, 이는 중요한 부분의 분리 문제입니다. 작업 메서드에서 데이터베이스 모델을 다시 사용하는 경우 사용자가 HTTP 요청에 해당 클래스의 모든 속성(또는 하위 속성)을 설정할 수 있습니다. 이 작업이 MVC에서 수행하려는 작업이 아닌 경우 사용자 입력에서 가져올 수 있는 데이터를 MVC에 대신 표시하려면 필터 목록이나 별도의 클래스 셰이프가 필요합니다.</li><li>**사용자 입력에 대한 별도의 바인딩 모델이 있는 경우 모든 데이터 주석 특성을 복제해야 합니까? -** 반드시 그렇지는 않습니다. 데이터베이스 모델 클래스의 MetadataTypeAttribute를 사용하여 모델 바인딩 클래스의 메타데이터에 연결할 수 있습니다. MetadataTypeAttribute에서 참조하는 형식은 참조하는 형식의 하위 집합이어야 합니다(더 적은 속성이 있을 수 있지만, 더 많지는 않음).</li><li>**사용자 입력 모델과 데이터베이스 모델 간에 데이터를 앞뒤로 이동하는 것은 지루한 작업입니다. 리플렉션을 사용하여 모든 속성을 복사할 수 있습니까? -** 예. 바인딩 모델에 표시되는 유일한 속성은 사용자 입력에 대해 안전하다고 확인한 속성입니다. 리플렉션을 사용하여 이 두 모델 간에 공통으로 존재하는 모든 속성을 복사하지 못하게 할 보안상의 이유는 없습니다.</li><li>**[Bind(Exclude ="â€¦")]는 어떤가요? 별도의 바인딩 모델을 사용하는 대신 이 특성을 사용할 수 있습니까? -** 이 방법은 권장되지 않습니다. [Bind(Exclude ="â€¦")]를 사용하면 기본적으로 새 속성을 바인딩할 수 있습니다. 새 속성이 추가되면 기본적으로 디자인을 안전하게 유지하는 것보다 작업들을 안전하게 유지하도록 기억하는 추가 단계가 있습니다. 속성이 추가될 때마다 개발자에 따라 이 목록을 확인하는 것은 위험합니다.</li><li>**편집 작업에 [Bind(Include ="â€¦")]가 유용한가요? -** 아니요. [Bind(Include ="â€¦")]는 삽입 스타일 작업(새 데이터 추가)에 적합합니다. 업데이트 방식 작업(기존 데이터 수정)의 경우 별도의 바인딩 모델을 사용하거나 허용되는 명시적 속성 목록을 UpdateModel 또는 TryUpdateModel에 전달하는 등의 다른 방법을 사용합니다. 편집 작업에 [Bind(Include ="â€¦")] 특성을 추가하면 MVC가 개체 인스턴스를 만들고 나열된 속성만을 설정하여 다른 속성을 기본값으로 유지합니다. 지속형 데이터인 경우 생략된 모든 속성의 값을 기본값으로 다시 설정하여 기존 엔터티를 완전히 대체합니다. 예를 들어 IsAdmin이 편집 작업의 [Bind(Include ="â€¦")] 특성에서 누락된 경우 이 작업을 통해 이름이 편집된 사용자는 IsAdmin = false으로 재설정됩니다(편집된 사용자는 관리자 상태를 상실함). 특정 속성에 대한 업데이트를 방지하려면 위에서 설명한 다른 방법 중 하나를 사용합니다. 일부 버전의 MVC 도구는 편집 작업에서 [Bind(Include ="â€¦")]를 사용하여 컨트롤러 클래스를 생성하고 해당 목록에서 속성을 제거하면 과도한 게시 공격을 방지합니다. 그러나 위에서 설명한 대로 이 방법은 의도한 대로 작동하지 않는 대신 생략된 속성의 모든 데이터를 기본값으로 다시 설정합니다.</li><li>**만들기 작업의 경우 별도 바인딩 모델보다 [Bind(Include ="â€¦")]를 사용하는 경우 주의할 것이 있나요? -** 예. 첫째, 이 방법은 편집 시나리오에서 작동하지 않으므로 모든 과도한 게시 취약성을 완화하기 위해서는 별도의 두 가지 방법을 유지해야 합니다. 두 번째로 [Bind(Include ="â€¦")]와 달리 별도 바인딩 모델은 사용자 입력에 사용된 셰이프와 지속성에 사용된 셰이프 간에 문제를 분리합니다. 세 번째로 [Bind(Include ="â€¦")]는 최상위 속성만을 처리할 수 있습니다. 특성에서 하위 속성(예: "Details.Name")의 일부만을 허용할 수 없습니다. 마지막으로 가장 중요한 점은 [Bind(Include ="â€¦")]를 사용하면 클래스가 모델 바인딩에 사용될 때 항상 수행해야 하는 추가 단계를 추가한다는 것입니다. 새 동작 메서드가 데이터 클래스에 직접 바인딩하고 [Bind(Include ="â€¦")] 특성을 포함하지 않는 경우 과도한 게시 공격에 취약할 수 있으므로 기본적으로 [Bind(Include ="â€¦")] 접근법은 상대적으로 안전하지 않습니다. [Bind(Include ="â€¦")]를 사용하는 경우 데이터 클래스가 동작 메서드 매개 변수로 나타날 때마다 지정해야 합니다.</li><li>**만들기 작업의 경우 모델 클래스 자체에 [Bind(Include ="â€¦")] 특성을 배치하면 어떨까요 여기서는 모든 작업 메서드에 이 특성을 배치하는 것을 기억할 필요가 없습니까? -** 이 방법은 경우에 따라 작동합니다. (이 클래스를 사용하는 작업 매개 변수가 아닌) 모델 형식 자체에 [Bind(Include ="â€¦")]를 사용하면 모든 동작 메서드에서 [Bind(Include ="â€¦")] 특성을 포함하지 않아도 됩니다. 클래스에서 특성을 직접 사용하면 모델 바인딩을 위해 이 클래스의 별도 노출 영역을 효과적으로 만듭니다. 그러나 이 방법은 모델 클래스마다 모델 바인딩 셰이프 하나만 허용합니다. 한 작업(예: 사용자 역할을 업데이트하는 관리자 전용 작업) 메서드에서 필드의 모델 바인딩을 허용해야 하고, 다른 작업에서는 이 필드의 모델 바인딩을 방지해야 하는 경우 이 방법은 작동하지 않습니다. 각 클래스에는 하나의 모델 바인딩 셰이프가 있을 수 있습니다. 다른 작업에 다른 모델 바인딩 셰이프가 필요한 경우 작업 메서드에서 별도 모델 바인딩 클래스 또는 별도 [Bind(Include ="â€¦")] 특성 중 하나를 사용하여 이러한 별도 셰이프를 나타내야 합니다.</li><li>**바인딩 모델이란 무엇입니까? 보기 모델과 동일한 것입니까? -** 여기에는 관련된 두 가지 개념이 있습니다. 바인딩 모델이라는 용어는 작업의 매개 변수 목록(MVC 모델 바인딩에서 작업 메서드로 전달된 셰이프)에 사용되는 모델 클래스를 가리킵니다. 보기 모델이라는 용어는 작업 메서드에서 보기로 전달된 모델 클래스를 가리킵니다. 보기 특정 모델을 사용하는 것은 작업 메서드에서 보기로 데이터를 전달하는 일반적인 방법입니다. 종종이 셰이프는 모델 바인딩에 대 한 적합 한도 및 양쪽 모두에서 사용 되는 동일한 모델을 가리키는 보기 모델 이라는 용어를 사용할 수 있습니다. 정확하게 이 절차는 바인딩 모델에 대해 구체적으로 설명하며, 작업에 전달되는 셰이프에 초점을 맞추고 있습니다. 이는 대량 할당을 위해 중요한 것입니다.</li></ul>| 

## <a id="rendering"></a>렌더링하기 전에 신뢰할 수 없는 웹 출력 인코딩

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, Web Forms, MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET에서 XSS(사이트 간 스크립팅)를 방지하는 방법](https://msdn.microsoft.com/library/ms998274.aspx)(영문), [XSS(사이트 간 스크립팅)](https://cwe.mitre.org/data/definitions/79.html)(영문), [XSS(사이트 간 스크립팅) 방지 참고 자료](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet)(영문) |
| **단계** | 사이트 간 스크립팅(약어로 XSS)은 온라인 서비스 또는 웹에서 입력을 사용하는 애플리케이션/구성 요소에 대한 공격 벡터입니다. XSS 취약성으로 인해 공격자가 취약한 웹 애플리케이션을 통해 다른 사용자의 컴퓨터에서 스크립트를 실행할 수 있습니다. 악성 스크립트는 쿠키를 도용하거나 그렇지 않으면 JavaScript를 통해 공격 대상의 컴퓨터에 손상을 입히는 데 사용할 수 있습니다. XSS는 사용자 입력의 유효성을 검사하고 웹 페이지에 렌더링하기 전에 형식과 인코딩이 올바른지 확인함으로써 방지됩니다. 입력 유효성 검사 및 출력 인코딩은 Web Protection Library를 사용하여 수행할 수 있습니다. 관리 코드 (C\#, VB.NET 등), 하나를 사용 하거나 더 적절 한 사용자 입력이 매니페스트 되는 컨텍스트에 따라 Web Protection (ANTI-XSS) 라이브러리에서 인코딩 메서드:| 

### <a name="example"></a>예

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>모든 문자열 형식 모델 속성에 대한 입력 유효성 검사 및 필터링 수행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [유효성 검사 추가](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [MVC 애플리케이션에서 모델 데이터 유효성 검사](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)(영문), [ASP.NET MVC 애플리케이션을 위한 기본 원칙](https://msdn.microsoft.com/magazine/dd942822.aspx)(영문) |
| **단계** | <p>악의적인 사용자 입력으로부터 애플리케이션을 보호하려면 애플리케이션에서 사용하기 전에 모든 입력 매개 변수의 유효성을 검사해야 합니다. 허용 목록 유효성 검사 전략으로 서버 쪽에서 정규식 유효성 검사를 사용하여 입력 값의 유효성을 검사합니다. 삭제되지 않은 사용자 입력/매개 변수가 메서드에 전달되면 코드 삽입 취약성이 발생할 수 있습니다.</p><p>웹 애플리케이션의 경우 양식 필드, QueryStrings, 쿠키, HTTP 헤더 및 웹 서비스 매개 변수가 진입점에 포함될 수도 있습니다.</p><p>모델 바인딩 시 다음과 같이 입력 유효성 검사를 수행해야 합니다.</p><ul><li>모델 속성에는 허용되는 문자 및 최대 허용 길이를 받아들이기 위해 RegularExpression 주석으로 주석을 추가해야 합니다.</li><li>컨트롤러 메서드에서 ModelState 유효성 검사를 수행해야 합니다.</li></ul>|

## <a id="richtext"></a>모든 문자를 허용하는 양식 필드(예: 서식 있는 텍스트 편집기)에서 삭제 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [안전하지 않은 입력 인코딩](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3)(영어), [HTML 삭제기](https://github.com/mganss/HtmlSanitizer)(영어) |
| **단계** | <p>사용하려는 정적 태그를 모두 식별합니다. 일반적으로 `<b>`(굵게) 및 `<i>`(기울임꼴)와 같은 안전한 HTML 요소로 서식 지정을 제한하는 것입니다.</p><p>데이터를 작성하기 전에 HTML로 인코딩합니다. 이렇게 하면 악성 스크립트를 실행 코드가 아닌 텍스트로 처리하므로 안전하게 됩니다.</p><ol><li>\@ Page 지시문에 ValidateRequest="false" 특성을 추가하여 ASP.NET 요청 유효성 검사를 사용하지 않도록 설정합니다.</li><li>HtmlEncode 메서드로 문자열 입력을 인코딩합니다.</li><li>StringBuilder를 사용하고 해당 Replace 메서드를 호출하여 허용하려는 HTML 요소의 인코딩을 선택적으로 제거합니다.</li></ol><p>page-in 참조는 `ValidateRequest="false"`를 설정하여 ASP.NET 요청 유효성 검사를 비활성화합니다. 입력을 HTML로 인코딩하고 선택적으로 `<b>` 및 `<i>`를 허용합니다. 또는 HTML 삭제를 위한 .NET 라이브러리를 사용할 수도 있습니다.</p><p>HtmlSanitizer는 XSS 공격을 일으킬 수 있는 구문에서 HTML 조각과 문서를 정리하는 .NET 라이브러리입니다. AngleSharp를 사용하여 HTML 및 CSS를 구문 분석, 조작 및 렌더링합니다. HtmlSanitizer는 NuGet 패키지로 설치할 수 있으며, 사용자 입력은 관련 HTML 또는 CSS 삭제 메서드를 통해 서버 쪽에서 전달할 수 있습니다. 보안 제어로서의 삭제는 마지막 옵션으로만 고려해야 합니다.</p><p>입력 유효성 검사 및 출력 Encoding은 보다 효과적인 보안 제어로 간주됩니다.</p> |

## <a id="inbuilt-encode"></a>기본 제공 인코딩이 없는 싱크에 DOM 요소 할당 금지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 다양한 JavaScript 함수는 기본적으로 인코딩을 수행하지 않습니다. 이러한 함수를 통해 신뢰할 수 없는 입력을 DOM 요소에 할당하면 XSS(사이트 간 스크립트) 실행이 발생할 수 있습니다.| 

### <a name="example"></a>예
다음은 안전하지 않은 예제입니다. 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
`innerHtml`을 사용하지 않는 대신 `innerText`를 사용합니다. 마찬가지로 `$("#elm").html()` 대신 `$("#elm").text()`를 사용합니다. 

## <a id="redirect-safe"></a>애플리케이션 내의 모든 리디렉션이 폐쇄되어 있거나 안전하게 수행되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [OAuth 2.0 권한 부여 프레임워크 - 오픈 리디렉터](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **단계** | <p>사용자가 제공한 위치로 리디렉션해야 하는 애플리케이션 디자인은 가능한 리디렉션 대상을 사이트 또는 도메인의 미리 정의된 "안전한" 목록으로 제한해야 합니다. 애플리케이션의 모든 리디렉션은 폐쇄되어 있거나 안전해야 합니다.</p><p>다음을 수행합니다.</p><ul><li>모든 리디렉션을 식별합니다.</li><li>리디렉션마다 적절한 완화를 구현합니다. 적절한 완화에는 리디렉션 허용 목록 또는 사용자 확인이 포함됩니다. 오픈 리디렉션 취약성이 있는 웹 사이트 또는 서비스에서 Facebook/OAuth/OpenID ID 공급자를 사용하는 경우 공격자가 사용자의 로그온 토큰을 도용하여 해당 사용자를 가장할 수 있습니다. 이는 RFC 6749 "OAuth 2.0 인증 프레임워크", 10.15 섹션 "오픈 리디렉션"에서 설명하는 OAuth를 사용할 때 내재된 위험입니다. 마찬가지로 오픈 리디렉션을 사용하는 스피어 피싱 공격으로 인해 사용자의 자격 증명이 손상될 수 있습니다.</li></ul>|

## <a id="string-method"></a>컨트롤러 메서드에서 허용하는 모든 문자열 형식 매개 변수에 대한 입력 유효성 검사 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [MVC 애플리케이션에서 모델 데이터 유효성 검사](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)(영문), [ASP.NET MVC 애플리케이션을 위한 기본 원칙](https://msdn.microsoft.com/magazine/dd942822.aspx)(영문) |
| **단계** | 인수로 모델이 아니라 기본 데이터 형식만 허용하는 메서드의 경우 정규식을 사용하여 입력 유효성 검사를 수행해야 합니다. 여기서는 Regex.IsMatch를 유효한 정규식 패턴과 함께 사용해야 합니다. 입력이 지정된 정규식과 일치하지 않으면 제어를 더 이상 진행하지 않아야 하며 유효성 검사 실패와 관련된 적절한 경고를 표시해야 합니다.| 

## <a id="dos-expression"></a>정규식 처리에 대한 시간 제한 상한값을 설정하여 잘못된 정규식으로 인한 DoS 방지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, Web Forms, MVC5, MVC6  |
| **특성**              | N/A  |
| **참조**              | [DefaultRegexMatchTimeout 속성](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **단계** | 잘못 작성된 정규식에 대한 DoS(서비스 거부) 공격을 방지하려면 많은 역 추적이 발생하므로 전역 기본 시간 제한을 설정합니다. 처리 시간이 정의된 상한보다 더 오래 걸리면 제한 시간 예외가 발생합니다. 아무 값도 구성되지 않으면 제한 시간은 무한입니다.| 

### <a name="example"></a>예
예를 들어 다음 구성에서 처리하는 데 5초 이상 걸리는 경우 RegexMatchTimeoutException이 발생합니다. 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Razor 보기에서 Html.Raw 사용 방지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| 단계 | ASP.NET WebPages (Razor)에 자동 HTML 인코딩을 수행 합니다. 포함된 코드 너깃(@ 블록)으로 인쇄된 모든 문자열은 자동으로 HTML로 인코딩됩니다. 그러나 `HtmlHelper.Raw` 메서드가 호출되면 HTML로 인코딩되지 않은 태그를 반환합니다. `Html.Raw()` 도우미 메서드를 사용하면 Razor에서 제공하는 자동 인코딩 보호를 무시합니다.|

### <a name="example"></a>예
다음은 안전하지 않은 예제입니다. 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
태그를 표시할 필요가 없는 경우 `Html.Raw()`를 사용하면 안됩니다. 이 메서드는 암시적으로 출력 인코딩을 수행하지 않습니다. 다른 ASP.NET 도우미(예: `@Html.DisplayFor()`)를 사용합니다. 

## <a id="stored-proc"></a>저장 프로시저에서 동적 쿼리 사용 금지

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>SQL 삽입 공격은 입력 유효성 검사의 취약성을 악용하여 데이터베이스에서 임의의 명령을 실행합니다. 데이터베이스에 액세스하기 위해 애플리케이션에서 입력을 사용하여 동적 SQL 문을 구성할 때 발생할 수 있습니다. 또한 코드에서 원시 사용자 입력을 포함한 문자열이 전달되는 저장 프로시저를 사용하는 경우에도 발생할 수 있습니다. 공격자가 SQL 삽입 공격을 사용하여 데이터베이스에서 임의의 명령을 실행할 수 있습니다. 모든 SQL 문(저장 프로시저의 SQL 문 포함)에는 매개 변수가 있어야 합니다. 매개 변수가 있는 SQL 문은 강력한 형식으로 인해 SQL에 특별한 의미가 있는 문자(예: 작은 따옴표)를 문제 없이 허용합니다. |

### <a name="example"></a>예
다음은 안전하지 않은 동적 저장 프로시저의 예제입니다. 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>예
다음은 안전하게 구현된 동일한 저장 프로시저입니다. 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>모델 유효성 검사가 Web API 메서드에서 수행되는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Web API의에서 모델 유효성 검사](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **단계** | 클라이언트에서 Web API로 데이터를 보낼 때 처리를 수행하기 전에 데이터의 유효성을 검사해야 합니다. 모델을 입력으로 받아들이는 ASP.NET Web API의 경우 모델의 데이터 주석을 사용하여 모델의 속성에 대한 유효성 검사 규칙을 설정합니다.|

### <a name="example"></a>예
다음 코드에서는 이러한 작업을 보여 줍니다. 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>예
API 컨트롤러의 작업 메서드에서 모델의 유효성은 아래와 같이 명시적으로 검사되어야 합니다. 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Web API 메서드에서 허용하는 모든 문자열 형식 매개 변수에 대한 입력 유효성 검사 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, MVC 5, MVC 6 |
| **특성**              | N/A  |
| **참조**              | [MVC 애플리케이션에서 모델 데이터 유효성 검사](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx)(영문), [ASP.NET MVC 애플리케이션을 위한 기본 원칙](https://msdn.microsoft.com/magazine/dd942822.aspx)(영문) |
| **단계** | 인수로 모델이 아니라 기본 데이터 형식만 허용하는 메서드의 경우 정규식을 사용하여 입력 유효성 검사를 수행해야 합니다. 여기서는 Regex.IsMatch를 유효한 정규식 패턴과 함께 사용해야 합니다. 입력이 지정된 정규식과 일치하지 않으면 제어를 더 이상 진행하지 않아야 하며 유효성 검사 실패와 관련된 적절한 경고를 표시해야 합니다.|

## <a id="typesafe-api"></a>Web API에서 데이터 액세스를 위해 형식이 안전한 매개 변수를 사용하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>매개 변수 컬렉션을 사용하는 경우 SQL은 입력을 실행 코드가 아닌 리터럴 값으로 처리합니다. 매개 변수 컬렉션을 사용하여 입력 데이터에 형식 및 길이 제약 조건을 적용할 수 있습니다. 범위를 벗어난 값은 예외를 트리거합니다. 형식이 안전한 SQL 매개 변수를 사용하지 않으면 공격자가 필터링되지 않은 입력에 포함된 삽입 공격을 실행할 수 있습니다.</p><p>필터링되지 않은 입력에서 발생할 수 있는 SQL 삽입 공격을 방지하려면 SQL 쿼리를 구성할 때 형식이 안전한 매개 변수를 사용합니다. 저장 프로시저 및 동적 SQL 문과 함께 형식이 안전한 매개 변수를 사용할 수 있습니다. 매개 변수는 데이터베이스에서 리터럴 값으로 처리되며 실행 코드로는 처리되지 않습니다. 또한 매개 변수의 형식 및 길이에 대해서도 검사합니다.</p>|

### <a name="example"></a>예
다음 코드에서는 저장 프로시저를 호출할 때 형식이 안전한 매개 변수를 SqlParameterCollection에 사용하는 방법을 보여 줍니다. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
이전의 코드 예제에서 입력 값은 11자를 초과할 수 없습니다. 데이터가 매개 변수에 정의된 형식 또는 길이를 따르지 않으면 SqlParameter 클래스에서 예외가 발생합니다. 

## <a id="sql-docdb"></a>Cosmos DB에 매개 변수가 있는 SQL 쿼리 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Document DB | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Cosmos DB에서 SQL 매개 변수 지정 발표](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **단계** | Azure Cosmos DB는 읽기 전용 쿼리만 지원하지만 사용자 입력과 연결하여 쿼리를 구성하면 여전히 SQL 삽입이 가능합니다. 사용자가 악성 SQL 쿼리를 만들어 동일한 컬렉션 내에서 액세스하면 안되는 데이터에 액세스할 수 있습니다. 사용자 입력을 기반으로 하여 쿼리를 구성한 경우 매개 변수가 있는 SQL를 사용합니다. |

## <a id="schema-binding"></a>WCF - 스키마 바인딩을 통한 입력 유효성 검사

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **단계** | <p>유효성 검사가 부족하면 다양한 형식 삽입 공격이 발생합니다.</p><p>메시지 유효성 검사는 WCF 애플리케이션을 보호하기 위한 하나의 방어선을 나타냅니다. 이 방법에서는 스키마를 통해 메시지의 유효성을 검사하여 악의적인 클라이언트의 공격으로부터 WCF 서비스 작업을 보호합니다. 악성 서비스에 의한 공격으로부터 클라이언트를 보호하기 위해 클라이언트에서 받은 모든 메시지의 유효성을 검사합니다. 메시지 유효성 검사를 통해 작업에서 메시지 계약이나 데이터 계약을 소비할 때 메시지의 유효성을 검사할 수 있으며, 매개 변수 유효성 검사로는 이러한 작업을 수행할 수 없습니다. 메시지 유효성 검사를 사용하면 스키마 내에 유효성 검사 논리를 만들어 유연성을 높이고 개발 시간을 줄일 수 있습니다. 스키마는 조직 내의 여러 애플리케이션 간에 다시 사용하여 데이터 표현을 위한 표준을 만들 수 있습니다. 또한 메시지 유효성 검사를 사용하면 비즈니스 논리를 나타내는 계약과 관련된 보다 복잡한 데이터 형식을 사용할 때 작업을 보호할 수 있습니다.</p><p>메시지 유효성 검사를 수행하려면 먼저 서비스 작업과 해당 작업에서 사용하는 데이터 형식을 나타내는 스키마를 빌드합니다. 그런 다음 사용자 지정 클라이언트 메시지 검사기와 사용자 지정 디스패처 메시지 검사기를 구현하는 .NET 클래스를 만들어 서비스와 보내고 받는 메시지의 유효성을 검사합니다. 다음으로 사용자 지정 엔드포인트 동작을 구현하여 클라이언트와 서비스 모두에서 메시지 유효성 검사를 사용하도록 설정합니다. 마지막으로 확장된 사용자 지정 엔드포인트 동작을 서비스 또는 클라이언트의 구성 파일에 노출할 수 있도록 하는 클래스에 사용자 지정 구성 요소를 구현합니다.</p>|

## <a id="parameters"></a>WCF - 매개 변수 검사기를 통한 입력 유효성 검사

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **단계** | <p>입력 및 데이터 유효성 검사는 WCF 애플리케이션을 보호하기 위한 하나의 중요한 방어선을 나타냅니다. 악성 클라이언트의 공격으로부터 서비스를 보호하기 위해 WCF 서비스 작업에 노출된 모든 매개 변수의 유효성을 검사해야 합니다. 반대로 악성 서비스에 의한 공격으로부터 클라이언트를 보호하기 위해 클라이언트에서 받은 모든 반환 값의 유효성도 검사해야 합니다.</p><p>WCF는 사용자 지정 확장을 만들어 WCF 런타임 동작을 사용자 지정할 수 있는 다양한 확장성 지점을 제공합니다. 메시지 검사기와 매개 변수 검사기는 클라이언트와 서비스 간에 전달되는 데이터를 보다 효율적으로 제어하는 데 사용되는 두 가지 확장성 메커니즘입니다. 입력 유효성 검사에 매개 변수 검사기를 사용해야 하며, 서비스에서 들어오고 나가는 전체 메시지를 검사해야 하는 경우에만 메시지 검사기를 사용해야 합니다.</p><p>입력 유효성 검사를 수행하려면 .NET 클래스를 빌드하고, 서비스의 작업에 대한 매개 변수의 유효성을 검사하기 위해 사용자 지정 매개 변수 검사기를 구현합니다. 그런 다음 사용자 지정 엔드포인트 동작을 구현하여 클라이언트와 서비스 모두에서 유효성 검사를 사용하도록 설정합니다. 마지막으로 확장된 사용자 지정 엔드포인트 동작을 서비스 또는 클라이언트의 구성 파일에 노출할 수 있도록 하는 클래스에 사용자 지정 구성 요소를 구현합니다.</p>|
