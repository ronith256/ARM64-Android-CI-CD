# Android CI/CD on ARM 64 Based Servers

This repository provides a seamless continuous integration and continuous delivery (CI/CD) pipeline for your Android projects, to be ran on ARM64 servers. 

## Prerequisites
- ARM64 based server.
- act_runner or something that can execute Github actions.
- Currently only supports Android-Platform-33

## Getting Started
### Workflow Setup
- Copy the GitHub Actions script provided and paste a .yml file in your workflows directory.

```
name: Android CI

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

env:
  ANDROID_HOME: ${{ github.workspace }}/android/sdk

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Download and unzip ARM64 Android SDK files
        run: |
          curl -LO https://github.com/ronith256/ARM64-Android-CI-CD/releases/download/33/armBuild33.zip
          unzip -q armBuild33.zip                    

      - name: Set up Android SDK
        run: |
          echo -e "\nsdk.dir=$ANDROID_HOME" >> local.properties
          echo -e "\nandroid.aapt2FromMavenOverride=$ANDROID_HOME/build-tools/33.0.3/aapt2" >> gradle.properties                    

      - name: Set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'temurin'
          cache: gradle
          java-home: /usr/lib/jvm/java-11-openjdk-arm64

      - name: Grant execute permission for gradlew
        run: chmod +x gradlew

      - name: Accept Android SDK licenses
        run: echo y | $ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --licenses --sdk_root=$ANDROID_HOME

      - name: Gradle build
        run: |
          ./gradlew build
```
## Notes
- If you want to build for a new platform, you'll have to compile Android Build tools for ARM64 from source.
- This was created so that I can create APKs using act_runner on my always free Oracle ARM64 server. 
