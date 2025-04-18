---
layout: default
title: Action rpmbuild
parent: Git
nav_order: 2
---

# Action rpmbuild

___

## github a action

```
name: Build RPM Package and Release

on: workflow_dispatch

jobs:
  build-and-release:
    runs-on: ubuntu-latest

    steps:
      - name: get the code
        uses: actions/checkout@v3

      - name: set timestamp
        run: echo "now=$(date +%Y.%m.%d.%H%M%S)" >> ${GITHUB_ENV}

      - name: make rpm
        uses: bpicode/github-action-fpm@master
        with:
          fpm_args: '[[file]] [[file2]] [[...]]'
          fpm_opts: >
            -n [[name]]
            -a [[architecture]]
            -t [[package format (rpm,deb...)]] -s dir
            -v [[version]]
            -d [[dependencies]]
            -m [[maintainer]]
            --rpm-auto-add-directories
            --prefix [[path_install]]
            --rpm-summary "[[description]]"
            --rpm-attr [[mode]],[[user]],[[group]]:[[file]]
            --chdir [[wrk_dir]]
            --license [[license]]

      - name: publish release
        id: create_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: [[tag]]
          release_name: [[release]]
          body: |
            - [[version description]]
          draft: false
          prerelease: false

      - name: publish rpm
        id: upload_release_asset
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: "[[path]]"
          asset_name: [[name]]
          asset_content_type: application/x-rpm
```