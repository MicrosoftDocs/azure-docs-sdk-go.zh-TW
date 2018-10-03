---
title: 使用 Azure SDK for Go 進行驗證
description: 了解 Azure SDK for Go 中可用的驗證方法及其使用方式。
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.technology: azure-sdk-go
ms.devlang: go
ms.component: authentication
ms.openlocfilehash: f5c2c56e43828f0bedad0b5781dc71991ce1fd3e
ms.sourcegitcommit: 172f81dd6e4c6a275dc8031815aa87cdb488cbf0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2018
ms.locfileid: "47231670"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a><span data-ttu-id="9386e-103">Azure SDK for Go 中可用的驗證方法</span><span class="sxs-lookup"><span data-stu-id="9386e-103">Authentication methods in the Azure SDK for Go</span></span>

<span data-ttu-id="9386e-104">Azure SDK for Go 提供多種方式來對 Azure 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-104">The Azure SDK for Go offers multiple ways to authenticate with Azure.</span></span> <span data-ttu-id="9386e-105">這些驗證_類型_可以透過不同的驗證_方法_加以叫用。</span><span class="sxs-lookup"><span data-stu-id="9386e-105">These authentication _types_ are invoked through different authentication _methods_.</span></span> <span data-ttu-id="9386e-106">本文將說明可用類型和方法，以及如何選擇最適合您應用程式的項目。</span><span class="sxs-lookup"><span data-stu-id="9386e-106">This article covers the available types, methods, and how to choose which are best for your application.</span></span>

## <a name="available-authentication-types-and-methods"></a><span data-ttu-id="9386e-107">可用的驗證類型和方法</span><span class="sxs-lookup"><span data-stu-id="9386e-107">Available authentication types and methods</span></span>

<span data-ttu-id="9386e-108">Azure SDK for Go 提供數種不同類型的驗證，各自使用不同的認證集。</span><span class="sxs-lookup"><span data-stu-id="9386e-108">The Azure SDK for Go offers several different types of authentication, using different credentials sets.</span></span> <span data-ttu-id="9386e-109">每種驗證類型都可以透過不同的驗證方法加以使用；而這些驗證方法就是 SDK 採用認證作為輸入值的方式。</span><span class="sxs-lookup"><span data-stu-id="9386e-109">Each authentication type is available through different authentication methods, which are how the SDK takes these credentials as input.</span></span> <span data-ttu-id="9386e-110">下表描述可用的驗證類型，以及建議您的應用程式使用該驗證類型的情況。</span><span class="sxs-lookup"><span data-stu-id="9386e-110">The following table describes the available types of authentication and situations in which they're recommended for use by your application.</span></span>

