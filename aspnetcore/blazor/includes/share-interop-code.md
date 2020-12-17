## <a name="share-interop-code-in-a-class-library"></a>クラス ライブラリで相互運用コードを共有する

JS 相互運用コードは、クラス ライブラリに含めて NuGet パッケージ内でコードを共有することができます。

クラス ライブラリは、ビルドされたアセンブリでの JavaScript リソースの埋め込みを処理します。 JavaScript ファイルは、`wwwroot` フォルダーに配置されます。 ライブラリのビルド時にツールがリソースの埋め込みを行います。

ビルド済みの NuGet パッケージは、NuGet パッケージの参照と同じ方法で、アプリのプロジェクト ファイルで参照されます。 パッケージが復元されたら、アプリ コードを C# と同じように JavaScript に呼び出すことができます。

詳細については、「<xref:blazor/components/class-libraries>」を参照してください。
