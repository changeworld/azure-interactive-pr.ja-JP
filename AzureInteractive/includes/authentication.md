---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079490"
---
<span data-ttu-id="75964-103">Azure App Service 認証を使用すると、Azure 関数アプリでターン キー認証がサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="75964-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="75964-104">Facebook、Twitter、Microsoft アカウント、Google、および Azure Active Directory とシームレスに統合されます。</span><span class="sxs-lookup"><span data-stu-id="75964-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="75964-105">Web アプリのバックエンド API を保護するには、App Service 認証を追加します。</span><span class="sxs-lookup"><span data-stu-id="75964-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="75964-106">App Service 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="75964-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="75964-107">Azure Portal で関数アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="75964-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="75964-108">**[プラットフォーム機能]** で、**[認証/承認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="75964-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![[認証/承認] を選択する](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="75964-110">次の値を選択します。</span><span class="sxs-lookup"><span data-stu-id="75964-110">Select the following values:</span></span>
    
    | <span data-ttu-id="75964-111">Setting</span><span class="sxs-lookup"><span data-stu-id="75964-111">Setting</span></span>      |  <span data-ttu-id="75964-112">推奨値</span><span class="sxs-lookup"><span data-stu-id="75964-112">Suggested value</span></span>   | <span data-ttu-id="75964-113">説明</span><span class="sxs-lookup"><span data-stu-id="75964-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="75964-114">**App Service 認証**</span><span class="sxs-lookup"><span data-stu-id="75964-114">**App Service Authentication**</span></span> | <span data-ttu-id="75964-115">On</span><span class="sxs-lookup"><span data-stu-id="75964-115">On</span></span> | <span data-ttu-id="75964-116">認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="75964-116">Enable authentication.</span></span> |
    | <span data-ttu-id="75964-117">**Action when request is not authenticated (要求が認証されない場合のアクション)**</span><span class="sxs-lookup"><span data-stu-id="75964-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="75964-118">Azure Active Directory でのログイン</span><span class="sxs-lookup"><span data-stu-id="75964-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="75964-119">構成済みの認証方法 (下記) を選択します。</span><span class="sxs-lookup"><span data-stu-id="75964-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="75964-120">**認証プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="75964-120">**Authentication Providers**</span></span> | <span data-ttu-id="75964-121">以下を参照</span><span class="sxs-lookup"><span data-stu-id="75964-121">See below</span></span> | <span data-ttu-id="75964-122">以下を参照</span><span class="sxs-lookup"><span data-stu-id="75964-122">See below</span></span> |
    | <span data-ttu-id="75964-123">**トークン ストア**</span><span class="sxs-lookup"><span data-stu-id="75964-123">**Token store**</span></span> | <span data-ttu-id="75964-124">On</span><span class="sxs-lookup"><span data-stu-id="75964-124">On</span></span> | <span data-ttu-id="75964-125">App Service でトークンを格納および管理できるようになります。</span><span class="sxs-lookup"><span data-stu-id="75964-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="75964-126">**[許可される外部リダイレクト URL]**</span><span class="sxs-lookup"><span data-stu-id="75964-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="75964-127">アプリケーションの URL。例: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="75964-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="75964-128">ユーザーが認証された後に App Service からリダイレクトできる URL。</span><span class="sxs-lookup"><span data-stu-id="75964-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="75964-129">**[Azure Active Directory]** を選択して **[Azure Active Directory の設定]** を表示します。</span><span class="sxs-lookup"><span data-stu-id="75964-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="75964-130">**[管理モード]** として **[簡易]** を選択し、次の情報を入力します。</span><span class="sxs-lookup"><span data-stu-id="75964-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="75964-131">Setting</span><span class="sxs-lookup"><span data-stu-id="75964-131">Setting</span></span>      |  <span data-ttu-id="75964-132">推奨値</span><span class="sxs-lookup"><span data-stu-id="75964-132">Suggested value</span></span>   | <span data-ttu-id="75964-133">説明</span><span class="sxs-lookup"><span data-stu-id="75964-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="75964-134">**管理モード**</span><span class="sxs-lookup"><span data-stu-id="75964-134">**Management mode**</span></span> | <span data-ttu-id="75964-135">[簡易]、[新しい AD アプリを作成する]</span><span class="sxs-lookup"><span data-stu-id="75964-135">Express, Create new AD app</span></span> | <span data-ttu-id="75964-136">サービス プリンシパルと Azure Active Directory 認証が自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="75964-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="75964-137">**アプリの作成**</span><span class="sxs-lookup"><span data-stu-id="75964-137">**Create app**</span></span> | <span data-ttu-id="75964-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="75964-138">my-serverless-webapp</span></span> | <span data-ttu-id="75964-139">一意のアプリケーション名を入力します。</span><span class="sxs-lookup"><span data-stu-id="75964-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="75964-140">**[OK]** をクリックして Azure Active Directory の設定を保存します。</span><span class="sxs-lookup"><span data-stu-id="75964-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![[認証/承認] と Azure Active Directory の設定](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="75964-142">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="75964-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="75964-143">Web アプリを変更して認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="75964-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="75964-144">Cloud Shell で、現在のディレクトリが **www/dist** フォルダーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="75964-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="75964-145">関数アプリで認証を有効にするには、次のコード行を **settings.js** に追加します。</span><span class="sxs-lookup"><span data-stu-id="75964-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="75964-146">次のコマンドを使用するか、VIM などのコマンド ライン エディターを使用して、変更を加え結果を表示します。</span><span class="sxs-lookup"><span data-stu-id="75964-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="75964-147">ファイルが変更されたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="75964-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="75964-148">このファイルを Blob Storage にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="75964-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="75964-149">アプリケーションをテストする</span><span class="sxs-lookup"><span data-stu-id="75964-149">Test the application</span></span>

1. <span data-ttu-id="75964-150">ブラウザーでアプリケーションを開きます。</span><span class="sxs-lookup"><span data-stu-id="75964-150">Open the application in a browser.</span></span> <span data-ttu-id="75964-151">**[ログイン]** をクリックしてログインします。</span><span class="sxs-lookup"><span data-stu-id="75964-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="75964-152">画像ファイルを選択してアップロードします。</span><span class="sxs-lookup"><span data-stu-id="75964-152">Select an image file and upload it.</span></span>

    ![サインイン ページ](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="75964-154">まとめ</span><span class="sxs-lookup"><span data-stu-id="75964-154">Summary</span></span>

<span data-ttu-id="75964-155">このモジュールでは、Azure App Service 認証を使用してアプリケーションに認証を追加する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="75964-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
