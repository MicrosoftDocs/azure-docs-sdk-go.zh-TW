---
title: 使用 Azure SDK for Go 進行驗證
description: 了解 Azure SDK for Go 中可用的驗證方法及其使用方式。
services: azure
author: sptramer
ms.author: sttramer
ms.date: 04/03/2018
ms.topic: article
ms.service: azure
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 39f9dc5a7cdf9ab84cfd9264446bacb31392ca80
ms.sourcegitcommit: 59d2b4c9d8da15fbbd15e36551093219fdaf256e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a><span data-ttu-id="bed16-103">Azure SDK for Go 中可用的驗證方法</span><span class="sxs-lookup"><span data-stu-id="bed16-103">Authentication methods in the Azure SDK for Go</span></span>

<span data-ttu-id="bed16-104">Azure SDK for Go 提供各種不同的驗證類型和方法，供您的應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="bed16-104">The Azure SDK for Go offers a variety of authentication types and methods that your application can use.</span></span> <span data-ttu-id="bed16-105">支援的方法範圍包括從環境變數中提取資訊，到網路式互動式驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-105">Supported authentication methods range from pulling information from environment variables to interactive web-based authentication.</span></span> <span data-ttu-id="bed16-106">本文會向您介紹 SDK 中可用的驗證類型，以及使用驗證類型的方法。</span><span class="sxs-lookup"><span data-stu-id="bed16-106">This article introduces you to the available types of authentication in the SDK, and the methods for using them.</span></span> <span data-ttu-id="bed16-107">您也會了解在選取最適合應用程式的驗證類型時，怎樣才是最佳作法。</span><span class="sxs-lookup"><span data-stu-id="bed16-107">You'll also learn best practices for selecting which authentication type is right for your application.</span></span>

## <a name="available-authentication-types-and-methods"></a><span data-ttu-id="bed16-108">可用的驗證類型和方法</span><span class="sxs-lookup"><span data-stu-id="bed16-108">Available authentication types and methods</span></span>

<span data-ttu-id="bed16-109">Azure SDK for Go 提供數種不同類型的驗證，各自使用不同的認證集。</span><span class="sxs-lookup"><span data-stu-id="bed16-109">The Azure SDK for Go offers several different types of authentication, using different credentials sets.</span></span> <span data-ttu-id="bed16-110">每種驗證類型都可以透過不同的驗證方法加以使用；而這些驗證方法就是 SDK 採用認證作為輸入值的方式。</span><span class="sxs-lookup"><span data-stu-id="bed16-110">Each of these authentication types are available through different authentication methods, which are how the SDK takes these credentials as input.</span></span> <span data-ttu-id="bed16-111">下表描述可用的驗證類型，以及建議您的應用程式使用該驗證類型的情況。</span><span class="sxs-lookup"><span data-stu-id="bed16-111">The following table describes the available types of authentication and situations in which they're recommended for use by your application.</span></span>

