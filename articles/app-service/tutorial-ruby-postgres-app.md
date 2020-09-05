---
title: '자습서: Postgres가 포함된 Linux Ruby 앱'
description: Azure의 PostgreSQL 데이터베이스와 연결하여 Azure App Service에서 Linux Ruby 앱이 작동하도록 하는 방법에 대해 알아봅니다. Rails는 자습서에서 사용됩니다.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 77892cc9a05c569e431fb9c66c2f6eb92068b3af
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082423"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Linux의 Azure App Service에서 Ruby 및 Postgres 앱 빌드

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Ruby 앱을 만들고 PostgreSQL 데이터베이스에 연결하는 방법을 보여줍니다. 완료되면 [Ruby on Rails](https://rubyonrails.org/) 앱이 Linux의 App Service에서 실행됩니다.

![Azure App Service에서 실행 중인 Ruby on Rails 앱](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * Ruby on Rails 앱을 PostgreSQL에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* [Git 설치](https://git-scm.com/)
* [Ruby 2.6 설치](https://www.ruby-lang.org/en/documentation/installation/)
* [Ruby on Rails 5.1 설치](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [PostgreSQL 설치 및 실행](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>로컬 Postgres 준비

이 단계에서는 이 자습서에서 사용할 데이터베이스를 로컬 Postgres 서버에 만듭니다.

### <a name="connect-to-local-postgres-server"></a>로컬 Postgres 서버에 연결

터미널 창을 열고 `psql`를 실행하여 로컬 Postgres 서버에 연결합니다.

```bash
sudo -u postgres psql
```

연결이 성공하면 Postgres 데이터베이스가 실행되고 있습니다. 연결이 실패하면 [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/)의 단계를 수행하여 로컬 Postgres 데이터베이스가 시작되도록 합니다.

`\q`를 입력하여 Postgres 클라이언트를 종료합니다. 

로그인한 Linux 사용자 이름을 사용하는 다음 명령을 실행하여 데이터베이스를 만들 수 있는 Postgres 사용자를 만듭니다.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>로컬로 Ruby on Rails 앱 만들기
이 단계에서는 Ruby on Rails 샘플 애플리케이션을 가져오고, 해당 데이터베이스를 구성한 후 로컬로 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd`를 사용하여 복제된 디렉터리로 이동합니다. 필요한 패키지를 설치합니다.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>로컬에서 샘플 실행

[Rails 마이그레이션](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations)을 실행하여 애플리케이션에 필요한 테이블을 만듭니다. 마이그레이션에서 만들어진 테이블을 보려면 Git 리포지토리의 _db/migrate_ 디렉터리를 살펴봅니다.

```bash
rake db:create
rake db:migrate
```

애플리케이션을 실행합니다.

```bash
rails server
```

브라우저에서 `http://localhost:3000` 으로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

![Ruby on Rails가 Postgres에 성공적으로 연결됩니다.](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Azure에서 Postgres 만들기

이 단계에서는 [Azure Database for PostgreSQL](/azure/postgresql/)에서 Postgres 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결할 Ruby on Rails 애플리케이션을 구성합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Azure에서 Postgres 데이터베이스 만들기

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

이 섹션에서는 Azure Database for PostgreSQL 서버 및 데이터베이스를 만듭니다. 시작하려면 다음 명령을 사용하여 `db-up` 확장을 설치합니다.

```azurecli
az extension add --name db-up
```

다음 예제와 같이 Azure에서 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 명령을 사용하여 Postgres 데이터베이스를 만듭니다. *\<postgresql-name>* 를 *고유* 이름으로 바꿉니다(서버 엔드포인트는 *https://\<postgresql-name>.postgres.database.azure.com*임). *\<admin-username>* 및 *\<admin-password>* 의 경우 이 Postgres 서버에 대한 관리자 사용자를 만들기 위한 자격 증명을 지정합니다.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

이 명령은 다음 작업을 수행하므로 시간이 좀 걸릴 수 있습니다.

- 없는 경우 `myResourceGroup`이라고 하는 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)을 만듭니다. 모든 Azure 리소스는 다음 중 하나에 있어야 합니다. `--resource-group`는 선택 사항입니다.
- 관리 사용자 권한으로 Postgres 서버를 만듭니다.
- `sampledb` 데이터베이스를 만듭니다.
- 로컬 IP 주소에서의 액세스를 허용합니다.
- Azure 서비스에서의 액세스를 허용합니다.
- `sampledb` 데이터베이스에 액세스할 수 있는 데이터베이스 사용자를 만듭니다.

모든 단계는 다른 `az postgres` 및 `psql` 명령으로 개별적으로 수행할 수 있지만 `az postgres up`은 이러한 모든 단계를 한 번에 수행합니다.

명령이 완료되면 `Ran Database Query:`가 있는 출력 줄을 찾습니다. `root` 사용자 이름 및 `Sampledb1` 암호를 사용하여 사용자를 위해 만든 데이터베이스의 사용자를 표시합니다. 이러한 정보는 나중에 앱을 데이터베이스에 연결하는 데 사용합니다.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/) 중 하나로 설정할 수 있습니다. 구독에 사용할 수 있는 지역은 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 명령을 사용하여 가져올 수 있습니다. 프로덕션 앱의 경우 데이터베이스와 앱을 동일한 위치에 배치합니다.

## <a name="connect-app-to-azure-postgres"></a>Azure Postgres에 앱 연결

이 단계에서는 Azure Database for PostgreSQL에서 만든 Postgres 데이터베이스에 Ruby on Rails 애플리케이션을 연결합니다.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

리포지토리에서 _config/database.yml_을 엽니다. 파일 맨 아래에서 프로덕션 변수를 다음 코드로 바꿉니다. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

변경 내용을 저장합니다.

### <a name="test-the-application-locally"></a>로컬에서 애플리케이션 테스트

로컬 터미널에서 다음과 같은 환경 변수를 설정합니다.

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

방금 구성한 프로덕션 값을 사용해서 Rails 데이터베이스 마이그레이션을 실행하고 Azure Database for PostgreSQL에서 Postgres 데이터베이스에 테이블을 만듭니다.

```bash
rake db:migrate RAILS_ENV=production
```

프로덕션 환경에서 실행하는 경우 Rails 애플리케이션에는 미리 컴파일된 자산이 필요합니다. 다음 명령을 사용하여 필요한 자산을 생성합니다.

```bash
rake assets:precompile
```

Rails 프로덕션 환경에서는 보안 관리를 위해 비밀도 사용하고 있습니다. 비밀 키를 생성합니다.

```bash
rails secret
```

비밀 키를 Rails 프로덕션 환경에서 사용되는 각 변수에 저장합니다. 편의상, 두 변수에 동일한 키를 사용합니다.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Rails 프로덕션 환경에서 JavaScript 및 CSS 파일을 제공하도록 합니다.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

프로덕션 환경에서 예제 애플리케이션을 실행합니다.

```bash
rails server -e production
```

`http://localhost:3000`로 이동합니다. 오류 없이 페이지가 로드되면 Ruby on Rails 애플리케이션이 Azure의 Postgres 데이터베이스에 연결됩니다.

해당 페이지에서 몇 가지 작업을 추가합니다.

![Ruby on Rails는 Azure Database for PostgreSQL에 성공적으로 연결됩니다.](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="commit-your-changes"></a>변경 내용을 커밋합니다

다음 Git 명령을 실행하여 변경 내용을 커밋합니다.

```bash
git add .
git commit -m "database.yml updates"
```

앱을 배포할 준비가 되었습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 Postgres에 연결된 Rails 애플리케이션을 Azure App Service에 배포합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용하여 App Service의 환경 변수를 _앱 설정_으로 설정합니다.

다음 Cloud Shell 명령에서는 `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` 및 `DB_PASSWORD` 앱 설정을 구성합니다. _&lt;appname>_ 및 _&lt;postgres-server-name>_ 자리 표시자를 대체합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Rails 환경 변수 구성

로컬 터미널에서 Azure의 Rails 프로덕션 환경에 대한 [새 비밀 키를 생성](configure-language-ruby.md#set-secret_key_base-manually)합니다.

```bash
rails secret
```

Rails 프로덕션 환경에 필요한 변수를 구성합니다.

다음 Cloud Shell 명령에서 두 개의 _&lt;output-of-rails-secret>_ 자리 표시자를 로컬 터미널에서 생성한 새 비밀 키로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`는 각 Git 배포의 자산을 미리 컴파일하도록 기본 Ruby 컨테이너에 지시합니다. 자세한 내용은 [자산 미리 컴파일](configure-language-ruby.md#precompile-assets) 및 [정적 자산 제공](configure-language-ruby.md#serve-static-assets)을 참조하세요.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 터미널에서 로컬 Git 리포지토리에 Azure 원격을 추가합니다.

```bash
git remote add azure <paste-copied-url-here>
```

Azure 원격 위치에 푸시하여 Ruby on Rails 애플리케이션을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 제공한 암호를 묻는 메시지가 표시됩니다.

```bash
git push azure master
```

배포하는 동안 Azure App Service는 진행 상황을 Git에 전합니다.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

`http://<app-name>.azurewebsites.net`으로 이동한 후 목록에 몇 가지 작업을 추가합니다.

![Azure App Service에서 실행 중인 Ruby on Rails 앱](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

축하합니다! Azure App Service에서 데이터 기반 Ruby on Rails 앱을 실행하고 있습니다.

## <a name="update-model-locally-and-redeploy"></a>로컬에서 모델 업데이트 및 다시 배포

이 단계에서는 `task` 데이터 모델과 웹앱을 간단히 변경한 다음 업데이트를 Azure에 게시합니다.

작업 시나리오의 경우 작업을 완료한 것으로 표시할 수 있도록 애플리케이션을 수정합니다.

### <a name="add-a-column"></a>열 추가

터미널에서 Git 리포지토리의 루트로 이동합니다.

`Tasks` 테이블에 `Done`이라는 부울 열을 추가하는 새 마이그레이션을 생성합니다.

```bash
rails generate migration AddDoneToTasks Done:boolean
```

이 명령은 _db/migrate_ 디렉터리에 새 마이그레이션 파일을 생성합니다.


터미널에서 Rails 데이터베이스 마이그레이션을 실행하여 로컬 데이터베이스를 변경합니다.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>애플리케이션 논리 업데이트

*app/controllers/tasks_controller.rb* 파일을 엽니다. 파일 끝에 다음 줄을 추가합니다.

```rb
params.require(:task).permit(:Description)
```

새 `Done` 매개 변수를 포함하도록 이 줄을 수정합니다.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>보기 업데이트

편집 형식에 해당하는 *app/views/tasks/_form.html.erb* 파일을 엽니다.

`<%=f.error_span(:Description) %>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

단일 레코드 보기 페이지인 *app/views/tasks/show.html.erb* 파일을 엽니다. 

`<dd><%= @task.Description %></dd>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

모레코든 드의 인덱스 페이지인 *app/views/tasks/index.html.erb* 파일을 엽니다.

`<th><%= model_class.human_attribute_name(:Description) %></th>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

같은 파일에서 `<td><%= task.Description %></td>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>로컬에서 변경 내용 테스트

로컬 터미널에서 Rails 서버를 실행합니다.

```bash
rails server
```

작업 상태가 변경되는 것을 확인하려면 `http://localhost:3000`으로 이동하고 항목을 추가하거나 편집합니다.

![작업에 확인란이 추가됨](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

터미널에서 프로덕션 환경에 대해 Rails 데이터베이스 마이그레이션을 실행하고, Azure 데이터베이스를 변경합니다.

```bash
rake db:migrate RAILS_ENV=production
```

Git에서 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push`가 완료되면 Azure 앱으로 이동하여 새 기능을 테스트합니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

모든 작업을 추가했으면 데이터베이스에서 유지됩니다. 데이터 스키마를 업데이트하는 경우 기존 데이터는 그대로 유지됩니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Azure 앱 관리

만든 앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/tutorial-php-mysql-app/access-portal.png)

앱의 [개요] 페이지가 표시됩니다. 여기서 중지, 시작, 다시 시작, 찾아보기 및 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

왼쪽 메뉴에서 앱을 구성하기 위한 페이지를 제공합니다.

![Azure Portal의 App Service 페이지](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 Postgres 데이터베이스 만들기
> * Ruby on Rails 앱을 Postgres에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [Ruby 앱 구성](configure-language-ruby.md)
