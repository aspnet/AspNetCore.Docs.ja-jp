---
title: ASP.NET Core のコンテキストヘッダー
author: rick-anderson
description: ASP.NET Core データ保護コンテキストヘッダーの実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051721"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="1c657-103">ASP.NET Core のコンテキストヘッダー</span><span class="sxs-lookup"><span data-stu-id="1c657-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="1c657-104">背景と理論</span><span class="sxs-lookup"><span data-stu-id="1c657-104">Background and theory</span></span>

<span data-ttu-id="1c657-105">データ保護システムでは、"キー" は認証された暗号化サービスを提供できるオブジェクトを意味します。</span><span class="sxs-lookup"><span data-stu-id="1c657-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="1c657-106">各キーは一意の id (GUID) によって識別され、それにはアルゴリズム情報と entropic マテリアルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1c657-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="1c657-107">各キーは一意のエントロピを伝達することを目的としていますが、システムではこれを強制することはできません。また、キーリング内の既存のキーのアルゴリズム情報を変更することによって、キーリングを手動で変更する可能性がある開発者にも考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c657-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="1c657-108">これらのケースによってセキュリティ要件を達成するために、データ保護システムには [暗号化の俊敏性](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)の概念があります。これにより、複数の暗号化アルゴリズムで1つの entropic 値を安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="1c657-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="1c657-109">暗号化の機敏性をサポートするほとんどのシステムは、ペイロード内のアルゴリズムに関する特定の情報を含めます。</span><span class="sxs-lookup"><span data-stu-id="1c657-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="1c657-110">アルゴリズムの OID は、通常、このような場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="1c657-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="1c657-111">ただし、1つの問題として、同じアルゴリズムを指定する方法は複数あります。 "AES" (CNG) とマネージ Aes、AesManaged、AesCryptoServiceProvider、AesCng、および RijndaelManaged (特定のパラメーター) クラスはすべて同じものであり、これらのすべてを正しい OID にマッピングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c657-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="1c657-112">開発者がカスタムアルゴリズム (または AES! の別の実装) を提供する必要がある場合は、その OID を知らせる必要があります。</span><span class="sxs-lookup"><span data-stu-id="1c657-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="1c657-113">この追加の登録手順では、システム構成が特に困難になります。</span><span class="sxs-lookup"><span data-stu-id="1c657-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="1c657-114">前のステップでは、間違った方向から問題に近づいていると判断しました。</span><span class="sxs-lookup"><span data-stu-id="1c657-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="1c657-115">OID はアルゴリズムがどのようなものかを示していますが、実際には気にしません。</span><span class="sxs-lookup"><span data-stu-id="1c657-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="1c657-116">2つの異なるアルゴリズムで1つの entropic 値を安全に使用する必要がある場合、アルゴリズムが実際にどのようなものかを知る必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1c657-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="1c657-117">実際には、どのように動作するかということに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1c657-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="1c657-118">任意の適正な対称ブロック暗号アルゴリズムは、強力な擬似乱数 (PRP) でもあります。入力 (キー、チェーンモード、IV、プレーンテキスト) を修正し、暗号文の出力は、同じ入力が指定された他の対称ブロック暗号アルゴリズムとは異なる確率で区別されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="1c657-119">同様に、適切なキー付きハッシュ関数も強力な擬似乱数関数 (PRF) であり、固定された入力セットを指定すると、その出力は他のキー付きハッシュ関数とは異なる overwhelmingly になります。</span><span class="sxs-lookup"><span data-stu-id="1c657-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="1c657-120">コンテキストヘッダーを構築するには、この強力な PRPs と Prps の概念を使用します。</span><span class="sxs-lookup"><span data-stu-id="1c657-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="1c657-121">このコンテキストヘッダーは基本的に、特定の操作で使用されているアルゴリズムに対する安定したサムプリントとして機能し、データ保護システムで必要とされる暗号化の機敏性を提供します。</span><span class="sxs-lookup"><span data-stu-id="1c657-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="1c657-122">このヘッダーは再現可能で、後で [サブキー派生プロセス](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)の一部として使用されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="1c657-123">コンテキストヘッダーを作成するには、基になるアルゴリズムの動作モードに応じて2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="1c657-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="1c657-124">CBC モード暗号化 + HMAC 認証</span><span class="sxs-lookup"><span data-stu-id="1c657-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="1c657-125">コンテキストヘッダーは、次のコンポーネントで構成されています。</span><span class="sxs-lookup"><span data-stu-id="1c657-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="1c657-126">[16 ビット]値 00 00。 "CBC encryption + HMAC authentication" という意味になります。</span><span class="sxs-lookup"><span data-stu-id="1c657-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="1c657-127">[32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1c657-128">[32 ビット]対称ブロック暗号アルゴリズムのブロックサイズ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1c657-129">[32 ビット]HMAC アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="1c657-130">(現在、キーサイズは常にダイジェストのサイズと一致しています)。</span><span class="sxs-lookup"><span data-stu-id="1c657-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="1c657-131">[32 ビット]HMAC アルゴリズムのダイジェストサイズ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="1c657-132">`EncCBC(K_E, IV, "")`。これは、空の文字列入力を指定した場合は対称ブロック暗号アルゴリズムの出力、IV はすべてゼロのベクターです。</span><span class="sxs-lookup"><span data-stu-id="1c657-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="1c657-133">の構築に `K_E` ついては、以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="1c657-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="1c657-134">`MAC(K_H, "")`。これは HMAC アルゴリズムの出力で、空の文字列入力が指定されています。</span><span class="sxs-lookup"><span data-stu-id="1c657-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="1c657-135">の構築に `K_H` ついては、以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="1c657-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="1c657-136">理想的には、とのすべてのゼロベクトルを渡すことができ `K_E` `K_H` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="1c657-137">ただし、基になるアルゴリズムが、何らかの操作 (特に DES と 3DES) を実行する前に、弱いキーの存在を確認するという状況を回避したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="1c657-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="1c657-138">代わりに、カウンタモードで NIST SP800-108 KDF を使用します ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1 を参照)。長さ0のキー、ラベル、およびコンテキストと HMACSHA512 を基になる PRF として使用します。</span><span class="sxs-lookup"><span data-stu-id="1c657-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="1c657-139">`| K_E | + | K_H |`出力のバイトを取得し、その結果をと自体に分解し `K_E` `K_H` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="1c657-140">数学的には、次のように表されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="1c657-141">例: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="1c657-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="1c657-142">例として、対称ブロック暗号アルゴリズムが AES-192-CBC で、検証アルゴリズムが HMACSHA256 の場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1c657-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="1c657-143">システムは、次の手順を使用してコンテキストヘッダーを生成します。</span><span class="sxs-lookup"><span data-stu-id="1c657-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="1c657-144">まず、 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 256 bits` 指定されたアルゴリズムごとにとを使用します。</span><span class="sxs-lookup"><span data-stu-id="1c657-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="1c657-145">これは `K_E = 5BB6..21DD` 、 `K_H = A04A..00A9` 次の例のとにつながります。</span><span class="sxs-lookup"><span data-stu-id="1c657-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="1c657-146">次に、 `Enc_CBC (K_E, IV, "")` 上記のように、AES-192-CBC を計算し `IV = 0*` `K_E` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="1c657-147">次に、 `MAC(K_H, "")` 上記の HMACSHA256 を計算し `K_H` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="1c657-148">これにより、以下の完全なコンテキストヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="1c657-149">このコンテキストヘッダーは、認証された暗号化アルゴリズムペアの拇印です (AES-192-CBC encryption + HMACSHA256 validation)。</span><span class="sxs-lookup"><span data-stu-id="1c657-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="1c657-150">[上記](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)のコンポーネントは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1c657-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="1c657-151">マーカー `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="1c657-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="1c657-152">ブロック暗号キーの長さ `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="1c657-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="1c657-153">ブロック暗号ブロックサイズ `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="1c657-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="1c657-154">HMAC キーの長さ `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1c657-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="1c657-155">HMAC ダイジェストサイズ `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1c657-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="1c657-156">ブロック暗号 PRP 出力 `(F4 74 - DB 6F)` と</span><span class="sxs-lookup"><span data-stu-id="1c657-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="1c657-157">HMAC PRF 出力 `(D4 79 - end)` 。</span><span class="sxs-lookup"><span data-stu-id="1c657-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="1c657-158">CBC モード暗号化 + HMAC 認証コンテキストヘッダーは、アルゴリズムの実装が Windows CNG によって提供されるか、マネージ SymmetricAlgorithm と KeyedHashAlgorithm 型によって提供されるかにかかわらず、同じように構築されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="1c657-159">これにより、異なるオペレーティングシステムで実行されているアプリケーションは、Os によってアルゴリズムの実装が異なる場合でも、同じコンテキストヘッダーを確実に生成できます。</span><span class="sxs-lookup"><span data-stu-id="1c657-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="1c657-160">(実際には、KeyedHashAlgorithm は適切な HMAC である必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1c657-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="1c657-161">任意のキー付きハッシュアルゴリズムの種類を使用できます)。</span><span class="sxs-lookup"><span data-stu-id="1c657-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="1c657-162">例: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="1c657-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="1c657-163">まず、 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 160 bits` 指定されたアルゴリズムごとにとを使用します。</span><span class="sxs-lookup"><span data-stu-id="1c657-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="1c657-164">これは `K_E = A219..E2BB` 、 `K_H = DC4A..B464` 次の例のとにつながります。</span><span class="sxs-lookup"><span data-stu-id="1c657-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="1c657-165">次に、 `Enc_CBC (K_E, IV, "")` 上記のように、3des-192-CBC を計算し `IV = 0*` `K_E` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="1c657-166">次に、 `MAC(K_H, "")` 上記の HMACSHA1 を計算し `K_H` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="1c657-167">これにより、次に示すように、認証された暗号化アルゴリズムペア (3DES-192-CBC encryption + HMACSHA1 validation) の拇印である完全なコンテキストヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="1c657-168">コンポーネントは次のように分類されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-168">The components break down as follows:</span></span>

