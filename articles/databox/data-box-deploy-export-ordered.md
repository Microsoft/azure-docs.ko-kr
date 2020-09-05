---
title: Azure Data Box에서 데이터를 내보내는 자습서 | Microsoft Docs
description: 배포 필수 구성 요소 및 Azure Data Box에서 데이터를 내보내는 방법에 대해 알아봅니다.
services: databox
author: twooley
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 07/10/2020
ms.author: twooley
ms.openlocfilehash: 0ddadd8d2bddda0fdff6a126fe6c09d863139b44
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783623"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>자습서: Azure Data Box (미리 보기)에 대 한 내보내기 순서 만들기

Azure Data Box은 Azure에서 사용자의 위치로 데이터를 이동할 수 있도록 하는 하이브리드 솔루션입니다. 이 자습서에서는 Azure Data Box에 대 한 내보내기 주문을 만드는 방법을 설명 합니다. 내보내기 순서를 만드는 주된 이유는 재해 복구를 위한 것입니다. 온-프레미스 저장소가 손상 되 고 백업을 복원 해야 하는 경우가 여기에 해당 합니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 내보내기에 대 한 필수 구성 요소
> * 내보내기에 대 한 Data Box 주문
> * 내보내기 순서 추적
> * 내보내기 순서를 취소 합니다.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>사전 요구 사항

장치를 주문 하기 전에 Data Box 서비스 및 장치에 대 한 다음 구성 필수 구성 요소를 완료 합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Azure Data Box 사용할 수 있는 기존 리소스 그룹이 있는지 확인 합니다.

* 데이터를 내보내려는 Azure Storage 계정이 [Data Box에 대해 지원 되는 저장소 계정](data-box-system-requirements.md#supported-storage-accounts)에 설명 된 대로 지원 되는 저장소 계정 유형 중 하나 인지 확인 합니다.

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.

* 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Azure Data Box의 데이터를이 컴퓨터로 복사 합니다. [Azure Data Box 시스템 요구 사항](data-box-system-requirements.md)에 설명된 대로 호스트 컴퓨터는 지원되는 운영 체제를 실행해야 합니다.

* 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용할 수 있지만 복사 속도에 영향을 미칩니다.

## <a name="order-data-box-for-export"></a>내보내기에 대 한 Data Box 주문

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)