| <span data-ttu-id="9386e-111">驗證類型</span><span class="sxs-lookup"><span data-stu-id="9386e-111">Authentication type</span></span> | <span data-ttu-id="9386e-112">建議使用的時機...</span><span class="sxs-lookup"><span data-stu-id="9386e-112">Recommended when...</span></span> |
|---------------------|---------------------|
| <span data-ttu-id="9386e-113">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-113">Certificate-based authentication</span></span> | <span data-ttu-id="9386e-114">您所具有的 X509 憑證，已針對 Azure Active Directory (AAD) 使用者或服務主體進行設定。</span><span class="sxs-lookup"><span data-stu-id="9386e-114">You have an X509 certificate that was configured for an Azure Active Directory (AAD) user or service principal.</span></span> <span data-ttu-id="9386e-115">若要深入了解，請參閱[開始在 Azure Active Directory 中使用憑證式驗證]。</span><span class="sxs-lookup"><span data-stu-id="9386e-115">To learn more, see [Get started with certificate-based authentication in Azure Active Directory].</span></span> |
| <span data-ttu-id="9386e-116">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="9386e-116">Client credentials</span></span> | <span data-ttu-id="9386e-117">您所具有的服務主體，已針對此應用程式或其所屬應用程式類別進行設定。</span><span class="sxs-lookup"><span data-stu-id="9386e-117">You have a configured service principal that is set up for this application or a class of applications it belongs to.</span></span> <span data-ttu-id="9386e-118">若要深入了解，請參閱[使用 Azure CLI 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="9386e-118">To learn more, see [Create a service principal with Azure CLI].</span></span> |
| <span data-ttu-id="9386e-119">適用於 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="9386e-119">Managed identities for Azure resources</span></span> | <span data-ttu-id="9386e-120">您的應用程式正在 Azure 資源上執行，該資源已使用受控識別進行設定。</span><span class="sxs-lookup"><span data-stu-id="9386e-120">Your application is running on an Azure resource that has been configured with a managed identity.</span></span> <span data-ttu-id="9386e-121">若要深入了解，請參閱[適用於 Azure 資源的受控識別]。</span><span class="sxs-lookup"><span data-stu-id="9386e-121">To learn more, see [Managed identities for Azure resources].</span></span> |
| <span data-ttu-id="9386e-122">裝置權杖</span><span class="sxs-lookup"><span data-stu-id="9386e-122">Device token</span></span> | <span data-ttu-id="9386e-123">您的應用程式__只__適用於以互動方式使用。</span><span class="sxs-lookup"><span data-stu-id="9386e-123">Your application is meant to be used interactively __only__.</span></span> <span data-ttu-id="9386e-124">使用者可能已啟用多重要素驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-124">Users may have multi-factor authentication enabled.</span></span> <span data-ttu-id="9386e-125">使用者可以存取網頁瀏覽器來登入。</span><span class="sxs-lookup"><span data-stu-id="9386e-125">Users have access to a web browser to sign in.</span></span> <span data-ttu-id="9386e-126">若要深入了解，請參閱[使用裝置權杖驗證](#use-device-token-authentication)。</span><span class="sxs-lookup"><span data-stu-id="9386e-126">For more information, see [Use device token authentication](#use-device-token-authentication).</span></span>|
| <span data-ttu-id="9386e-127">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="9386e-127">Username/password</span></span> | <span data-ttu-id="9386e-128">您擁有的互動式應用程式無法使用任何其他驗證方法。</span><span class="sxs-lookup"><span data-stu-id="9386e-128">You have an interactive application that can't use any other authentication method.</span></span> <span data-ttu-id="9386e-129">您的使用者未啟用 AAD 登入的多重要素驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-129">Your users don't have multi-factor authentication enabled for their AAD sign-in.</span></span> |

> [!IMPORTANT]
> <span data-ttu-id="9386e-130">若您使用的是用戶端認證以外的驗證類型，則必須在 Azure Active Directory 中註冊您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="9386e-130">If you use an authentication type other than client credentials, your application must be registered in Azure Active Directory.</span></span> <span data-ttu-id="9386e-131">若要了解註冊方式，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。</span><span class="sxs-lookup"><span data-stu-id="9386e-131">To learn how, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).</span></span>
>
> [!NOTE]
> <span data-ttu-id="9386e-132">除非您有特殊需求，請避免使用者名稱/密碼驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-132">Unless you have special requirements, avoid username/password authentication.</span></span> <span data-ttu-id="9386e-133">在使用者式登入適用的情況下，通常可改用裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-133">In situations where user-based sign in is appropriate, device token authentication can usually be used instead.</span></span>

[開始在 Azure Active Directory 中使用憑證式驗證]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Get started with certificate-based authentication in Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 建立服務主體]: /cli/azure/create-an-azure-service-principal-azure-cli
[Create a service principal with Azure CLI]: /cli/azure/create-an-azure-service-principal-azure-cli
[適用於 Azure 資源的受控識別]: /azure/active-directory/managed-identities-azure-resources/overview
[Managed identities for Azure resources]: /azure/active-directory/managed-identities-azure-resources/overview

<span data-ttu-id="9386e-137">這些驗證類型可以透過不同的驗證方法加以使用。</span><span class="sxs-lookup"><span data-stu-id="9386e-137">These authentication types are available through different methods.</span></span>

