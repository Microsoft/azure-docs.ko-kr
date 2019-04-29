---
title: Data Lake Storage Gen1의 액세스 제어에 대한 개요 | Microsoft Docs
description: Azure Data Lake Storage Gen1에서 액세스 제어가 작동하는 방식 이해하기
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 211cb32298b17bb9e4023bf8bc74233c3916f58d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879109"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1의 액세스 제어

Azure Data Lake Storage Gen1은 HDFS에서 파생된 액세스 제어 모델을 구현하고, HDFS는 POSIX 액세스 제어 모델에서 파생됩니다. 이 문서에서는 Data Lake Storage Gen1에 대한 액세스 제어 모델의 기본 사항을 요약합니다. 

## <a name="access-control-lists-on-files-and-folders"></a>파일 및 폴더에 대한 액세스 제어 목록

**액세스 ACL** 및 **기본 ACL**이라는 두 가지 ACL(액세스 제어 목록)이 있습니다.

* **액세스 ACL**: 개체에 대한 액세스를 제어합니다. 파일과 폴더에는 모두 액세스 ACL이 있습니다.

* **기본 ACL**: 해당 폴더 아래에 만든 모든 자식 항목에 대한 액세스 ACL을 결정하는 폴더와 연결된 ACL의 "템플릿"입니다. 파일에는 기본 ACL이 없습니다.


액세스 ACL 및 기본 ACL은 모두 구조가 동일합니다.



> [!NOTE]
> 부모 항목에서 기본 ACL을 변경하면 이미 존재하는 자식 항목의 액세스 ACL 또는 기본 ACL에 적용되지 않습니다.
>
>

## <a name="permissions"></a>권한

파일 시스템 개체에 대한 권한은 **읽기**, **쓰기** 및 **실행**이며 아래 표에서 보여 주듯이 파일과 폴더에 사용할 수 있습니다.

|            |    파일     |   폴더 |
|------------|-------------|----------|
| **읽기(R)** | 파일의 내용을 읽을 수 있습니다. | 폴더의 내용을 나열하려면 **읽기** 및 **실행**이 필요합니다.|
| **쓰기(W)** | 쓰거나 파일에 추가할 수 있습니다. | 폴더에 자식 항목을 만들려면 **쓰기** 및 **실행**이 필요합니다. |
| **실행(X)** | Data Lake Storage Gen1의 컨텍스트에서 아무것도 의미하지 않습니다. | 폴더의 자식 항목을 트래버스하는 데 필요합니다. |

### <a name="short-forms-for-permissions"></a>사용 권한에 대한 짧은 형식

**RWX**는 **읽기 + 쓰기 + 실행**을 나타내는 데 사용됩니다. **읽기=4**, **쓰기=2** 및 **실행=1**의 압축된 숫자 형식이 있으며, 그 합계는 권한을 나타냅니다. 다음은 몇 가지 예입니다.

| 숫자 형식 | 짧은 형식 |      의미     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 읽기 + 쓰기 + 실행 |
| 5            | `R-X`        | 읽기 + 실행         |
| 4            | `R--`        | 읽기                   |
| 0            | `---`        | 사용 권한 없음         |


### <a name="permissions-do-not-inherit"></a>사용 권한은 상속하지 않습니다.

Data Lake Storage Gen1에서 사용하는 POSIX 스타일 모델에서 항목에 대한 권한은 항목 자체에 저장됩니다. 즉, 항목에 대한 사용 권한은 부모 항목에서 상속될 수 없습니다.

## <a name="common-scenarios-related-to-permissions"></a>사용 권한과 관련된 일반적인 시나리오

Data Lake Storage Gen1 계정에서 특정 작업을 수행하는 데 필요한 권한을 이해하는 데 도움이 되는 몇 가지 일반적인 시나리오는 다음과 같습니다.

| 작업(Operation) | Object              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| 읽기      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| 추가 | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| 삭제    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| 생성    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| 나열      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| 나열      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| 나열      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> 위의 두 조건이 참(true)이면 파일에 대한 쓰기 권한은 파일을 삭제하는 데 필요하지 않습니다.
>
>


## <a name="users-and-identities"></a>사용자 및 ID

모든 파일 및 폴더에는 이러한 ID에 대한 고유한 사용 권한이 있습니다.

* 소유 그룹
* 소유 그룹
* 명명된 사용자
* 명명된 그룹
* 기타 모든 사용자

사용자 및 그룹의 ID는 Azure AD(Azure Active Directory) ID입니다. 달리 설명하지 않는 한 Data Lake Storage Gen1의 컨텍스트에서 "사용자"는 Azure AD 사용자 또는 Azure AD 보안 그룹을 의미할 수 있습니다.

