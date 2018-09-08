# Sing-up with social and local account

With Azure AD B2C a user can have multiple identities. Sign-in with local account, and link a social account to an existing local account. This Azure AD B2C sample demonstrates how to create a single account with social and local identities.

This is scenario may help in case you provide the user the ability to sign-in with your organization account (such as ADFS, Azure AD, or Salcefoce account), but also want to allow users to continue sign-in with their local account after they leave the organization. For example, a user may not belong anymore to the organization, but he/she should still be able to access the application. With this demo, when user sign-in with Facebook account, you ask the user to provide and verify the email address and password. When B2C creates the account, the account is created with:
- **signInName** containing the email address for the local identity
- **password** for local identity
- **userIdentities** with the social or enterprise id, for the social identity
- User profile, such as first name, last name, and display name
 
Following is an example of such account:

```JSON

{
    "displayName": "Yoel Hor",
    "givenName": "Yoel",
    "jobTitle": null,
    "surname": "Hor",
    "signInNames": [
        {
            "type": "emailAddress",
            "value": "your@contoso.com"
        }
    ],
    "userIdentities": [
        {
            "issuer": "facebook.com",
            "issuerUserId": "RTEwAjQwBTM7Mjg4NERv"
        }
    ]
}
```

## Disclaimer
The migration application is developed and managed by the open-source community in GitHub. The application is not part of Azure AD B2C product and it's not supported under any Microsoft standard support program or service. 
This migration app is provided AS IS without warranty of any kind.

> Note:  This sample policy is based on [SocialAndLocalAccounts starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts). All changes are marked with **Demo:** comment inside the policy XML files.