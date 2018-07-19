---
title: 使用 Azure SDK for Go 進行驗證
description: 了解 Azure SDK for Go 中可用的驗證方法及其使用方式。
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 04/03/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.service: active-directory
ms.component: authentication
ms.openlocfilehash: f5e76fc745512a3a52172f560c3a24f510e96feb
ms.sourcegitcommit: d1790b317a8fcb4d672c654dac2a925a976589d4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039534"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a><span data-ttu-id="8e2cd-103">Azure SDK for Go 中可用的驗證方法</span><span class="sxs-lookup"><span data-stu-id="8e2cd-103">Authentication methods in the Azure SDK for Go</span></span>

<span data-ttu-id="8e2cd-104">Azure SDK for Go 提供各種不同的驗證類型和方法，供您的應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-104">The Azure SDK for Go offers a variety of authentication types and methods that your application can use.</span></span> <span data-ttu-id="8e2cd-105">支援的方法範圍包括從環境變數中提取資訊，到網路式互動式驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-105">Supported authentication methods range from pulling information from environment variables to interactive web-based authentication.</span></span> <span data-ttu-id="8e2cd-106">本文會向您介紹 SDK 中可用的驗證類型，以及使用驗證類型的方法。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-106">This article introduces you to the available types of authentication in the SDK, and the methods for using them.</span></span> <span data-ttu-id="8e2cd-107">您也會了解在選取最適合應用程式的驗證類型時，怎樣才是最佳作法。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-107">You'll also learn best practices for selecting which authentication type is right for your application.</span></span>

## <a name="available-authentication-types-and-methods"></a><span data-ttu-id="8e2cd-108">可用的驗證類型和方法</span><span class="sxs-lookup"><span data-stu-id="8e2cd-108">Available authentication types and methods</span></span>

<span data-ttu-id="8e2cd-109">Azure SDK for Go 提供數種不同類型的驗證，各自使用不同的認證集。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-109">The Azure SDK for Go offers several different types of authentication, using different credentials sets.</span></span> <span data-ttu-id="8e2cd-110">每種驗證類型都可以透過不同的驗證方法加以使用；而這些驗證方法就是 SDK 採用認證作為輸入值的方式。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-110">Each of these authentication types are available through different authentication methods, which are how the SDK takes these credentials as input.</span></span> <span data-ttu-id="8e2cd-111">下表描述可用的驗證類型，以及建議您的應用程式使用該驗證類型的情況。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-111">The following table describes the available types of authentication and situations in which they're recommended for use by your application.</span></span>

