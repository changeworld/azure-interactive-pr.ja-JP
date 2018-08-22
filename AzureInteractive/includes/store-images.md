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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079466"
---
<span data-ttu-id="46648-103">Azure Cosmos DB は、Microsoft のサーバーレスなグローバル分散型マルチモデル データベースです。</span><span class="sxs-lookup"><span data-stu-id="46648-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="46648-104">このモジュールでは、Azure Functions を使用して、画像のメタデータを JSON ドキュメントとして Cosmos DB に格納および取得する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="46648-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="46648-105">Cosmos DB アカウント、データベース、およびコレクションを作成する</span><span class="sxs-lookup"><span data-stu-id="46648-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="46648-106">Cosmos DB アカウントは、Cosmos DB データベースを含む Azure リソースです。</span><span class="sxs-lookup"><span data-stu-id="46648-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="46648-107">Cloud Shell に引き続きサインインしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="46648-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="46648-108">そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択して、Cloud Shell ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="46648-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="46648-109">このチュートリアルの他のリソースと同じリソース グループ内で、Cosmos DB アカウントを作成し、一意の名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="46648-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="46648-110">Cosmos DB アカウントを作成したら、そのアカウントに **imagesdb** という名前の新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="46648-111">データベースを作成したら、400 要求ユニット (RU) のスループットで、そのデータベースに **images** という名前の新しいコレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="46648-112">サムネイルの作成時にドキュメントを Cosmos DB に保存する</span><span class="sxs-lookup"><span data-stu-id="46648-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="46648-113">Cosmos DB の出力バインディングを使用すると、Azure Functions から Cosmos DB コレクション内にドキュメントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="46648-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="46648-114">次の手順で、**ResizeImage** 関数の Cosmos DB 出力バインディングを構成し、保存するドキュメント (オブジェクト) を返すようにこの関数を変更します。</span><span class="sxs-lookup"><span data-stu-id="46648-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="46648-115">Azure Portal で関数アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="46648-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="46648-116">左側のナビゲーションで、**ResizeImage** 関数を展開し、**[統合]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="46648-117">**[出力]** で **[新しい出力]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="46648-118">**[Azure Cosmos DB]** 項目を見つけて選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="46648-119">**[選択]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-119">Then click **Select**.</span></span>

    ![[新しい出力] を選択する](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="46648-121">**[Azure Cosmos DB output]\(Azure Cosmos DB 出力\)** の各フィールドに次の値を入力します。</span><span class="sxs-lookup"><span data-stu-id="46648-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="46648-122">Setting</span><span class="sxs-lookup"><span data-stu-id="46648-122">Setting</span></span>      |  <span data-ttu-id="46648-123">推奨値</span><span class="sxs-lookup"><span data-stu-id="46648-123">Suggested value</span></span>   | <span data-ttu-id="46648-124">説明</span><span class="sxs-lookup"><span data-stu-id="46648-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="46648-125">**[ドキュメント パラメーター名]**</span><span class="sxs-lookup"><span data-stu-id="46648-125">**Document parameter name**</span></span> | <span data-ttu-id="46648-126">**[関数の戻り値を使用する]** を選択します</span><span class="sxs-lookup"><span data-stu-id="46648-126">Select **Use function return value**</span></span> | <span data-ttu-id="46648-127">テキスト ボックスの値が自動的に **$return** に設定されます。</span><span class="sxs-lookup"><span data-stu-id="46648-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="46648-128">**データベース名**</span><span class="sxs-lookup"><span data-stu-id="46648-128">**Database name**</span></span> | <span data-ttu-id="46648-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="46648-129">imagesdb</span></span> | <span data-ttu-id="46648-130">作成したデータベースの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="46648-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="46648-131">**[コレクション名]**</span><span class="sxs-lookup"><span data-stu-id="46648-131">**Collection name**</span></span> | <span data-ttu-id="46648-132">images</span><span class="sxs-lookup"><span data-stu-id="46648-132">images</span></span> | <span data-ttu-id="46648-133">作成したコレクションの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="46648-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="46648-134">**[Azure Cosmos DB アカウント接続]** の横にある **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="46648-135">以前に作成した Cosmos DB アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-135">Select the Cosmos DB account you previously created.</span></span>

    ![Azure Cosmos DB 出力バインディングの設定を入力する](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="46648-137">**[保存]** をクリックして Cosmos DB 出力バインディングを作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="46648-138">左側の関数名 **ResizeImage** をクリックして、この関数を開きます。</span><span class="sxs-lookup"><span data-stu-id="46648-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="46648-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="46648-139">**C#**</span></span>

    1. <span data-ttu-id="46648-140">(C#) 関数の戻り値の型を **void** から **object** に変更します。</span><span class="sxs-lookup"><span data-stu-id="46648-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="46648-141">(C#) 関数の末尾に次のコード ブロックを追加して、保存するドキュメントを返すようにします。</span><span class="sxs-lookup"><span data-stu-id="46648-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![変更後の ResizeImages 関数の run.csx](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="46648-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="46648-143">**JavaScript**</span></span>

    1. <span data-ttu-id="46648-144">(JavaScript) `else` 句の `context.done()` ステートメントを変更して、Cosmos DB に保存するドキュメントを返すようにします。</span><span class="sxs-lookup"><span data-stu-id="46648-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="46648-145">コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="46648-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="46648-146">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-146">Click **Save**.</span></span> <span data-ttu-id="46648-147">関数が正常に保存されエラーがないことを、ログ パネルで確認します。</span><span class="sxs-lookup"><span data-stu-id="46648-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="46648-148">Cosmos DB の画像を一覧表示する関数を作成する</span><span class="sxs-lookup"><span data-stu-id="46648-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="46648-149">この Web アプリケーションでは、Cosmos DB の画像のメタデータを取得するための API が必要です。</span><span class="sxs-lookup"><span data-stu-id="46648-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="46648-150">次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="46648-150">In the following steps.</span></span> <span data-ttu-id="46648-151">Cosmos DB 入力バインディングを使用してデータベース コレクションを照会する、HTTP によってトリガーされる関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="46648-152">関数アプリで、左側の **[Functions]** にポインターを合わせて **+** をクリックし、新しい関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="46648-153">**HttpTrigger** テンプレートを検索して選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="46648-154">以下の値を使用して、取得する画像の URL を生成する関数を作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="46648-155">Setting</span><span class="sxs-lookup"><span data-stu-id="46648-155">Setting</span></span>      |  <span data-ttu-id="46648-156">推奨値</span><span class="sxs-lookup"><span data-stu-id="46648-156">Suggested value</span></span>   | <span data-ttu-id="46648-157">説明</span><span class="sxs-lookup"><span data-stu-id="46648-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="46648-158">**関数名の指定**</span><span class="sxs-lookup"><span data-stu-id="46648-158">**Name your function**</span></span> | <span data-ttu-id="46648-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="46648-159">GetImages</span></span> | <span data-ttu-id="46648-160">アプリケーションから関数を検出できるように、この名前を、表示されているとおりに入力します。</span><span class="sxs-lookup"><span data-stu-id="46648-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="46648-161">**承認レベル**</span><span class="sxs-lookup"><span data-stu-id="46648-161">**Authorization level**</span></span> | <span data-ttu-id="46648-162">匿名</span><span class="sxs-lookup"><span data-stu-id="46648-162">Anonymous</span></span> | <span data-ttu-id="46648-163">関数からパブリックにアクセスすることを許可します。</span><span class="sxs-lookup"><span data-stu-id="46648-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="46648-164">**Create** をクリックしてください。</span><span class="sxs-lookup"><span data-stu-id="46648-164">Click **Create**.</span></span>

1. <span data-ttu-id="46648-165">新しい関数が作成されたら、左側のナビゲーションの関数名の下にある **[統合]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="46648-166">**[新しい入力]** をクリックして、**[Azure Cosmos DB]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![[新しい入力] を選択する](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="46648-168">**[選択]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-168">Click **Select**.</span></span>

1. <span data-ttu-id="46648-169">次の値に入力します。</span><span class="sxs-lookup"><span data-stu-id="46648-169">Fill out the following values:</span></span>

    | <span data-ttu-id="46648-170">Setting</span><span class="sxs-lookup"><span data-stu-id="46648-170">Setting</span></span>      |  <span data-ttu-id="46648-171">推奨値</span><span class="sxs-lookup"><span data-stu-id="46648-171">Suggested value</span></span>   | <span data-ttu-id="46648-172">説明</span><span class="sxs-lookup"><span data-stu-id="46648-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="46648-173">**[ドキュメント パラメーター名]**</span><span class="sxs-lookup"><span data-stu-id="46648-173">**Document parameter name**</span></span> | <span data-ttu-id="46648-174">documents</span><span class="sxs-lookup"><span data-stu-id="46648-174">documents</span></span> | <span data-ttu-id="46648-175">関数内のパラメーター名と一致します。</span><span class="sxs-lookup"><span data-stu-id="46648-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="46648-176">**データベース名**</span><span class="sxs-lookup"><span data-stu-id="46648-176">**Database name**</span></span> | <span data-ttu-id="46648-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="46648-177">imagesdb</span></span> |  |
    | <span data-ttu-id="46648-178">**[コレクション名]**</span><span class="sxs-lookup"><span data-stu-id="46648-178">**Collection name**</span></span> | <span data-ttu-id="46648-179">images</span><span class="sxs-lookup"><span data-stu-id="46648-179">images</span></span> |  |
    | <span data-ttu-id="46648-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="46648-180">**SQL query**</span></span> | <span data-ttu-id="46648-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="46648-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="46648-182">ドキュメントを取得します。最新のドキュメントが最初に取得されます。</span><span class="sxs-lookup"><span data-stu-id="46648-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="46648-183">**Azure Cosmos DB アカウント接続**</span><span class="sxs-lookup"><span data-stu-id="46648-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="46648-184">既存の接続文字列を選択する</span><span class="sxs-lookup"><span data-stu-id="46648-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="46648-185">**[保存]** をクリックして入力バインディングを作成します。</span><span class="sxs-lookup"><span data-stu-id="46648-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="46648-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="46648-186">**C#**</span></span>

    1. <span data-ttu-id="46648-187">関数の名前をクリックしてコード ウィンドウを開き、**run.csx** のすべてを [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="46648-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="46648-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="46648-188">**JavaScript**</span></span>

    1. <span data-ttu-id="46648-189">関数の名前をクリックしてコード ウィンドウを開き、**index.js** のすべてを [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="46648-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="46648-190">コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="46648-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="46648-191">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="46648-191">Click **Save**.</span></span> <span data-ttu-id="46648-192">関数が正常に保存されエラーがないことを、ログ パネルで確認します。</span><span class="sxs-lookup"><span data-stu-id="46648-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="46648-193">アプリケーションをテストする</span><span class="sxs-lookup"><span data-stu-id="46648-193">Test the application</span></span>

1. <span data-ttu-id="46648-194">ブラウザーでアプリケーションを開きます。</span><span class="sxs-lookup"><span data-stu-id="46648-194">Open the application in a browser.</span></span> <span data-ttu-id="46648-195">画像ファイルを選択し、アップロードします。</span><span class="sxs-lookup"><span data-stu-id="46648-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="46648-196">数秒後に、新しい画像のサムネイルがこのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="46648-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="46648-197">Azure Portal の検索ボックスを使用して、Cosmos DB アカウントを名前で検索します。</span><span class="sxs-lookup"><span data-stu-id="46648-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="46648-198">クリックして開きます。</span><span class="sxs-lookup"><span data-stu-id="46648-198">Click it to open it.</span></span>

1. <span data-ttu-id="46648-199">左側の **[データ エクスプローラー]** をクリックし、コレクションとドキュメントを参照します。</span><span class="sxs-lookup"><span data-stu-id="46648-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="46648-200">**imagesdb** データベースの下で、**images** コレクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="46648-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="46648-201">アップロードした画像に対してドキュメントが作成されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="46648-201">Confirm that a document was created for the uploaded image.</span></span>

    ![アップロードした画像のドキュメントが表示されているデータ エクスプローラー](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="46648-203">まとめ</span><span class="sxs-lookup"><span data-stu-id="46648-203">Summary</span></span>

<span data-ttu-id="46648-204">このモジュールでは、Cosmos DB アカウント、データベース、およびコレクションを作成する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="46648-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="46648-205">Cosmos DB のバインディングを使用して、Cosmos DB コレクション内に画像のメタデータを保存し取得する方法も学習しました。</span><span class="sxs-lookup"><span data-stu-id="46648-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="46648-206">次に、Microsoft Cognitive Services を使用して、アップロードした各画像のキャプションを自動的に生成する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="46648-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>