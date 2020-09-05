---
title: Azure Red Hat OpenShift 클러스터 관리자 역할 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자 역할의 할당 및 사용
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 38686ba35285159d7a27724b5402a6b6e2f3a696
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815524"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 고객 관리자 역할
 
Azure Red Hat OpenShift 클러스터의 클러스터 관리자입니다. 사용자 계정의 사용 권한 및 사용자가 만든 모든 프로젝트에 대 한 액세스 권한이 증가 했습니다.

계정에 바인딩된 고객 관리 클러스터 권한 부여 역할이 있는 경우 프로젝트를 자동으로 관리할 수 있습니다.

> [!Note] 
> 고객 관리 클러스터의 클러스터 역할은 클러스터 관리자 클러스터 역할과 동일 하지 않습니다.

예를 들어, 동사 () 집합에 연결 된 작업을 실행 `create` 하 여 일련의 리소스 이름 ()에 대해 작업을 수행할 수 있습니다 `templates` . 이러한 역할 및 해당 동사 및 리소스 집합에 대 한 세부 정보를 보려면 다음 명령을 실행 합니다.

`$ oc get clusterroles customer-admin-cluster -o yaml`

동사 이름이 반드시 명령에 직접 매핑될 필요는 없습니다 `oc` . 이러한 작업은 일반적으로 수행할 수 있는 CLI 작업 유형에 더 일반적입니다. 

예를 들어 동사가 있으면 `list` 리소스 이름 ()의 모든 개체 목록을 표시할 수 있음을 의미 합니다 `oc get` . `get`동사는 이름 ()을 알고 있는 경우 특정 개체의 세부 정보를 표시할 수 있음을 의미 합니다 `oc describe` .

## <a name="configure-the-customer-administrator-role"></a>고객 관리자 역할 구성

플래그를 제공 하 여 클러스터를 만드는 동안에만 고객 관리 클러스터 클러스터 역할을 구성할 수 있습니다 `--customer-admin-group-id` . 이 필드는 현재 Azure Portal에서 구성할 수 없습니다. Azure Active Directory 및 관리자 그룹을 구성 하는 방법에 [대 한 자세한 내용은 Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)(영문)을 참조 하세요.

## <a name="confirm-membership-in-the-customer-administrator-role"></a>고객 관리자 역할의 멤버 자격 확인

Customer admin 그룹의 멤버 자격을 확인 하려면 OpenShift CLI 명령 또는를 시도 `oc get nodes` 합니다 `oc projects` . `oc get nodes` 고객 관리-클러스터 역할을 보유 하 고 있는 경우에는 노드 목록이 표시 되 고, 고객 관리-프로젝트 역할만 있는 경우에는 사용 권한 오류가 표시 됩니다. `oc projects` 에서는 작업 중인 프로젝트 뿐 아니라 클러스터의 모든 프로젝트를 표시 합니다.

클러스터에서 역할 및 사용 권한을 자세히 살펴보려면 명령을 사용할 수 있습니다 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) .

## <a name="next-steps"></a>다음 단계

고객 관리-클러스터 클러스터 역할을 구성 합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합](howto-aad-app-configuration.md)
