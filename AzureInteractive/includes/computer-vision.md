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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079494"
---
<span data-ttu-id="ee663-103">現段階では、このアプリケーションは画像をアップロードして表示できる機能ギャラリーです。</span><span class="sxs-lookup"><span data-stu-id="ee663-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="ee663-104">このモジュールでは、Microsoft Cognitive Services の Computer Vision API を使用して、アップロードした画像のキャプションを生成し、Cosmos DB 内の画像メタデータと共にそのキャプションを保存する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ee663-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="ee663-105">Computer Vision アカウントを作成する</span><span class="sxs-lookup"><span data-stu-id="ee663-105">Create a Computer Vision account</span></span>

<span data-ttu-id="ee663-106">Microsoft Cognitive Services は、開発者がよりインテリジェントなアプリケーションを開発するためのサービスのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="ee663-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="ee663-107">Computer Vision API は、高度なアルゴリズムを使用して画像を処理し、各画像に関する情報を返す、サーバーレス サービスです。</span><span class="sxs-lookup"><span data-stu-id="ee663-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="ee663-108">Free レベルでは、1 か月あたり最大 5,000 回 API 呼び出しを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ee663-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="ee663-109">Cloud Shell に引き続きサインインしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee663-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="ee663-110">そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択して、Cloud Shell ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="ee663-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="ee663-111">リソース グループ内で一意の名前を持つ、**ComputerVision** という種類の新しい Cognitive Services アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ee663-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="ee663-112">Free レベルの場合は、**F0** を SKU として使用します。</span><span class="sxs-lookup"><span data-stu-id="ee663-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="ee663-113">Computer Vision の既存のアカウントが既にある場合は、Standard アカウント (S1) の作成が必要になる場合があります。ただし、これによってコストが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="ee663-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="ee663-114">Computer Vision の URL およびキーの Function App 設定を作成する</span><span class="sxs-lookup"><span data-stu-id="ee663-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="ee663-115">Computer Vision API を呼び出すには、URL とキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="ee663-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="ee663-116">Computer Vision API のキーと URL を取得し、bash 変数に保存します。</span><span class="sxs-lookup"><span data-stu-id="ee663-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="ee663-117">それぞれ **COMP_VISION_KEY** と **COMP_VISION_URL** という名前のアプリ設定を、関数アプリで作成します。</span><span class="sxs-lookup"><span data-stu-id="ee663-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="ee663-118">ResizeImage 関数から Computer Vision API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="ee663-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="ee663-119">次の手順では、**ResizeImage** 関数を変更して Computer Vision API を呼び出し、アップロードした各画像について説明してその説明を Cosmos DB に保存します。</span><span class="sxs-lookup"><span data-stu-id="ee663-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="ee663-120">Azure Portal で関数アプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="ee663-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="ee663-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="ee663-121">**C#**</span></span>

    1. <span data-ttu-id="ee663-122">(C#) 左側のナビゲーションを使用して、**ResizeImage** 関数を見つけ、コード ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="ee663-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="ee663-123">(C#) このコードを [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ee663-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="ee663-124">このコードでは、`HttpClient` を使用して Computer Vision API を呼び出し、その結果を Cosmos DB に保存しています。</span><span class="sxs-lookup"><span data-stu-id="ee663-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="ee663-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="ee663-125">**JavaScript**</span></span>

    1. <span data-ttu-id="ee663-126">(JavaScript) この関数では、Computer Vision API への HTTP 呼び出しを行うために、npm の `axios` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="ee663-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="ee663-127">npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ee663-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="ee663-128">(JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。</span><span class="sxs-lookup"><span data-stu-id="ee663-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="ee663-129">(JavaScript) コンソールで `npm install axios` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ee663-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="ee663-130">操作が完了するまでに 1 分から 2 分かかる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ee663-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="ee663-131">(JavaScript) 左側のナビゲーションで関数名 (**ResizeImage**) をクリックして関数を表示し、**index.js** のすべてを [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js) の内容で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ee663-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="ee663-132">コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。</span><span class="sxs-lookup"><span data-stu-id="ee663-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="ee663-133">**[Save]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ee663-133">Click **Save**.</span></span> <span data-ttu-id="ee663-134">関数が正常に保存されエラーがないことを、ログ パネルで確認します。</span><span class="sxs-lookup"><span data-stu-id="ee663-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="ee663-135">アプリケーションをテストする</span><span class="sxs-lookup"><span data-stu-id="ee663-135">Test the application</span></span>

1. <span data-ttu-id="ee663-136">ブラウザーでアプリケーションを開きます。</span><span class="sxs-lookup"><span data-stu-id="ee663-136">Open the application in a browser.</span></span> <span data-ttu-id="ee663-137">画像ファイルを選択してアップロードします。</span><span class="sxs-lookup"><span data-stu-id="ee663-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="ee663-138">数秒後に、新しい画像のサムネイルがこのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ee663-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="ee663-139">画像上にポインターを合わせると、Computer Vision によって生成された説明が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ee663-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="ee663-140">まとめ</span><span class="sxs-lookup"><span data-stu-id="ee663-140">Summary</span></span>

<span data-ttu-id="ee663-141">このモジュールでは、Microsoft Cognitive Services の Computer Vision API を使用して、アップロードした各画像のキャプションを自動的に生成する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="ee663-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="ee663-142">次に、Azure App Service 認証を使用して、アプリケーションに認証を追加する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="ee663-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>