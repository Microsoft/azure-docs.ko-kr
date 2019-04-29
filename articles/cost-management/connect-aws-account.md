---
title: Azure의 Cloudyn에 Amazon Web Services 계정 연결 | Microsoft Docs
description: Cloudyn 보고서에서 비용 및 사용 현황 데이터를 보려면 Amazon Web Services 계정을 연결합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 1d87774ab667dd2d4f7f9fa7827a3ddf17c09044
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431774"
---
# <a name="connect-an-amazon-web-services-account"></a>Amazon Web Services 계정 연결

AWS(Amazon Web Services)를 Cloudyn에 연결하는 옵션은 두 가지입니다. IAM 역할 또는 읽기 전용 IAM 사용자 계정으로 연결할 수 있습니다. 정의된 권한이 포함된 액세스 권한을 신뢰할 수 있는 엔터티에 위임할 수 있으므로 IAM 역할을 사용하는 것이 좋습니다. IAM 역할은 장기 액세스 키를 공유하지 않아도 됩니다. AWS 계정을 Cloudyn에 연결하면 Cloudyn 보고서에서 비용 및 사용 현황 데이터를 볼 수 있습니다. 이 문서에서는 두 가지 옵션을 모두 안내합니다.

AWS IAM ID에 대한 자세한 내용은 [ID(사용자, 그룹 및 역할)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)를 참조하세요.

또한 ASW 상세 청구 보고서를 사용하도록 설정하고 해당 정보를 AWS 단순 저장소 서비스(S3) 버킷에 저장합니다. 상세 청구 보고서는 태그와 시간별 리소스 정보를 포함하는 청구 정보를 제공합니다. 보고서를 저장하면 Cloudyn이 버킷에서 정보를 가져와 해당 보고서에 표시할 수 있습니다.


## <a name="aws-role-based-access"></a>AWS 역할 기반 액세스

다음 섹션에서는 읽기 전용 IAM 역할을 만들어서 Cloudyn에 대한 액세스 권한을 제공하는 과정을 안내합니다.

### <a name="get-your-cloudyn-account-external-id"></a>Cloudyn 계정 외부 ID 가져오기

첫 번째 단계는 Cloudyn 포털에서 고유한 연결 암호를 가져오는 것입니다. AWS에서 **외부 ID**로 사용됩니다.

