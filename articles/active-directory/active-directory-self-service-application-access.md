---
title: "Azure Active Directory 的自助应用程序访问和委派管理 | Microsoft Docs"
description: "本文介绍如何使用 Azure Active Directory 启用自助应用程序访问和委派管理"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 448a7fe8-a162-475e-9ba2-2e3ab59302bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 57093159c0b9c63b6e2dc0971e3aea645a33aa55
ms.contentlocale: zh-cn
ms.lasthandoff: 12/08/2016


---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Azure Active Directory 的自助应用程序访问和委派管理
为最终用户启用自助服务功能是常见的企业 IT 方案。 许多用户、许多应用程序以及最有把握做出访问权授与决策的人员可能不是目录管理员。 通常，决定谁可以访问应用程序的最佳人选是小组负责人或委派的其他管理员。 但是，在一天结束时，使用应用的人是用户，而用户知道他们需要什么才能完成其作业。

自助应用程序访问是 [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) 的一项功能，可让目录管理员执行以下操作：

* 允许用户使用 [Azure AD 访问面板](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中的“获取更多应用程序”磁贴访问应用程序
* 设置用户可以请求访问哪些应用程序
* 设置用户是否需要经过审批才能够自我分配对应用程序的访问
* 设置谁应该审批请求以及管理对每个应用程序的访问

现在，[Azure Active Directory 应用程序库](https://azure.microsoft.com/marketplace/active-directory/all/)中所有预先集成的应用程序，以及支持联合或基于密码的单一登录的自定义应用程序（包括 Salesforce、Dropbox、Google Apps 等应用程序）都支持此功能。
本文将介绍如何：

* 为最终用户配置自助应用程序访问，包括配置可选的审批工作流 
* 将特定应用程序的访问管理权限委派给组织中最适当的人员，使他们能够使用 Azure AD 访问面板来审批访问请求，直接将访问权限分配给选定的用户，或者（可选）在配置基于密码的单一登录时设置用于访问应用程序的凭据

## <a name="configuring-self-service-application-access"></a>配置自助应用程序访问
若要启用自助应用程序访问和配置最终用户可以添加或请求哪些应用程序，请遵循以下说明。

**1：**登录 [Azure 经典门户](https://manage.windowsazure.com/)。

**2：**在“Active Directory”部分下选择你的目录，然后选择“应用程序”选项卡。 

**3：**选择“添加”按钮，然后使用库选项来选择和添加应用程序。

**4：**添加应用程序之后，将显示“快速启动”页。 单击“配置单一登录”，选择所需的单一登录模式，然后保存配置。 

**5：**接下来，选择“配置”选项卡。 若要允许用户请求从 Azure AD 访问面板访问此应用，请将“允许自助应用程序访问”设置为“是”。

![][1]

**6：**若要选择配置访问请求的审批工作流，请将“授予访问权限之前需要审批”设置为“是”。 然后可以使用“审批人”按钮选择一个或多个审批人。

审批人可以是组织中具有 Azure AD 帐户并且可能负责帐户预配、许可或组织在授予应用访问权限前要求执行的任何其他业务流程的任何用户。 审批人甚至可以是一个或多个共享帐户组的组所有者，可以将用户分配给其中一个组，使他们能够通过共享帐户进行访问。 

如果不需要审批，用户会立即将应用程序添加到其 Azure AD 访问面板。 这适用于应用程序已设置了[自动用户预配](active-directory-saas-app-provisioning.md)，或已设置[“用户管理的”密码 SSO 模式](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)，而该模式中的用户已有帐户并知道密码的情况。

**7：**如果应用程序已设置为使用基于密码的单一登录，则审批人还可以使用一个选项来代表每个用户设置 SSO 凭据。 有关详细信息，请参阅下一部分中有关委派访问管理的信息。

**8：**最后，“自我分配的用户组”显示的组名将用于存储被授予或分配应用程序访问权限的用户。 访问审批人将成为此组的所有者。 如果显示的组名不存在，系统将自动创建名称。 （可选）可将此组名设置为现有组的名称。

**9：**单击屏幕底部的“保存”以保存配置。 现在，用户可以从访问面板请求访问此应用。

**10：**若要尝试用户体验，请通过 https://myapps.microsoft.com 登录组织的 Azure AD 访问面板（最好不要使用应用程序审批人的帐户）。 

**11：**在“应用程序”选项卡下，单击“获取更多应用程序”磁贴。 此时会显示目录中所有已启用自助应用程序访问的库，你可以根据左侧的应用类别进行搜索和筛选。 

**12：**单击一个应用可以发起请求过程。 如果不需要审批过程，则在经过简短的确认之后，应用程序将立即添加到“应用程序”选项卡下面。 如果需要审批，则显示的对话框会指明需要审批，并向审批人发送电子邮件。 （简短说明：你需要以非审批人身份登录访问面板才能查看此请求过程）。

**13：**电子邮件指示审批人登录 Azure AD 访问面板并审批请求。 审批人批准请求（并执行你定义的任何特殊过程）之后，用户将看到该应用程序出现在其可登录的“应用程序”选项卡下面。

## <a name="delegated-application-access-management"></a>委派的应用程序访问管理
应用程序访问权限审批人可以是组织中最适合批准或拒绝访问相关应用程序的任何用户。 此用户可能负责帐户预配、许可或组织在授予应用访问权限前要求执行的任何其他业务流程的任何用户。

配置上述自助应用程序访问时，所有分配的应用程序审批人都会在 Azure AD 访问面板中看到额外的“管理应用程序”磁贴，其中显示了他们身为访问管理员的应用程序。 单击应用会显示包含多个选项的屏幕。

![][2]

### <a name="approve-requests"></a>审批请求
“审批请求”磁贴可让审批人看到该应用程序特定的未决审批，并重新定向到可确认或拒绝请求的“审批”选项卡。 请注意，每当创建请求时，审批人也会收到自动发送的电子邮件，其中指示了要执行的操作。

### <a name="add-users"></a>添加用户
“添加用户”磁贴可让审批人直接将应用程序的访问权限授予选定的用户。 单击此磁贴时，审批人会看到一个对话框，可在其中查看和搜索其目录中的用户。 添加用户会导致应用程序显示在这些用户的 Azure AD 访问面板或 Office 365 中。 如果在用户能够登录之前需要在应用中进行任何手动用户预配过程，则审批人应在分配访问权限之前执行此过程。  

### <a name="manage-users"></a>管理用户
审批人可以使用“管理用户”磁贴直接更新或删除可访问应用程序的用户。 

### <a name="configure-password-sso-credentials-if-applicable"></a>设置密码 SSO 凭据（如果适用）
只有在 IT 管理员将应用程序配置为使用基于密码的单一登录，并且管理员授予了审批人设置密码 SSO 凭据的功能（如前所述）之后，才会显示“配置”磁贴。 选择该磁贴后，审批人将看到几个选项，用于配置如何将凭据传播给分配的用户：

![][3]

* **用户使用其自己的密码登录** – 在此模式下，分配的用户知道其应用程序的用户名和密码，并且他们在首次登录应用程序时，系统会提示输入这些信息。 这相当于[用户管理凭据](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)的密码 SSO 方案。
* **用户使用我管理的独立帐户自动登录** – 在此模式下，分配的用户在登录应用程序时不需要输入或知道其应用程序特定的凭据。 而是由审批人在使用“添加用户”磁贴分配访问权限之后为每个用户设置凭据。 当用户单击其访问面板或 Office 365 中的应用程序时，系统会使用审批人设置的凭据将他们自动登录。 这相当于[管理员管理凭据](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)的密码 SSO 方案。
* **用户使用我管理的单个帐户自动登录** - 这是一种特殊方案，适用于所有分配的用户有权使用单个共享帐户进行访问的情况。 此选项的最常见用例就是社交媒体应用程序，其中，组织有单个“公司”帐户，多个用户需要对该帐户进行更新。 这也相当于[管理员管理凭据](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)的密码 SSO 方案。 不过，选择此选项后，系统将提示审批人输入单个共享帐户的用户名和密码。 完成后，所有分配的用户在单击 Azure AD 访问面板或 Office 365 中的应用程序时，将使用此帐户登录。

## <a name="additional-resources"></a>其他资源
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

