# Azure AD B2C .NET 8 Web Application

A complete implementation of Azure Active Directory B2C authentication for .NET 8 web applications, featuring secure sign-in, sign-up, profile management, and user claim handling.

## 🚀 Features

- **🔐 Secure Authentication**: Enterprise-grade security with Azure AD B2C
- **👤 User Management**: Self-service registration, sign-in, and profile editing
- **🔄 Password Reset**: Built-in password reset functionality
- **📊 Claims Management**: Complete user claims viewing and handling
- **🛡️ Authorization**: Secure access control for protected resources
- **📱 Responsive UI**: Modern Bootstrap-based interface

## 🛠️ Prerequisites

- .NET 8.0 SDK
- Azure subscription
- Visual Studio 2022 or VS Code
- Azure AD B2C tenant

## 📁 Project Structure

```
AzureB2CApp/
├── Controllers/
│   └── HomeController.cs          # Main controller with authentication logic
├── Views/
│   ├── Home/
│   │   ├── Index.cshtml          # Home page
│   │   ├── Privacy.cshtml        # Privacy policy page
│   │   ├── Profile.cshtml        # User profile page
│   │   └── Claims.cshtml         # User claims display
│   └── Shared/
│       ├── _Layout.cshtml        # Main layout template
│       ├── _LoginPartial.cshtml  # Login/logout partial view
│       ├── _ViewStart.cshtml     # View start configuration
│       └── _ViewImports.cshtml   # Global view imports
├── Program.cs                    # Application startup and configuration
├── appsettings.json             # Configuration settings
└── AzureB2CApp.csproj          # Project file with dependencies
```

## ⚙️ Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/vignesh101/azure-ad-b2c-dotnet.git
cd azure-ad-b2c-dotnet
```

### 2. Configure Azure AD B2C
Update `appsettings.json` with your Azure AD B2C settings:

```json
{
  "AzureAdB2C": {
    "Instance": "https://yourdomain.b2clogin.com/",
    "ClientId": "your-client-id-here",
    "Domain": "yourdomain.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin",
    "ResetPasswordPolicyId": "B2C_1_passwordreset",
    "EditProfilePolicyId": "B2C_1_profileediting",
    "CallbackPath": "/signin-oidc"
  }
}
```

### 3. Restore and Run
```bash
dotnet restore
dotnet run
```

Navigate to `https://localhost:7049` to see the application in action.

## 🔧 Azure AD B2C Setup

### Step 1: Create B2C Tenant
1. Go to Azure Portal
2. Create new Azure AD B2C resource
3. Note your tenant domain

### Step 2: Register Application
1. In B2C tenant → App registrations → New registration
2. Set redirect URI: `https://localhost:7049/signin-oidc`
3. Note the Client ID

### Step 3: Create User Flows
Create these user flows in your B2C tenant:
- **Sign-up/Sign-in**: `B2C_1_signupsignin`
- **Password Reset**: `B2C_1_passwordreset`
- **Profile Editing**: `B2C_1_profileediting`

## 📦 Dependencies

- **Microsoft.Identity.Web** (2.17.2) - Core Azure AD integration
- **Microsoft.Identity.Web.UI** (2.17.2) - UI components for authentication
- **Microsoft.AspNetCore.Authentication.OpenIdConnect** (8.0.4) - OpenID Connect support

## 🔒 Security Features

- **HTTPS Enforcement**: All communication encrypted
- **Authorization Policies**: Granular access control
- **Secure Token Handling**: Automatic token management
- **CSRF Protection**: Built-in cross-site request forgery protection

## 🎯 Usage Examples

### Protected Controller Action
```csharp
[Authorize]
public IActionResult SecureAction()
{
    var userName = User.Identity?.Name;
    var userClaims = User.Claims;
    // Your secure logic here
    return View();
}
```

### Anonymous Access
```csharp
[AllowAnonymous]
public IActionResult PublicAction()
{
    // Accessible without authentication
    return View();
}
```

## 🛠️ Customization

### Adding Custom Claims
Modify the user flows in Azure AD B2C to collect additional user attributes and claims.

### UI Customization
Update the views in the `Views/` folder to match your branding and requirements.

### Authorization Policies
Extend `Program.cs` to add custom authorization policies based on user roles or claims.

## 🚀 Deployment

### Local Development
- Uses HTTPS development certificates
- Configured for `https://localhost:7049`

### Production Deployment
- Update redirect URIs in Azure AD B2C
- Use Azure Key Vault for secrets
- Configure proper CORS policies
- Implement comprehensive logging

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Support

- **Azure AD B2C Documentation**: [Microsoft Docs](https://docs.microsoft.com/azure/active-directory-b2c/)
- **Issues**: [GitHub Issues](https://github.com/vignesh101/azure-ad-b2c-dotnet/issues)
- **.NET Documentation**: [Microsoft .NET Docs](https://docs.microsoft.com/dotnet/)

## 📊 Screenshots

### Home Page
Clean and modern interface with authentication status display.

### User Profile
Complete user information and claims management interface.

### Secure Access
Properly protected routes with automatic redirection to Azure AD B2C.

---

**Built with ❤️ using .NET 8 and Azure AD B2C**
