## Intellisense

もしかしたらもう気づいたかもしれませんが、CakeチームはVisual Studio CodeでCakeファイルを編集中にIntellisenseが効くよう開発をおこなってきました。今日、[OmniSharp](http://www.omnisharp.net/)が私たちのプルリクエスト([#932](https://github.com/OmniSharp/omnisharp-roslyn/pull/932)および[#1681](https://github.com/OmniSharp/omnisharp-vscode/pull/1681))をmergeし、Cakeのサポートを含む[C# extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)のv1.13.0がリリースされたことをアナウンスすることができ、大変嬉しく思います。これに加え、私たちは[bakery](https://github.com/cake-build/bakery)という新しいツールをリリースしました。これはスクリプト解析/コード生成ツールエンジンであり、CakeのIntellisenseを可能にしています。bakeryのより詳細な情報や、バックグラウンドで何が起こっているのかについては、後日ブログで紹介します。

## How

どうやってIntellisenseを有効化するのでしょうか？それでは以下の手順に従ってください(Visual Studio Codeは既にインストール済みであると仮定します)。

1. Visual Studio Codeを起動し、Cakeファイルの存在するフォルダへ移動します。
1. [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)のv1.13.0がインストールされていることを確認します。
1. [Visual Studio Code Extension for Cake](https://marketplace.visualstudio.com/items?itemName=cake-build.cake-vscode)のv0.10.1がインストールされていることを確認します。
1. [Cake](https://www.nuget.org/packages/Cake/)が`tools`フォルダにインストールされていることを確認します。addinが2回インストールされることを避けるため、v0.22.0以降を推奨します。最も簡単な方法は、`.\build.ps1`を実行することです。
1. [Bakery](https://www.nuget.org/packages/Cake.Bakery/)が`tools`フォルダにインストールされていることを確認します。最も簡単な方法は、Visual Studio Code Extension for Cakeの`Install intellisense support`コマンドを実行することです。コマンドパレットを開いて「cake」と入力し、当該オプションを選択してください。
1. ここで、Visual Studio Code上で当該フォルダを閉じ、再び開く必要があります。
1. Success!
<br/>![Intellisense](/assets/img/intellisense-vscode/intellisense-vscode.png)

## Troubleshooting

**Q: 上記手順を実行しましたが、Intellisenseが有効になりません。**

C# Extension for Visual Studio Codeは、全ての`*.sln`ファイルを検知し、`omnisharp`起動時にそのディレクトリを対象とします。もし`*.cake`ファイルが異なる階層に配置されていた場合、[project selector](https://code.visualstudio.com/docs/languages/csharp#_roslyn-and-omnisharp)で`Cake`プロジェクトを選択する必要があります。

**Q: `Cake`プロジェクトを選択しましたが、Intellisenseが有効になりません。**

`OmniSharp Log`にエラーや警告が表示されていないかどうか確認してください。

```
[warn]: OmniSharp.Cake.CakeProjectSystem
    Cake script service not connected. Aborting.
```

上記のログは、`Cake.Bakery`が`tools`フォルダにインストールされていないことを示しています。それに対して、以下のログは大抵の場合、`Cake`が`tools`フォルダにインストールされていないことを示しています(よりよいエラーメッセージに向けて修正中です)。

```
[fail]: OmniSharp.Cake.CakeProjectSystem
    c:\Users\mb\src\gh\bakery\setup.cake will be ignored due to an following error
    System.TypeLoadException: A null or zero length string does not represent a valid Type.
```

**Q: エラーメッセージは表示されていませんが、それでもIntellisenseが有効になりません。**

`OmniSharp Log`を表示し、`Cake`に関係する箇所を探してください。もし適切にセットアップされていれば、少なくとも以下のようなログが表示されているはずです。

```
[info]: OmniSharp.Cake.CakeProjectSystem
    Detecting Cake files in 'c:\Users\mb\src\gh\bakery'.
[info]: OmniSharp.Cake.CakeProjectSystem
    Found 29 Cake files.
```

表示されていない場合、C#拡張機能v1.13.0のインストール時に問題が発生した可能性があります。拡張機能を一度アンインストールし、その後再度インストールしてみてください。その後、Windowsの場合は`%userprofile%\.vscode\extensions\ms-vscode.csharp-1.13.0\.omnisharp\`、LinuxないしmacOSの場合は`~/.vscode/extensions/ms-vscode.csharp-1.13.0/.omnisharp/`を開き、`OmniSharp.Cake.dll`が存在することを確認してください。

**Q: Intellisenseが有効になりましたが、その後追加したファイルが表示されません。**

これは`omnisharp`の既知の問題であり、`*.csx`でも同様です。`omnisharp`チームとともに今後のリリースで改善されるよう取り組んでいます。

**Q: `Cake.CoreCLR`が`tools`フォルダにインストールされていればそれで充分でしょうか。**

いいえ、現時点では`Cake.CoreCLR`ではIntellisenseが有効になりません。

**Q: 上記全てを試しましたが、まだIntellisenseが有効になりません。**

Github上で[bakery](https://github.com/cake-build/bakery)リポジトリにissueを立てるか、[Gitter](https://gitter.im/cake-build/cake)で私たちにコンタクトをとってみてください。