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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460043"
---
Azure App Service 認証を使用すると、Azure 関数アプリでターン キー認証がサポートされるようになります。 Facebook、Twitter、Microsoft アカウント、Google、および Azure Active Directory とシームレスに統合されます。 Web アプリのバックエンド API を保護するには、App Service 認証を追加します。

## <a name="enable-app-service-authentication"></a>App Service 認証を有効にする

1. Azure Portal で関数アプリを開きます。

1. **[プラットフォーム機能]** で、**[認証/承認]** を選択します。

    ![[認証/承認] を選択する](media/functions-first-serverless-web-app/6-authorization.jpg)

1. 次の値を選択します。
    
    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **App Service 認証** | On | 認証を有効にします。 |
    | **Action when request is not authenticated (要求が認証されない場合のアクション)** | Azure Active Directory でのログイン | 構成済みの認証方法 (下記参照) を選択します。 |
    | **認証プロバイダー** | 下記参照 | 下記参照 |
    | **トークン ストア** | On | App Service でトークンの格納および管理を許可します。 |
    | **[許可される外部リダイレクト URL]** | 作成したアプリケーションの URL。例: https://firstserverlessweb.z4.web.core.windows.net/ | ユーザーが認証された後に App Service からリダイレクトできる URL。 |

1. **[Azure Active Directory]** を選択して **[Azure Active Directory の設定]** を表示します。

    1. **[管理モード]** として **[簡易]** を選択し、以下の値を入力します。
    
        | Setting      |  推奨値   | 説明                                        |
        | --- | --- | ---|
        | **管理モード** | [簡易]、[新しい AD アプリを作成する] | サービス プリンシパルと Azure Active Directory 認証が自動的に設定されます。 |
        | **アプリの作成** | my-serverless-webapp | 一意のアプリケーション名を入力します。 |
    
    1. **[OK]** をクリックして Azure Active Directory の設定を保存します。

    ![[認証/承認] と Azure Active Directory の設定](media/functions-first-serverless-web-app/6-create-aad.png)

1. **[Save]** をクリックします。


## <a name="modify-the-web-app-to-enable-authentication"></a>Web アプリを変更して認証を有効にする

1. Cloud Shell で、現在のディレクトリが **www/dist** フォルダーであることを確認します。

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. 関数アプリで認証を有効にするには、次のコード行を **settings.js** に追加します。

    `window.authEnabled = true`

    次のコマンドを使用するか、VIM などのコマンド ライン エディターを使用して、変更を加え結果を表示します。

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    ファイルが変更されたことを確認します。

    ```azurecli
    cat settings.js
    ```

1. ファイルを Blob Storage にアップロードします。

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>アプリケーションをテストする

1. ブラウザーでアプリケーションを開きます。 **[ログイン]** をクリックしてログインします。

1. 画像ファイルを選択し、アップロードします。

    ![サインイン ページ](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>まとめ

このモジュールでは、Azure App Service 認証を使用してアプリケーションに認証を追加する方法について説明しました。
