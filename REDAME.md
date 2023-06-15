# artifacts2release
A small Github Action for publishing artefacts to an existing release.

## Usage
This Action should run after creating artifacts,
inside it own job running on `ubuntu`.

At the example below you can see an minimal workflow,
which publishs all previous created artifacts to release.
Make sure your `GITHUB_TOKEN` has enable write access.
Ensure also artifacts2release runs after the jobs, which are creating the artifacts, by using `needs: [SOME_JOBS]`
and that it is only run once per Release by checking the release type.


```yml
on:
  push:
  pull_request:
  release:
    types: [released]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/upload-artifact@v3
        with:
          name: foo
          path: foo
      - uses: actions/upload-artifact@v3
        with:
          name: dir
          path: dir
    
  github_publish:
    runs-on: ubuntu-latest
    needs: [build]
    if: ${{ github.event_name == 'release' }}
    steps:
    - uses: actions/checkout@v3 #needed to get github.event.release.tag_name 
    - name: Upload Release Artifacts
      uses: LuckyTurtleDev/artifacts2release@main
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name              | Required | Description                                                  | Type   | Default                              |
|:-----------------:|:--------:|:-------------------------------------------------------------|:------:|:------------------------------------:|
| github_token      |    ✔     | GitHub secret token, usually `${{secrets.GITHUB_TOKEN}}`.   | string |                                      |
| release_name      |          | Github release to which the artifacts are uploaded.          | string | `${{github.event.release.tag_name}}` |
| artifacts         |          | Artifacts, which will be uploaded. (default all)             | string |                                      |
