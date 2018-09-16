# Azure AD B2C: Force password reset first logon

This solution demonstrates how to force user to reset password on the first logon. The solution is based on an extension attribute. Read here how to [configure extension attributes](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-create-custom-attributes-profile-edit-custom). When you create a B2C account via Graph API, you set this property to `true`. On run time, when user sign-in, Azure AD B2C checks the value of this extension attribute. If set to `true`, B2C asks the user to reset the password and removes this attributes from the user account. 

To create the extension attribute extension_mustResetPassword, you should upload the policy and create one account. When you create an account, B2C also creates the attribute in the WebApp-GraphAPI-DirectoryExtensions app. Then COMMENT OUT **AAD-UserWriteUsingLogonEmail** technical profile in the extension policy. Note: you can also create the extension attribute using Graph API, but I'd recomend you using this safe method.

After you create the first account, you can run following Graph query, to make sure the extension attribute is created:
https://graph.windows.net/myorganization/applications/00000000-0000-0000-0000-000000000000/extensionProperties.
Replace the 00000000-0000-0000-0000-000000000000 with your application object Id. the result of this query should look like
```JSON
"value": [
    {
        "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
        "objectType": "ExtensionProperty",
        "objectId": "ad2f05d9-f623-440f-b2c1-0012b45699c5",
        "deletionTimestamp": null,
        "appDisplayName": "",
        "name": "extension_00000000000000000000000000000000_mustResetPassword",
        "dataType": "Boolean",
        "isSyncedFromOnPremises": false,
        "targetObjects": [
            "User"
        ]
    }
]
```
After the extension attribute is configured, when you create the accounts using Graph API, just set the value of this extension attribute. 
Replace the id with your application Id
```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "1de4141d-d1c2-448d-877d-c9e92bda87f5",
    "signInNames": [{
        "type": "emailAddress",
        "value": "jamesm@contoso.com"
                    }
    ],
    "creationType": "LocalAccount",
    "displayName": "James Martin",
    "givenName": "James",
    "surname": "Martin",
    "passwordProfile": {
        "password": "1",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [],
    "otherMails": [],
    "userPrincipalName": "1de4141d-d1c2-448d-877d-c9e92bda87f5@your-tenant.onmicrosoft.com",
    
    // Add this line to your JSON
    "extension_00000000000000000000000000000000_mustResetPassword": true
}
```
The name of the extension attribute is “extension” _ “the application ID” _ the name you give the attribute. While in B2C custom policy you don’t need to specify the application ID.

Follow the demo comments in the extension policy. To merge the policy with yours, you need:
1.	Add the **extension_mustResetPassword** claim type and set the display name
2.	Add the **AAD-UserReadUsingObjectId**, **AAD-UserRemoveMustResetPasswordUsingObjectId**, **AAD-UserWriteUsingLogonEmail**, **AAD-Common** technical profiles
3.	Add the extra orchestration steps 7 and 8 before the last orchestration step

## Test the policy by using Run Now
1. Create an account using Graph API, and set the value of **extension_00000000000000000000000000000000_mustResetPassword** extension attribute to **true**
1. From Azure Portal select **Azure AD B2C Settings**, and then select **Identity Experience Framework**.
1. Open **B2C_1A_signup_signin**, the relying party (RP) custom policy that you uploaded, and then select **Run now**.
1. Sign-in with the account you created. 
1. Make sure Azure AD B2C asks you to reset the password. Type your password and your profile, and click **Continue**
1. Sign-out form your application, or open new browser in private mode (incognito)
1. Open **B2C_1A_signup_signin** again, the relying party (RP) custom policy that you uploaded, and then select **Run now**.
1. Sign-in with the local identity (email address) you specified
1. You can also check the account you crated by using [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/). And make sure the **extension_00000000000000000000000000000000_mustResetPassword** is removed from the user profile

## Disclaimer
The sample is developed and managed by the open-source community in GitHub. The application is not part of Azure AD B2C product and it's not supported under any Microsoft standard support program or service. The sample (Azure AD B2C policy and any companion code) is provided AS IS without warranty of any kind.

> Note:  This sample policy is based on [SocialAndLocalAccounts starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts). All changes are marked with **Demo:** comment inside the policy XML files. Make the nessacery changes in the **Demo action required** sections.