* <span data-ttu-id="1c657-169">マーカー `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="1c657-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="1c657-170">ブロック暗号キーの長さ `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="1c657-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="1c657-171">ブロック暗号ブロックサイズ `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="1c657-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="1c657-172">HMAC キーの長さ `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="1c657-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="1c657-173">HMAC ダイジェストサイズ `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="1c657-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="1c657-174">ブロック暗号 PRP 出力 `(AB B1 - E1 0E)` と</span><span class="sxs-lookup"><span data-stu-id="1c657-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="1c657-175">HMAC PRF 出力 `(76 EB - end)` 。</span><span class="sxs-lookup"><span data-stu-id="1c657-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="1c657-176">Galois/カウンタモードの暗号化 + 認証</span><span class="sxs-lookup"><span data-stu-id="1c657-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="1c657-177">コンテキストヘッダーは、次のコンポーネントで構成されています。</span><span class="sxs-lookup"><span data-stu-id="1c657-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="1c657-178">[16 ビット]値 00 01。 "GCM encryption + authentication" を意味します。</span><span class="sxs-lookup"><span data-stu-id="1c657-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="1c657-179">[32 ビット]対称ブロック暗号アルゴリズムのキーの長さ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1c657-180">[32 ビット]認証済みの暗号化操作中に使用される nonce のサイズ (バイト単位、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="1c657-181">(システムの場合は、nonce のサイズが96ビットに固定されています)。</span><span class="sxs-lookup"><span data-stu-id="1c657-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="1c657-182">[32 ビット]対称ブロック暗号アルゴリズムのブロックサイズ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="1c657-183">(GCM の場合は、ブロックサイズが128ビットに固定されています)。</span><span class="sxs-lookup"><span data-stu-id="1c657-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="1c657-184">[32 ビット]認証された暗号化関数によって生成される認証タグのサイズ (バイト、ビッグエンディアン)。</span><span class="sxs-lookup"><span data-stu-id="1c657-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="1c657-185">(システムの場合は、タグサイズが128ビットに固定されています)。</span><span class="sxs-lookup"><span data-stu-id="1c657-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="1c657-186">[128 ビット]のタグ。 `Enc_GCM (K_E, nonce, "")` これは、空の文字列入力が指定された場合は、対称ブロック暗号アルゴリズムの出力であり、nonce は96ビットのすべてゼロベクターです。</span><span class="sxs-lookup"><span data-stu-id="1c657-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="1c657-187">`K_E` は、CBC encryption + HMAC 認証シナリオと同じメカニズムを使用して派生します。</span><span class="sxs-lookup"><span data-stu-id="1c657-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="1c657-188">ただし、ここには何もないので、基本的には `K_H` を持ち、 `| K_H | = 0` アルゴリズムは次の形式に折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="1c657-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="1c657-189">例: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="1c657-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="1c657-190">まず、 `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` where を使用 `| K_E | = 256 bits` します。</span><span class="sxs-lookup"><span data-stu-id="1c657-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="1c657-191">次に、上記のように、 `Enc_GCM (K_E, nonce, "")` 指定された AES-256-GCM の認証タグを計算し `nonce = 096` `K_E` ます。</span><span class="sxs-lookup"><span data-stu-id="1c657-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="1c657-192">これにより、以下の完全なコンテキストヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="1c657-193">コンポーネントは次のように分類されます。</span><span class="sxs-lookup"><span data-stu-id="1c657-193">The components break down as follows:</span></span>

* <span data-ttu-id="1c657-194">マーカー `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="1c657-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="1c657-195">ブロック暗号キーの長さ `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1c657-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="1c657-196">nonce のサイズ `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="1c657-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="1c657-197">ブロック暗号ブロックサイズ `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="1c657-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="1c657-198">認証タグのサイズ `(00 00 00 10)` と</span><span class="sxs-lookup"><span data-stu-id="1c657-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="1c657-199">ブロック暗号を実行する認証タグ `(E7 DC - end)` 。</span><span class="sxs-lookup"><span data-stu-id="1c657-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
