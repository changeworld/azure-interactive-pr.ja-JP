---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460060"
---
Azure Cosmos DB は、Microsoft のサーバーレスなグローバル分散型マルチモデル データベースです。 このモジュールでは、Azure Functions を使用して、画像のメタデータを JSON ドキュメントとして Cosmos DB に格納および取得する方法を説明します。

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Cosmos DB アカウント、データベース、およびコレクションを作成する

Cosmos DB アカウントは、Cosmos DB データベースを含む Azure リソースです。

1. Cloud Shell にまだサインインしていることを確認してください。  そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択し、Cloud Shell ウィンドウを開きます。 

1. このチュートリアルの他のリソースと同じリソース グループ内で、Cosmos DB アカウントを作成し、一意の名前を付けます。

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Cosmos DB アカウントを作成したら、そのアカウントに **imagesdb** という名前の新しいデータベースを作成します。

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. データベースを作成したら、400 要求ユニット (RU) のスループットで、そのデータベースに **images** という名前の新しいコレクションを作成します。

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>サムネイルの作成時にドキュメントを Cosmos DB に保存する

Cosmos DB の出力バインディングを使用すると、Azure Functions から Cosmos DB コレクション内にドキュメントを作成できます。 次の手順で、**ResizeImage** 関数の Cosmos DB 出力バインディングを構成し、保存するドキュメント (オブジェクト) を返すようにこの関数を変更します。

1. Azure Portal で関数アプリを開きます。

1. 左側のナビゲーションで、**ResizeImage** 関数を展開し、**[統合]** を選択します。

1. **[出力]** で **[新しい出力]** をクリックします。

1. **[Azure Cosmos DB]** を選択し、 **[選択]** をクリックします。

    ![[新しい出力] を選択する](media/functions-first-serverless-web-app/4-new-output.jpg)

1. **[Azure Cosmos DB output]\(Azure Cosmos DB 出力\)** の各フィールドに次の値を入力します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **[ドキュメント パラメーター名]** | **[関数の戻り値を使用する]** を選択します | テキスト ボックスの値が自動的に **$return** に設定されます。 |
    | **データベース名** | imagesdb | 作成したデータベースの名前を使用します。 |
    | **[コレクション名]** | images | 作成したコレクションの名前を使用します。 |

1. **[Azure Cosmos DB アカウント接続]** の横にある **[新規]** をクリックします。 以前に作成した Cosmos DB アカウントを選択します。

    ![Azure Cosmos DB 出力バインディングの設定を入力する](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. **[保存]** をクリックして Cosmos DB 出力バインディングを作成します。

1. 左側の関数名 **ResizeImage** をクリックして、この関数を開きます。

1. **C# を選択した場合**

    1. (C#) 関数の戻り値の型を **void** から **object** に変更します。

    1. (C#) 関数の末尾に次のコード ブロックを追加して、保存するドキュメントを返すようにします。
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![変更後の ResizeImages 関数の run.csx](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript を選択した場合**

    1. (JavaScript) `else` 句の `context.done()` ステートメントを変更して、Cosmos DB に保存するドキュメントを返すようにします。

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

1. コード ウィンドウ下部の **[ログ]** をクリックして、ログ パネルを展開します。

1. **[保存]** をクリックします。 関数が正しく保存され、エラーがないことを、ログ パネルで確認します。


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Cosmos DB の画像を一覧表示する関数を作成する

この Web アプリケーションでは、 Cosmos DB から画像のメタデータを取得するための API が必要です。 次の手順では、 Cosmos DB 入力バインディングを使用してデータベース コレクションを照会する HTTP トリガー関数を作成します。

1. 関数アプリで、左側の **[Functions]** にポインターを合わせて **+** をクリックし、新しい関数を作成します。

1. **HttpTrigger** テンプレートを検索して選択します。

1. 以下の値を使用して、取得する画像の URL を生成する関数を作成します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **関数名の指定** | GetImages | アプリケーションが関数を検出できるように、この名前を正確に表示されているとおりに入力します。 |
    | **承認レベル** | Anonymous | 関数にパブリックにアクセスできるようにします。 |

1. **作成** をクリックします。

1. 新しい関数が作成されたら、左側のナビゲーションの関数名の下にある **[統合]** をクリックします。

1. **[新しい入力]** をクリックして、**[Azure Cosmos DB]** を選択します。 

    ![[新しい入力] を選択する](media/functions-first-serverless-web-app/4-new-input.jpg)

1. **[選択]** をクリックします。

1. 次の値に入力します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **[ドキュメント パラメーター名]** | documents | 関数内のパラメーター名と一致します。 |
    | **データベース名** | imagesdb |  |
    | **[コレクション名]** | images |  |
    | **SQL query** | select * from c order by c._ts desc | ドキュメントを取得します。最新のドキュメントが最初に取得されます。 |
    | **Azure Cosmos DB アカウント接続** | 既存の接続文字列を選択する |  |

1. **[保存]** をクリックし、入力バインディングを作成します。

1. **C# を選択した場合**

    1. 関数の名前をクリックしてコード ウィンドウを開き、**run.csx** のすべてを [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx) の内容で置き換えます。

1. **JavaScript を選択した場合**

    1. 関数の名前をクリックしてコード ウィンドウを開き、**index.js** のすべてを [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js) の内容で置き換えます。

1. コード ウィンドウ下部の **[ログ]** をクリックして、ログ パネルを展開します。

1. **[保存]** をクリックします。 関数が正常に保存され、エラーがないことを、ログ パネルで確認します


## <a name="test-the-application"></a>アプリケーションをテストする

1. ブラウザーでアプリケーションを開きます。 画像ファイルを選択し、アップロードします。

1. 数秒後に、新しい画像のサムネイルがこのページに表示されます。

1. Azure Portal の検索ボックスを使用して、Cosmos DB アカウントを名前で検索し、 クリックして開きます。

1. 左側の **[データ エクスプローラー]** をクリックし、コレクションとドキュメントを表示します。

1. **imagesdb** データベースの下で、**images** コレクションを選択します。

1. アップロードした画像に対してドキュメントが作成されていることを確認します。

    ![アップロードした画像のドキュメントが表示されているデータ エクスプローラー](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>まとめ

このモジュールでは、Cosmos DB アカウント、データベース、およびコレクションを作成する方法について説明しました。 Cosmos DB のバインディングを使用して、Cosmos DB コレクション内に画像のメタデータを保存し取得する方法も学習しました。 次に、Microsoft Cognitive Services を使用して、アップロードした各画像のキャプションを自動的に生成する方法を説明します。
