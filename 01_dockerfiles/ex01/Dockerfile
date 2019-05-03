FROM debian

# update debian packages
RUN apt-get update && apt-get dist-upgrade -y && apt-get install -y wget bzip2 && apt-get clean

# Teamspeak server environment
ENV TS3SERVER_LICENSE=accept

RUN mkdir /usr/local/src/ts3
WORKDIR /usr/local/src/ts3

# Teamspeak serveur download
RUN wget http://dl.4players.de/ts/releases/3.0.13.8/teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
RUN tar xvjf teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
RUN rm teamspeak3-server_linux_amd64-3.0.13.8.tar.bz2
RUN mv teamspeak3-server_linux_amd64 teamspeak3

RUN mv /usr/local/src/ts3/teamspeak3 /usr/local/share
WORKDIR /usr/local/share/teamspeak3

EXPOSE 9987/udp 10011 30033

# Teamspeak serveur start & rename container host
ENTRYPOINT ./ts3server_minimal_runscript.sh


# FREE container & image:
# docker container rm --force $(docker container ls -aq)
# docker rmi $(docker images -aq)

# BUILD: docker build -t dev-teamspeak .
# RUN  : docker run -it --rm -p=9987:9987/udp -p=10011:10011 -p=30033:30033 --name teamspeak101 dev-teamspeak

# VALIDATE:
# connect with Teamspeak: connect to serveur 127.0.0.1 & copy serveur token
# bash: docker exec -it teamspeak101 /bin/bash