| <span data-ttu-id="8e2cd-112">驗證類型</span><span class="sxs-lookup"><span data-stu-id="8e2cd-112">Authentication type</span></span> | <span data-ttu-id="8e2cd-113">建議使用的時機...</span><span class="sxs-lookup"><span data-stu-id="8e2cd-113">Recommended when...</span></span> |
|---------------------|---------------------|
| <span data-ttu-id="8e2cd-114">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-114">Certificate-based authentication</span></span> | <span data-ttu-id="8e2cd-115">您所具有的 X509 憑證，已針對 Azure Active Directory (AAD) 使用者或服務主體進行設定。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-115">You have an X509 certificate that was configured for an Azure Active Directory (AAD) user or service principal.</span></span> <span data-ttu-id="8e2cd-116">若要深入了解，請參閱[開始在 Azure Active Directory 中使用憑證式驗證]。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-116">To learn more, see [Get started with certificate-based authentication in Azure Active Directory].</span></span> |
| <span data-ttu-id="8e2cd-117">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-117">Client credentials</span></span> | <span data-ttu-id="8e2cd-118">您所具有的服務主體，已針對此應用程式或其所屬應用程式類別進行設定。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-118">You have a configured service principal that is set up for this application or a class of applications it belongs to.</span></span> <span data-ttu-id="8e2cd-119">若要深入了解，請參閱[使用 Azure CLI 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-119">To learn more, see [Create a service principal with Azure CLI].</span></span> |
| <span data-ttu-id="8e2cd-120">受控服務識別 (MSI)</span><span class="sxs-lookup"><span data-stu-id="8e2cd-120">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="8e2cd-121">您的應用程式正在 Azure 資源上執行，該資源已使用受控服務識別 (MSI) 進行設定。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-121">Your application is running on an Azure resource that has been configured with Managed Service Identity (MSI).</span></span> <span data-ttu-id="8e2cd-122">若要深入了解，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-122">To learn more, see [Managed Service Identity (MSI) for Azure resources].</span></span> |
| <span data-ttu-id="8e2cd-123">裝置權杖</span><span class="sxs-lookup"><span data-stu-id="8e2cd-123">Device token</span></span> | <span data-ttu-id="8e2cd-124">您的應用程式__只__適用於以互動方式使用，且可能會有來自多個 AAD 租用戶的多位使用者。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-124">Your application is meant to be used interactively __only__ and will have a variety of users, potentially from multiple AAD tenants.</span></span> <span data-ttu-id="8e2cd-125">使用者可以存取網頁瀏覽器來登入。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-125">Users have access to a web browser to sign in.</span></span> <span data-ttu-id="8e2cd-126">若要深入了解，請參閱[使用裝置權杖驗證](#use-device-token-authentication)。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-126">For more information, see [Use device token authentication](#use-device-token-authentication).</span></span>|
| <span data-ttu-id="8e2cd-127">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="8e2cd-127">Username/password</span></span> | <span data-ttu-id="8e2cd-128">您所具有的應用程式無法使用任何其他驗證方法。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-128">You have an interactive application that cannot use any other authentication method.</span></span> <span data-ttu-id="8e2cd-129">您的使用者未啟用 AAD 登入的多重要素驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-129">Your users do not have multi-factor authentication enabled for their AAD sign in.</span></span> |

> [!IMPORTANT]
> <span data-ttu-id="8e2cd-130">若您使用的是用戶端認證以外的驗證類型，則必須在 Azure Active Directory 中註冊您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-130">If you use an authentication type other than client credentials, your application must be registered in Azure Active Directory.</span></span> <span data-ttu-id="8e2cd-131">若要了解註冊方式，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-131">To learn how, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).</span></span>
>
> [!NOTE]
> <span data-ttu-id="8e2cd-132">除非您有特殊需求，請避免使用者名稱/密碼驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-132">Unless you have special requirements, avoid username/password authentication.</span></span> <span data-ttu-id="8e2cd-133">在使用者式登入適用的情況下，通常可改用裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-133">In situations where user-based sign in is appropriate, device token authentication can usually be used instead.</span></span>

[開始在 Azure Active Directory 中使用憑證式驗證]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Get started with certificate-based authentication in Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 建立服務主體]: /cli/azure/create-an-azure-service-principal-azure-cli
[Create a service principal with Azure CLI]: /cli/azure/create-an-azure-service-principal-azure-cli
[適用於 Azure 資源的受控服務識別 (MSI)]: /azure/active-directory/managed-service-identity/overview
[Managed Service Identity (MSI) for Azure resources]: /azure/active-directory/managed-service-identity/overview