* <span data-ttu-id="9386e-138">[環境式驗證](#use-environment-based-authentication)會直接從程式的環境讀取認證。</span><span class="sxs-lookup"><span data-stu-id="9386e-138">[_Environment-based authentication_](#use-environment-based-authentication) reads credentials directly from the program's environment.</span></span>
* <span data-ttu-id="9386e-139">[檔案式驗證](#use-file-based-authentication)會載入包含服務主體認證的檔案。</span><span class="sxs-lookup"><span data-stu-id="9386e-139">[_File-based authentication_](#use-file-based-authentication) loads a file containing service principal credentials.</span></span>
* <span data-ttu-id="9386e-140">[_用戶端式驗證_](#use-an-authentication-client)會使用程式碼中的物件，讓您必須負責在程式執行期間提供認證。</span><span class="sxs-lookup"><span data-stu-id="9386e-140">[_Client-based authentication_](#use-an-authentication-client) uses an object in code and makes you responsible for providing the credentials during program execution.</span></span>
* <span data-ttu-id="9386e-141">[_裝置權杖驗證_](#use-device-token-authentication)會要求使用者使用權杖從 Web 瀏覽器中以互動方式登入。</span><span class="sxs-lookup"><span data-stu-id="9386e-141">[_Device token authentication_](#use-device-token-authentication) requires users to sign in interactively through a web browser with a token.</span></span>

<span data-ttu-id="9386e-142">所有驗證函式和類型在 `github.com/Azure/go-autorest/autorest/azure/auth` 套件中都有提供。</span><span class="sxs-lookup"><span data-stu-id="9386e-142">All authentication functions and types are available in the `github.com/Azure/go-autorest/autorest/azure/auth` package.</span></span>

> [!NOTE]
> <span data-ttu-id="9386e-143">除非您有特殊需求，請避免使用用戶端式驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-143">Unless you have special requirements, avoid client-based authentication.</span></span> <span data-ttu-id="9386e-144">這種驗證方法會助長錯誤的作法。</span><span class="sxs-lookup"><span data-stu-id="9386e-144">This method of authentication encourages bad practices.</span></span> <span data-ttu-id="9386e-145">尤其是使用用戶端式驗證，會讓人更容易想用硬式編碼寫入認證。</span><span class="sxs-lookup"><span data-stu-id="9386e-145">In particular, using client-based authentication makes it tempting to hard-code credentials.</span></span> <span data-ttu-id="9386e-146">若未來的 SDK 版本驗證需求有所變更，撰寫的自訂驗證程式碼到時候也可能會失效。</span><span class="sxs-lookup"><span data-stu-id="9386e-146">Writing custom code for authentication may also break under future SDK releases if authentication requirements change.</span></span>

## <a name="use-environment-based-authentication"></a><span data-ttu-id="9386e-147">使用環境式驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-147">Use environment-based authentication</span></span>

<span data-ttu-id="9386e-148">若您是在受控制的設定下執行應用程式，環境式驗證是合理的選擇。</span><span class="sxs-lookup"><span data-stu-id="9386e-148">If you're running your application in a controlled setting, environment-based authentication is a natural choice.</span></span> <span data-ttu-id="9386e-149">透過這種驗證方法，您可以先設定殼層環境，再執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="9386e-149">With this authentication method, you configure the shell environment before running your application.</span></span> <span data-ttu-id="9386e-150">在執行階段上，Go SDK 會讀取這些環境變數，以向 Azure 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-150">At runtime, the Go SDK reads these environment variables to authenticate with Azure.</span></span>

<span data-ttu-id="9386e-151">環境式驗證可支援除了裝置權杖以外的所有驗證方法，並以下列順序依次進行評估：</span><span class="sxs-lookup"><span data-stu-id="9386e-151">Environment-based authentication has support for all authentication methods except device tokens, evaluated in the following order:</span></span>

* <span data-ttu-id="9386e-152">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="9386e-152">Client credentials</span></span>
* <span data-ttu-id="9386e-153">X509 憑證</span><span class="sxs-lookup"><span data-stu-id="9386e-153">X509 certificates</span></span>
* <span data-ttu-id="9386e-154">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="9386e-154">Username/password</span></span>
* <span data-ttu-id="9386e-155">適用於 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="9386e-155">Managed identities for Azure resources</span></span>

<span data-ttu-id="9386e-156">如果驗證類型有未設定的值或遭到拒絕，SDK 會自動嘗試下一個驗證類型。</span><span class="sxs-lookup"><span data-stu-id="9386e-156">If an authentication type has unset values or is refused, the SDK automatically tries the next authentication type.</span></span> <span data-ttu-id="9386e-157">沒有更多的類型可供試用時，SDK 就會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="9386e-157">When no more types are available to try, the SDK returns an error.</span></span>

<span data-ttu-id="9386e-158">下表詳細說明針對環境式驗證所支援的每種驗證類型，需要設定的環境變數。</span><span class="sxs-lookup"><span data-stu-id="9386e-158">The following table details the environment variables that need to be set for each authentication type supported by environment-based authentication.</span></span>

| <span data-ttu-id="9386e-159">驗證類型</span><span class="sxs-lookup"><span data-stu-id="9386e-159">Authentication type</span></span> | <span data-ttu-id="9386e-160">環境變數</span><span class="sxs-lookup"><span data-stu-id="9386e-160">Environment variable</span></span> | <span data-ttu-id="9386e-161">說明</span><span class="sxs-lookup"><span data-stu-id="9386e-161">Description</span></span> |
| ------------------- | -------------------- | ----------- |
| <span data-ttu-id="9386e-162">__用戶端認證__</span><span class="sxs-lookup"><span data-stu-id="9386e-162">__Client credentials__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="9386e-163">服務主體所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="9386e-163">The ID for the Active Directory tenant that the service principal belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="9386e-164">服務主體的名稱或 ID。</span><span class="sxs-lookup"><span data-stu-id="9386e-164">The name or ID of the service principal.</span></span> |
| | `AZURE_CLIENT_SECRET` | <span data-ttu-id="9386e-165">與服務主體關聯的密碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-165">The secret associated with the service principal.</span></span> |
| <span data-ttu-id="9386e-166">__憑證__</span><span class="sxs-lookup"><span data-stu-id="9386e-166">__Certificate__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="9386e-167">憑證註冊的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="9386e-167">The ID for the Active Directory tenant that the certificate is registered with.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="9386e-168">與憑證相關聯的用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-168">The application client ID associated with the certificate.</span></span> |
| | `AZURE_CERTIFICATE_PATH` | <span data-ttu-id="9386e-169">用戶端憑證檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="9386e-169">The path to the client certificate file.</span></span> |
| | `AZURE_CERTIFICATE_PASSWORD` | <span data-ttu-id="9386e-170">用戶端憑證的密碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-170">The password for the client certificate.</span></span> |
| <span data-ttu-id="9386e-171">__使用者名稱/密碼__</span><span class="sxs-lookup"><span data-stu-id="9386e-171">__Username/Password__</span></span> | `AZURE_TENANT_ID` | <span data-ttu-id="9386e-172">使用者所屬的 Active Directory 租用戶 ID。</span><span class="sxs-lookup"><span data-stu-id="9386e-172">The ID for the Active Directory tenant that the user belongs to.</span></span> |
| | `AZURE_CLIENT_ID` | <span data-ttu-id="9386e-173">應用程式用戶端識別碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-173">The application client ID.</span></span> |
| | `AZURE_USERNAME` | <span data-ttu-id="9386e-174">用來登入的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="9386e-174">The username to sign in with.</span></span> |
| | `AZURE_PASSWORD` | <span data-ttu-id="9386e-175">用來登入的密碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-175">The password to sign in with.</span></span> |
| <span data-ttu-id="9386e-176">__受控身分識別__</span><span class="sxs-lookup"><span data-stu-id="9386e-176">__Managed identity__</span></span> | | <span data-ttu-id="9386e-177">受控識別驗證不需要任何認證。</span><span class="sxs-lookup"><span data-stu-id="9386e-177">No credentials are needed for managed identity authentication.</span></span> <span data-ttu-id="9386e-178">但應用程式必須要在設定為使用受控識別的 Azure 資源上執行。</span><span class="sxs-lookup"><span data-stu-id="9386e-178">The application must be running on an Azure resource configured to use managed identities.</span></span> <span data-ttu-id="9386e-179">若要了解詳細資料，請參閱[適用於 Azure 資源的受控識別]。</span><span class="sxs-lookup"><span data-stu-id="9386e-179">For details, see [Managed identities for Azure resources].</span></span> |

<span data-ttu-id="9386e-180">若要連線到預設 Azure 公用雲端以外的雲端或管理端點，請設定下列環境變數。</span><span class="sxs-lookup"><span data-stu-id="9386e-180">To connect to a cloud or management endpoint other than the default Azure public cloud, set the following environment variables.</span></span> <span data-ttu-id="9386e-181">最常見的幾個原因是您使用了 Azure Stack、位於不同地理區域的雲端，或傳統部署模型。</span><span class="sxs-lookup"><span data-stu-id="9386e-181">The most common reasons are if you use Azure Stack, a cloud in a different geographic region, or the classic deployment model.</span></span>

| <span data-ttu-id="9386e-182">環境變數</span><span class="sxs-lookup"><span data-stu-id="9386e-182">Environment variable</span></span> | <span data-ttu-id="9386e-183">說明</span><span class="sxs-lookup"><span data-stu-id="9386e-183">Description</span></span>  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | <span data-ttu-id="9386e-184">要連線到的雲端環境名稱。</span><span class="sxs-lookup"><span data-stu-id="9386e-184">The name of the cloud environment to connect to.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="9386e-185">連線時要使用的 Active Directory 資源識別碼，會作為您管理端點的 URI。</span><span class="sxs-lookup"><span data-stu-id="9386e-185">The Active Directory resource ID to use when connecting, as a URI to your management endpoint.</span></span> |

<span data-ttu-id="9386e-186">使用環境式驗證時，呼叫 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函式可取得授權者物件。</span><span class="sxs-lookup"><span data-stu-id="9386e-186">When using environment-based authentication, call the [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) function to get your authorizer object.</span></span> <span data-ttu-id="9386e-187">然後在用戶端的 `Authorizer` 屬性上設定此物件，允許用戶端存取 Azure。</span><span class="sxs-lookup"><span data-stu-id="9386e-187">This object is then set on the `Authorizer` property of clients to allow them access to Azure.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a><span data-ttu-id="9386e-188">在 Azure Stack 上進行驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-188">Authentication on Azure Stack</span></span>

<span data-ttu-id="9386e-189">若要在 Azure Stack 上進行驗證，您需要設定以下變數：</span><span class="sxs-lookup"><span data-stu-id="9386e-189">To authenticate on Azure Stack, you need to set the following variables:</span></span>

| <span data-ttu-id="9386e-190">環境變數</span><span class="sxs-lookup"><span data-stu-id="9386e-190">Environment variable</span></span> | <span data-ttu-id="9386e-191">說明</span><span class="sxs-lookup"><span data-stu-id="9386e-191">Description</span></span>  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | <span data-ttu-id="9386e-192">Azure Active Directory 端點。</span><span class="sxs-lookup"><span data-stu-id="9386e-192">The Active Directory endpoint.</span></span> |
| `AZURE_AD_RESOURCE` | <span data-ttu-id="9386e-193">Active Directory 資源識別碼。</span><span class="sxs-lookup"><span data-stu-id="9386e-193">The Active Directory resource ID.</span></span> |

<span data-ttu-id="9386e-194">可以從 Azure Stack 中繼資料資訊中擷取這些變數。</span><span class="sxs-lookup"><span data-stu-id="9386e-194">These variables can be retrieved from Azure Stack metadata information.</span></span> <span data-ttu-id="9386e-195">若要擷取中繼資料，請在 Azure Stack 環境中開啟網路瀏覽器，並使用下列 URL：`(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span><span class="sxs-lookup"><span data-stu-id="9386e-195">To retrieve the metadata, open a web browser in your Azure Stack environment and use the url: `(ResourceManagerURL)/metadata/endpoints?api-version=1.0`</span></span>

<span data-ttu-id="9386e-196">`ResourceManagerURL` 會依 Azure Stack 部署的區域名稱、機器名稱和外部完整網域名稱 (FQDN) 而有所不同：</span><span class="sxs-lookup"><span data-stu-id="9386e-196">The `ResourceManagerURL` varies based on the region name, machine name, and external fully qualified domain name (FQDN) of your Azure Stack deployment:</span></span>

| <span data-ttu-id="9386e-197">環境</span><span class="sxs-lookup"><span data-stu-id="9386e-197">Environment</span></span> | <span data-ttu-id="9386e-198">ResourceManagerURL</span><span class="sxs-lookup"><span data-stu-id="9386e-198">ResourceManagerURL</span></span> |
|----------------------|--------------|
| <span data-ttu-id="9386e-199">開發套件</span><span class="sxs-lookup"><span data-stu-id="9386e-199">Development Kit</span></span> | `https://management.local.azurestack.external/` |
| <span data-ttu-id="9386e-200">整合系統</span><span class="sxs-lookup"><span data-stu-id="9386e-200">Integrated Systems</span></span> | `https://management.(region).ext-(machine-name).(FQDN)` |

<span data-ttu-id="9386e-201">如需如何在 Azure Stack 上使用 Azure SDK for Go 的詳細資訊，請參閱[在 Azure Stack 中搭配使用 Go 與 API 版本設定檔](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span><span class="sxs-lookup"><span data-stu-id="9386e-201">For more information on how to use the Azure SDK for Go on Azure Stack, see [Use API version profiles with Go in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)</span></span>

## <a name="use-file-based-authentication"></a><span data-ttu-id="9386e-202">使用檔案式驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-202">Use file-based authentication</span></span>

<span data-ttu-id="9386e-203">檔案式驗證會使用 [Azure CLI](/cli/azure) 所產生的檔案格式。</span><span class="sxs-lookup"><span data-stu-id="9386e-203">File-based authentication uses a file format generated by [the Azure CLI](/cli/azure).</span></span> <span data-ttu-id="9386e-204">以 `--sdk-auth` 參數建立新的服務主體時，您可以輕鬆地建立此檔案。</span><span class="sxs-lookup"><span data-stu-id="9386e-204">You can easily create this file when creating a new service principal with the `--sdk-auth` parameter.</span></span> <span data-ttu-id="9386e-205">若您計劃使用檔案式驗證，請務必在建立服務主體時，提供這個引數。</span><span class="sxs-lookup"><span data-stu-id="9386e-205">If you plan on using file-based authentication, make sure that this argument is provided when creating a service principal.</span></span> <span data-ttu-id="9386e-206">因為 CLI 會將輸出列印到 `stdout`，請將輸出重新導向到檔案。</span><span class="sxs-lookup"><span data-stu-id="9386e-206">Since the CLI prints output to `stdout`, redirect output to a file.</span></span>

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

<span data-ttu-id="9386e-207">請將 `AZURE_AUTH_LOCATION` 環境變數設定成授權檔案所在的位置。</span><span class="sxs-lookup"><span data-stu-id="9386e-207">Set the `AZURE_AUTH_LOCATION` environment variable to where the authorization file is located.</span></span> <span data-ttu-id="9386e-208">應用程式會讀取這個環境變數，並剖析其中的認證。</span><span class="sxs-lookup"><span data-stu-id="9386e-208">This environment variable is read by the application, and the credentials within it are parsed.</span></span> <span data-ttu-id="9386e-209">若您需要在執行階段取授權檔案，請使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函式來操縱程式的環境。</span><span class="sxs-lookup"><span data-stu-id="9386e-209">If you need to select the authorization file at runtime, manipulate the program's environment using the [os.Setenv](https://golang.org/pkg/os/#Setenv) function.</span></span>

<span data-ttu-id="9386e-210">若要載入驗證資訊，請呼叫 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函式。</span><span class="sxs-lookup"><span data-stu-id="9386e-210">To load the authentication information, call the [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) function.</span></span> <span data-ttu-id="9386e-211">與環境式授權不同，檔案式授權需要資源端點。</span><span class="sxs-lookup"><span data-stu-id="9386e-211">Unlike environment-based authorization, file-based authorization requires a resource endpoint.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

<span data-ttu-id="9386e-212">如需有關使用服務主體，以及管理其存取權限的詳細資訊，請參閱[使用 Azure CLI 建立服務主體]。</span><span class="sxs-lookup"><span data-stu-id="9386e-212">For more on using service principals and managing their access permissions, see [Create a service principal with Azure CLI].</span></span>

## <a name="use-device-token-authentication"></a><span data-ttu-id="9386e-213">使用裝置權杖驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-213">Use device token authentication</span></span>

<span data-ttu-id="9386e-214">若您希望使用者以互動方式登入，最好的方式是透過裝置權杖驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-214">If you want users to sign in interactively, the best way is through device token authentication.</span></span> <span data-ttu-id="9386e-215">此驗證流程會將權杖傳遞給使用者，讓使用者貼入 Microsoft 登入網站，然後在此使用 Azure Active Directory (AAD) 帳戶進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-215">This authentication flow passes the user a token to paste into a Microsoft sign-in site, where they then authenticate with an Azure Active Directory (AAD) account.</span></span> <span data-ttu-id="9386e-216">這種驗證方法不同於使用者名稱/密碼驗證，可支援已啟用多重要素驗證的帳戶。</span><span class="sxs-lookup"><span data-stu-id="9386e-216">This authentication method supports accounts that have multi-factor authentication enabled, unlike standard username/password authentication.</span></span>

<span data-ttu-id="9386e-217">若要使用裝置權杖驗證，請使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函式建立 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授權者。</span><span class="sxs-lookup"><span data-stu-id="9386e-217">To use device token authentication, create a [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) authorizer with the [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) function.</span></span> <span data-ttu-id="9386e-218">呼叫結果物件上的 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以開始驗證程序。</span><span class="sxs-lookup"><span data-stu-id="9386e-218">Call [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) on the resulting object to start the authentication process.</span></span> <span data-ttu-id="9386e-219">裝置流程驗證會封鎖程式執行，直到整個驗證流程完成為止。</span><span class="sxs-lookup"><span data-stu-id="9386e-219">Device flow authentication blocks program execution until the whole authentication flow is complete.</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a><span data-ttu-id="9386e-220">使用驗證用戶端</span><span class="sxs-lookup"><span data-stu-id="9386e-220">Use an authentication client</span></span>

<span data-ttu-id="9386e-221">若您需要特定類型的驗證，並願意讓程式自行載入來自使用者的驗證資訊，可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 介面的用戶端。</span><span class="sxs-lookup"><span data-stu-id="9386e-221">If you require a specific type of authentication and are willing to have your program do the work to load authentication information from the user, you can use any client that conforms to the [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) interface.</span></span> <span data-ttu-id="9386e-222">請在以下時機使用會實作此介面的類型：</span><span class="sxs-lookup"><span data-stu-id="9386e-222">Use a type that implements this interface when you:</span></span>

* <span data-ttu-id="9386e-223">撰寫互動式程式</span><span class="sxs-lookup"><span data-stu-id="9386e-223">Write an interactive program</span></span>
* <span data-ttu-id="9386e-224">使用特定的組態檔</span><span class="sxs-lookup"><span data-stu-id="9386e-224">Use specialized configuration files</span></span>
* <span data-ttu-id="9386e-225">有防止使用內建驗證方法的需求</span><span class="sxs-lookup"><span data-stu-id="9386e-225">Have a requirement that prevents using a built-in authentication method</span></span>

> [!WARNING]
> <span data-ttu-id="9386e-226">請勿用硬式編碼將 Azure 認證寫入應用程式中。</span><span class="sxs-lookup"><span data-stu-id="9386e-226">Never hard-code Azure credentials into an application.</span></span> <span data-ttu-id="9386e-227">將祕密放入應用程式二進位檔會使攻擊者易於擷取，無論該應用程式是否正在執行。</span><span class="sxs-lookup"><span data-stu-id="9386e-227">Putting secrets into an application binary makes it easier for an attacker to extract them, whether the application is running or not.</span></span> <span data-ttu-id="9386e-228">這會使獲得該認證授權的所有 Azure 資源暴露在風險下！</span><span class="sxs-lookup"><span data-stu-id="9386e-228">This puts all Azure resources the credentials are authorized for at risk!</span></span>

<span data-ttu-id="9386e-229">下表列出 SDK 中符合 `AuthorizerConfig` 介面的類型。</span><span class="sxs-lookup"><span data-stu-id="9386e-229">The following table lists the types in the SDK that conform to the `AuthorizerConfig` interface.</span></span>

| <span data-ttu-id="9386e-230">驗證類型</span><span class="sxs-lookup"><span data-stu-id="9386e-230">Authentication type</span></span> | <span data-ttu-id="9386e-231">授權者類型</span><span class="sxs-lookup"><span data-stu-id="9386e-231">Authorizer type</span></span> |
|---------------------|-----------------------|
| <span data-ttu-id="9386e-232">憑證式驗證</span><span class="sxs-lookup"><span data-stu-id="9386e-232">Certificate-based authentication</span></span> | <span data-ttu-id="9386e-233">[ClientCertificateConfig]</span><span class="sxs-lookup"><span data-stu-id="9386e-233">[ClientCertificateConfig]</span></span> |
| <span data-ttu-id="9386e-234">用戶端認證</span><span class="sxs-lookup"><span data-stu-id="9386e-234">Client credentials</span></span> | <span data-ttu-id="9386e-235">[ClientCredentialsConfig]</span><span class="sxs-lookup"><span data-stu-id="9386e-235">[ClientCredentialsConfig]</span></span> |
| <span data-ttu-id="9386e-236">適用於 Azure 資源的受控識別</span><span class="sxs-lookup"><span data-stu-id="9386e-236">Managed identities for Azure resources</span></span> | <span data-ttu-id="9386e-237">[MSIConfig]</span><span class="sxs-lookup"><span data-stu-id="9386e-237">[MSIConfig]</span></span> |
| <span data-ttu-id="9386e-238">使用者名稱/密碼</span><span class="sxs-lookup"><span data-stu-id="9386e-238">Username/password</span></span> | <span data-ttu-id="9386e-239">[UsernamePasswordConfig]</span><span class="sxs-lookup"><span data-stu-id="9386e-239">[UsernamePasswordConfig]</span></span> |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

<span data-ttu-id="9386e-244">使用與其相關聯的 `New` 函式建立驗證器，然後再呼叫結果物件上的 `Authorize` 來執行驗證。</span><span class="sxs-lookup"><span data-stu-id="9386e-244">Create an authenticator with its associated `New` function, and then call `Authorize` on the resulting object to authenticate.</span></span> <span data-ttu-id="9386e-245">例如，使用憑證式驗證：</span><span class="sxs-lookup"><span data-stu-id="9386e-245">For example, to use certificate-based authentication:</span></span>

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
