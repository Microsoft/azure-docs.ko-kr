---
title: '서비스 간 인증: Azure Active Directory 사용 하 여 azure Data Lake Storage Gen1 | Microsoft Docs'
description: Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241367"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증
> [!div class="op_single_selector"]
> * [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1은 인증을 위해 Azure Active Directory를 사용합니다. Azure Data Lake Storage Gen1과 함께 작동하는 애플리케이션을 제작하기 전에 Azure AD(Azure Active Directory)를 사용하여 애플리케이션을 인증하는 방법을 결정해야 합니다. 사용할 수 있는 두 가지 주요 옵션은 다음과 같습니다.

* 최종 사용자 인증 
* 서비스 간 인증(이 문서) 

이 두 옵션 모두 Data Lake Storage Gen1을 대상으로 만들어진 각 요청에 연결하는 OAuth 2.0 토큰과 함께 제공되는 애플리케이션에서 발생합니다.

이 문서에서는 **서비스 간 인증을 위한 Microsoft Azure Active Directory 웹 애플리케이션을 만드는** 방법에 대해 설명합니다. 최종 사용자 인증을 위해 Azure AD 애플리케이션 구성을 수행하는 방법은 [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="step-1-create-an-active-directory-web-application"></a>1단계: Active Directory 웹 응용 프로그램 만들기

Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증을 위한 Azure AD 웹 애플리케이션을 만들고 구성합니다. 지침에 대해서는 [Azure AD 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

이전 연결에 있는 지침을 수행하는 동안 다음 스크린샷과 같이 애플리케이션 형식으로 **웹앱/API**를 선택해야 합니다.

![웹앱 만들기](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "웹앱 만들기")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2단계: 응용 프로그램 ID, 인증 키 및 테넌트 ID 가져오기
프로그래밍 방식으로 로그인하는 경우 애플리케이션에 대한 ID가 필요합니다. 애플리케이션이 자체 자격 증명에서 실행되는 경우 인증 키도 필요합니다.

* 애플리케이션에 대한 애플리케이션 ID 및 인증 키(클라이언트 비밀이라고도 함)를 검색하는 방법에 대한 지침은 [애플리케이션 ID 및 인증 키 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)를 참조하세요.

* 테넌트 ID를 검색하는 방법에 대한 지침은 [테넌트 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)를 참조하세요.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>3단계: Azure 데이터 레이크 저장소 Gen1 계정 파일 또는 폴더를 Azure AD 응용 프로그램 할당


1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 앞에서 만든 Azure Active Directory 애플리케이션에 연결할 Data Lake Storage Gen1 계정을 엽니다.
2. Data Lake Storage Gen1 계정 블레이드에서 **데이터 탐색기**를 클릭합니다.
   
    ![Data Lake Storage Gen1 계정에 디렉터리 만들기](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Data Lake 계정에 디렉터리 만들기")
3. **데이터 탐색기** 블레이드에서 Azure AD 애플리케이션에 대한 액세스를 제공할 파일 또는 폴더를 클릭하고 **액세스**를 클릭합니다. 파일에 대한 액세스를 구성하려면 **파일 미리 보기** 블레이드에서 **액세스**를 클릭해야 합니다.
   
    ![Data Lake 파일 시스템에 ACL 설정](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Data Lake 파일 시스템에 ACL 설정")
4. **액세스** 블레이드는 루트에 이미 할당된 표준 액세스 및 사용자 지정 액세스를 나열합니다. **추가** 아이콘을 클릭하여 사용자 지정 수준 ACL을 추가합니다.
   
    ![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")
5. **추가** 아이콘을 클릭하여 **사용자 지정 액세스 추가** 블레이드를 엽니다. 이 블레이드에서 **사용자 또는 그룹 선택**을 클릭한 다음, **사용자 또는 그룹 선택** 블레이드에서 이전에 만든 Azure Active Directory 애플리케이션을 찾습니다. 검색할 그룹이 많은 경우 위쪽의 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. 추가하려는 그룹을 클릭한 다음 **선택**을 클릭합니다.
   
    ![그룹 추가](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "그룹 추가")
6. **사용 권한 선택**을 클릭하고 사용 권한 및 이러한 권한을 기본 ACL로 할당할지, 액세스 ALC로 할당할지 또는 둘 다로 할당할지 선택합니다. **확인**을 클릭합니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "그룹에 권한 할당")
   
    Data Lake Storage Gen1의 사용 권한 및 기본/액세스 ACL에 대한 자세한 내용은 [Data Lake Storage Gen1에서 액세스 제어](data-lake-store-access-control.md)를 참조하세요.
7. **사용자 지정 액세스 추가** 블레이드에서 **확인**을 클릭합니다. 이제 연결된 권한을 사용하여 새로 추가된 그룹이 **액세스** 블레이드에 나열됩니다.
   
    ![그룹에 권한 할당](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "그룹에 권한 할당")

> [!NOTE]
> Azure Active Directory 애플리케이션을 특정 폴더로 제한하려면 동일한 Azure Active Directory 애플리케이션에 루트에 대한 **실행** 권한도 부여하여 .NET SDK를 통한 파일 생성 액세스를 활성화해야 합니다.

> [!NOTE]
> SDK를 사용하여 Data Lake Storage Gen1 계정을 만들려면 Data Lake Storage Gen1 계정을 만들 리소스 그룹에 Azure AD 웹 애플리케이션을 역할로 할당해야 합니다.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4단계: OAuth 2.0 토큰 엔드포인트 (Java 기반 응용 프로그램)에 가져오기

1. [Azure Portal](https://portal.azure.com)에 로그온하고 왼쪽 창에서 Active Directory를 클릭합니다.

2. 왼쪽 창에서 **앱 등록**을 클릭합니다.

3. 앱 등록 블레이드 맨 위에서 **엔드포인트**를 클릭합니다.

    ![OAuth 토큰 엔드포인트](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth 토큰 엔드포인트")

4. 엔드포인트 목록에서 OAuth 2.0 토큰 엔드포인트를 복사합니다.

    ![OAuth 토큰 엔드포인트](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth 토큰 엔드포인트")   

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Azure Active Directory 웹 애플리케이션을 만들고 .NET SDK, Java, Python, REST API 등을 사용하여 만든 클라이언트 애플리케이션에 필요한 정보를 수집했습니다. 이제 다음 문서를 읽고 Azure AD 네이티브 애플리케이션을 사용하여 Data Lake Storage Gen1로 인증한 다음, 저장소에서 다른 작업을 수행하는 방법을 알아볼 수 있습니다.

* [Java를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-java.md)
* [.NET SDK를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Python을 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-python.md)
* [REST API를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-rest-api.md)


