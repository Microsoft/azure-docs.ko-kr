---
title: Storage Explorer로 Azure Blob Storage 리소스 관리 | Microsoft Docs
description: Storage Explorer로 Azure Blob 컨테이너 및 Blob 관리
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442974"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리

## <a name="overview"></a>개요

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md)는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다.
Blob Storage를 사용하여 세상에 공개적으로 표시하거나 애플리케이션 데이터를 비공개적으로 저장할 수 있습니다. 이 문서에서는 Storage Explorer를 사용하여 Blob 컨테이너 및 Blob으로 작업하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음과 같이 하는 것이 필요합니다.

* [Storage 탐색기 다운로드 및 설치](https://www.storageexplorer.com)
* [Azure storage 계정 또는 서비스에 연결](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blob 컨테이너 만들기

모든 blob은 단지 blob의 논리적 그룹화인 blob 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에 제한이 없으며, 각 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.

다음 단계에서는 Storage Explorer 내에서 Blob 컨테이너를 만드는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 blob 컨테이너를 만들고자 하는 곳의 스토리지 계정을 확장합니다.
3. 마우스 오른쪽 단추로 **Blob 컨테이너**를 클릭하고, 상황에 맞는 메뉴에서 **Blob 컨테이너 만들기**를 선택합니다.

   ![Blob 컨테이너 상황에 맞는 메뉴 만들기][0]
4. 텍스트 상자가 **Blob 컨테이너** 폴더 아래에 표시됩니다. Blob 컨테이너에 대한 이름을 입력합니다. 참조 [컨테이너를 만드는](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) 규칙 및 blob 컨테이너 이름 명명 제한 사항에 대한 정보에 대한 합니다.

   ![Blob 컨테이너 텍스트 상자 만들기][1]
5. Blob 컨테이너 만들기가 끝나면 **Enter** 키를 누르거나 **Esc** 키를 눌러 취소합니다. Blob 컨테이너가 성공적으로 만들어졌다면 선택한 스토리지 계정에 대해 **Blob 컨테이너** 폴더 아래에 표시됩니다.

   ![만든 Blob 컨테이너][2]

## <a name="view-a-blob-containers-contents"></a>Blob 컨테이너 내용 보기

Blob 컨테이너는 blob 및 폴더(blob도 포함할 수 있음)를 포함하고 있습니다.

다음 단계에서는 Storage Explorer 내에서 Blob 컨테이너의 콘텐츠를 보는 방법을 설명합니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 보려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 마우스 오른쪽 단추로 보려는 Blob 컨테이너를 클릭하고, 상황에 맞는 메뉴에서 **Blob 컨테이너 편집기 열기**를 클릭합니다.
   또한 보려는 blob 컨테이너를 두 번 클릭할 수도 있습니다.

   ![blob 컨테이너 편집기 상황에 맞는 메뉴 열기][19]
5. 기본 창에 blob 컨테이너 내용이 표시됩니다.

   ![Blob 컨테이너 편집기][3]

## <a name="delete-a-blob-container"></a>Blob 컨테이너 삭제

필요에 따라 Blob 컨테이너를 쉽게 만들고 삭제할 수 있습니다. (개별 Blob 삭제 방법에 대한 자세한 내용은 [Blob 컨테이너의 Blob 관리](#managing-blobs-in-a-blob-container)섹션을 참조하세요.)

다음 단계에서는 Storage Explorer 내에서 Blob 컨테이너를 삭제하는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 보려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 마우스 오른쪽 단추로 삭제하려는 Blob 컨테이너를 클릭하고, 상황에 맞는 메뉴에서 **삭제**를 클릭합니다.
   또한 현재 선택된 blob 컨테이너를 삭제하려면 **삭제** 를 누를 수 있습니다.

   ![Blob 컨테이너 상황에 맞는 메뉴 삭제][4]
5. 확인 대화 상자에서 **확인** 을 선택합니다.

   ![Blob 컨테이너 확인 삭제][5]

## <a name="copy-a-blob-container"></a>Blob 컨테이너 복사

Storage Explorer를 사용하여 Blob 컨테이너를 클립보드에 복사한 다음, 다른 스토리지 계정에 붙여넣을 수 있습니다. (개별 Blob 복사 방법에 대한 자세한 내용은 [Blob 컨테이너의 Blob 관리](#managing-blobs-in-a-blob-container)섹션을 참조하세요.)

다음 단계에서는 한 스토리지 계정에서 다른 계정으로 blob 컨테이너를 복사하는 방법을 보여 줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 복사하려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 마우스 오른쪽 단추로 복사하려는 Blob 컨테이너를 클릭하고, 상황에 맞는 메뉴에서 **Blob 컨테이너 복사**를 클릭합니다.

   ![Blob 컨테이너 상황에 맞는 메뉴 복사][6]
5. 마우스 오른쪽 단추로 blob 컨테이너를 붙여 넣을 원하는 "대상" 저장소 계정을 클릭하고, 상황에 맞는 메뉴에서 **Blob 컨테이너 붙여넣기**를 선택합니다.

   ![Blob 컨테이너 상황에 맞는 메뉴 붙여넣기][7]

## <a name="get-the-sas-for-a-blob-container"></a>Blob 컨테이너에 대한 SAS 가져오기

[SAS(공유 액세스 서명)](storage/common/storage-dotnet-shared-access-signature-part-1.md)는 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다.
즉, 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에게 스토리지 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다.

다음 단계에서는 Blob 컨테이너에 대한 SAS를 만드는 방법을 보여 줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 SAS 가져오기를 하려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 마우스 오른쪽 단추로 원하는 blob 컨테이너를 클릭하고, 상황에 맞는 메뉴에서 **공유 액세스 서명 가져오기**를 클릭합니다.

   ![SAS 상황에 맞는 메뉴 가져오기][8]
5. **공유 액세스 서명** 대화 상자에서 정책, 시작 및 만료 날짜, 표준 시간대, 리소스에 적용할 액세스 수준을 지정합니다.

   ![SAS 옵션 가져오기][9]
6. SAS 옵션 지정하기를 마치면 **만들기**를 선택합니다.
7. Blob 컨테이너와 함께 스토리지 리소스에 액세스하는 데 사용할 수 있는 URL 및 쿼리 문자열이 나열되는 두 번째 **공유 액세스 서명** 대화 상자가 표시됩니다.
   클립보드에 복사할 URL 옆에 있는 **복사** 를 선택합니다.

   ![SAS Url 복사][10]
8. 완료되면 **닫기**를 선택합니다.

## <a name="manage-access-policies-for-a-blob-container"></a>Blob 컨테이너에 대한 액세스 정책 관리

다음 단계에서는 Blob 컨테이너에 대한 액세스 정책을 관리(추가 및 제거)하는 방법을 보여 줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 액세스 정책을 관리하려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 원하는 blob 컨테이너를 선택하고, 상황에 맞는 메뉴에서 **액세스 정책 관리**를 선택합니다.

   ![액세스 정책 상황에 맞는 메뉴 관리][11]
5. **액세스 정책** 대화 상자는 선택된 blob 컨테이너에 대해 이미 만들어진 모든 액세스 정책을 나열합니다.

   ![액세스 정책 옵션][12]
6. 액세스 정책 관리 작업에 따라 다음 단계를 수행하세요.

   * **새 액세스 정책 추가** - **추가**를 선택합니다. 생성되었다면 **액세스 정책** 대화 상자는 (기본 설정을 사용하여) 새로 추가된 액세스 정책을 표시합니다.
   * **액세스 정책 편집** -원하는 편집을 모두 마치고, **저장**을 선택합니다.
   * **액세스 정책 제거** - 제거하려는 액세스 정책 옆에 있는 **제거**를 선택합니다.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Blob 컨테이너에 대한 공용 액세스 수준 설정

기본적으로 모든 blob 컨테이너는 “공용 액세스 없음”으로 설정됩니다.

다음 단계에서는 Blob 컨테이너에 대한 공용 액세스 수준을 지정하는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 액세스 정책을 관리하려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 원하는 blob 컨테이너를 선택하고, 상황에 맞는 메뉴에서 **공용 액세스 수준 설정**을 선택합니다.

   ![공용 액세스 수준 상황에 맞는 메뉴 설정][13]
5. **컨테이너 공용 액세스 수준 설정** 대화 상자에서 원하는 액세스 수준을 지정합니다.

   ![공용 액세스 수준 옵션 설정][14]
6. **적용**을 선택합니다.

## <a name="managing-blobs-in-a-blob-container"></a>blob 컨테이너의 blob 관리

Blob 컨테이너를 만들었다면 blob 컨테이너에 blob 업로드, 로컬 컴퓨터에 blob 다운로드, 로컬 컴퓨터에서 blob 열기 등 많은 작업을 할 수 있습니다.

다음 단계에서는 Blob 컨테이너 내에서 blobs (및 폴더)를 관리하는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.
2. 왼쪽 창에서 관리하려는 blob 컨테이너가 들어 있는 스토리지 계정을 확장합니다.
3. 스토리지 계정의 **Blob 컨테이너**를 확장합니다.
4. 보려는 blob 컨테이너를 두 번 클릭합니다.
5. 기본 창에 blob 컨테이너 내용이 표시됩니다.

   ![Blob 컨테이너 보기][3]
6. 기본 창에 blob 컨테이너 내용이 표시됩니다.
7. 수행하려는 작업에 따라서 다음 단계를 수행하세요.

   * **Blob 컨테이너에 파일 업로드**

     1. 기본 창 도구 모음에서 **업로드**를 선택하고, 드롭 다운 메뉴에서 **파일 업로드**를 합니다.

        ![파일 메뉴 업로드][15]
     2. **파일 업로드** 대화 상자에서 **파일** 텍스트 상자 오른쪽에 있는 줄임표( **…** ) 단추를 선택하여 업로드할 파일을 선택합니다.

        ![파일 옵션 업로드][16]
     3. **Blob 유형**을 지정합니다. 참조 [컨테이너를 만드는](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) 자세한 내용은 합니다.
     4. 필요에 따라 선택한 파일을 업로드할 대상 폴더를 지정합니다. 대상 폴더가 없다면 폴더가 만들어집니다.
     5. **업로드**를 선택합니다.
   * **Blob 컨테이너에 폴더 업로드**

     1. 기본 창 도구 모음에서 **업로드**를 선택하고, 드롭 다운 메뉴에서 **폴더 업로드**를 선택합니다.

        ![폴더 메뉴 업로드][17]
     2. **폴더 업로드** 대화 상자에서 **폴더** 텍스트 상자 오른쪽에 있는 줄임표( **…** ) 단추를 선택하여 내용을 업로드할 폴더를 선택합니다.

        ![폴더 옵션 업로드][18]
     3. **Blob 유형**을 지정합니다. 참조 [컨테이너를 만드는](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) 자세한 내용은 합니다.
     4. 필요에 따라 선택한 폴더 내용을 업로드할 대상 폴더를 지정합니다. 대상 폴더가 없다면 폴더가 만들어집니다.
     5. **업로드**를 선택합니다.
   * **로컬 컴퓨터에 blob 다운로드**

     1. 다운로드하려는 blob을 선택합니다.
     2. 기본 창 도구 모음에서 **다운로드**를 선택합니다.
     3. **다운로드한 blob을 저장할 위치 지정** 대화 상자에서 blob을 다운로드할 위치와 명명하려는 이름을 지정합니다.  
     4. **저장**을 선택합니다.
   * **로컬 컴퓨터에서 blob 열기**

     1. 열려는 blob을 선택합니다.
     2. 기본 창 도구 모음에서 **열기**를 선택합니다.
     3. Blob은 blob의 기본 파일 형식과 연결된 애플리케이션을 사용하여 다운로드하고 엽니다.
   * **blob을 클립보드에 복사**

     1. 복사하려는 blob을 선택합니다.
     2. 기본 창 도구 모음에서 **복사**를 선택합니다.
     3. 왼쪽 창에서 또 다른 blob 컨테이너로 이동하고 기본 창에서 보려면 두 번 클릭합니다.
     4. 기본 창 도구 모음에서 blob의 복사본을 만들려면 **붙여넣기** 를 선택합니다.
   * **Blob 삭제**

     1. 삭제하려는 blob을 선택합니다.
     2. 기본 창 도구 모음에서 **삭제**를 선택합니다.
     3. 확인 대화 상자에서 **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [최신 Storage 탐색기 릴리스 정보 및 비디오](https://www.storageexplorer.com)를 보세요.
* [Azure blob, 테이블, 큐 및 파일을 사용하여 애플리케이션을 작성](https://azure.microsoft.com/documentation/services/storage/)하는 방법 알아보기.

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
