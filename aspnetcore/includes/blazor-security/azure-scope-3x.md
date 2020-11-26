<span data-ttu-id="435e1-101">Azure portal では、AAD テナントに[検証済みまたは未確認の発行元ドメイン](/azure/active-directory/develop/howto-configure-publisher-domain)があるかどうかに基づいて、次のアプリ ID URI 形式のいずれかが指定されています。</span><span class="sxs-lookup"><span data-stu-id="435e1-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="435e1-102">上記の 2 つのアプリ ID URI のうち、クライアント アプリでスコープ URI を指定するために後者が使用され、承認が成功しない場合は、スキームとホストなしでスコープ URI を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="435e1-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="435e1-103">例:</span><span class="sxs-lookup"><span data-stu-id="435e1-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
