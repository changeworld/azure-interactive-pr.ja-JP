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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079471"
---
<span data-ttu-id="356bc-103">作成しているアプリケーションはフォト ギャラリーです。</span><span class="sxs-lookup"><span data-stu-id="356bc-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="356bc-104">ここでは、クライアント側 JavaScript を使用して API が呼び出され、画像がアップロードおよび表示されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="356bc-105">このモジュールでは、時間制限付き URL を生成して画像をアップロードするサーバーレス関数を使用して、API を作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="356bc-106">この Web アプリケーションでは、[Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) を使って Blob Storage に画像をアップロードするために、生成済み URL が使用されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="356bc-107">画像用の Blob Storage コンテナーを作成する</span><span class="sxs-lookup"><span data-stu-id="356bc-107">Create a blob storage container for images</span></span>

<span data-ttu-id="356bc-108">アプリケーションには、画像をアップロードしてホストするために、別個のストレージ コンテナーが必要です。</span><span class="sxs-lookup"><span data-stu-id="356bc-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="356bc-109">Cloud Shell (bash) に現在もサインインしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="356bc-110">そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択して、Cloud Shell ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="356bc-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="356bc-111">すべての BLOB へのパブリック アクセスを持つストレージ アカウント内に **images** という名前の新しいコンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="356bc-112">Azure Function App の作成</span><span class="sxs-lookup"><span data-stu-id="356bc-112">Create an Azure Function app</span></span>

