---
title: Azure Red Hat OpenShift에서 리소스 관리 Microsoft Docs
description: Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지 스트림 관리
services: openshift
keywords: red hat openshift 프로젝트는 자체 provisioner를 요청 합니다.
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 35474df96254b158a39b6d913171c553bf30aea4
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816442"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 프로젝트, 템플릿, 이미지 스트림 관리

OpenShift 컨테이너 플랫폼에서 프로젝트는 관련 개체를 그룹화 하 고 격리 하는 데 사용 됩니다. 관리자는 개발자에 게 특정 프로젝트에 대 한 액세스 권한을 부여 하 고 자신의 프로젝트를 만들 수 있도록 하며 개별 프로젝트에 대 한 관리 권한을 부여할 수 있습니다.

## <a name="self-provisioning-projects"></a>자동 프로 비전 프로젝트

개발자가 자신의 프로젝트를 만드는 데 사용할 수 있습니다. API 끝점은 project request 라는 템플릿에 따라 프로젝트를 프로 비전 합니다. 웹 콘솔과 `oc new-project` 명령은 개발자가 새 프로젝트를 만들 때이 끝점을 사용 합니다.

프로젝트 요청이 제출 되 면 API는 템플릿에서 다음 매개 변수를 대체 합니다.

| 매개 변수               | Description                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 프로젝트의 이름입니다. 필수 요소.             |
| PROJECT_DISPLAYNAME     | 프로젝트의 표시 이름입니다. 비어 있을 수 있습니다. |
| PROJECT_DESCRIPTION     | 프로젝트에 대한 설명입니다. 비어 있을 수 있습니다.  |
| PROJECT_ADMIN_USER      | 관리 사용자의 사용자 이름입니다.       |
| PROJECT_REQUESTING_USER | 요청 하는 사용자의 사용자 이름입니다.           |

API에 대 한 액세스는 개발자에 게 자체 provisioners 클러스터 역할 바인딩을 통해 부여 됩니다. 이 기능은 기본적으로 모든 인증 된 개발자가 사용할 수 있습니다.

## <a name="modify-the-template-for-a-new-project"></a>새 프로젝트에 대 한 템플릿 수정 

1. 권한이 있는 사용자로 로그인 `customer-admin` 합니다.

2. 기본 프로젝트 요청 템플릿을 편집 합니다.

   ```
   oc edit template project-request -n openshift
   ```

3. 다음 주석을 추가 하 여 Azure Red Hat OpenShift (ARO) 업데이트 프로세스에서 기본 프로젝트 템플릿을 제거 합니다. `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   프로젝트 요청 템플릿은 ARO 업데이트 프로세스에 의해 업데이트 되지 않습니다. 이렇게 하면 고객이 템플릿을 사용자 지정 하 고 클러스터를 업데이트할 때 이러한 사용자 지정을 유지할 수 있습니다.

## <a name="disable-the-self-provisioning-role"></a>자동 프로 비전 역할을 사용 하지 않도록 설정

인증 된 사용자 그룹이 새 프로젝트를 자동으로 프로 비전 하지 못하도록 방지할 수 있습니다.

1. 권한이 있는 사용자로 로그인 `customer-admin` 합니다.

2. 자체 provisioners 클러스터 역할 바인딩을 편집 합니다.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 다음 주석을 추가 하 여 ARO 업데이트 프로세스에서 역할을 제거 합니다. `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 프로젝트를 만들지 못하도록 클러스터 역할 바인딩을 변경 합니다 `system:authenticated:oauth` .

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>기본 템플릿 및 imageStreams 관리

Azure Red Hat OpenShift에서 네임 스페이스 내의 기본 템플릿 및 이미지 스트림의 업데이트를 사용 하지 않도록 설정할 수 있습니다 `openshift` .
모든 `Templates` 및 `ImageStreams` 네임 스페이스에 대 한 업데이트를 사용 하지 않도록 설정 하려면 `openshift` :

1. 권한이 있는 사용자로 로그인 `customer-admin` 합니다.

2. `openshift`네임 스페이스 편집:

   ```
   oc edit namespace openshift
   ```

3. `openshift`다음 주석을 추가 하 여 ARO 업데이트 프로세스에서 네임 스페이스를 제거 합니다.`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   `openshift`네임 스페이스에 주석을 추가 하 여 해당 네임 스페이스의 개별 개체를 업데이트 프로세스에서 제거할 수 있습니다 `openshift.io/reconcile-protect: "true"` .

## <a name="next-steps"></a>다음 단계

자습서를 사용해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
