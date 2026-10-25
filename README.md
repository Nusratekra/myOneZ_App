# MyoneZ — AI-Powered Personal Communication Assistant

MyoneZ is a mobile application designed to simplify personal communication and daily organization. It helps users manage contacts, maintain personal memories, review communication history, create message drafts, schedule activities, and track notifications from one place.

The project consists of a React Native mobile application built with Expo and a Django REST API backend. The mobile app communicates with the backend over HTTP.

## Features

- **Authentication:** Sign up and sign in using email, password, and a six-digit email verification code.
- **Home Dashboard:** View relevant information and access key app features.
- **Contacts:** Manage contacts and access associated memories, history, and communication rhythm.
- **AI-Assisted Message Drafts:** Create, review, and manage message drafts.
- **Draft Approval:** Approve drafts and track sending status.
- **Communication History:** Access available message and check-in records.
- **Schedule:** View and manage scheduled activities supported by the backend.
- **Notifications:** View in-app notifications.
- **Personal Settings:** Configure the default communication channel, tone, and notification preferences.
- **Mock Data Support:** Use configurable mock authentication, home, and draft data for development and testing.

> Note: Features that depend on backend endpoints, external AI services, or message delivery integrations require those services to be configured and available. Mock mode is intended for development and does not represent real authentication or delivery.

## Technology Stack

### Frontend
- React Native
- Expo
- TypeScript
- Node.js and npm
- HTTP API integration

### Backend
- Python
- Django
- Django REST Framework
- Database migrations
- Token-based authentication

## Project Structure

```text
MyoneZ/
├── backend/
│   ├── manage.py
│   ├── requirements.txt
│   └── ...
├── Frontend/
│   ├── src/
│   │   ├── api/
│   │   ├── pages/
│   │   └── store/
│   ├── .env.example
│   ├── package.json
│   └── ...
├── .env.example
├── .gitignore
└── README.md
```

The exact file structure may vary depending on the current project version.

## Requirements

Install the following software before running the project:

