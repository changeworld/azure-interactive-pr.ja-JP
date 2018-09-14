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
作成しているアプリケーションはフォト ギャラリーです。クライアントサイドの JavaScript を使用して API を呼び出し、画像をアップロードおよび表示します。この章では、画像をアップロードするための時間制限付き URL を生成するサーバレスファンクションを使用し、 API を作成します。この Web アプリケーションでは、[Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) を使用し、生成された URL　を使用し、 Blob Storage に画像をアップロードします。

## <a name="create-a-blob-storage-container-for-images"></a>画像用の Blob Storage コンテナーを作成する

アプリケーションには、画像をアップロードしてホストするための別のストレージ コンテナーが必要です。

1. Cloud Shell (bash) にまだサインインしていることを確認してください。そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択し、 Cloud Shell ウィンドウを開きます。

1.  すべての BLOB へのパブリック アクセスを持つストレージ アカウント内に **images** という名前の新しいコンテナーを作成します。

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

Azure Functions はサーバーレスファンクションを実行するためのサービスです。サーバーレスファンクションは、 HTTP リクエストや BLOB がストレージ コンテナーに作成されるといったイベントをトリガーにして実行 (呼び出す)できます。

Azure Functions アプリは、 1 つ以上のサーバーレスファンクションのためのコンテナーです。

1. 以前作成した **first-serverless-app** というリソース グループに一意の名前を持つ新しい Azure Function アプリを作成します。関数アプリにはストレージ アカウントが必要です。このチュートリアルでは、既存のストレージ アカウントを使用します。

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a>HTTP リクエストによってトリガーされるサーバーレスファンクションを作成する

フォト ギャラリー Web アプリケーションでは、サーバーレスファンクションへの HTTP リクエストにより、画像を Blob storage に安全にアップロードするための時間制限付き URL が生成されます。この関数は HTTP リクエストによってトリガーされ、、 Azure Storage SDK を使用し、セキュアな URL を生成して返します。

1. 関数アプリを作成後、 Azure portal の検索ボックスでアプリを検索し、クリックして開きます。

    ![関数アプリを開く](media/functions-first-serverless-web-app/2-search-function-app.png)

1. 関数アプリ ウィンドウの左側のナビゲーションで、**[関数]** にポインターを合わせて **+** をクリックし、新しいサーバーレスファンクションの作成を開始します。

    ![新しい関数を作成する](media/functions-first-serverless-web-app/2-new-function.png)

1. **[カスタム関数]** をクリックして、関数テンプレートの一覧を表示します。