<span data-ttu-id="8e2cd-137">這些驗證類型可以透過不同的驗證方法加以使用。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-137">These authentication types are available through different methods.</span></span> <span data-ttu-id="8e2cd-138">[環境式驗證](#use-environment-based-authentication)會直接從程式的環境讀取認證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-138">[_Environment-based authentication_](#use-environment-based-authentication) reads credentials directly from the program's environment.</span></span> <span data-ttu-id="8e2cd-139">[檔案式驗證](#use-file-based-authentication)會載入包含服務主體認證的檔案。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-139">[_File-based authentication_](#use-file-based-authentication) loads a file containing service principal credentials.</span></span> <span data-ttu-id="8e2cd-140">[用戶端式驗證](#use-an-authentication-client)會使用 Go 程式碼中的物件，使得您要負責在程式執行期間提供認證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-140">[_Client-based authentication_](#use-an-authentication-client) uses an object in Go code and makes you responsible for providing the credentials during program execution.</span></span> <span data-ttu-id="8e2cd-141">最後，[_裝置權杖驗證_](#use-device-token-authentication)需要使用者透過網頁瀏覽器，用權杖以互動方式登入，而且不能搭配環境式或檔案式驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-141">Finally, [_Device token authentication_](#use-device-token-authentication) requires users to sign in interactively through a web browser with a token, and cannot be used with environment- or file-based authentication.</span></span>

<span data-ttu-id="8e2cd-142">所有驗證函式和類型在 `github.com/Azure/go-autorest/autorest/azure/auth` 套件中都有提供。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-142">All authentication functions and types are available in the `github.com/Azure/go-autorest/autorest/azure/auth` package.</span></span>

> [!NOTE]
> <span data-ttu-id="8e2cd-143">除非您有特殊需求，請避免使用用戶端式驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-143">Unless you have special requirements, avoid client-based authentication.</span></span> <span data-ttu-id="8e2cd-144">這種驗證方法會助長錯誤的作法。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-144">This method of authentication encourages bad practices.</span></span> <span data-ttu-id="8e2cd-145">尤其是使用用戶端式驗證，會讓人更容易想用硬式編碼寫入認證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-145">In particular, using client-based authentication makes it tempting to hard-code credentials.</span></span> <span data-ttu-id="8e2cd-146">若未來的 SDK 版本驗證需求有所變更，撰寫的自訂驗證程式碼到時候也可能會失效。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-146">Writing custom code for authentication may also break under future SDK releases if authentication requirements change.</span></span>

## <a name="use-environment-based-authentication"></a><span data-ttu-id="8e2cd-147">使用環境式驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-147">Use environment-based authentication</span></span>

<span data-ttu-id="8e2cd-148">若您是在嚴格控制的環境下執行您的應用程式 (例如在容器中)，環境式驗證是合理的選擇。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-148">If you're running your application in a tightly controlled environment such as in a container, environment-based authentication is a natural choice.</span></span> <span data-ttu-id="8e2cd-149">您會先設定 Shell 環境再執行應用程式，且 Go SDK 會在執行階段讀取這些環境變數，向 Azure 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-149">You configure the shell environment before running your application and the Go SDK reads these environment variables at runtime to authenticate with Azure.</span></span>

<span data-ttu-id="8e2cd-150">環境式驗證可支援除了裝置權杖以外的所有驗證方法，並以下列順序依次進行評估：用戶端認證、憑證、使用者名稱/密碼，最後是受控服務識別 (MSI)。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-150">Environment-based authentication has support for all authentication methods except device tokens, evaluated in the following order: Client credentials, certificates, username/password, and Managed Service Identity (MSI).</span></span> <span data-ttu-id="8e2cd-151">若未設定必要的環境變數或 SDK 遭到驗證服務拒絕，則會嘗試下一種驗證類型。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-151">If a required environment variable is unset or the SDK gets a refusal from the authentication service, the next authentication type is tried.</span></span> <span data-ttu-id="8e2cd-152">若 SDK 無法在環境中進行驗證，便會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-152">If the SDK cannot authenticate from the environment, it returns an error.</span></span>

<span data-ttu-id="8e2cd-153">下表詳細說明針對環境式驗證所支援的每種驗證類型，需要設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-153">The following table details the environment variables that need to be set for each authentication type supported by environment-based authentication.</span></span>

| <span data-ttu-id="8e2cd-154">驗證類型</span><span class="sxs-lookup"><span data-stu-id="8e2cd-154">Authentication type</span></span> | <span data-ttu-id="8e2cd-155">環境變數</span><span class="sxs-lookup"><span data-stu-id="8e2cd-155">Environment variable</span></span> | <span data-ttu-id="8e2cd-156">說明</span><span class="sxs-lookup"><span data-stu-id="8e2cd-156">Description</span></span> |
| ------------------- | -------------------- | ----------- |
| <span data-ttu-id="8e2cd-157">__用戶端認證__</span><span class="sxs-lookup"><span data-stu-id="8e2cd-157">__Client credentials__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="8e2cd-158">服務主體所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-158">The ID for the Active Directory tenant that the service principal belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="8e2cd-159">服務主體的名稱或 ID。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-159">The name or ID of the service principal.</span></span> |
| | `AZURE_CLIENT_SECRET` | <span data-ttu-id="8e2cd-160">與服務主體關聯的密碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-160">The secret associated with the service principal.</span></span> |
| <span data-ttu-id="8e2cd-161">__憑證__</span><span class="sxs-lookup"><span data-stu-id="8e2cd-161">__Certificate__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="8e2cd-162">憑證註冊的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-162">The ID for the Active Directory tenant that the certificate is registered with.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="8e2cd-163">與憑證相關聯的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-163">The application client ID associated with the certificate.</span></span> |
| | `AZURE_CERTIFICATE_PATH` | <span data-ttu-id="8e2cd-164">用戶端憑證檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-164">The path to the client certificate file.</span></span> |
| | `AZURE_CERTIFICATE_PASSWORD` | <span data-ttu-id="8e2cd-165">用戶端憑證的密碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-165">The password for the client certificate.</span></span> |
| <span data-ttu-id="8e2cd-166">__使用者名稱/密碼__</span><span class="sxs-lookup"><span data-stu-id="8e2cd-166">__Username/Password__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="8e2cd-167">使用者所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-167">The ID for the Active Directory tenant that the user belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="8e2cd-168">應用程式用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-168">The application client ID.</span></span> |
| | `AZURE_USERNAME` | <span data-ttu-id="8e2cd-169">用來登入的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-169">The username to sign in with.</span></span> |
| | `AZURE_PASSWORD` | <span data-ttu-id="8e2cd-170">用來登入的密碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-170">The password to sign in with.</span></span> |
| <span data-ttu-id="8e2cd-171">__MSI__</span><span class="sxs-lookup"><span data-stu-id="8e2cd-171">__MSI__</span></span> | | <span data-ttu-id="8e2cd-172">MSI 不需要設定任何認證，</span><span class="sxs-lookup"><span data-stu-id="8e2cd-172">MSI does not require any credentials to be set.</span></span> <span data-ttu-id="8e2cd-173">但應用程式必須要在設定為使用 MSI 的 Azure 資源上執行。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-173">The application must be running on an Azure resource configured to use MSI.</span></span> <span data-ttu-id="8e2cd-174">欲知詳情，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-174">For details, see [Managed Service Identity (MSI) for Azure resources].</span></span> |

<span data-ttu-id="8e2cd-175">若需要連線到預設 Azure 公用雲端以外的雲端或管理端點，您也可以設定下列環境變數。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-175">If you need to connect to a cloud or management endpoint other than the default Azure public cloud, you can also set the following environment variables.</span></span> <span data-ttu-id="8e2cd-176">最常需要設定這些環境變數的幾個原因是您使用了 Azure Stack、位於不同地理區域的雲端，或 Azure 傳統部署模型。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-176">The most common reasons to set them are if you use Azure Stack, a cloud in a different geographic region, or the Azure Classic deployment model.</span></span>

| <span data-ttu-id="8e2cd-177">環境變數</span><span class="sxs-lookup"><span data-stu-id="8e2cd-177">Environment variable</span></span> | <span data-ttu-id="8e2cd-178">說明</span><span class="sxs-lookup"><span data-stu-id="8e2cd-178">Description</span></span>  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | <span data-ttu-id="8e2cd-179">要連線到的雲端環境名稱。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-179">The name of the cloud environment to connect to.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="8e2cd-180">連線時要使用的 Active Directory 資源 ID。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-180">The Active Directory resource ID to use when connecting.</span></span> <span data-ttu-id="8e2cd-181">這應該是指向管理端點的 URI。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-181">This should be a URI pointing to your management endpoint.</span></span> |

<span data-ttu-id="8e2cd-182">使用環境式驗證時，呼叫 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函式可取得授權者物件。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-182">When using environment-based authentication, call the [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) function to get your authorizer object.</span></span> <span data-ttu-id="8e2cd-183">然後在用戶端的 `Authorizer` 屬性上設定此物件，允許用戶端存取 Azure。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-183">This object is then set on the `Authorizer` property of clients to allow them access to Azure.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a><span data-ttu-id="8e2cd-184">在 Azure Stack 上進行驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-184">Authentication on Azure Stack</span></span>

<span data-ttu-id="8e2cd-185">若要在 Azure Stack 上進行驗證，您需要設定以下變數：</span><span class="sxs-lookup"><span data-stu-id="8e2cd-185">To authenticate on Azure Stack, you need to set the following variables:</span></span>

| <span data-ttu-id="8e2cd-186">環境變數</span><span class="sxs-lookup"><span data-stu-id="8e2cd-186">Environment variable</span></span> | <span data-ttu-id="8e2cd-187">說明</span><span class="sxs-lookup"><span data-stu-id="8e2cd-187">Description</span></span>  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | <span data-ttu-id="8e2cd-188">Azure Active Directory 端點。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-188">The Active Directory endpoint.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="8e2cd-189">Active Directory 資源識別碼。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-189">The Active Directory resource ID.</span></span> |

<span data-ttu-id="8e2cd-190">可以從 Azure Stack 中繼資料資訊中擷取這些變數。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-190">These variables can be retrieved from Azure Stack metadata information.</span></span> <span data-ttu-id="8e2cd-191">若要擷取中繼資料，請在 Azure Stack 環境中開啟網路瀏覽器，並使用下列 URL：`(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span><span class="sxs-lookup"><span data-stu-id="8e2cd-191">To retrieve the metadata, open a web browser in your Azure Stack environment and use the url: `(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span></span>

<span data-ttu-id="8e2cd-192">`ResourceManagerURL` 會依 Azure Stack 部署的區域名稱、機器名稱和外部完整網域名稱 (FQDN) 而有所不同：</span><span class="sxs-lookup"><span data-stu-id="8e2cd-192">The `ResourceManagerURL` varies based on the region name, machine name and external fully qualified domain name (FQDN) of your Azure Stack deployment:</span></span>

| <span data-ttu-id="8e2cd-193">環境</span><span class="sxs-lookup"><span data-stu-id="8e2cd-193">Environment</span></span> | <span data-ttu-id="8e2cd-194">ResourceManagerURL</span><span class="sxs-lookup"><span data-stu-id="8e2cd-194">ResourceManagerURL</span></span> |
|----------------------|--------------|
| <span data-ttu-id="8e2cd-195">開發套件</span><span class="sxs-lookup"><span data-stu-id="8e2cd-195">Development Kit</span></span> | `https://management.local.azurestack.external/` |
| <span data-ttu-id="8e2cd-196">整合系統</span><span class="sxs-lookup"><span data-stu-id="8e2cd-196">Integrated Systems</span></span> | `https://management.(region).ext-(machine-name).(FQDN)` |

<span data-ttu-id="8e2cd-197">如需如何在 Azure Stack 上使用 Azure SDK for Go 的詳細資訊，請參閱[在 Azure Stack 中搭配使用 Go 與 API 版本設定檔](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span><span class="sxs-lookup"><span data-stu-id="8e2cd-197">For more details on how to use Azure SDK for Go on Azure Stack see [Use API version profiles with Go in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span></span>

## <a name="use-file-based-authentication"></a><span data-ttu-id="8e2cd-198">使用檔案式驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-198">Use file-based authentication</span></span>

<span data-ttu-id="8e2cd-199">檔案式驗證只適用於由 [Azure CLI](/cli/azure) 所產生、以本機檔案格式儲存的用戶端認證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-199">File-based authentication only works with client credentials when they are stored in a local file format generated by [the Azure CLI](/cli/azure).</span></span> <span data-ttu-id="8e2cd-200">以 `--sdk-auth` 參數建立新的服務主體時，您可以輕鬆地建立此檔案。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-200">You can easily create this file when creating a new service principal with the `--sdk-auth` parameter.</span></span> <span data-ttu-id="8e2cd-201">若您計劃使用檔案式驗證，請務必在建立服務主體時，提供這個引數。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-201">If you plan on using file-based authentication, make sure that this argument is provided when creating a service principal.</span></span> <span data-ttu-id="8e2cd-202">因為 CLI 會將輸出列印到 `stdout`，請將輸出重新導向到檔案。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-202">Since the CLI prints output to `stdout`, redirect output to a file.</span></span>

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

<span data-ttu-id="8e2cd-203">請將 `AZURE_AUTH_LOCATION` 環境變數設定成授權檔案所在的位置。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-203">Set the `AZURE_AUTH_LOCATION` environment variable to where the authorization file is located.</span></span> <span data-ttu-id="8e2cd-204">應用程式會讀取這個環境變數，並剖析其中的認證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-204">This environment variable is read by the application, and the credentials within it are parsed.</span></span> <span data-ttu-id="8e2cd-205">若您需要在執行階段取授權檔案，請使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函式來操縱程式的環境。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-205">If you need to select the authorization file at runtime, manipulate the program's environment using the [os.Setenv](https://golang.org/pkg/os/#Setenv) function.</span></span>

<span data-ttu-id="8e2cd-206">若要載入驗證資訊，請呼叫 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函式。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-206">To load the authentication information, call the [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) function.</span></span> <span data-ttu-id="8e2cd-207">與環境式授權不同，檔案式授權需要資源端點。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-207">Unlike environment-based authorization, file-based authorization requires a resource endpoint.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

<span data-ttu-id="8e2cd-208">如需有關使用服務主體，以及管理其存取權限的詳細資訊，請參閱[使用 Azure CLI 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-208">For more on using service principals and managing their access permissions, see [Create a service principal with Azure CLI].</span></span>

## <a name="use-device-token-authentication"></a><span data-ttu-id="8e2cd-209">使用裝置權杖驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-209">Use device token authentication</span></span>

<span data-ttu-id="8e2cd-210">若您想讓使用者以互動方式登入，提供這項功能最好的方式是透過裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-210">If you want users to sign in interactively, the best way to offer that capability is through device token authentication.</span></span> <span data-ttu-id="8e2cd-211">此驗證流程會將權杖傳遞給使用者，讓使用者貼入 Microsoft 登入網站，然後在此使用 Azure Active Directory (AAD) 帳戶進行驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-211">This authentication flow passes the user a token to paste into a Microsoft sign-in site, where they then authenticate with an Azure Active Directory (AAD) account.</span></span> <span data-ttu-id="8e2cd-212">這種驗證方法不同於使用者名稱/密碼驗證，可支援已啟用多重要素驗證的帳戶。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-212">This authentication method supports accounts that have multi-factor authentication enabled, unlike standard username/password authentication.</span></span>

<span data-ttu-id="8e2cd-213">若要使用裝置權杖驗證，請使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函式建立 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授權者。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-213">To use device token authentication, create a [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) authorizer with the [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) function.</span></span> <span data-ttu-id="8e2cd-214">呼叫結果物件上的 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以開始驗證程序。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-214">Call [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) on the resulting object to start the authentication process.</span></span> <span data-ttu-id="8e2cd-215">裝置流程驗證會封鎖程式執行，直到整個驗證流程完成為止。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-215">Device flow authentication blocks program execution until the whole authentication flow is complete.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a><span data-ttu-id="8e2cd-216">使用驗證用戶端</span><span class="sxs-lookup"><span data-stu-id="8e2cd-216">Use an authentication client</span></span>

<span data-ttu-id="8e2cd-217">若您需要特定類型的驗證，並願意讓程式自行載入來自使用者的驗證資訊，可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 介面的用戶端。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-217">If you require a specific type of authentication and are willing to have your program do the work to load authentication information from the user, you can use any client that conforms to the [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) interface.</span></span> <span data-ttu-id="8e2cd-218">若您需要互動式程式、使用特定的組態檔，或因故無法使用其他驗證方法時，請使用可實作這個介面的驗證類型。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-218">Use a type that implements this interface when you want an interactive program, use specialized configuration files, or have a requirement that prevents you from using another authentication method.</span></span>

> [!WARNING]
> <span data-ttu-id="8e2cd-219">請勿用硬式編碼將 Azure 認證寫入應用程式中。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-219">Never hard-code Azure credentials into an application.</span></span> <span data-ttu-id="8e2cd-220">將祕密放入應用程式二進位檔會使攻擊者易於擷取，無論該應用程式是否正在執行。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-220">Putting secrets into an application binary makes it easier for an attacker to extract them, whether the application is running or not.</span></span> <span data-ttu-id="8e2cd-221">這會使獲得該認證授權的所有 Azure 資源暴露在風險下！</span><span class="sxs-lookup"><span data-stu-id="8e2cd-221">This puts all Azure resources the credentials are authorized for at risk!</span></span>

<span data-ttu-id="8e2cd-222">下表列出 SDK 中符合 `AuthorizerConfig` 介面的類型。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-222">The following table lists the types in the SDK that conform to the `AuthorizerConfig` interface.</span></span>

| <span data-ttu-id="8e2cd-223">驗證類型</span><span class="sxs-lookup"><span data-stu-id="8e2cd-223">Authentication type</span></span> | <span data-ttu-id="8e2cd-224">授權者類型</span><span class="sxs-lookup"><span data-stu-id="8e2cd-224">Authorizer type</span></span> |
|---------------------|-----------------------|
| <span data-ttu-id="8e2cd-225">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-225">Certificate-based authentication</span></span> | <span data-ttu-id="8e2cd-226">[ClientCertificateConfig]</span><span class="sxs-lookup"><span data-stu-id="8e2cd-226">[ClientCertificateConfig]</span></span> |
| <span data-ttu-id="8e2cd-227">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="8e2cd-227">Client credentials</span></span> | <span data-ttu-id="8e2cd-228">[ClientCredentialsConfig]</span><span class="sxs-lookup"><span data-stu-id="8e2cd-228">[ClientCredentialsConfig]</span></span> |
| <span data-ttu-id="8e2cd-229">受控服務識別 (MSI)</span><span class="sxs-lookup"><span data-stu-id="8e2cd-229">Managed Service Identity (MSI)</span></span> | <span data-ttu-id="8e2cd-230">[MSIConfig]</span><span class="sxs-lookup"><span data-stu-id="8e2cd-230">[MSIConfig]</span></span> |
| <span data-ttu-id="8e2cd-231">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="8e2cd-231">Username/password</span></span> | <span data-ttu-id="8e2cd-232">[UsernamePasswordConfig]</span><span class="sxs-lookup"><span data-stu-id="8e2cd-232">[UsernamePasswordConfig]</span></span> |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

<span data-ttu-id="8e2cd-237">使用與其相關聯的 `New` 函式建立驗證器，然後再呼叫結果物件上的 `Authorize` 來執行驗證。</span><span class="sxs-lookup"><span data-stu-id="8e2cd-237">Create an authenticator with its associated `New` function, and then call `Authorize` on the resulting object to perform authentication.</span></span> <span data-ttu-id="8e2cd-238">例如，使用憑證式驗證：</span><span class="sxs-lookup"><span data-stu-id="8e2cd-238">For example, to use certificate-based authentication:</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
