name: Build VIKI SOCIAL Android APK & AAB

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Source Code
      uses: actions/checkout@v3

    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: 18

    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        distribution: 'zulu'
        java-version: '17'

    - name: Install Dependencies & Prepare Capacitor
      run: |
        npm install
        npm run build
        npx cap add android
        npx cap copy android

    - name: Build Android Release APK & Bundle (AAB)
      run: |
        cd android
        chmod +x gradlew
        ./gradlew assembleRelease
        ./gradlew bundleRelease

    - name: Upload APK Artifact
      uses: actions/upload-artifact@v3
      with:
        name: VIKI-SOCIAL-Release-APK
        path: android/app/build/outputs/apk/release/app-release-unsigned.apk

    - name: Upload AAB Artifact
      uses: actions/upload-artifact@v3
      with:
        name: VIKI-SOCIAL-Release-AAB
        path: android/app/build/outputs/bundle/release/app-release.aab