### <a name="the-super-user"></a>슈퍼 사용자

슈퍼 사용자는 Data Lake Storage Gen1 계정의 모든 사용자 중 가장 많은 권한을 가집니다. 슈퍼 사용자의 권한은 다음과 같습니다.

* **모든** 파일과 폴더에 대한 RWX 권한을 가집니다.
* 모든 파일이나 폴더에 대한 권한을 변경할 수 있습니다.
* 모든 파일이나 폴더의 소유 사용자 또는 소유 그룹을 변경할 수 있습니다.

Data Lake Storage Gen1 계정에 대한 **소유자** 역할에 속해 있는 모든 사용자는 자동으로 슈퍼 사용자가 됩니다.

### <a name="the-owning-user"></a>소유 그룹

항목을 만든 사용자는 자동으로 항목의 소유 사용자가 됩니다. 소유 사용자는 다음을 수행할 수 있습니다.

* 소유한 파일의 권한을 변경합니다.
* 소유 사용자가 대상 그룹의 멤버이면 소유한 파일의 소유 그룹을 변경합니다.

> [!NOTE]
> 소유 사용자는 파일 또는 폴더의 소유 사용자를 *변경할 수 없습니다*. 슈퍼 사용자만이 파일이나 폴더의 소유 사용자를 변경할 수 있습니다.
>
>

### <a name="the-owning-group"></a>소유 그룹

**배경**

POSIX ACL에서 모든 사용자는 "주 그룹"과 연결됩니다. 예를 들어 "alice" 사용자는 "finance" 그룹에 속할 수 있습니다. 또한 Alice는 여러 그룹에 속할 수 있지만 항상 한 그룹을 주 그룹으로 지정합니다. POSIX에서 Alice가 파일을 만들 때는 해당 파일의 소유 그룹이 자신의 주 그룹(여기서는 "finance"임)으로 설정됩니다. 그렇지 않으면 소유 그룹은 다른 사용자/그룹에 할당된 사용 권한과 유사하게 동작합니다.

Data Lake Storage Gen1의 사용자와 연결된 “기본 그룹”이 없으므로 아래와 같이 소유 중인 그룹이 할당됩니다.

**새 파일이나 폴더의 소유 그룹 할당**

* **사례 1**: "/" 루트 폴더입니다. 이 폴더는 Data Lake Storage Gen1 계정이 만들어질 때 생성됩니다. 이 경우 소유 그룹은 모두 0 GUID로 설정됩니다.  이 값은 어떠한 액세스도 허용하지 않습니다.  그룹이 할당될 때까지는 자리 표시자입니다.
* **사례 2**(기타 모든 경우): 새 항목을 만들 때 소유 그룹이 부모 폴더에서 복사됩니다.

**소유 그룹 변경**

소유 그룹은 다음에 의해 변경될 수 있습니다.
* 모든 슈퍼 사용자
* 소유 사용자가 대상 그룹의 구성원이기도 한 경우 소유 사용자입니다.

> [!NOTE]
> 소유 그룹은 파일 또는 폴더의 ACL을 *변경할 수 없습니다*.
>
> 2018년 9월 이전에 생성된 계정의 경우, 위의 **사례 1**에 대한 루트 폴더의 사례에서 계정을 만든 사용자로 소유 그룹이 설정되었습니다.  단일 사용자 계정이 소유 그룹을 통해 권한을 제공하는 데 유효하지 않으므로 이 기본 설정으로 사용 권한이 부여되지 않습니다. 올바른 사용자 그룹에 이 권한을 할당할 수 있습니다.


## <a name="access-check-algorithm"></a>액세스 검사 알고리즘

다음 의사 코드는 Data Lake Storage Gen1 계정에 대한 액세스 확인 알고리즘을 나타냅니다.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & e.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>마스크

액세스 확인 알고리즘에 나와 있는 것처럼 마스크는 **명명된 사용자**, **소유 그룹** 및 **명명된 그룹**에 대한 액세스를 제한합니다.  

> [!NOTE]
> 새 Data Lake Storage Gen1 계정의 경우 루트 폴더(“/”)의 액세스 ACL에 대한 마스크는 기본적으로 RWX를 사용합니다.
>
>

### <a name="the-sticky-bit"></a>고정 비트

고정 비트는 POSIX 파일 시스템의 고급 기능입니다. Data Lake Storage Gen1의 컨텍스트에서 고정 비트가 필요할 가능성은 없습니다. 요약하자면, 폴더에 대해 스티키 비트를 사용하도록 설정하는 경우 하위 항목 담당 사용자만이 하위 항목을 삭제하거나 이름을 바꿀 수 있습니다.

고정 비트는 Azure Portal에서 표시하지 않습니다.

