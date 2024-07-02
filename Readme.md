This is a simple one file project to build a multi stage docker image.

Run the following commands to see the difference-

docker build -t app .

docker build -t multi_stage -f Dockerfile_with_multi_stage .

simple workflow file-
name: My First GitHub Actions
on: [push]
jobs:
  action-job:
    runs-on: self-hosted
    steps:
    - uses: actions/checkout@v1
    - name: Build and push image
      run: |
        docker build -t multi_stage -f Dockerfile_with_multi_stage .
