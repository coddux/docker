FROM alpine:latest

# update alpine packages
RUN apk update && apk upgrade && apk add --no-cache vim

# launch vim
CMD vim


# FREE container & image:
# docker container rm --force $(docker container ls -aq)
# docker rmi $(docker images -aq)

# BUILD: docker build -t dev-alpvim .
# RUN  : docker run -it --name alpinvim101 dev-alpvim

# VALIDATE:
# vim : :Explore (vim explore command)
# bash: docker exec -it alpinvim101 /bin/sh