## <a name="default-permissions-on-new-files-and-folders"></a>새 파일 및 폴더에 대한 기본 권한

기존 폴더에서 새 파일이나 폴더를 만들 때 부모 폴더에 대한 기본 ACL은 다음 항목을 결정합니다.

- 자식 폴더의 기본 ACL 및 액세스 ACL
- 자식 파일의 액세스 ACL(파일에 기본 ACL이 없는 경우)

### <a name="umask"></a>umask

파일이나 폴더를 만들 때는 umask를 사용하여 자식 항목에서 기본 ACL이 설정되는 방식을 수정합니다. umask는 **담당 사용자**, **소유 그룹** 및 **기타**에 해당하는 RWX 값이 포함된 상위 폴더의 9비트 값입니다.

Azure Data Lake Storage Gen1의 umask는 007로 설정된 상수 값입니다. 이 값은 다음 표와 같이 변환됩니다.

| umask 구성 요소     | 숫자 형식 | 짧은 형식 | 의미 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 담당 사용자의 경우 상위 항목의 기본 ACL을 하위 항목의 액세스 ACL에 복사합니다. | 
| umask.owning_group  |    0         |   `---`      | 소유 그룹의 경우 상위 항목의 기본 ACL을 하위 항목의 액세스 ACL에 복사합니다. | 
| umask.other         |    7         |   `RWX`      | 기타의 경우 하위 항목 액세스 ACL에서 모든 권한을 제거합니다. |

Azure Data Lake Storage Gen1에서 사용되는 umask 값에서는 기본 ACL에 지정된 내용에 관계없이 기타에 해당하는 값이 기본적으로 새 하위 항목에 전송되지 않습니다. 

다음 의사 코드는 하위 항목의 ACL을 만들 때 umask가 적용되는 방식을 보여 줍니다.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1의 ACL에 대한 일반적인 질문

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL에 대한 지원을 사용하도록 설정해야 하나요?

아니요. ACL을 통한 액세스 제어는 Data Lake Storage Gen1 계정에 대해 항상 켜져 있습니다.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>폴더 및 해당 내용을 재귀적으로 삭제하는 데 필요한 권한은 무엇인가요?

* 부모 폴더에는 **쓰기 + 실행** 권한이 있어야 합니다.
* 삭제할 폴더와 그 안의 모든 폴더에는 **읽기 + 쓰기 + 실행** 권한이 필요합니다.

> [!NOTE]
> 폴더의 파일을 삭제하는 데에는 쓰기 권한이 필요하지 않습니다. 또한 "/" 루트 폴더는 **절대로** 삭제할 수 없습니다.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>파일이나 폴더의 소유자는 누구인가요?

파일 또는 폴더의 작성자는 소유자가 됩니다.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>파일이나 폴더를 만들 때 소유 그룹으로 설정되는 그룹은 무엇인가요?

소유 그룹은 새 파일이나 폴더가 만들어지는 부모 폴더의 소유 그룹에서 복사됩니다.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>파일의 소유 사용자이지만 필요한 RWX 사용 권한이 없습니다. 어떻게 해야 합니까?

소유 사용자는 파일의 권한을 변경하여 필요한 RWX 권한을 부여할 수 있습니다.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Azure Portal에서 ACL을 확인할 때는 사용자 이름이 표시되지만 API를 통해 확인할 때는 GUID가 표시되는 이유는 무엇인가요?

ACL의 항목은 Azure AD의 사용자에 해당하는 GUID로 저장됩니다. API는 GUID를 있는 그대로 반환합니다. Azure Portal은 가능한 경우 GUID를 친숙한 이름으로 변환하여 ACL을 보다 쉽게 사용하려고 합니다.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Azure Portal을 사용할 때 때때로 ACL에 GUID가 표시되는 이유는 무엇인가요?

사용자가 Azure AD에 더 이상 존재하지 않으면 GUID가 표시됩니다. 일반적으로 사용자가 퇴사하거나 Azure AD에서 해당 계정이 삭제될 때 이러한 현상이 발생합니다.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Data Lake Storage Gen1이 ACL의 상속을 지원하나요?

아니요, 기본 ACL은 자식 파일과 부모 폴더에서 새로 만든 자식 파일 및 폴더에 대해 ACL을 설정하는 데 사용할 수 있습니다.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX 액세스 제어 모델에 대한 어디서 자세히 알아볼 수 있나요?

* [Linux의 POSIX Access Control 목록](https://www.linux.com/news/posix-acls-linux)(영문)
* [HDFS 권한 가이드](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)(영문)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu의 POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Linux의 액세스 제어 목록 사용](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)(영문)

## <a name="see-also"></a>참고 항목

* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
