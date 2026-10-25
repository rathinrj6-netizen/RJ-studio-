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
          if [ -f RJ-Studio-Project.zip