1. Azure Portal에서 Cloudyn 포털을 열거나 [https://azure.cloudyn.com](https://azure.cloudyn.com)으로 이동하여 로그인합니다.
2. 톱니바퀴 기호를 클릭하고 **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +** 를 클릭합니다.
4. **AWS 계정 추가** 대화 상자에서 **외부 ID**를 복사하여 다음 섹션의 AWS 역할 만들기 단계를 위해 저장합니다. 외부 ID는 계정에 고유합니다. 다음 그림에서 예제 외부 ID는 _Contoso_와 숫자로 이루어집니다. 사용자의 ID는 다릅니다.  
    ![AWS 계정 추가 상자에 표시된 외부 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS 읽기 전용 역할 기반 액세스 추가

1. https://console.aws.amazon.com/iam/home에서 AWS 콘솔에 로그인하고 **역할**을 선택합니다.
2. **역할 만들기**를 클릭한 다음, **Another AWS account**(다른 AWS 계정)을 선택합니다.
3. **계정 ID** 상자에 `432263259397`을 붙여넣습니다. 이 계정 ID는 AWS가 Cloudyn 서비스에 할당하는 Cloudyn 데이터 수집기 계정입니다. 표시된 정확한 계정 ID를 사용합니다.
4. **옵션** 옆에 있는 **외부 ID 필요**를 선택합니다. Cloudyn의 **외부 ID** 필드에서 이전에 복사한 고유한 값을 붙여넣습니다. 그런 다음, **다음: 사용 권한**을 클릭합니다.  
    ![Cloudyn의 외부 ID를 역할 만들기 페이지에 붙여넣기](./media/connect-aws-account/create-role01.png)
5. **권한 연결 정책**의 **정책 유형** 필터 상자 검색에 `ReadOnlyAccess`를 입력하고 **ReadOnlyAccess**를 선택한 다음, **다음: 검토**를 클릭합니다.  
    ![정책 이름 목록에서 읽기 전용 액세스 선택](./media/connect-aws-account/readonlyaccess.png)
6. 검토 페이지에서 선택 내용이 올바른지 확인하고 **역할 이름**을 입력합니다. 예: *Azure-Cost-Mgt*. **역할 설명**을 입력합니다. 예를 들어 ‘Cloudyn을 위한 역할 할당’을 입력한 다음, **역할 만들기**를 클릭합니다.
7. 만들어진 역할을 **역할** 목록에서 클릭하고 요약 페이지에서 **역할 ARN** 값을 복사합니다. 역할 ARN(Amazon Resource Name) 값은 나중에 Cloudyn에서 구성을 등록할 때 사용합니다.  
    ![요약 페이지에서 역할 ARN 복사](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Cloudyn에서 AWS IAM 역할 액세스 구성

1. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com/으로 이동하여 로그인합니다.
2. 톱니바퀴 기호를 클릭하고 **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +** 를 클릭합니다.
4. **계정 이름**에 계정의 이름을 입력합니다.
5. **액세스 유형** 옆에서 **IAM 역할**을 선택합니다.
6. **역할 ARN** 필드에 이전에 복사한 값을 붙여 넣은 다음, **저장**을 클릭합니다.  
    ![AWS 계정 추가 상자에 역할 ARN 붙여넣기](./media/connect-aws-account/add-aws-account-box.png)


AWS 계정은 계정 목록에 나타납니다. 나열된 **소유자 ID**는 역할 ARN 값과 일치합니다. **계정 상태**에는 Cloudyn이 AWS 계정에 액세스할 수 있음을 나타내는 녹색 확인 표시 기호가 있어야 합니다. 상세 AWS 청구를 사용하도록 설정할 때까지 통합 상태는 **독립 실행형**으로 표시됩니다.

![계정 관리 페이지에 표시된 AWS 계정 상태](./media/connect-aws-account/aws-account-status01.png)

Cloudyn에서 데이터를 수집하고 보고서를 채우기 시작합니다. 그런 다음, [상세 AWS 대금 청구를 사용하도록 설정](#enable-detailed-aws-billing)합니다.


## <a name="aws-user-based-access"></a>AWS 사용자 기반 액세스

다음 섹션에서는 읽기 전용 사용자를 만들어서 Cloudyn에 대한 액세스 권한을 제공하는 과정을 안내합니다.

### <a name="add-aws-read-only-user-based-access"></a>AWS 읽기 전용 사용자 기반 액세스 추가

1. https://console.aws.amazon.com/iam/home에서 AWS 콘솔에 로그인하고 **사용자**를 선택합니다.
2. **사용자 추가**를 클릭합니다.
3. **사용자 이름** 필드에 사용자 이름을 입력합니다.
4. **액세스 유형**에 **프로그래밍 방식 액세스**를 선택하고 **다음: 사용 권한**을 클릭합니다.  
    ![사용자 추가 페이지의 사용자 이름 입력](./media/connect-aws-account/add-user01.png)
5. 권한에 대해 **Attach existing policies directly**(기존 정책 직접 연결)을 선택합니다.
6. **권한 연결 정책**의 **정책 유형** 필터 상자 검색에 `ReadOnlyAccess`를 입력하고 **ReadOnlyAccess**를 선택한 다음, **다음: 검토**를 클릭합니다.  
    ![사용자에 대한 사용 권한을 설정하기 위해 ReadOnlyAccess 선택](./media/connect-aws-account/set-permission-for-user.png)
7. 검토 페이지에서 선택 내용이 올바른지 확인한 다음, **사용자 만들기**를 입력합니다.
8. 완료 페이지에 액세스 키 ID 및 비밀 액세스 키가 표시됩니다. 이 정보를 사용하여 Cloudyn에서 등록을 구성합니다.
9. **Download .csv**를 클릭하고 credentials.csv 파일을 안전한 위치에 저장합니다.  
    ![.csv 다운로드를 클릭하여 자격 증명을 저장](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Cloudyn에서 AWS IAM 사용자 기반 액세스 구성

1. Azure Portal에서 Cloudyn 포털을 열거나 https://azure.cloudyn.com/으로 이동하여 로그인합니다.
2. 톱니바퀴 기호를 클릭하고 **클라우드 계정**을 선택합니다.
3. 계정 관리에서 **AWS 계정** 탭을 클릭한 다음, **새로 추가 +** 를 클릭합니다.
4. **계정 이름**에 계정 이름을 입력합니다.
5. **액세스 유형** 옆에서 **IAM 사용자**를 선택합니다.
6. **액세스 키**에 credentials.csv 파일의 **액세스 키 ID** 값을 붙여넣습니다.
7. **비밀 키**에 credentials.csv 파일의 **비밀 액세스 키** 값을 붙여넣은 다음, **저장**을 클릭합니다.  

AWS 계정은 계정 목록에 나타납니다. **계정 상태**에는 녹색 확인 표시 기호가 있어야 합니다.

Cloudyn에서 데이터를 수집하고 보고서를 채우기 시작합니다. 그런 다음, [상세 AWS 대금 청구를 사용하도록 설정](#enable-detailed-aws-billing)합니다.

## <a name="enable-detailed-aws-billing"></a>상세 AWS 청구 사용

다음 단계를 사용하여 AWS 역할 ARN을 가져옵니다. 역할 ARN을 사용하여 청구 버킷에 대한 읽기 권한을 부여합니다.

1. https://console.aws.amazon.com에서 AWS 콘솔에 로그인하고 **서비스**를 선택합니다.
2. 서비스 검색 상자에 *IAM*을 입력하고 해당 옵션을 선택합니다.
3. 왼쪽 메뉴에서 **역할**을 선택합니다.
4. 역할 목록에서 Cloudyn 액세스에 대해 만든 역할을 선택합니다.
5. 역할 요약 페이지에서 **역할 ARN**을 클릭하여 복사합니다. 이후 단계를 위해 역할 ARN을 보관합니다.

### <a name="create-an-s3-bucket"></a>S3 버킷 만들기

상세 청구 정보를 저장할 S3 버킷을 만듭니다.

1. https://console.aws.amazon.com에서 AWS 콘솔에 로그인하고 **서비스**를 선택합니다.
2. 서비스 검색 상자에 *S3*를 입력하고 **S3**를 선택합니다.
3. Amazon S3 페이지에서 **버킷 만들기**를 클릭합니다.
4. 버킷 만들기 마법사에서 버킷 이름 및 지역을 선택하고 **다음**을 클릭합니다.  
    ![버킷 만들기 페이지의 정보 예제](./media/connect-aws-account/create-bucket.png)
5. **속성 설정** 페이지에서 기본값을 유지하고 **다음**을 클릭합니다.
6. 검토 페이지에서 **버킷 만들기**를 클릭합니다. 버킷 목록이 표시됩니다.
7. 만든 버킷을 클릭하고 **권한** 탭을 선택한 다음, **버킷 정책**을 선택합니다. 버킷 정책 편집기가 열립니다.
8. 다음 JSON의 예제를 복사한 후 버킷 정책 편집기에 붙여넣습니다.
   - `<BillingBucketName>`을 S3 버킷의 이름으로 바꿉니다.
   - `<ReadOnlyUserOrRole>`을 이전에 복사한 역할 또는 사용자 ARN으로 바꿉니다.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. **저장**을 클릭합니다.  
    ![버킷 정책 편집기에서 저장 클릭](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS 청구 보고서 사용

S3 버킷을 만들고 구성한 후 AWS 콘솔에서 [청구 기본 설정](https://console.aws.amazon.com/billing/home?#/preference)으로 이동합니다.

1. 기본 설정 페이지에서 **청구 보고서 수신**을 선택합니다.
2. **청구 보고서 수신** 아래에서 만든 버킷의 이름을 입력하고 **확인**을 클릭합니다.  
3. 4개의 모든 보고서 세분성 옵션을 선택하고 **기본 설정 저장**을 클릭합니다.  
    ![세분성을 선택하여 보고서를 사용하도록 설정](./media/connect-aws-account/enable-reports.png)

Cloudyn은 S3 버킷에서 상세 청구 정보를 검색하고 상세 청구가 사용되도록 설정된 후에 보고서를 채웁니다. 상세 청구 데이터가 Cloudyn 콘솔에 나타날 때까지 최대 24시간이 걸릴 수 있습니다. 상세 청구 데이터를 사용할 수 있게 되면 계정 통합 상태가 **통합됨**으로 표시됩니다. 계정 상태가 **완료됨**으로 표시됩니다.

![AWS 계정 탭에 표시된 통합 상태](./media/connect-aws-account/consolidated-status.png)

일부 최적화 보고서는 정확한 권장 사항을 위한 적절한 데이터 샘플 크기를 가져오는 데 며칠 동안의 데이터가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Cloudyn에 대해 자세히 알아보려면 Cloudyn용 [사용량 및 비용 검토](tutorial-review-usage.md) 자습서를 계속 확인하세요.