<span data-ttu-id="356bc-113">Azure Functions はサーバーレス関数を実行するためのサービスです。</span><span class="sxs-lookup"><span data-stu-id="356bc-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="356bc-114">サーバーレス関数は、HTTP 要求などのイベントによって、または BLOB がストレージ コンテナーに作成されたときに、トリガーする (呼び出す) ことができます。</span><span class="sxs-lookup"><span data-stu-id="356bc-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="356bc-115">Azure 関数アプリは、1 つ以上のサーバーレス関数のためのコンテナーです。</span><span class="sxs-lookup"><span data-stu-id="356bc-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="356bc-116">新しい Azure 関数アプリを、以前作成した **first-serverless-app** というリソース グループ内に作成し、一意の名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="356bc-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="356bc-117">関数アプリにはストレージ アカウントが必要です。このチュートリアルでは、既存のストレージ アカウントを使用します。</span><span class="sxs-lookup"><span data-stu-id="356bc-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="356bc-118">HTTP によってトリガーされるサーバーレス関数を作成する</span><span class="sxs-lookup"><span data-stu-id="356bc-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="356bc-119">フォト ギャラリー Web アプリケーションでは、サーバーレス関数への HTTP 要求により、時間制限付き URL が生成されます。この URL により、画像が Blob Storage に安全にアップロードされます。</span><span class="sxs-lookup"><span data-stu-id="356bc-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="356bc-120">関数は HTTP 要求によってトリガーされます。この関数では Azure Storage SDK が使用されます。これにより、セキュリティで保護された URL が生成され、返されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="356bc-121">関数アプリが作成されたら、検索ボックスを使用して Azure portal 内でそのアプリを検索し、クリックして開きます。</span><span class="sxs-lookup"><span data-stu-id="356bc-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![関数アプリを開く](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="356bc-123">関数アプリ ウィンドウの左側のナビゲーションで、**[Functions]** にポインターを合わせて **+** をクリックし、新しいサーバーレス関数の作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="356bc-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![新しい関数を作成する](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="356bc-125">**[カスタム関数]** をクリックして、関数テンプレートの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="356bc-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="356bc-126">**HttpTrigger** テンプレートを見つけて、使用する言語 (C# または JavaScript) をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="356bc-127">以下の値を使用して、BLOB のアップロード URL を生成する関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="356bc-128">Setting</span><span class="sxs-lookup"><span data-stu-id="356bc-128">Setting</span></span>      |  <span data-ttu-id="356bc-129">推奨値</span><span class="sxs-lookup"><span data-stu-id="356bc-129">Suggested value</span></span>   | <span data-ttu-id="356bc-130">説明</span><span class="sxs-lookup"><span data-stu-id="356bc-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="356bc-131">**言語**</span><span class="sxs-lookup"><span data-stu-id="356bc-131">**Language**</span></span> | <span data-ttu-id="356bc-132">C# または JavaScript</span><span class="sxs-lookup"><span data-stu-id="356bc-132">C# or JavaScript</span></span> | <span data-ttu-id="356bc-133">使用する言語を選択します。</span><span class="sxs-lookup"><span data-stu-id="356bc-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="356bc-134">**関数名の指定**</span><span class="sxs-lookup"><span data-stu-id="356bc-134">**Name your function**</span></span> | <span data-ttu-id="356bc-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="356bc-135">GetUploadUrl</span></span> | <span data-ttu-id="356bc-136">アプリケーションから関数を検出できるように、この名前を、表示されているとおりに入力します。</span><span class="sxs-lookup"><span data-stu-id="356bc-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="356bc-137">**承認レベル**</span><span class="sxs-lookup"><span data-stu-id="356bc-137">**Authorization level**</span></span> | <span data-ttu-id="356bc-138">匿名</span><span class="sxs-lookup"><span data-stu-id="356bc-138">Anonymous</span></span> | <span data-ttu-id="356bc-139">関数にパブリックにアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="356bc-139">Allow the function to be accessed publicly.</span></span> |

    ![HTTP によってトリガーされる新しい関数の設定を入力する](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="356bc-141">**[作成]** をクリックして、関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="356bc-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="356bc-142">**C#**</span></span> 

    1. <span data-ttu-id="356bc-143">関数のソース コードが表示されたら、**run.csx** のすべてを [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="356bc-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="356bc-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="356bc-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="356bc-145">(JavaScript) この関数には、セキュリティで保護された URL の作成に必要な Shared Access Signature (SAS) トークンを生成するために、npm の `azure-storage` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="356bc-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="356bc-146">npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="356bc-147">(JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。</span><span class="sxs-lookup"><span data-stu-id="356bc-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![コンソール ウィンドウを開く](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="356bc-149">(JavaScript) `cd d:\home\site\wwwroot` コマンドを実行して、現在のディレクトリが **d:\home\site\wwwroot** であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="356bc-150">(JavaScript) `npm init -y` コマンドを実行して、空の **package.json** ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="356bc-151">(JavaScript) コンソールで `npm install --save azure-storage` コマンドを実行して、パッケージをインストールし、**package.json** に保存します。</span><span class="sxs-lookup"><span data-stu-id="356bc-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="356bc-152">操作が完了するまでに 1 分から 2 分かかる場合があります。</span><span class="sxs-lookup"><span data-stu-id="356bc-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="356bc-153">(JavaScript) 左側のナビゲーションで関数名 (**GetUploadUrl**) をクリックして関数を表示し、**index.js** のすべてを [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="356bc-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![更新後の index.js](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="356bc-155">コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="356bc-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="356bc-156">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-156">Click **Save**.</span></span> <span data-ttu-id="356bc-157">関数が正常にコンパイルされていることを、ログ パネルで確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="356bc-158">関数により、Blob Storage にファイルをアップロードするために使用される、いわゆる Shared Access Signature (SAS) URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="356bc-159">SAS URL の有効期間は短く、この URL でアップロードが許可されるのは 1 つのファイルだけです。</span><span class="sxs-lookup"><span data-stu-id="356bc-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="356bc-160">[Shared Access Signature の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)の詳細については、Blob Storage のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="356bc-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="356bc-161">ストレージ接続文字列に環境変数を追加する</span><span class="sxs-lookup"><span data-stu-id="356bc-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="356bc-162">作成した関数で SAS URL を生成できるようにするには、ストレージ アカウントの接続文字列が必要です。</span><span class="sxs-lookup"><span data-stu-id="356bc-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="356bc-163">接続文字列は、関数の本文でハードコーディングするのではなく、アプリケーション設定として格納できます。</span><span class="sxs-lookup"><span data-stu-id="356bc-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="356bc-164">アプリケーション設定には、関数アプリ内のすべての関数が環境変数としてアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="356bc-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="356bc-165">Cloud Shell で、ストレージ アカウント接続文字列のクエリを実行し、**STORAGE_CONNECTION_STRING** という名前の bash 変数に保存します。</span><span class="sxs-lookup"><span data-stu-id="356bc-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="356bc-166">変数が正常に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="356bc-167">前の手順で保存した値を使用して、**AZURE_STORAGE_CONNECTION_STRING** という名前の新しいアプリケーション設定を作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="356bc-168">コマンドの出力に、正しい値が指定された新しいアプリケーション設定が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="356bc-169">サーバーレス関数をテストする</span><span class="sxs-lookup"><span data-stu-id="356bc-169">Test the serverless function</span></span>

<span data-ttu-id="356bc-170">Azure portal には、関数の作成と編集だけでなく、関数をテストするツールも組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="356bc-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="356bc-171">HTTP サーバーレス関数をテストするには、コード ウィンドウの右側にある **[テスト]** タブをクリックして、テスト パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="356bc-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="356bc-172">**[HTTP メソッド]** を **[GET]** に変更します。</span><span class="sxs-lookup"><span data-stu-id="356bc-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="356bc-173">**[クエリ]** の下の *[+ パラメーターの追加]*\* をクリックし、以下のパラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="356bc-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="356bc-174">name</span><span class="sxs-lookup"><span data-stu-id="356bc-174">name</span></span>      |  <span data-ttu-id="356bc-175">value</span><span class="sxs-lookup"><span data-stu-id="356bc-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="356bc-176">filename</span><span class="sxs-lookup"><span data-stu-id="356bc-176">filename</span></span> | <span data-ttu-id="356bc-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="356bc-177">image1.jpg</span></span> |

1. <span data-ttu-id="356bc-178">テスト パネルで **[実行]** をクリックして、HTTP 要求を関数に送信します。</span><span class="sxs-lookup"><span data-stu-id="356bc-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="356bc-179">関数の出力にアップロード URL が返されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="356bc-180">関数の実行は、ログ パネルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="356bc-180">The function execution appears in the logs panel.</span></span>

    ![関数が正常に実行されたことを示すログ](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="356bc-182">関数アプリでの CORS を構成する</span><span class="sxs-lookup"><span data-stu-id="356bc-182">Configure CORS in the function app</span></span>

<span data-ttu-id="356bc-183">アプリのフロントエンドは Blob Storage でホストされているため、そのドメイン名は、Azure 関数アプリとは異なります。</span><span class="sxs-lookup"><span data-stu-id="356bc-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="356bc-184">先ほど作成した関数がクライアント側の JavaScript によって正常に呼び出されるように、関数アプリは、クロス オリジン リソース共有 (CORS) 用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="356bc-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="356bc-185">関数アプリ ウィンドウの左側のナビゲーション バーで、お使いの関数アプリの名前をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="356bc-186">**[プラットフォーム機能]** をクリックして、高度な機能の一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="356bc-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="356bc-187">**[API]** で **[CORS]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-187">Under **API**, click **CORS**.</span></span>

    ![CORS を選択する](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="356bc-189">前のモジュールのアプリケーション URL の許可されたオリジンを、末尾の **/** を省略して追加します (例: `https://firstserverlessweb.z4.web.core.windows.net`)。</span><span class="sxs-lookup"><span data-stu-id="356bc-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![追加されたサーバーレス Web アプリの URL を示す CORS 設定](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="356bc-191">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-191">Click **Save**.</span></span>

1. <span data-ttu-id="356bc-192">C#</span><span class="sxs-lookup"><span data-stu-id="356bc-192">C#</span></span>

   1. <span data-ttu-id="356bc-193">(C#) `GetUploadUrl` 関数に戻り、**[統合]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="356bc-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="356bc-194">(C#) 選択した HTTP メソッドで、**[OPTIONS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="356bc-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="356bc-195">**[GET]**、**[POST]**、および **[OPTIONS]** がすべて選択されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="356bc-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="356bc-196">CORS では OPTIONS メソッドが使用されます。C# 関数の既定では、これは選択されていません。</span><span class="sxs-lookup"><span data-stu-id="356bc-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="356bc-197">(C#) **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="356bc-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="356bc-198">ポータルで関数アプリに移動し、**[概要]** タブを選択して、**[再起動]** をクリックし、CORS の変更が反映されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="356bc-199">ストレージ アカウントで CORS を構成する</span><span class="sxs-lookup"><span data-stu-id="356bc-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="356bc-200">アプリでは Blob Storage に対してクライアント側 JavaScript 呼び出しを実行して、ファイルをアップロードするため、ストレージ アカウントを CORS 用に構成する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="356bc-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="356bc-201">次のコマンドを実行して、すべてのオリジンが、ファイルをストレージ アカウントにアップロードできるようにします。</span><span class="sxs-lookup"><span data-stu-id="356bc-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="356bc-202">画像をアップロードする Web アプリを変更する</span><span class="sxs-lookup"><span data-stu-id="356bc-202">Modify the web app to upload images</span></span>

<span data-ttu-id="356bc-203">Web アプリでは、**settings.js** という名前のファイルから設定を取得します。</span><span class="sxs-lookup"><span data-stu-id="356bc-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="356bc-204">次の手順では、Cloud Shell を使用してファイルを作成し、`window.apiBaseUrl` を関数アプリの URL に設定し、`window.blobBaseUrl` を Azure Blob Storage エンドポイントの URL に設定します。</span><span class="sxs-lookup"><span data-stu-id="356bc-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="356bc-205">Cloud Shell で、現在のディレクトリが **www/dist** フォルダーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="356bc-206">関数アプリの URL のクエリを実行し、**FUNCTION_APP_URL** という名前の bash 変数に格納します。</span><span class="sxs-lookup"><span data-stu-id="356bc-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="356bc-207">変数が正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="356bc-208">API 呼び出しのベース URI をお使いの関数アプリに設定するには、次のように **settings.js** を作成し、次のような、関数アプリの URL を追加します。</span><span class="sxs-lookup"><span data-stu-id="356bc-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="356bc-209">変更を行うには、次のコマンドを実行するか、VIM などのコマンド ライン エディターを使用します。</span><span class="sxs-lookup"><span data-stu-id="356bc-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="356bc-210">ファイルが正常に書き込まれたことを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="356bc-211">Blob Storage のベース URL のクエリを実行し、**BLOB_BASE_URL** という名前の bash 変数に格納します。</span><span class="sxs-lookup"><span data-stu-id="356bc-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="356bc-212">変数が正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="356bc-213">API 呼び出しのベース URI をお使いの関数アプリに設定するには、次のコード行のようなストレージ URL を **settings.js** に追加します。</span><span class="sxs-lookup"><span data-stu-id="356bc-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="356bc-214">変更を行うには、次のコマンドを実行するか、VIM などのコマンド ライン エディターを使用します。</span><span class="sxs-lookup"><span data-stu-id="356bc-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="356bc-215">ファイルが正常に書き込まれたこと、および 2 行が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="356bc-216">ファイルを Blob Storage にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="356bc-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="356bc-217">Web アプリケーションをテストする</span><span class="sxs-lookup"><span data-stu-id="356bc-217">Test the web application</span></span>

<span data-ttu-id="356bc-218">この時点で、ギャラリー アプリケーションでは Blob Storage に画像をアップロードできますが、まだ画像は表示できません。</span><span class="sxs-lookup"><span data-stu-id="356bc-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="356bc-219">`GetImages` 関数の呼び出しが試行されますが、この関数はまだ存在しません。これは後のモジュールで作成します。</span><span class="sxs-lookup"><span data-stu-id="356bc-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="356bc-220">この呼び出しは失敗し、Web ページには "分析中…" と表示されたままになりますが、選択する画像は正常にアップロードされます。</span><span class="sxs-lookup"><span data-stu-id="356bc-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="356bc-221">画像が正常にアップロードされていることを確認するには、Azure portal で **images** コンテナーの内容を確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="356bc-222">ブラウザー ウィンドウで、アプリケーションを参照します。</span><span class="sxs-lookup"><span data-stu-id="356bc-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="356bc-223">画像ファイルを選択し、アップロードします。</span><span class="sxs-lookup"><span data-stu-id="356bc-223">Select an image file and upload it.</span></span> <span data-ttu-id="356bc-224">アップロードが完了しますが、画像を表示する機能はまだ追加していないため、アップロードした写真はアプリに表示されません </span><span class="sxs-lookup"><span data-stu-id="356bc-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="356bc-225">(Web ページには "画像分析中..." と表示されたままになります。これは後で修正します)。</span><span class="sxs-lookup"><span data-stu-id="356bc-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="356bc-226">Cloud Shell で、**images** コンテナーに画像がアップロードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="356bc-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="356bc-227">次のチュートリアルに進む前に、**images** コンテナー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="356bc-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="356bc-228">まとめ</span><span class="sxs-lookup"><span data-stu-id="356bc-228">Summary</span></span>

<span data-ttu-id="356bc-229">このモジュールでは、Azure 関数アプリを作成しました。また、サーバーレス関数を使用して、Web アプリケーションで画像を Blob Storage にアップロードできるようにする方法を学習しました。</span><span class="sxs-lookup"><span data-stu-id="356bc-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="356bc-230">次は、BLOB によってトリガーされるサーバーレス関数を使用して、アップロードされた画像のサムネイルを作成する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="356bc-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
