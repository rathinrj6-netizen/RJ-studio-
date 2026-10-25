# RJ-studio-
Demo 
name: Build RJ Studio APK

on:
  workflow_dispatch:
  push:
    paths:
      - 'RJ-Studio-Project.zip'
      - '.github/workflows/build.yml'

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Get project
        uses: actions/checkout@v4

      - name: Extract project ZIP
        run: |
          if [ -f RJ-Studio-Project.zip ]; then
            mkdir -p project
            unzip -o RJ-Studio-Project.zip -d project
          else
            mkdir -p project
            cp -R . project/
            rm -rf project/.git project/project
          fi

      - name: Find Android project
        run: |
          FILE=$(find project -name gradlew -type f | head -n 1)
          if [ -z "$FILE" ]; then
            echo "ERROR: gradlew missing from project"
            exit 1
          fi
          echo "GRADLEW=$FILE" >> "$GITHUB_ENV"

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Build APK
        run: |
          chmod +x "$GRADLEW"
          cd "$(dirname "$GRADLEW")"
          ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: RJStudio-APK
          path: project/**/build/outputs/apk/debug/*.apk
          if-no-files-found: error
          retention-days: 30