| <span data-ttu-id="bed16-112">驗證類型</span><span class="sxs-lookup"><span data-stu-id="bed16-112">Authentication type</span></span> | <span data-ttu-id="bed16-113">建議使用的時機...</span><span class="sxs-lookup"><span data-stu-id="bed16-113">Recommended when...</span></span> |
|---------------------|---------------------|
| <span data-ttu-id="bed16-114">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="bed16-114">Certificate-based authentication</span></span> | <span data-ttu-id="bed16-115">您所具有的 X509 憑證，已針對 Azure Active Directory (AAD) 使用者或服務主體進行設定。</span><span class="sxs-lookup"><span data-stu-id="bed16-115">You have an X509 certificate that was configured for an Azure Active Directory (AAD) user or service principal.</span></span> <span data-ttu-id="bed16-116">若要深入了解，請參閱[開始在 Azure Active Directory 中使用憑證式驗證]。</span><span class="sxs-lookup"><span data-stu-id="bed16-116">To learn more, see [Get started with certificate-based authentication in Azure Active Directory].</span></span> |
| <span data-ttu-id="bed16-117">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="bed16-117">Client credentials</span></span> | <span data-ttu-id="bed16-118">您所具有的服務主體，已針對此應用程式或其所屬應用程式類別進行設定。</span><span class="sxs-lookup"><span data-stu-id="bed16-118">You have a configured service principal that is set up for this application or a class of applications it belongs to.</span></span> <span data-ttu-id="bed16-119">若要深入了解，請參閱[使用 Azure CLI 2.0 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="bed16-119">To learn more, see [Create a service principal with Azure CLI 2.0].</span></span> |
| <span data-ttu-id="bed16-120">受控服務識別 (MSI)</span><span class="sxs-lookup"><span data-stu-id="bed16-120">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="bed16-121">您的應用程式正在 Azure 資源上執行，該資源已使用受控服務識別 (MSI) 進行設定。</span><span class="sxs-lookup"><span data-stu-id="bed16-121">Your application is running on an Azure resource that has been configured with Managed Service Identity (MSI).</span></span> <span data-ttu-id="bed16-122">若要深入了解，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="bed16-122">To learn more, see [Managed Service Identity (MSI) for Azure resources].</span></span> |
| <span data-ttu-id="bed16-123">裝置權杖</span><span class="sxs-lookup"><span data-stu-id="bed16-123">Device token</span></span> | <span data-ttu-id="bed16-124">您的應用程式__只__適用於以互動方式使用，且可能會有來自多個 AAD 租用戶的多位使用者。</span><span class="sxs-lookup"><span data-stu-id="bed16-124">Your application is meant to be used interactively __only__ and will have a variety of users, potentially from multiple AAD tenants.</span></span> <span data-ttu-id="bed16-125">使用者可以存取網頁瀏覽器來登入。</span><span class="sxs-lookup"><span data-stu-id="bed16-125">Users have access to a web browser to log in.</span></span> <span data-ttu-id="bed16-126">若要深入了解，請參閱[使用裝置權杖驗證](#use-device-token-authentication)。</span><span class="sxs-lookup"><span data-stu-id="bed16-126">For more information, see [Use device token authentication](#use-device-token-authentication).</span></span>|
| <span data-ttu-id="bed16-127">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="bed16-127">Username/password</span></span> | <span data-ttu-id="bed16-128">您所具有的應用程式無法使用任何其他驗證方法。</span><span class="sxs-lookup"><span data-stu-id="bed16-128">You have an interactive application that cannot use any other authentication method.</span></span> <span data-ttu-id="bed16-129">您的使用者未啟用 AAD 登入的多重要素驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-129">Your users do not have multi-factor authentication enabled for their AAD login.</span></span> |

> [!IMPORTANT]
> <span data-ttu-id="bed16-130">若您使用的是用戶端認證以外的驗證類型，則必須在 Azure Active Directory 中註冊您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="bed16-130">If you use an authentication type other than client credentials, your application must be registered in Azure Active Directory.</span></span> <span data-ttu-id="bed16-131">若要了解註冊方式，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。</span><span class="sxs-lookup"><span data-stu-id="bed16-131">To learn how, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).</span></span>

> [!NOTE]
> <span data-ttu-id="bed16-132">除非您有特殊需求，請避免使用者名稱/密碼驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-132">Unless you have special requirements, avoid username/password authentication.</span></span> <span data-ttu-id="bed16-133">在使用者式登入適用的情況下，通常可改用裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-133">In situations where user-based login is appropriate, device token authentication can usually be used instead.</span></span>

[開始在 Azure Active Directory 中使用憑證式驗證]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Get started with certificate-based authentication in Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 2.0 建立服務主體]: /cli/azure/create-an-azure-service-principal-azure-cli
[Create a service principal with Azure CLI 2.0]: /cli/azure/create-an-azure-service-principal-azure-cli
[適用於 Azure 資源的受控服務識別 (MSI)]: /azure/active-directory/managed-service-identity/overview
[Managed Service Identity (MSI) for Azure resources]: /azure/active-directory/managed-service-identity/overview

