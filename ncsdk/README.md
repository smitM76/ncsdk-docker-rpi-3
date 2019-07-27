
# To build:
`docker build -t ncsdk-alpr:0.1 .`

# To run:
`docker run --net=host --privileged -v /dev:/dev --name ncsdk -i -t ncsdk-alpr:0.1 /bin/bash`

# Run existing container
`docker start -a -i ncsdk`

# Reference
- [#Privileged Docker Containers] section
https://movidius.github.io/ncsdk/docker.html

