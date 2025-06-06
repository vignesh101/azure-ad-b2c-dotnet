# Azure AD B2C Complete Setup Guide

This guide will walk you through setting up Azure AD B2C authentication for your .NET 8 web application from scratch.

## 📋 Prerequisites

- Azure subscription
- .NET 8.0 SDK installed
- Visual Studio 2022 or VS Code
- Basic understanding of ASP.NET Core

## 🔧 Azure AD B2C Configuration

### Step 1: Create Azure AD B2C Tenant

1. **Navigate to Azure Portal**
   - Go to [portal.azure.com](https://portal.azure.com)
   - Sign in with your Azure account

2. **Create B2C Resource**
   - Click "Create a resource"
   - Search for "Azure Active Directory B2C"
   - Click "Create"

3. **Choose Creation Option**
   - Select "Create a new Azure AD B2C Tenant"
   - Fill in the details:
     - **Organization name**: Your organization name
     - **Initial domain name**: Choose a unique domain (e.g., `mycompany`)
     - **Country/Region**: Your location
   - Click "Review + create"

4. **Note Your Tenant Information**
   - After creation, note your tenant domain: `yourdomain.onmicrosoft.com`
   - Note your tenant ID

### Step 2: Switch to B2C Tenant

1. **Switch Directory**
   - In Azure Portal, click on your profile (top right)
   - Click "Switch directory"
   - Select your newly created B2C tenant

2. **Access B2C Service**
   - Search for "Azure AD B2C" in the portal
   - Click on your B2C service

### Step 3: Register Your Application

1. **Navigate to App Registrations**
   - In your B2C tenant, go to "App registrations"
   - Click "New registration"

2. **Configure Registration**
   - **Name**: `Azure B2C Web App`
   - **Supported account types**: "Accounts in any identity provider or organizational directory (for authenticating users with user flows)"
   - **Redirect URI**: 
     - Platform: Web
     - URL: `https://localhost:7049/signin-oidc`
   - Click "Register"

3. **Note Application Details**
   - Copy the **Application (client) ID** - you'll need this
   - Copy the **Directory (tenant) ID**

### Step 4: Configure Authentication Settings

1. **Go to Authentication**
   - In your app registration, click "Authentication"

2. **Configure Implicit Grant**
   - Under "Implicit grant and hybrid flows", check:
     - ✅ Access tokens (used for implicit flows)
     - ✅ ID tokens (used for implicit and hybrid flows)

3. **Add Logout URL**
   - Under "Front-channel logout URL": `https://localhost:7049/signout-oidc`
   - Click "Save"

### Step 5: Create User Flows

User flows define the user experience for sign-up, sign-in, profile editing, and password reset.

#### Create Sign-up and Sign-in Flow

1. **Navigate to User Flows**
   - In B2C service, go to "User flows"
   - Click "New user flow"

2. **Select Flow Type**
   - Choose "Sign up and sign in"
   - Select "Recommended" version
   - Click "Create"

3. **Configure Flow**
   - **Name**: `signupsignin` (will become `B2C_1_signupsignin`)
   - **Identity providers**: 
     - ✅ Local Account (Email signup)
   - **User attributes and claims**:
     - **Collect attributes**: Email Address, Given Name, Surname
     - **Return claims**: Email Addresses, Given Name, Surname, Display Name, User's Object ID
   - Click "Create"

#### Create Password Reset Flow

1. **Create New User Flow**
   - Click "New user flow"
   - Select "Password reset"
   - Select "Recommended"

2. **Configure Password Reset**
   - **Name**: `passwordreset`
   - **Identity providers**: Local Account
   - **Application claims**: Email Addresses, Given Name, Surname
   - Click "Create"

#### Create Profile Editing Flow

1. **Create New User Flow**
   - Click "New user flow"
   - Select "Profile editing"
   - Select "Recommended"

2. **Configure Profile Editing**
   - **Name**: `profileediting`
   - **Identity providers**: Local Account
   - **User attributes**: Given Name, Surname
   - **Application claims**: Email Addresses, Given Name, Surname, Display Name
   - Click "Create"

### Step 6: Test User Flows

1. **Test Sign-up and Sign-in**
   - Go to your `B2C_1_signupsignin` user flow
   - Click "Run user flow"
   - Test with a dummy user to ensure it works

## 🚀 Application Configuration

### Step 7: Update appsettings.json

Replace the placeholder values in `appsettings.json` with your actual Azure B2C configuration:

```json
{
  "AzureAdB2C": {
    "Instance": "https://yourdomain.b2clogin.com/",
    "ClientId": "your-actual-client-id-from-step-3",
    "Domain": "yourdomain.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin",
    "ResetPasswordPolicyId": "B2C_1_passwordreset",
    "EditProfilePolicyId": "B2C_1_profileediting",
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

**Replace these values:**
- `yourdomain`: Your B2C tenant domain name
- `your-actual-client-id-from-step-3`: The client ID from your app registration

### Step 8: Run the Application

1. **Restore NuGet Packages**
   ```bash
   dotnet restore
   ```

2. **Build the Application**
   ```bash
   dotnet build
   ```

3. **Run the Application**
   ```bash
   dotnet run
   ```

4. **Test Authentication**
   - Navigate to `https://localhost:7049`
   - Click "Sign In / Sign Up"
   - Test user registration and login

## 🔒 Production Deployment

### Additional Configuration for Production

1. **Update Redirect URIs**
   - In your Azure B2C app registration
   - Add your production URLs (e.g., `https://yourdomain.com/signin-oidc`)

2. **Use Azure Key Vault**
   ```csharp
   // In Program.cs, add Key Vault configuration
   builder.Configuration.AddAzureKeyVault(
       "https://your-keyvault.vault.azure.net/",
       new DefaultAzureCredential());
   ```

3. **Configure HTTPS**
   - Ensure SSL certificates are properly configured
   - Use HTTPS redirects in production

4. **Set Up Application Insights**
   ```csharp
   builder.Services.AddApplicationInsightsTelemetry();
   ```

## 🛠️ Troubleshooting

### Common Issues and Solutions

1. **Redirect URI Mismatch**
   - **Error**: "The redirect URI specified in the request does not match"
   - **Solution**: Ensure redirect URIs in Azure match your app URLs exactly

2. **Policy Not Found**
   - **Error**: "Policy 'B2C_1_signupsignin' not found"
   - **Solution**: Verify policy names in Azure match your appsettings.json

3. **CORS Errors**
   - **Error**: Cross-origin request blocked
   - **Solution**: Configure CORS properly in your application

4. **SSL/TLS Certificate Issues**
   - **Error**: Certificate validation errors
   - **Solution**: Accept development certificates with `dotnet dev-certs https --trust`

### Debugging Tips

1. **Enable Detailed Logging**
   ```json
   "Logging": {
     "LogLevel": {
       "Default": "Debug",
       "Microsoft.AspNetCore.Authentication": "Debug"
     }
   }
   ```

2. **Check Browser Developer Tools**
   - Network tab for failed requests
   - Console for JavaScript errors

3. **Verify Token Contents**
   - Use jwt.io to decode tokens
   - Check claims are being returned correctly

## 📚 Additional Resources

- [Azure AD B2C Documentation](https://docs.microsoft.com/azure/active-directory-b2c/)
- [Microsoft Identity Web Documentation](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web)
- [ASP.NET Core Authentication Documentation](https://docs.microsoft.com/aspnet/core/security/authentication/)

## 🔐 Security Best Practices

1. **Token Validation**
   - Always validate tokens server-side
   - Verify token signatures and claims

2. **HTTPS Everywhere**
   - Use HTTPS for all authentication flows
   - Never send tokens over HTTP

3. **Secret Management**
   - Store client secrets in Azure Key Vault
   - Never commit secrets to source control

4. **Monitor and Audit**
   - Enable Azure AD B2C audit logs
   - Monitor failed authentication attempts

5. **Keep Dependencies Updated**
   - Regularly update Microsoft.Identity.Web packages
   - Monitor security advisories

## ✅ Verification Checklist

- [ ] Azure AD B2C tenant created
- [ ] Application registered in B2C
- [ ] User flows created (sign-up/sign-in, password reset, profile editing)
- [ ] appsettings.json updated with correct values
- [ ] Application runs without errors
- [ ] User can sign up successfully
- [ ] User can sign in successfully
- [ ] Profile page displays user information
- [ ] Claims are being returned correctly
- [ ] Sign out works properly

---

**Need Help?** 
- Check the [GitHub Issues](https://github.com/vignesh101/azure-ad-b2c-dotnet/issues) for common problems
- Review the Azure AD B2C documentation for advanced scenarios