<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="fc29a-101">検証規則をムービー モデルに追加する</span><span class="sxs-lookup"><span data-stu-id="fc29a-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="fc29a-102">DataAnnotations 名前空間には、クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性があります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="fc29a-103">また、DataAnnotations には、書式設定を支援し、どの検証を行わない `DataType` のような書式設定属性もあります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="fc29a-104">組み込みの `Required`、`StringLength`、`RegularExpression`、および `Range` 検証属性を利用するように、`Movie` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="fc29a-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="fc29a-105">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="fc29a-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="fc29a-106">`Required` および `MinimumLength` 属性は、プロパティに値が必要であることを示します。ただし、この検証を満たすためにユーザーが空白を入力することは禁止されていません。</span><span class="sxs-lookup"><span data-stu-id="fc29a-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="fc29a-107">`RegularExpression` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc29a-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="fc29a-108">上のコード "Genre" では:</span><span class="sxs-lookup"><span data-stu-id="fc29a-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="fc29a-109">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-109">Must only use letters.</span></span>
  * <span data-ttu-id="fc29a-110">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="fc29a-111">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="fc29a-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="fc29a-112">`RegularExpression` "評価":</span><span class="sxs-lookup"><span data-stu-id="fc29a-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="fc29a-113">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="fc29a-114">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc29a-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="fc29a-115">"PG-13" は評価に対して有効ですが、"Genre" に対しては失敗します。</span><span class="sxs-lookup"><span data-stu-id="fc29a-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="fc29a-116">`Range` 属性は、指定した範囲内に値を制限します。</span><span class="sxs-lookup"><span data-stu-id="fc29a-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="fc29a-117">`StringLength` 属性では、文字列プロパティの最大長を設定でき、オプションとして最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="fc29a-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="fc29a-118">値の型 (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="fc29a-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="fc29a-119">ASP.NET Core によって検証規則が自動的に適用されるようにすると、アプリをより堅牢にできます。</span><span class="sxs-lookup"><span data-stu-id="fc29a-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="fc29a-120">また、ユーザーが何かを検証することを忘れてしまい、データベースに不適切なデータが誤って格納されることもなくなります。</span><span class="sxs-lookup"><span data-stu-id="fc29a-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
