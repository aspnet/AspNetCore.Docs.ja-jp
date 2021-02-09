<span data-ttu-id="b55d8-101">HTTP トレーラーは HTTP ヘッダーに似ていますが、応答本文の送信後に送信される点が異なります。</span><span class="sxs-lookup"><span data-stu-id="b55d8-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="b55d8-102">IIS と HTTP.sys の場合は、HTTP/2 応答トレーラーのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b55d8-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="b55d8-103">上記のコード例では次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="b55d8-103">In the preceding example code:</span></span>

* <span data-ttu-id="b55d8-104">`SupportsTrailers` によって、応答におけるトレーラーが確実にサポートされます。</span><span class="sxs-lookup"><span data-stu-id="b55d8-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="b55d8-105">`DeclareTrailer` によって、指定したトレーラー名が `Trailer` 応答ヘッダーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b55d8-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="b55d8-106">応答のトレーラーを宣言することは省略可能ですが、推奨されています。</span><span class="sxs-lookup"><span data-stu-id="b55d8-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="b55d8-107">`DeclareTrailer` を呼び出す場合、それは、応答ヘッダーを送信する前に行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="b55d8-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="b55d8-108">`AppendTrailer` によって、トレーラーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="b55d8-108">`AppendTrailer` appends the trailer.</span></span>
