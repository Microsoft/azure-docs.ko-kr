---
title: Azure Data Lake Storage Gen1의 데이터 분석 - Power BI
description: Power BI Desktop를 사용 하 여 Azure Data Lake Storage Gen1에 저장 된 데이터를 분석 하 고 시각화 하는 방법을 알아봅니다.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 282ac6f87d5ecff75e77b075ee1092f79b4923ad
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192868"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Power BI를 사용하여 Azure Data Lake Storage Gen1의 데이터 분석
이 문서에서는 Power BI Desktop을 사용하여 Azure Data Lake Storage Gen1에 저장된 데이터를 분석하고 시각화하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다. 이 문서에서는 이미 **myadlsg1**이라는 Data Lake Storage Gen1 계정을 만들어 샘플 데이터 파일(**Drivers.txt**)을 업로드했다고 가정합니다. 이 샘플 파일은 [Azure Data Lake Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)에서 다운로드할 수 있습니다.
* **Power BI Desktop**. [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=45331)에서 다운로드할 수 있습니다. 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop에서 보고서 만들기
1. 컴퓨터에서 Power BI Desktop을 시작합니다.
2. **홈** 리본 메뉴에서 **데이터 가져오기**, [자세히]를 차례로 클릭합니다. **데이터 가져오기** 대화 상자에서 **Azure**, **Azure Data Lake Store**, **연결**을 차례로 클릭합니다.
   
    ![Data Lake Storage Gen1에 연결](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Data Lake Storage Gen1에 연결")
3. 커넥터가 개발 단계에 있다는 대화 상자가 표시되면, 계속 진행하도록 선택합니다.
4. **Azure Data Lake Store** 대화 상자에서 Data Lake Storage Gen1 계정에 대한 URL을 제공한 다음, **확인**을 클릭합니다.
   
    ![Data Lake Storage Gen1의 URL](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1의 URL")
5. 다음 대화 상자에서 **로그인**을 클릭하여 Data Lake Storage Gen1 계정에 로그인합니다. 조직의 로그인 페이지로 리디렉션됩니다. 프롬프트에 따라 계정에 로그인합니다.
   
    ![Data Lake Storage Gen1에 로그인](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Data Lake Storage Gen1에 로그인")
6. 성공적으로 로그인한 후에 **연결**을 클릭합니다.
   
    ![Data Lake Storage Gen1에 연결](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Data Lake Storage Gen1에 연결")
7. 다음 대화 상자에서 Data Lake Storage Gen1 계정에 업로드한 파일이 표시됩니다. 정보를 확인한 다음 **로드**를 클릭합니다.
   
    ![Data Lake Storage Gen1에서 데이터 로드](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Data Lake Storage Gen1에서 데이터 로드")
8. 데이터가 Power BI에 성공적으로 로드된 후에, **필드** 탭에 다음 필드가 표시됩니다.
   
    ![가져온 필드](./media/data-lake-store-power-bi/imported-fields.png "가져온 필드")
   
    하지만, 데이터를 시각화하고 분석하려면, 다음과 같은 필드에 따라 데이터가 제공되는 것이 좋습니다.
   
    ![원하는 필드](./media/data-lake-store-power-bi/desired-fields.png "원하는 필드")
   
    다음 단계에서는 가져온 데이터를 원하는 형식으로 변환하기 위해 쿼리를 업데이트합니다.
9. **홈** 리본 메뉴에서 **쿼리 편집**을 클릭합니다.
   
    ![쿼리 편집](./media/data-lake-store-power-bi/edit-queries.png "쿼리 편집")
10. [쿼리 편집기]에서 **콘텐츠** 열 아래쪽의 **이진**를 클릭합니다.
    
    ![쿼리 편집](./media/data-lake-store-power-bi/convert-query1.png "쿼리 편집")
11. 업로드해 놓은 **Drivers.txt** 파일을 나타내는 파일 아이콘이 표시됩니다. 파일을 마우스 오른쪽 단추로 클릭하고 **CSV**를 클릭합니다.    
    
    ![쿼리 편집](./media/data-lake-store-power-bi/convert-query2.png "쿼리 편집")
12. 아래와 같이 출력이 표시됩니다. 이제 시각화를 만드는 데 사용할 수 있는 형식으로 데이터가 준비되었습니다.
    
    ![쿼리 편집](./media/data-lake-store-power-bi/convert-query3.png "쿼리 편집")
13. **홈** 리본 메뉴에서 **닫기 및 적용**을 클릭한 다음 **닫기 및 적용**을 다시 클릭합니다.
    
    ![쿼리 편집](./media/data-lake-store-power-bi/load-edited-query.png "쿼리 편집")
14. 쿼리가 업데이트되면, 시각화에 사용할 수 있는 새로운 필드가 **Fields** (필드) 탭에 표시됩니다.
    
    ![업데이트된 필드](./media/data-lake-store-power-bi/updated-query-fields.png "업데이트된 필드")
15. 지정된 국가/지역에 대한 각 도시의 드라이버를 나타내는 원형 차트를 만들겠습니다. 이렇게 하려면, 다음을 선택합니다.
    
    1. Visualizations(시각화) 탭에서 원형 차트 기호를 클릭합니다.
       
        ![원형 차트 만들기](./media/data-lake-store-power-bi/create-pie-chart.png "원형 차트 만들기")
    2. 사용할 열은 **열 4**(도시 이름) 및 **열 7**(국가/지역 이름)입니다. 아래와 같이 열을 **필드** 탭에서 **시각화** 탭으로 끌어갑니다.
       
        ![시각화 만들기](./media/data-lake-store-power-bi/create-visualizations.png "시각화 만들기")
    3. 이제 원형 차트가 아래와 비슷하게 표시되는 것을 볼 수 있습니다.
       
        ![원형 차트](./media/data-lake-store-power-bi/pie-chart.png "시각화 만들기")
16. 페이지 수준 필터에서 특정 국가/지역을 선택하면, 선택한 국가/지역의 각 도시 내 드라이버의 수를 볼 수 있습니다. 예를 들어 **시각화** 탭의 **페이지 수준 필터**에서 **브라질**을 선택합니다.
    
    ![국가/지역 선택](./media/data-lake-store-power-bi/select-country.png "국가/지역 선택")
17. 원형 차트가 브라질의 도시 내 드라이버를 나타내도록 자동으로 업데이트됩니다.
    
    ![국가/지역 내 드라이버](./media/data-lake-store-power-bi/driver-per-country.png "국가/지역별 드라이버")
18. **파일** 메뉴에서 **저장**을 클릭하여 시각화를 Power BI Desktop 파일로 저장합니다.

## <a name="publish-report-to-power-bi-service"></a>Power BI 서비스에 보고서 게시
Power BI Desktop에서 시각화를 만들고 나면, Power BI 서비스에 게시하여 다른 사람들과 공유할 수 있습니다. 수행하는 방법에 대한 지침은 [Power BI Desktop에서 게시](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)를 참조하세요.

## <a name="see-also"></a>참고 항목
* [Data Lake Analytics를 사용한 Data Lake Storage Gen1의 데이터 분석](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

