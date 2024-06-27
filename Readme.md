This is a simple one file project to build a multi stage docker image.

Run the following commands to see the difference-

docker build -t app .

docker build -t multi_stage -f Dockerfile_with_multi_stage .