- Git
- Python and pip
- Node.js 20 or 22 LTS
- npm
- Expo Go on your phone:
  - **Android:** [Download Expo Go APK (v58.0.5)](https://github.com/expo/expo-go-releases/releases/download/Expo-Go-58.0.5/Expo-Go-58.0.5.apk)
  - **iOS:** Install Expo Go from the App Store

For testing on a physical phone, connect your phone and computer to the same Wi-Fi network.

> The Expo Go version must match the Expo SDK version used by the project (see `Frontend/package.json`).

## Installation and Setup

### 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd MyoneZ
```

Replace `<YOUR_REPOSITORY_URL>` with the URL of your GitHub repository.

### 2. Set Up the Backend

Create a virtual environment from the repository root.

**Windows PowerShell:**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install the backend dependencies:

```powershell
python -m pip install -r backend/requirements.txt
```

If your `requirements.txt` is located directly inside `backend/`, use the path above from the repository root.

Copy the environment template:

```powershell
Copy-Item .env.example .env
```

Configure the required environment variables in the root `.env` file. Never commit secrets, passwords, or API keys.

For local development without an email provider, configure Django to print verification emails in the backend terminal:

```env
EMAIL_BACKEND=django.core.mail.backends.console.EmailBackend
```

Apply database migrations:

```powershell
cd backend
python manage.py migrate
```

If the project provides the optional demo-data command, run:

```powershell
python manage.py seed_demo
```

Start the development server:

```powershell
python manage.py runserver 0.0.0.0:8000
```

The backend will be available on port `8000`.

The `0.0.0.0` binding allows devices on your local network to reach the development server, subject to firewall and network configuration.

### 3. Find Your Computer's Local IP Address

On Windows, open another terminal and run:

```cmd
ipconfig
```

Find the IPv4 Address of the active Wi-Fi adapter. For example:

```text
192.168.0.105
```

Check that the backend is running by opening the following address in your phone's browser:

```text
http://192.168.0.105:8000/health/
```

Replace the example IP address with your computer's actual IPv4 address. A successful response should look like:

```json
{
  "status": "ok"
}
```

This endpoint must exist in the backend for the health check to work.

### 4. Install Expo Go on Your Phone

On Android, download and install the APK:

https://github.com/expo/expo-go-releases/releases/download/Expo-Go-58.0.5/Expo-Go-58.0.5.apk

You may need to allow installation from unknown sources in your phone settings. On iOS, install Expo Go from the App Store.

### 5. Configure the Mobile App

Open a second terminal and navigate to the frontend:

```powershell
cd Frontend
```

Create the frontend environment file:

```powershell
Copy-Item .env.example .env
```

Set the backend URL in `Frontend/.env`:

```env
EXPO_PUBLIC_API_URL=http://192.168.0.105:8000
```

Use your own computer's local IPv4 address. Do not use `localhost` or `127.0.0.1` when connecting from a physical phone, because those addresses refer to the phone itself.

Install the frontend dependencies:

```powershell
npm install
```

Start Expo:

```powershell
npx expo start -c
```

Scan the QR code using Expo Go on Android or the Camera app on iOS, then open the project in Expo Go.

If the QR code does not connect, check your firewall and network settings. You can also try:

```powershell
npx expo start --tunnel
```

### 6. Create an Account and Sign In

The application uses a two-step authentication flow:

1. Enter your email address and password.
2. Enter the six-digit verification code.

When the console email backend is enabled, the verification code is printed in the backend terminal and may also be displayed on the verification screen, depending on the app implementation.

Passwords must contain at least ten characters.

Use a configured email provider for real email delivery. The console email backend is intended for local development.

## API Integration

The frontend communicates with the backend through the API client in `Frontend/src/api/`.

| Component | Responsibility |
|---|---|
| `src/api/client.ts` | Base API configuration, bearer-token headers, pagination, and error handling |
| `src/api/authApi.ts` | Authentication API integration and mock authentication option |
| `src/store/auth.ts` | Authentication state |
| `src/pages/VerifyOtp.tsx` | Verification-code screen |
| `src/api/contactsApi.ts` | Contacts, memories, history, and communication rhythm |
| `src/api/draftsReal.ts` | Backend-connected draft operations |
| `src/api/messagesApi.ts` | Message and check-in operations |
| `src/api/homeApi.ts` | Home dashboard data |
| `src/api/scheduleApi.ts` | Schedule data |
| `src/api/notificationsApi.ts` | Notification data |
| `src/api/drafts.ts` | Mock draft data |

### API Conventions

- **Base path:** `/api/v1/`
- **Authentication:** Bearer token
- **Mobile client header:** `X-Client: mobile`
- **Authentication response:** Access and refresh tokens are returned in the JSON body for the mobile client.
- **Pagination:** List retrieval supports paginated responses, with 25 items per page.
- **Error format:** API errors use the following structure:

```json
{
  "error": {
    "detail": "Error description"
  }
}
```

The frontend handles API errors through its shared error-message utility.

## Mock Data Configuration

Some frontend modules provide mock modes for development without connecting to the corresponding backend features.

The relevant flags are defined in these files:

| Module | Configuration |
|---|---|
| Authentication | `USE_MOCK_AUTH` in `src/api/authApi.ts` |
| Home dashboard | `USE_MOCK_HOME` in `src/api/homeApi.ts` |
| Drafts | `USE_MOCK_DRAFTS` in `src/api/drafts.ts` |

Set the relevant flag to `true` to enable its mock implementation, if supported by the current code.

Mock mode may not cover every application feature. Mock authentication should not be used as a production security mechanism.

## Settings and Notifications

Personal preferences, including the default communication channel, tone, and notification settings, are stored locally on the phone.

The backend does not currently provide a settings endpoint for these preferences.

In-app notifications are supported through the application's notification integration. Push notifications are not currently supported by the backend configuration described here.

## Common Problems and Solutions

| Problem | Possible solution |
|---|---|
| `Unable to resolve package` | Install the missing dependency using `npx expo install <package>` when appropriate. |
| `Can't reach the server` | Check the IP address, API URL, backend status, Wi-Fi connection, and firewall. |
| Changes to `.env` are not reflected | Restart Expo using `npx expo start -c`. |
| Browser CORS error | Check Django CORS configuration and restart the backend. |
| QR code keeps loading | Check network isolation and firewall settings, or try Expo tunnel mode. |
| Expo Go says the project is incompatible | Install the Expo Go version that matches the project's Expo SDK. |
| Verification email is not received | Check the email backend configuration and the backend terminal output. |
| API authentication fails | Check the token, `X-Client: mobile` header, and authentication endpoint response. |
| Push notifications do not arrive | Push notifications require additional backend and mobile notification configuration. |

## Security Notes

- Never commit `.env` files containing secrets.
- Keep API keys, database credentials, and production secrets out of source control.
- Use HTTPS for production API communication.
- Configure appropriate CORS, allowed hosts, and authentication settings before deployment.
- Do not use development email settings or mock authentication in production.

## Development Status

MyoneZ is a mobile application backed by a Django REST API. Features and integrations may vary by implementation and configuration.

Before production release, verify authentication, API permissions, message delivery, database configuration, and notification behavior.

## Credits

| Role | Contributor |
|---|---|
| Backend | [0Junaid0](https://github.com/0Junaid0) ([source repository](https://github.com/0Junaid0/MyoneZ)) |
| Frontend | [Sejon Hossain](https://github.com/Sejonhossain), [Nusrat Ahmmed Ekra](https://github.com/Nusratekra) |

## License

No license has been specified yet. Add a `LICENSE` file and update this section before distributing the project publicly.