1. **HTTP trigger** テンプレートを見つけて、使用する言語 (C# または JavaScript) をクリックします。

1. 以下の値を使用し、 BLOB へのアップロード URL を生成する関数を作成します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **言語** | C# または JavaScript | 使用したい言語を選択 |
    | **関数名の指定** | GetUploadUrl | アプリケーションが関数を検出できるように、この名前を正確に表示されているとおりに入力します |
    | **承認レベル** | Anonymous | 関数にパブリックにアクセスできるようにします。 |

    ![HTTP によってトリガーされる新しい関数の設定を入力する](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. **[作成]** をクリックし、関数を作成します。

1. **C# を選択した場合**

    1. 関数のソース コードが表示されたら、 **run.csx** のすべてを [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx) の内容で書き換えます。

1. **JavaScript を選択した場合**

    1. (JavaScript) この関数には、セキュアな URL の作成に必要な Shared Access Signature (SAS) トークンを生成するために、 npm の `azure-storage` パッケージが必要です。 npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。

    1. (JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。

        ![コンソール ウィンドウを開く](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) `cd d:\home\site\wwwroot` コマンドを実行し、カレントディレクトリが **d:\home\site\wwwroot** であることを確認します。

    1. (JavaScript) `npm init -y` コマンドを実行し、空の **package.json** ファイルを作成します。

    1. (JavaScript) コンソールで `npm install --save azure-storage` コマンドを実行してパッケージをインストールし、 **package.json** に保存します。 操作が完了するまでに 1 分から 2 分かかる場合があります。

    1. (JavaScript) 左側のナビゲーションで関数名 (**GetUploadUrl**) をクリックして関数を表示し、 **index.js** のすべてを [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js) の内容で書き換えます。
    
        ![更新後の index.js](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. コード ウィンドウの下部の **[ログ]** をクリックし、ログ パネルを展開します。

1. **[Save]** をクリックします。 関数が正常にコンパイルされていることを、ログ パネルで確認します。

関数によって Blob Storage にファイルをアップロードするために使用される、いわゆる Shared Access Signature (SAS) URL が生成されます。 SAS URL の有効期間は短く、この URL でアップロードが許可されるのは 1 つのファイルだけです。 [Shared Access Signature の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)の詳細については、 Blob Storage のドキュメントを参照してください。

## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>ストレージ接続文字列に環境変数を追加する

作成した関数で SAS URL を生成できるようにするには、ストレージ アカウントの接続文字列が必要です。接続文字列は、関数の本体でハードコーディングするのではなく、アプリケーション設定として格納できます。アプリケーション設定には、関数アプリ内のすべての関数が環境変数としてアクセスできます。

1. Cloud Shell で、ストレージ アカウント接続文字列のクエリを実行し、 **STORAGE_CONNECTION_STRING** という名前の bash 変数に保存します。

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    変数が正常に設定されていることを確認します。

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. 前の手順で保存した値を使用して、 **AZURE_STORAGE_CONNECTION_STRING** という名前の新しいアプリケーション設定を作成します。

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    コマンドの出力に、正しい値の新しいアプリケーション設定が含まれていることを確認します。


## <a name="test-the-serverless-function"></a>サーバーレスファンクションをテストする

Azure portal には、関数の作成と編集だけでなく、関数をテストするツールも組み込まれています。

1. HTTP サーバーレスファンクションをテストするには、コード ウィンドウの右側にある **[テスト]** タブをクリックし、テスト パネルを展開します。

1. **[HTTP メソッド]** を **[GET]** に変更します。

1. **[クエリ]** の下の *[+ パラメーターの追加]** をクリックし、以下のパラメーターを追加します。

    | name      |  value   | 
    | --- | --- |
    | filename | image1.jpg |

1. テスト パネルで **[実行]** をクリックし、HTTP リクエストを関数に送信します。

1. この関数は出力にアップロード用 URL を返します。関数の実行は、ログ パネルに表示されます。

    ![関数が正常に実行されたことを示すログ](media/functions-first-serverless-web-app/2-test-function.png)

## <a name="configure-cors-in-the-function-app"></a>関数アプリでの CORS を設定する

アプリのフロントエンドは Blob Storage でホストされているため、そのドメイン名は、Azure 関数アプリとは異なります。クライアントサイドの JavaScript が先ほど作成した関数を正常に呼び出すためには、関数アプリは、cross-origin resource sharing (CORS) 用に設定する必要があります。

1. 関数アプリ ウィンドウの左側のナビゲーション バーで、関数アプリの名前をクリックします。

1. **[プラットフォーム機能]** をクリックし、拡張機能の一覧を表示します。

1. **[API]** にある **[CORS]** をクリックします。

    ![CORS を選択する](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. アクセス元として前章で作成したアプリケーションの URL を末尾の **/** を省略して追加します (例: `https://firstserverlessweb.z4.web.core.windows.net`)。

    ![追加されたサーバーレス Web アプリの URL を示す CORS 設定](media/functions-first-serverless-web-app/2-add-cors.png)

1. **[Save]** をクリックします。

1. C# を選択した場合

   1. (C#) `GetUploadUrl` 関数に戻り、**[統合]** タブを選択します。

   1. (C#) 選択した HTTP メソッドで、**[OPTIONS]** を選択します。

      **[GET]**、**[POST]**、および **[OPTIONS]** がすべて選択されている必要があります。 CORS では OPTIONS メソッドが使用されます。C# 関数の既定では、これは選択されていません。

   1. (C#) **[保存]** をクリックします。

1.  Azure portal で関数アプリに移動し、**[概要]** タブを選択して、 CORS を有効化するために **[再起動]** をクリックします。

## <a name="configure-cors-in-the-storage-account"></a>ストレージ アカウントで CORS を設定する

アプリでは Blob Storage へのクライアントサイド JavaScript の呼び出しによって、ファイルをアップロードするため、ストレージ アカウントを CORS 用に設定する必要もあります。

1. 次のコマンドを実行して、すべてのアクセス元が、ファイルをストレージ アカウントにアップロードできるようにします。

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>画像をアップロードするように Web アプリを修正する

Web アプリでは、 **settings.js** という名前のファイルから設定を取得します。次の手順では、 Cloud Shell を使用してファイルを作成し、 `window.apiBaseUrl` を関数アプリの URL に設定し、 `window.blobBaseUrl` を Azure Blob Storage エンドポイントの URL に設定します。

1. Cloud Shell で、カレントディレクトリが **www/dist** フォルダーであることを確認します。

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. 関数アプリの URL のクエリを実行し、 **FUNCTION_APP_URL** という名前の bash 変数に格納します。

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    変数が正しく設定されていることを確認します。

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. API 呼び出しのベース URI を関数アプリに設定するには、 **settings.js** を作成し、次のような関数アプリの URL を追加します。

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    次のコマンドを実行するか、Vim などのコマンド ライン エディターを使用し、変更を加えることができます。

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    ファイルが正しく書き込まれたことを確認します。

    ```azurecli
    cat settings.js
    ```

1. Blob Storage のベース URL のクエリを実行し、 **BLOB_BASE_URL** という名前の bash 変数に格納します。

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    変数が正しく設定されていることを確認します。

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. API 呼び出しのベース URI を関数アプリに設定するには、 **settings.js** にストレージ URL として以下のようなコードを追加します。

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    次のコマンドを実行するか、Vim などのコマンド ライン エディターを使用し、変更を加えることができます。

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    ファイルが正しく書き込まれたこと、および 2 行のコードが含まれていることを確認します。

    ```azurecli
    cat settings.js
    ```

1. ファイルを Blob Storage にアップロードします。

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Web アプリケーションをテストする

この時点で、ギャラリー アプリケーションは Blob Storage に画像をアップロードできますが、まだ画像を表示できません。後の章で作成するまだ存在しない `GetImages` 関数を呼び出そうとします。この呼び出しは失敗し、 Web ページには "分析中…" と表示されたままになりますが、選択する画像は正常にアップロードされます。

画像が正常にアップロードされていることを確認するには、 Azure portal で **images** コンテナーの内容を確認します。

1. ブラウザー ウィンドウで、アプリケーションにアクセスします。画像ファイルを選択し、アップロードします。アップロードが完了しますが、画像を表示する機能がまだ追加されていないため、アップロードした画像はアプリに表示されません (Web ページには "画像分析中..." と表示されたままになりますが、これは後で修正します)。

1. Cloud Shell で、**images** コンテナーに画像がアップロードされていることを確認します。

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. 次のチュートリアルに進む前に、**images** コンテナー内のすべてのファイルを削除します。

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```

## <a name="summary"></a>まとめ

本章では、 Azure Function アプリを作成し、サーバーレスファンクションを使用して、 Web アプリケーションが画像を Blob Storage にアップロードできるようにする方法を学習しました。次に、 Blob でトリガーされたサーバーレスファンクションを使用し、アップロードされた画像のサムネイルを作成する方法を学習します。
