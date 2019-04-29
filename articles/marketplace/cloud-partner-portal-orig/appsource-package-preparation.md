---
title: AppSource 패키지 준비 | Microsoft Docs
description: AppSource 패키지를 준비 및 빌드하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc06ddf2fce93bef27e7aaee85b47179608e192b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625476"
---
# <a name="appsource-package-preparation"></a>AppSource 패키지 준비

solution.zip 파일 외에 **AppSource 패키지**도 필요합니다. 이 패키지는 고객 CRM 환경에 솔루션을 배포하는 프로세스를 자동화하는 데 필요한 모든 자산이 포함된 .zip 파일입니다. **AppSource 패키지**의 구성 요소는 다음과 같습니다.

* Package Deployer용 패키지
* 사용할 자산이 포함된 **Content_Types.xml** 파일
* 앱별 데이터가 포함된 xml 파일
* 관리 센터에서 목록과 함께 표시할 32x32 로고
* 사용 조건, 개인정보취급방침

아래 단계에 따라 AppSource 패키지를 만들 수 있습니다.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Package Deployer용 패키지 만들기

Package Deployer용 패키지는 AppSource 패키지의 한 부분입니다.

Package Deployer용 패키지를 만들려면 [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx) 페이지의 지침을 따르세요. 완성된 패키지는 다음 자산으로 구성됩니다.

1. 패키지 폴더: 모든 솔루션, 구성 데이터, 플랫 파일 및 패키지용 콘텐츠를 포함합니다. _참고: 다음 예제에서 가정 패키지 폴더 "PkgFolder" 라고 합니다._
2. dll: 어셈블리는 패키지에 대 한 사용자 지정 코드를 포함합니다. _참고: 이 파일은 "MicrosoftSample.dll." 라고 가정은 다음에 나오는 예제_

이제 "**Content_Types.xml**" 파일을 만들어야 합니다. 이 파일에는 패키지에 포함된 모든 자산 확장명이 나열됩니다. 해당 파일의 예제 코드는 다음과 같습니다.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

마지막 단계에서는 다음 항목을 파일 하나에 압축하여 이름을 **package.zip**으로 지정합니다. 이 파일에는 다음 항목이 포함됩니다.

1. PkgFolder(폴더 내의 모든 항목 포함)
2. dll
3. **Content_Types.xml**

package.zip을 만드는 단계는 다음과 같습니다.

1. 패키지 폴더, **Content_Types.xml** 파일 및 PackageName.dll을 디렉터리 하나에 저장합니다.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. 폴더의 모든 항목을 선택하고 마우스 오른쪽 단추를 클릭한 다음 보내기 -> 압축(ZIP) 폴더를 선택합니다.

![CRMScreenShot3](media/CRMScreenShot3.png)

1. 이름을 package.zip으로 변경합니다.

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. AppSource 패키지 만들기

AppSource 패키지에는 다음과 같은 몇 가지 추가 파일이 필요합니다.

1. jpg(해상도 32x32)
2. html(HTML 서식 파일)
3. **Content_Types.xml**(위의 파일과 같음)
4. Xml

input.xml의 예제 코드는 다음과 같습니다. 정의는 아래 표를 참조하세요.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**여기서 각 필드의 의미는 다음과 같습니다.**

|필드|세부 정보|
|---|---|
|ProviderName|솔루션의 출처입니다. Microsoft 팀이 솔루션을 제공하는 경우 Microsoft로 지정해야 합니다.|
|PackageFile |content\_types.xml 파일과 함께 압축된 Package Deployer 자산입니다. 이 zip 파일은 Package Deployer 어셈블리 및 Package Deployer 자산이 들어 있는 폴더(package.zip)를 포함해야 합니다.|
|SolutionAnchorName |솔루션 자산의 표시 이름과 설명에 사용되는 Package Deployer의 솔루션 zip 파일 이름입니다.|
| StartDate| 솔루션 패키지를 제공할 날짜입니다. MM/DD/YYYY 형식입니다.|
|EndDate|솔루션 패키지 제공을 중지할 날짜입니다. MM/DD/YYYY 형식입니다. |
|SupportedCountries |이 패키지를 표시할 국가의 쉼표로 구분된 목록입니다. 모든 현재 국가 코드의 목록을 확인하려면 온라인 서비스에 문의하세요. 동시에이 문서가 작성 목록이 했습니다. AE, AL, AM, AO, 아르헨티나, AT "," 오스트레일리아, AZ "," BA "," BB "," BD, 수, BG, BH, BM, BN, BO, BY, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK, 수행, DZ, EC, EE, 브라질, 예를 들어, ES, FI, FR, GB, GE, GH, GR, GT, HK HN, HR, HU, ID, IE, IL, IN, IQ, 인 것, JM, JO, JP, KE, KG, KN, 한국, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, 월, MT, MU, MX, MY, NG, NI, NL, 아니요, NZ OM, PA, PE, PH PK, PL, PR, PS, PT, PY, QA, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA, 미국, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | 이 패키지의 추가 정보 페이지 URL입니다. |
|Locales|기본 설정 솔루션 UX에서 지원할 각 UX 언어에 대해 이 노드 인스턴스를 하나씩 생성합니다. 이 노드에는 각 언어의 로캘, 로고 및 용어를 설명하는 하위 요소가 포함됩니다.|
|로캘: PackageLocale.Code|이 노드의 언어 LCID입니다. 예제: 영어 (미국)가 1033|
|로캘: PackageLocale.IsDefault|기본 언어를 나타냅니다. 고객이 선택한 UX 언어가 제공되지 않는 경우 대체 언어로 사용됩니다.|
|로캘: 로고|이 패키지에 사용할 로고입니다. 아이콘 크기는 32x32입니다. 허용되는 형식은 PNG와 JPG입니다.|
|로캘: 조건: PackageTerm.File|사용 조건이 포함된 HTML 문서의 파일 이름입니다.|

로고가 표시되는 위치는 다음과 같습니다.

![CRMScreenShot5](media/CRMScreenShot5.png)

마지막 단계에서는 다음 항목을 파일 하나에 압축합니다.

1. 앞에서 만든 zip 파일
2. **Content_Types.xml**
3. Xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

파일 이름을 앱에 적합하게 바꿉니다. 회사 이름과 앱 이름을 포함하는 것이 좋습니다. 예를 들어 **_Microsoft_SamplePackage.zip**과 같은 이름을 사용합니다.