<span data-ttu-id="bed16-137">這些驗證類型可以透過不同的驗證方法加以使用。</span><span class="sxs-lookup"><span data-stu-id="bed16-137">These authentication types are available through different methods.</span></span> <span data-ttu-id="bed16-138">[環境式驗證](#use-environment-based-authentication)會直接從程式的環境讀取認證。</span><span class="sxs-lookup"><span data-stu-id="bed16-138">[_Environment-based authentication_](#use-environment-based-authentication) reads credentials directly from the program's environment.</span></span> <span data-ttu-id="bed16-139">[檔案式驗證](#use-file-based-authentication)會載入包含服務主體認證的檔案。</span><span class="sxs-lookup"><span data-stu-id="bed16-139">[_File-based authentication_](#use-file-based-authentication) loads a file containing service principal credentials.</span></span> <span data-ttu-id="bed16-140">[用戶端式驗證](#use-an-authentication-client)會使用 Go 程式碼中的物件，使得您要負責在程式執行期間提供認證。</span><span class="sxs-lookup"><span data-stu-id="bed16-140">[_Client-based authentication_](#use-an-authentication-client) uses an object in Go code and makes you responsible for providing the credentials during program execution.</span></span> <span data-ttu-id="bed16-141">最後，[裝置權杖驗證](#use-device-token-authentication)需要使用者透過網頁瀏覽器，用權杖以互動方式登入，而且不能搭配環境式或檔案式驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-141">Finally, [_Device token authentication_](#use-device-token-authentication) requires users to log in interactively through a web browser with a token, and cannot be used with environment- or file-based authentication.</span></span>

<span data-ttu-id="bed16-142">所有驗證函式和類型在 `github.com/Azure/go-autorest/autorest/azure/auth` 套件中都有提供。</span><span class="sxs-lookup"><span data-stu-id="bed16-142">All authentication functions and types are available in the `github.com/Azure/go-autorest/autorest/azure/auth` package.</span></span>

> [!NOTE]
> <span data-ttu-id="bed16-143">除非您有特殊需求，請避免使用用戶端式驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-143">Unless you have special requirements, avoid client-based authentication.</span></span> <span data-ttu-id="bed16-144">這種驗證方法會助長錯誤的作法。</span><span class="sxs-lookup"><span data-stu-id="bed16-144">This method of authentication encourages bad practices.</span></span> <span data-ttu-id="bed16-145">尤其是使用用戶端式驗證，會讓人更容易想用硬式編碼寫入認證。</span><span class="sxs-lookup"><span data-stu-id="bed16-145">In particular, using client-based authentication makes it tempting to hard-code credentials.</span></span> <span data-ttu-id="bed16-146">若未來的 SDK 版本驗證需求有所變更，撰寫的自訂驗證程式碼到時候也可能會失效。</span><span class="sxs-lookup"><span data-stu-id="bed16-146">Writing custom code for authentication may also break under future SDK releases if authentication requirements change.</span></span>

## <a name="use-environment-based-authentication"></a><span data-ttu-id="bed16-147">使用環境式驗證</span><span class="sxs-lookup"><span data-stu-id="bed16-147">Use environment-based authentication</span></span>

<span data-ttu-id="bed16-148">若您是在嚴格控制的環境下執行您的應用程式 (例如在容器中)，環境式驗證是合理的選擇。</span><span class="sxs-lookup"><span data-stu-id="bed16-148">If you're running your application in a tightly controlled environment such as in a container, environment-based authentication is a natural choice.</span></span> <span data-ttu-id="bed16-149">您會先設定 Shell 環境再執行應用程式，且 Go SDK 會在執行階段讀取這些環境變數，向 Azure 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-149">You configure the shell environment before running your application and the Go SDK reads these environment variables at runtime to authenticate with Azure.</span></span> 

<span data-ttu-id="bed16-150">環境式驗證可支援除了裝置權杖以外的所有驗證方法，並以下列順序依次進行評估：用戶端認證、憑證、使用者名稱/密碼，最後是受控服務識別 (MSI)。</span><span class="sxs-lookup"><span data-stu-id="bed16-150">Environment-based authentication has support for all authentication methods except device tokens, evaluated in the following order: Client credentials, certificates, username/password, and Managed Service Identity (MSI).</span></span> <span data-ttu-id="bed16-151">若未設定必要的環境變數或 SDK 遭到驗證服務拒絕，則會嘗試下一種驗證類型。</span><span class="sxs-lookup"><span data-stu-id="bed16-151">If a required environment variable is unset or the SDK gets a refusal from the authentication service, the next authentication type is tried.</span></span> <span data-ttu-id="bed16-152">若 SDK 無法在環境中進行驗證，便會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="bed16-152">If the SDK cannot authenticate from the environment, it returns an error.</span></span>

<span data-ttu-id="bed16-153">下表詳細說明針對環境式驗證所支援的每種驗證類型，需要設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="bed16-153">The following table details the environment variables that need to be set for each authentication type supported by environment-based authentication.</span></span>

| <span data-ttu-id="bed16-154">驗證類型</span><span class="sxs-lookup"><span data-stu-id="bed16-154">Authentication type</span></span> | <span data-ttu-id="bed16-155">環境變數</span><span class="sxs-lookup"><span data-stu-id="bed16-155">Environment variable</span></span> | <span data-ttu-id="bed16-156">說明</span><span class="sxs-lookup"><span data-stu-id="bed16-156">Description</span></span> |
| ------------------- | -------------------- | ----------- |
| <span data-ttu-id="bed16-157">__用戶端認證__</span><span class="sxs-lookup"><span data-stu-id="bed16-157">__Client credentials__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="bed16-158">服務主體所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="bed16-158">The ID for the Active Directory tenant that the service principal belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="bed16-159">服務主體的名稱或 ID。</span><span class="sxs-lookup"><span data-stu-id="bed16-159">The name or ID of the service principal.</span></span> |
| | `AZURE_CLIENT_SECRET` | <span data-ttu-id="bed16-160">與服務主體關聯的密碼。</span><span class="sxs-lookup"><span data-stu-id="bed16-160">The secret associated with the service principal.</span></span> |
| <span data-ttu-id="bed16-161">__憑證__</span><span class="sxs-lookup"><span data-stu-id="bed16-161">__Certificate__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="bed16-162">憑證註冊的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="bed16-162">The ID for the Active Directory tenant that the certificate is registered with.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="bed16-163">與憑證相關聯的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="bed16-163">The application client ID associated with the certificate.</span></span> |
| | `AZURE_CERTIFICATE_PATH` | <span data-ttu-id="bed16-164">用戶端憑證檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="bed16-164">The path to the client certificate file.</span></span> |
| | `AZURE_CERTIFICATE_PASSWORD` | <span data-ttu-id="bed16-165">用戶端憑證的密碼。</span><span class="sxs-lookup"><span data-stu-id="bed16-165">The password for the client certificate.</span></span> |
| <span data-ttu-id="bed16-166">__使用者名稱/密碼__</span><span class="sxs-lookup"><span data-stu-id="bed16-166">__Username/Password__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="bed16-167">使用者所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="bed16-167">The ID for the Active Directory tenant that the user belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="bed16-168">應用程式用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="bed16-168">The application client ID.</span></span> |
| | `AZURE_USERNAME` | <span data-ttu-id="bed16-169">用以登入的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="bed16-169">The username to log in with.</span></span> |
| | `AZURE_PASSWORD` | <span data-ttu-id="bed16-170">用以登入的密碼。</span><span class="sxs-lookup"><span data-stu-id="bed16-170">The password to log in with.</span></span> |
| <span data-ttu-id="bed16-171">__MSI__</span><span class="sxs-lookup"><span data-stu-id="bed16-171">__MSI__</span></span> | | <span data-ttu-id="bed16-172">MSI 不需要設定任何認證，</span><span class="sxs-lookup"><span data-stu-id="bed16-172">MSI does not require any credentials to be set.</span></span> <span data-ttu-id="bed16-173">但應用程式必須要在設定為使用 MSI 的 Azure 資源上執行。</span><span class="sxs-lookup"><span data-stu-id="bed16-173">The application must be running on an Azure resource configured to use MSI.</span></span> <span data-ttu-id="bed16-174">欲知詳情，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="bed16-174">For details, see [Managed Service Identity (MSI) for Azure resources].</span></span> |

<span data-ttu-id="bed16-175">若需要連線到預設 Azure 公用雲端以外的雲端或管理端點，您也可以設定下列環境變數。</span><span class="sxs-lookup"><span data-stu-id="bed16-175">If you need to connect to a cloud or management endpoint other than the default Azure public cloud, you can also set the following environment variables.</span></span> <span data-ttu-id="bed16-176">最常需要設定這些環境變數的幾個原因是您使用了 Azure Stack、位於不同地理區域的雲端，或 Azure 傳統部署模型。</span><span class="sxs-lookup"><span data-stu-id="bed16-176">The most common reasons to set them are if you use Azure Stack, a cloud in a different geographic region, or the Azure Classic deployment model.</span></span>

| <span data-ttu-id="bed16-177">環境變數</span><span class="sxs-lookup"><span data-stu-id="bed16-177">Environment variable</span></span> | <span data-ttu-id="bed16-178">說明</span><span class="sxs-lookup"><span data-stu-id="bed16-178">Description</span></span>  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | <span data-ttu-id="bed16-179">要連線到的雲端環境名稱。</span><span class="sxs-lookup"><span data-stu-id="bed16-179">The name of the cloud environment to connect to.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="bed16-180">連線時要使用的 Active Directory 資源 ID。</span><span class="sxs-lookup"><span data-stu-id="bed16-180">The Active Directory resource ID to use when connecting.</span></span> <span data-ttu-id="bed16-181">這應該是指向管理端點的 URI。</span><span class="sxs-lookup"><span data-stu-id="bed16-181">This should be a URI pointing to your management endpoint.</span></span> |

<span data-ttu-id="bed16-182">使用環境式驗證時，呼叫 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函式可取得授權者物件。</span><span class="sxs-lookup"><span data-stu-id="bed16-182">When using environment-based authentication, call the [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) function to get your authorizer object.</span></span> <span data-ttu-id="bed16-183">然後在用戶端的 `Authorizer` 屬性上設定此物件，允許用戶端存取 Azure。</span><span class="sxs-lookup"><span data-stu-id="bed16-183">This object is then set on the `Authorizer` property of clients to allow them access to Azure.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

## <a name="use-file-based-authentication"></a><span data-ttu-id="bed16-184">使用檔案式驗證</span><span class="sxs-lookup"><span data-stu-id="bed16-184">Use file-based authentication</span></span>

<span data-ttu-id="bed16-185">檔案式驗證只適用於由 [Azure CLI 2.0](/cli/azure) 所產生、以本機檔案格式儲存的用戶端認證。</span><span class="sxs-lookup"><span data-stu-id="bed16-185">File-based authentication only works with client credentials when they are stored in a local file format generated by [the Azure CLI 2.0](/cli/azure).</span></span> <span data-ttu-id="bed16-186">以 `--sdk-auth` 參數建立新的服務主體時，您可以輕鬆地建立此檔案。</span><span class="sxs-lookup"><span data-stu-id="bed16-186">You can easily create this file when creating a new service principal with the `--sdk-auth` parameter.</span></span> <span data-ttu-id="bed16-187">若您計劃使用檔案式驗證，請務必在建立服務主體時，提供這個引數。</span><span class="sxs-lookup"><span data-stu-id="bed16-187">If you plan on using file-based authentication, make sure that this argument is provided when creating a service principal.</span></span> <span data-ttu-id="bed16-188">因為 CLI 會將輸出列印到 `stdout`，請將輸出重新導向到檔案。</span><span class="sxs-lookup"><span data-stu-id="bed16-188">Since the CLI prints output to `stdout`, redirect output to a file.</span></span>

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

<span data-ttu-id="bed16-189">請將 `AZURE_AUTH_LOCATION` 環境變數設定成授權檔案所在的位置。</span><span class="sxs-lookup"><span data-stu-id="bed16-189">Set the `AZURE_AUTH_LOCATION` environment variable to where the authorization file is located.</span></span> <span data-ttu-id="bed16-190">應用程式會讀取這個環境變數，並剖析其中的認證。</span><span class="sxs-lookup"><span data-stu-id="bed16-190">This environment variable is read by the application, and the credentials within it are parsed.</span></span> <span data-ttu-id="bed16-191">若您需要在執行階段取授權檔案，請使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函式來操縱程式的環境。</span><span class="sxs-lookup"><span data-stu-id="bed16-191">If you need to select the authorization file at runtime, manipulate the program's environment using the [os.Setenv](https://golang.org/pkg/os/#Setenv) function.</span></span>

<span data-ttu-id="bed16-192">若要載入驗證資訊，請呼叫 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函式。</span><span class="sxs-lookup"><span data-stu-id="bed16-192">To load the authentication information, call the [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) function.</span></span> <span data-ttu-id="bed16-193">與環境式授權不同，檔案式授權需要資源端點。</span><span class="sxs-lookup"><span data-stu-id="bed16-193">Unlike environment-based authorization, file-based authorization requires a resource endpoint.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

<span data-ttu-id="bed16-194">如需有關使用服務主體，以及管理其存取權限的詳細資訊，請參閱[使用 Azure CLI 2.0 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="bed16-194">For more on using service principals and managing their access permissions, see [Create a service principal with Azure CLI 2.0].</span></span>

## <a name="use-device-token-authentication"></a><span data-ttu-id="bed16-195">使用裝置權杖驗證</span><span class="sxs-lookup"><span data-stu-id="bed16-195">Use device token authentication</span></span>

<span data-ttu-id="bed16-196">若您想讓使用者以互動方式登入，提供這項功能最好的方式是透過裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-196">If you want users to log in interactively, the best way to offer that capability is through device token authentication.</span></span> <span data-ttu-id="bed16-197">此驗證流程會傳遞使用者權杖，並貼入 Microsoft 登入網站，然後在此使用 Azure Active Directory (AAD) 帳戶登入。</span><span class="sxs-lookup"><span data-stu-id="bed16-197">This authentication flow passes the user a token to paste into a Microsoft login site, where they then log in with an Azure Active Directory (AAD) account.</span></span> <span data-ttu-id="bed16-198">這種驗證方法不同於使用者名稱/密碼驗證，可支援已啟用多重要素驗證的帳戶。</span><span class="sxs-lookup"><span data-stu-id="bed16-198">This authentication method supports accounts that have multi-factor authentication enabled, unlike standard username/password authentication.</span></span>

<span data-ttu-id="bed16-199">若要使用裝置權杖驗證，請使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函式建立 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授權者。</span><span class="sxs-lookup"><span data-stu-id="bed16-199">To use device token authentication, create a [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) authorizer with the [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) function.</span></span> <span data-ttu-id="bed16-200">呼叫結果物件上的 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以開始驗證程序。</span><span class="sxs-lookup"><span data-stu-id="bed16-200">Call [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) on the resulting object to start the authentication process.</span></span> <span data-ttu-id="bed16-201">裝置流程驗證會封鎖程式執行，直到整個驗證流程完成為止。</span><span class="sxs-lookup"><span data-stu-id="bed16-201">Device flow authentication blocks program execution until the whole authentication flow is complete.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a><span data-ttu-id="bed16-202">使用驗證用戶端</span><span class="sxs-lookup"><span data-stu-id="bed16-202">Use an authentication client</span></span>

<span data-ttu-id="bed16-203">若您需要特定類型的驗證，並願意讓程式自行載入來自使用者的驗證資訊，可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 介面的用戶端。</span><span class="sxs-lookup"><span data-stu-id="bed16-203">If you require a specific type of authentication and are willing to have your program do the work to load authentication information from the user, you can use any client that conforms to the [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) interface.</span></span> <span data-ttu-id="bed16-204">若您需要互動式程式、使用特定的組態檔，或因故無法使用其他驗證方法時，請使用可實作這個介面的驗證類型。</span><span class="sxs-lookup"><span data-stu-id="bed16-204">Use a type that implements this interface when you want an interactive program, use specialized configuration files, or have a requirement that prevents you from using another authentication method.</span></span>

> [!WARNING]
> <span data-ttu-id="bed16-205">請勿用硬式編碼將 Azure 認證寫入應用程式中。</span><span class="sxs-lookup"><span data-stu-id="bed16-205">Never hard-code Azure credentials into an application.</span></span> <span data-ttu-id="bed16-206">將祕密放入應用程式二進位檔會使攻擊者易於擷取，無論該應用程式是否正在執行。</span><span class="sxs-lookup"><span data-stu-id="bed16-206">Putting secrets into an application binary makes it easier for an attacker to extract them, whether the application is running or not.</span></span> <span data-ttu-id="bed16-207">這會使獲得該認證授權的所有 Azure 資源暴露在風險下！</span><span class="sxs-lookup"><span data-stu-id="bed16-207">This puts all Azure resources the credentials are authorized for at risk!</span></span>

<span data-ttu-id="bed16-208">下表列出 SDK 中符合 `AuthorizerConfig` 介面的類型。</span><span class="sxs-lookup"><span data-stu-id="bed16-208">The following table lists the types in the SDK that conform to the `AuthorizerConfig` interface.</span></span>

| <span data-ttu-id="bed16-209">驗證類型</span><span class="sxs-lookup"><span data-stu-id="bed16-209">Authentication type</span></span> | <span data-ttu-id="bed16-210">授權者類型</span><span class="sxs-lookup"><span data-stu-id="bed16-210">Authorizer type</span></span> |
|---------------------|-----------------------|
| <span data-ttu-id="bed16-211">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="bed16-211">Certificate-based authentication</span></span> | <span data-ttu-id="bed16-212">[ClientCertificateConfig]</span><span class="sxs-lookup"><span data-stu-id="bed16-212">[ClientCertificateConfig]</span></span> |
| <span data-ttu-id="bed16-213">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="bed16-213">Client credentials</span></span> | <span data-ttu-id="bed16-214">[ClientCredentialsConfig]</span><span class="sxs-lookup"><span data-stu-id="bed16-214">[ClientCredentialsConfig]</span></span> |
| <span data-ttu-id="bed16-215">受控服務識別 (MSI)</span><span class="sxs-lookup"><span data-stu-id="bed16-215">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="bed16-216">[MSIConfig]</span><span class="sxs-lookup"><span data-stu-id="bed16-216">[MSIConfig]</span></span> |
| <span data-ttu-id="bed16-217">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="bed16-217">Username/password</span></span> | <span data-ttu-id="bed16-218">[UsernamePasswordConfig]</span><span class="sxs-lookup"><span data-stu-id="bed16-218">[UsernamePasswordConfig]</span></span> |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

<span data-ttu-id="bed16-223">使用與其相關聯的 `New` 函式建立驗證器，然後再呼叫結果物件上的 `Authorize` 來執行驗證。</span><span class="sxs-lookup"><span data-stu-id="bed16-223">Create an authenticator with its associated `New` function, and then call `Authorize` on the resulting object to perform authentication.</span></span> <span data-ttu-id="bed16-224">例如，使用憑證式驗證：</span><span class="sxs-lookup"><span data-stu-id="bed16-224">For example, to use certificate-based authentication:</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