2. **+ 리소스 만들기**를 선택하고 *Azure Data Box*를 검색합니다. **Azure Data Box**를 선택합니다.

   ![리소스 만들기](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. **만들기**를 선택합니다.

   ![Azure Data Box 만들기](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. 사용자의 지역에서 Azure Data Box 서비스를 사용할 수 있는지 확인 합니다. 다음 정보를 입력하거나 선택하고 **적용**을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |전송 형식     | **Azure로 내보내기를**선택 합니다.        |
    |Subscription     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |Resource group     |    기존 리소스 그룹을 선택합니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다.         |
    |원본 Azure 지역    |    현재 데이터가 있는 Azure 지역을 선택 합니다.         |
    |대상 국가     |     장치를 배송 하려는 국가를 선택 합니다.        |

   ![Data Box 설정 선택](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. **Data Box**를 선택합니다. 단일 주문의 최대 사용 가능한 용량은 80TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

   ![Data Box 용량 선택](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. **순서**대로 **기본** 주문 세부 정보를 지정 합니다. 다음 정보를 입력하거나 선택하고 **다음**을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |Subscription     | 이전에 선택한 내용에 따라 구독이 자동으로 채워집니다.|
    |Resource group | 이전에 선택한 리소스 그룹입니다. |
    |내보내기 주문 이름     |  주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.      |

    ![내보내기 주문 기본 사항](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    **다음: 데이터 선택** 을 선택 하 여 계속 진행 합니다.

7. **데이터 선택**에서 **저장소 계정 추가 및 형식 내보내기**를 선택 합니다.

    ![저장소 계정 및 내보내기 유형 추가](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. **내보내기 옵션 선택**에서 내보내기 옵션 세부 정보를 지정 합니다. 다음 정보를 입력 하거나 선택 하 고 **추가**를 선택 합니다.

    |설정  |값  |
    |---------|---------|
    |스토리지 계정     | 데이터를 내보낼 Azure Storage 계정입니다. |
    |내보내기 유형     | **모든 개체** 에서 내보내고 **XML 파일을 사용**하는 데이터 형식을 지정 합니다.<ul><li> **모든 개체** - **전송 옵션**에 대 한 선택 항목에 따라 작업에서 모든 데이터를 내보내도록 지정 합니다.</li><li> **Xml 파일 사용** – 저장소 계정에서 내보낼 blob 및/또는 파일의 경로 및 접두사 집합을 포함 하는 xml 파일을 지정 합니다. XML 파일은 선택한 저장소 계정의 컨테이너에 있어야 하 고, 파일 공유에서 선택 하는 기능은 현재 지원 되지 않습니다. 파일은 비어 있지 않은 .xml 파일 이어야 합니다.</li></ul>        |
    |전송 옵션     |  **모두 선택**, **모든 blob**및 **모든 파일**에서 데이터 전송 옵션을 지정 합니다. <ul><li> **모두 선택** -모든 Blob 및 Azure 파일을 내보내도록 지정 합니다. Blob만 지 원하는 저장소 계정 (Blob Storage 계정)을 사용 하는 경우 **모든 파일** 옵션은 선택할 수 없습니다.</li><li> **모든 blob** -블록 및 페이지 blob만 내보내도록 지정 합니다.</li><li> **모든 파일** -blob을 제외 하 고 모든 파일을 내보내도록 지정 합니다. 저장소 계정 유형 (GPv1 및 GPv2, premium storage 또는 blob storage)은 내보낼 수 있는 데이터의 유형을 결정 합니다. 자세한 내용은 [지원 되는 저장소 계정 내보내기](../storage/common/storage-import-export-requirements.md#supported-storage-types)를 참조 하세요.</li></ul>         |
    |자세한 로그 포함     | 성공적으로 내보낸 모든 파일의 목록을 포함 하는 자세한 로그 파일이 필요한 지 여부를 나타냅니다.        |

    > [!NOTE]
    >
    > **내보내기 유형** 설정에 **xml 파일 사용** 을 선택 하는 경우 xml에 유효한 경로 및/또는 접두사가 포함 되어 있는지 확인 해야 합니다. XML 파일을 생성 하 고 제공 해야 합니다.  파일이 잘못 되었거나 지정 된 경로와 일치 하는 데이터가 없는 경우에는 주문이 부분 데이터로 종료 되거나 데이터를 내보내지 않습니다.

    컨테이너에 XML 파일을 추가 하는 방법에 대 한 자세한 내용은 [xml 파일을 사용 하 여 순서 내보내기](data-box-deploy-export-ordered.md#export-order-using-xml-file)를 참조 하세요.

   ![내보내기 옵션 선택](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Xml 입력의 예를 보려면 [샘플 xml 입력](data-box-deploy-export-ordered.md#sample-xml-file) 을 참조 하세요.

9. **데이터 선택**에서 설정을 검토 하 고 **다음: 연락처 세부 정보>** 를 선택 합니다.

   ![연락처 세부 정보](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

10. **연락처 세부 정보**에서 **+ 배송 주소 추가** 를 선택 하 여 배송 정보를 입력 합니다.

    ![배송 주소 추가](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. **배송 주소 추가**에서 회사의 성과 이름, 이름 및 우편 주소를 입력 하 고 유효한 전화 번호를 입력 합니다. **유효성 검사**를 선택합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다.

    ![배송 주소 유효성 검사](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    자체 관리 전달 기능을 사용할 수 있는 지역에서 순서를 지정 하는 경우이 옵션을 선택할 수 있습니다. 자체 관리형 배송에 대한 자세한 내용은 [자체 관리형 배송 사용](data-box-portal-customer-managed-shipping.md)을 참조하세요.

12. 배송 세부 정보의 유효성을 검사 하 고 나면 **배송 주소 추가** 를 선택 합니다.

13. **연락처 세부 정보**에서 배송 주소와 전자 메일 주소를 검토 합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

    ![주문 세부 정보](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. **다음: 검토 + 순서>** 를 선택 합니다. 주문 만들기를 계속 하려면 사용 약관에 동의 해야 합니다.

15. **주문**을 선택합니다. 주문을 만드는 데 몇 분 정도 걸립니다.

    ![커밋 순서](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>XML 파일을 사용 하 여 주문 내보내기

**XML 파일 사용**을 선택 하는 경우 내보내려는 특정 컨테이너와 blob (페이지 및 블록)를 지정할 수 있습니다. XML 서식 지정에 대 한 [예제 xml 파일 테이블](#sample-xml-file) 사양을 따라야 합니다. 아래 단계에서는 XML 파일을 사용 하 여 데이터를 내보내는 방법을 보여 줍니다.

1. **내보내기 유형**에서 **XML 파일 사용**을 선택 합니다. 내보내려는 특정 blob 및 Azure 파일을 지정 하는 XML 파일입니다. XML 파일을 추가 하려면 **xml 파일을 선택 하려면 여기를 클릭**하십시오 .를 선택 합니다.
     ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. **+ 컨테이너** 를 선택 하 여 컨테이너를 만듭니다.
    ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. Azure Portal 오른쪽에서 팝 되는 **새 컨테이너** 탭에서 컨테이너의 이름을 추가 합니다. 이름은 소문자 여야 하 고 숫자 및 대시 '-'를 포함할 수 있습니다. 그런 다음 드롭다운 목록 상자에서 **공용 액세스 수준을** 선택 합니다. 다른 사용자가 데이터에 액세스 하지 못하도록 하려면 **개인 (익명이 아닌 액세스)** 을 선택 하는 것이 좋습니다. 컨테이너 액세스 수준에 대 한 자세한 내용은 [컨테이너 액세스 권한](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)을 참조 하세요.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. **만들기**를 선택합니다.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   컨테이너가 성공적으로 생성 되 면 다음과 같은 메시지가 표시 됩니다.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. 만든 컨테이너를 선택 하 고 두 번 클릭 합니다.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. 컨테이너를 두 번 클릭 하면 컨테이너 속성 보기가 표시 됩니다. 이제 내보내려는 blob 및/또는 Azure 파일 목록이 포함 된 XML 파일을 첨부 (또는 탐색) 하려고 합니다. **업로드**를 선택합니다.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. 컨테이너에 XML 파일을 추가 했습니다. 이 XML에서 지정한 blob 및 Azure 파일만 내보내집니다.

   ![XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box 주문, **개요**로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

장치 준비가 완료 되 면 선택한 저장소 계정에서 데이터 복사가 시작 됩니다. 포털에서는 **데이터 복사 진행** 중 상태의 순서를 표시 합니다.

![Data Box 내보내기 주문 처리 됨](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box은 원본 저장소 계정에서 데이터를 복사 합니다. 데이터 복사가 완료 되 면 Data Box 잠기고 포털에 **복사 완료 됨** 상태가 표시 됩니다.

![Data Box 내보내기 데이터 복사 완료](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

장치를 사용할 수 없는 경우 알림을 받게 됩니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 장치를 준비 하는 동안 다음 작업이 수행 됩니다.

* 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
* 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
* 장치가 잠겨 있으며 장치 잠금 해제 암호를 사용 하 여 액세스할 수 있습니다. 암호를 검색 하려면 Azure Portal 계정에 로그인 하 고 **장치 세부 정보**를 선택 해야 합니다.

그러면 Microsoft에서 지역 캐리어를 통해 장치를 준비 하 고 디스패치합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box 내보내기 주문 디스패치](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

자체 관리 배송을 선택한 경우 데이터 센터에서 장치를 선택할 준비가 되 면 다음 단계와 함께 전자 메일 알림을 받게 됩니다. 자체 관리 배송에 대 한 자세한 내용은 [자체 관리 전달](data-box-portal-customer-managed-shipping.md)을 참조 하세요.

![픽업에 대해 자체 관리 되는 배송 준비](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요**로 이동하고, 명령 모음에서 **취소**를 선택합니다.

주문을 처리 하기 전에 언제 든 지 취소할 수 있습니다.

취소된 주문을 삭제하려면 **개요**로 이동하고, 명령 모음에서 **삭제**를 선택합니다.

## <a name="sample-xml-file"></a>샘플 XML 파일

다음 xml은 **xml 파일 사용** 옵션을 선택할 때 내보내기 순서에서 사용 하는 xml 형식에 포함 된 blob 이름, blob 접두사 및 Azure 파일의 예를 보여 줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Xml 파일에 대 한 몇 가지 중요 한 사항은 다음과 같습니다.

* Xml 태그는 대/소문자를 구분 하므로 위의 샘플에 지정 된 것과 정확 하 게 일치 해야 합니다.
* 여는 태그와 닫는 태그는 일치 해야 합니다.
* Xml 태그나 형식이 잘못 되 면 데이터 내보내기 오류가 발생할 수 있습니다.
* Blob 및/또는 파일 접두사가 잘못 된 경우 데이터를 내보낼 수 없습니다.

### <a name="examples-of-valid-blob-paths"></a>유효한 Blob 경로의 예

다음 표에는 유효한 Blob 경로의 예가 있습니다.

   | 선택기 | Blob 경로 | 설명 |
   | --- | --- | --- |
   | 시작 문자 |/ |스토리지 계정의 모든 Blob을 내보냄 |
   | 시작 문자 |/$root/ |루트 컨테이너의 모든 Blob을 내보냄 |
   | 시작 문자 |/컨테이너 |접두사 **컨테이너로** 시작 하는 모든 컨테이너의 모든 blob을 내보냅니다. |
   | 시작 문자 |/container-name/ |컨테이너 **컨테이너 이름** 에 있는 모든 blob을 내보냅니다. |
   | 시작 문자 |/container-name/prefix |접두사 **접두사로** 시작 하는 컨테이너 **컨테이너 이름** 에 있는 모든 blob을 내보냅니다. |
   | 같음 |$root/logo.bmp |루트 컨테이너의 Blob **logo.bmp** 를 내보냄 |
   | 같음 |8tbpageblob/mydata.txt |컨테이너 **8tbpageblob** blob **mydata.txt** 를 내보냅니다. |

## <a name="sample-log-files"></a>샘플 로그 파일

이 섹션에서는 내보내기 중 생성 되는 샘플 로그 파일을 제공 합니다. 오류 로그는 자동으로 생성 됩니다. 자세한 정보 표시 로그 파일을 생성 하려면 내보내기 순서를 구성할 때 Azure Portal에 **자세한 정보 표시 로그 포함** 을 선택 해야 합니다.
복사 및 자세한 정보 로그에 대 한 자세한 내용은 [로그 복사](data-box-deploy-export-copy-data.md#copy-data-from-data-box)를 참조 하세요.

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * 내보내기에 대 한 필수 구성 요소
> * 내보내기에 대 한 Data Box 주문
> * 내보내기 순서 추적
> * 내보내기 순서를 취소 합니다.

Data Box를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box 설정](./data-box-deploy-set-up.md)
