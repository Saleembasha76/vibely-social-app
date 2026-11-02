# Vibely — Social Media App Starter

A responsive React + Vite social app starter with a premium black/purple theme, sample feed, story row, search, sign-up/login UI, and Firebase integration hooks. Capacitor configuration is included for packaging the web app as an Android project.

## Important: what is and is not included
This is a **starter project**, not a finished production social network. Demo posts and interface work immediately. Real account creation and post uploads require your own Firebase project. Messaging, stories, notifications, comments, follows, moderation, and deployment need additional implementation and testing before public launch. No APK is included because it must be built and signed in an Android build environment.

## Requirements
- Node.js 20 or newer recommended
- npm
- A Firebase account for real users and posts
- Android Studio for building an Android APK

## Run locally
```bash
npm install
npm run dev
```
Open the local URL printed by Vite (usually http://localhost:5173).

## Connect Firebase
1. Create a project at https://console.firebase.google.com/
2. Register a Web App in Project settings and copy its configuration.
3. Paste the config into `src/firebase.js`.
4. In Firebase Authentication, enable Email/Password.
5. Create a Firestore database.
6. Enable Firebase Storage for photo uploads.
7. Set Firestore and Storage security rules before testing. Do not use open public write rules in production.

Example **starting point only** for Firestore rules (review and harden before launch):
```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signedIn() { return request.auth != null; }
    match /users/{uid} {
      allow read: if signedIn();
      allow create, update: if signedIn() && request.auth.uid == uid;
    }
    match /posts/{postId} {
      allow read: if true;
      allow create: if signedIn() && request.resource.data.uid == request.auth.uid;
      allow update, delete: if signedIn() && resource.data.uid == request.auth.uid;
      match /likes/{uid} {
        allow read: if true;
        allow create, delete: if signedIn() && request.auth.uid == uid;
      }
    }
  }
}
```
These rules are illustrative. Test them against your full app design and add validation, rate limits, abuse prevention, and appropriate privacy controls. For Storage, create restrictive rules that require authentication and validate file size/type.

## Build website
```bash
npm run build
```
The production website files are created in `dist/`. Deploy `dist/` using a host such as Firebase Hosting, Netlify, or another static host. Configure SPA rewrites if needed.

## Build Android project
Install Android Studio and its Android SDK first:
```bash
npm install
npx cap add android
npm run android:sync
npm run android:open
```
In Android Studio, build and test the app. For an installable APK use **Build > Build Bundle(s) / APK(s) > Build APK(s)**. For Play Store distribution, generate a signed release and follow current Play Console requirements.

## Before public launch
- Implement and test comments, follows, messages, stories, and notifications.
- Add account deletion, reporting, blocking, moderation, and privacy policy/terms.
- Add image upload validation, rate limits, abuse prevention, and secure database/storage rules.
- Review accessibility, analytics consent, data retention, and legal obligations.
