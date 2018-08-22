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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079358"
---
<span data-ttu-id="83034-103">前のモジュールでは、サーバーレス関数を使用して、Web アプリケーションから Blob Storage に画像をアップロードするときにセキュリティで保護する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="83034-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="83034-104">このモジュールでは、アップロードされた画像を監視し、そこからサムネイルを作成するために、別のサーバーレス関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="83034-105">サムネイル用の Blob Storage コンテナーを作成する</span><span class="sxs-lookup"><span data-stu-id="83034-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="83034-106">フル サイズの画像は、**images** という名前のコンテナーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="83034-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="83034-107">これらの画像のサムネイルを格納するには、別のコンテナーが必要になります。</span><span class="sxs-lookup"><span data-stu-id="83034-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="83034-108">Cloud Shell (bash) に現在もサインインしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83034-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="83034-109">そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択して、Cloud Shell ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="83034-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="83034-110">すべての BLOB へのパブリック アクセスを持つストレージ アカウント内に、**thumbnails** という名前の新しいコンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="83034-111">BLOB によってトリガーされるサーバーレス関数を作成する</span><span class="sxs-lookup"><span data-stu-id="83034-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="83034-112">トリガーは、関数を呼び出す方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="83034-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="83034-113">次に作成する関数では、BLOB トリガーを使用します。この関数は、BLOB (画像ファイル) が **images** コンテナーにアップロードされるときに自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="83034-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="83034-114">1 つの関数には 1 つのトリガーしか含められません。</span><span class="sxs-lookup"><span data-stu-id="83034-114">A function must have one trigger.</span></span> <span data-ttu-id="83034-115">トリガーにはデータが関連付けられていて、通常そのデータは、その関数をトリガーしたペイロードです。</span><span class="sxs-lookup"><span data-stu-id="83034-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="83034-116">バインディングは、関数が Azure またはサード パーティのサービスでデータを読み書きする方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="83034-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="83034-117">この関数では、関数をトリガーする画像のサムネイル バージョンを作成し、そのサムネイルを *thumbnails* コンテナーに保存します。</span><span class="sxs-lookup"><span data-stu-id="83034-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="83034-118">Azure Portal で関数アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="83034-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="83034-119">関数アプリ ウィンドウの左側のナビゲーションで、**[Functions]** にポインターを合わせて **+** をクリックし、新しいサーバーレス関数の作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="83034-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="83034-120">クイック スタート ページが表示されたら、**[カスタム関数]** をクリックして、関数テンプレートの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="83034-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="83034-121">**BlobTrigger** テンプレートを検索し、選択します。</span><span class="sxs-lookup"><span data-stu-id="83034-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="83034-122">以下の値を使用して、画像がアップロードされるときにサムネイルを作成する関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="83034-123">Setting</span><span class="sxs-lookup"><span data-stu-id="83034-123">Setting</span></span>      |  <span data-ttu-id="83034-124">推奨値</span><span class="sxs-lookup"><span data-stu-id="83034-124">Suggested value</span></span>   | <span data-ttu-id="83034-125">説明</span><span class="sxs-lookup"><span data-stu-id="83034-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="83034-126">**言語**</span><span class="sxs-lookup"><span data-stu-id="83034-126">**Language**</span></span> | <span data-ttu-id="83034-127">C# または JavaScript</span><span class="sxs-lookup"><span data-stu-id="83034-127">C# or JavaScript</span></span> | <span data-ttu-id="83034-128">優先する言語を選択します。</span><span class="sxs-lookup"><span data-stu-id="83034-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="83034-129">**関数名の指定**</span><span class="sxs-lookup"><span data-stu-id="83034-129">**Name your function**</span></span> | <span data-ttu-id="83034-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="83034-130">ResizeImage</span></span> | <span data-ttu-id="83034-131">アプリケーションから関数を検出できるように、この名前を、表示されているとおりに入力します。</span><span class="sxs-lookup"><span data-stu-id="83034-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="83034-132">**パス**</span><span class="sxs-lookup"><span data-stu-id="83034-132">**Path**</span></span> | <span data-ttu-id="83034-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="83034-133">images/{name}</span></span> | <span data-ttu-id="83034-134">**images** コンテナーにファイルが入れられると関数が実行されます。</span><span class="sxs-lookup"><span data-stu-id="83034-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="83034-135">**ストレージ アカウント情報**</span><span class="sxs-lookup"><span data-stu-id="83034-135">**Storage account information**</span></span> | <span data-ttu-id="83034-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="83034-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="83034-137">接続文字列を使用して前に作成した環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="83034-137">Use the environment variable previously created with the connection string.</span></span> |

    ![新しい関数の設定を入力する](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="83034-139">**[作成]** をクリックして、関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="83034-140">関数が作成されたら、**[統合]** をクリックして、そのトリガー、入力、および出力バインディングを表示します。</span><span class="sxs-lookup"><span data-stu-id="83034-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="83034-141">**[新規出力]** をクリックし、新しい出力バインディングを作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-141">Click **New output** to create a new output binding.</span></span>

    ![[統合] タブで新しい出力を選択する](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="83034-143">**[Azure Blob Storage]** を選択し、**[選択]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83034-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="83034-144">**[選択]** ボタンを見つけるために、下へスクロールする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="83034-144">You may have to scroll down to see the **Select** button.</span></span>

    ![[Azure Blob Storage] を選択する](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="83034-146">次の値を入力します。</span><span class="sxs-lookup"><span data-stu-id="83034-146">Enter the following values.</span></span>

    | <span data-ttu-id="83034-147">Setting</span><span class="sxs-lookup"><span data-stu-id="83034-147">Setting</span></span>      |  <span data-ttu-id="83034-148">推奨値</span><span class="sxs-lookup"><span data-stu-id="83034-148">Suggested value</span></span>   | <span data-ttu-id="83034-149">説明</span><span class="sxs-lookup"><span data-stu-id="83034-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="83034-150">**BLOB パラメーター名**</span><span class="sxs-lookup"><span data-stu-id="83034-150">**Blob parameter name**</span></span> | <span data-ttu-id="83034-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="83034-151">thumbnail</span></span> | <span data-ttu-id="83034-152">この関数では、この名前のパラメーターを使用してサムネイルを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="83034-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="83034-153">**関数の戻り値を使用する**</span><span class="sxs-lookup"><span data-stu-id="83034-153">**Use function return value**</span></span> | <span data-ttu-id="83034-154">いいえ </span><span class="sxs-lookup"><span data-stu-id="83034-154">No</span></span> |  |
    | <span data-ttu-id="83034-155">**パス**</span><span class="sxs-lookup"><span data-stu-id="83034-155">**Path**</span></span> | <span data-ttu-id="83034-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="83034-156">thumbnails/{name}</span></span> | <span data-ttu-id="83034-157">サムネイルは、**thumbnails** という名前のコンテナーに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="83034-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="83034-158">**ストレージ アカウント接続**</span><span class="sxs-lookup"><span data-stu-id="83034-158">**Storage account connection**</span></span> | <span data-ttu-id="83034-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="83034-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="83034-160">接続文字列を使用して前に作成した環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="83034-160">Use the environment variable previously created with the connection string.</span></span> |

    ![BLOB 出力バインディングの設定を入力する](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="83034-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="83034-162">**JavaScript**</span></span>

    1. <span data-ttu-id="83034-163">(JavaScript) ウィンドウの右上隅にある **[詳細エディター]** をクリックして、バインディングを表す JSON を表示します。</span><span class="sxs-lookup"><span data-stu-id="83034-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="83034-164">(JavaScript) `blobTrigger` バインディングで、`binary` の値を持つ `dataType` という名前のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="83034-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="83034-165">これによって、BLOB コンテンツをバイナリ データとして関数に渡すように、バインディングが構成されます。</span><span class="sxs-lookup"><span data-stu-id="83034-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="83034-166">**[保存]** をクリックして新しいバインディングを作成します。</span><span class="sxs-lookup"><span data-stu-id="83034-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="83034-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="83034-167">**C#**</span></span>

    1. <span data-ttu-id="83034-168">(C#) 左側のナビゲーションで、関数名 **ResizeImage** を選択して、この関数のソース コードを開きます。</span><span class="sxs-lookup"><span data-stu-id="83034-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="83034-169">(C#) この関数でサムネイルを生成するには、**ImageResizer** という NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="83034-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="83034-170">NuGet パッケージは、**project.json** ファイルを使用して C# 関数に追加します。</span><span class="sxs-lookup"><span data-stu-id="83034-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="83034-171">このファイルを作成するには、右にある **[ファイルの表示]** をクリックして、関数を構成するファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="83034-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="83034-172">(C#) **[追加]** をクリックして、**project.json** という名前の新しいファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="83034-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="83034-173">(C#) [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) の内容を、新しく作成したファイルにコピーします。</span><span class="sxs-lookup"><span data-stu-id="83034-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="83034-174">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="83034-174">Save the file.</span></span> <span data-ttu-id="83034-175">ファイルが更新されると、パッケージが自動的に復元されます。</span><span class="sxs-lookup"><span data-stu-id="83034-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![project.json ファイルと ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="83034-177">(C#) **[ファイルの表示]** で **run.csx** を選択し、その内容を [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83034-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="83034-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="83034-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="83034-179">(JavaScript) この関数で写真のサイズを変更するには、npm の `jimp` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="83034-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="83034-180">npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83034-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="83034-181">(JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。</span><span class="sxs-lookup"><span data-stu-id="83034-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="83034-182">(JavaScript) コンソールで `npm install jimp` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="83034-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="83034-183">操作が完了するまでに 1 分から 2 分かかる場合があります。</span><span class="sxs-lookup"><span data-stu-id="83034-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="83034-184">(JavaScript) 左側のナビゲーションで関数名 **ResizeImage** をクリックして関数を表示し、**index.js** のすべてを [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="83034-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="83034-185">コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="83034-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="83034-186">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="83034-186">Click **Save**.</span></span> <span data-ttu-id="83034-187">関数が正常に保存されエラーがないことを、ログ パネルで確認します。</span><span class="sxs-lookup"><span data-stu-id="83034-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="83034-188">サーバーレス関数をテストする</span><span class="sxs-lookup"><span data-stu-id="83034-188">Test the serverless function</span></span>

1. <span data-ttu-id="83034-189">ブラウザーでアプリケーションを開きます。</span><span class="sxs-lookup"><span data-stu-id="83034-189">Open the application in a browser.</span></span> <span data-ttu-id="83034-190">画像ファイルを選択してアップロードします。</span><span class="sxs-lookup"><span data-stu-id="83034-190">Select an image file and upload it.</span></span> <span data-ttu-id="83034-191">アップロードが完了しますが、画像を表示する機能はまだ追加していないため、アップロードした写真はアプリに表示されません。</span><span class="sxs-lookup"><span data-stu-id="83034-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="83034-192">Cloud Shell で、**images** コンテナーに画像がアップロードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83034-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="83034-193">**thumbnails** いう名前のコンテナー内に、サムネイルが作成されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="83034-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="83034-194">サムネイルの URL を取得します。</span><span class="sxs-lookup"><span data-stu-id="83034-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="83034-195">ブラウザーでこの URL を開き、サムネイルが正常に作成されたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="83034-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="83034-196">次のチュートリアルに進む前に、**images** と **thumbnails** の各コンテナー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="83034-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="83034-197">まとめ</span><span class="sxs-lookup"><span data-stu-id="83034-197">Summary</span></span>

<span data-ttu-id="83034-198">このモジュールでは、画像が Blob Storage コンテナーにアップロードされるたびにサムネイルを作成するサーバーレス関数を作成しました。</span><span class="sxs-lookup"><span data-stu-id="83034-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="83034-199">次に、Azure Cosmos DB を使用して、画像のメタデータの格納および一覧表示を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="83034-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
