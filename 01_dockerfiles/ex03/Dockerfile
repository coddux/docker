FROM debian

# update & install required packages
RUN apt-get update \
    && apt-get dist-upgrade -y \
    && DEBIAN_FRONTEND=noninteractive \
    && apt-get -qq -y install --no-install-recommends \
       ca-certificates curl openssh-server postfix

# download & install Gitlab CE
RUN EXTERNAL_URL="https://gitlab.example.com"
RUN curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN apt-get update && apt-get -y install --no-install-recommends gitlab-ce \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# work-around sysctl read-only
RUN sed -i -e 's~command "cat /etc/sysctl.conf /etc/sysctl.d/\*.conf  | sysctl -e -p -"~command "cat /etc/sysctl.conf"~g' /opt/gitlab/embedded/cookbooks/package/resources/sysctl.rb

# create ssl certificate
RUN mkdir /etc/gitlab/ssl/
RUN openssl req -nodes -newkey rsa:2048 -keyout /etc/gitlab/ssl/gitlab.example.com.key -out /etc/gitlab/ssl/gitlab.example.com.pem -subj "/C=FR/ST=Rhone/L=Lyon/O=Global Security/OU=IT Department/CN=gitlab.example.com"
RUN openssl x509 -req -days 365 -in "/etc/gitlab/ssl/gitlab.example.com.pem" -signkey "/etc/gitlab/ssl/gitlab.example.com.key"  -out "/etc/gitlab/ssl/gitlab.example.com.crt"

# configure https
RUN sed -i -e "s~external_url 'http://gitlab.example.com'~external_url 'https://gitlab.example.com'~g" /etc/gitlab/gitlab.rb
RUN sed -i -e "s/# nginx\['redirect_http_to_https'\] = false/nginx\['redirect_http_to_https'\] = true/g" /etc/gitlab/gitlab.rb
RUN sed -i -e "s/# nginx\['redirect_http_to_https_port/nginx\['redirect_http_to_https_port/g" /etc/gitlab/gitlab.rb

# expose https & ssh
EXPOSE 443 22

# reload configuration & launch gitlab-ce & launch ssh daemon
ENTRYPOINT (/opt/gitlab/embedded/bin/runsvdir-start&EXTERNAL_URL="localhost") && gitlab-ctl reconfigure && /etc/init.d/ssh restart && tail -f /dev/null


# FREE container & image:
# docker container rm --force $(docker container ls -aq)
# docker rmi $(docker images -aq)

# BUILD: docker build -t dev-gitlabce .
# RUN  : docker run -it -p 8022:22 -p 8443:443 --name gitlab101 dev-gitlabce

# VALIDATE:
# bash : docker exec -it gitlab101 /bin/bash
# https: https://0.0.0.0:8443
# ssh  : ssh -T git@0.0.0.0 -p 8022 (see setup & config here after) --> result "Welcome to GitLab, @root!"
#  cat ~/.ssh/id_rsa.pub
#  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCxOkuhgmb/XYn3RFW/TW68hsEokBNs02ouz4uUbDiQZDZ9sAgLgvuW0AaOB8o+LU0woSgST/SX3jCJ6ramXtGNEaUvdfelfdqktU+5/Nn2DCGwu79h0EyZRO9kbsgjH61py3BmlvJBFCvtSHzCF7jH4nymxLwK3EjRQnDqCm1tUjI02wahs47JOSeOrNXMAxoJSw5uGYxs9w96YzeR4TZEBu87HuR4lAXkPosUFNUVOhOrihpypvf3fb/nzVqUAixuErBbeCCRtwEdtUjTyr4Eal4YnzGj2DHfSEa9U5ZKkdOuOiquLSF8th8l6Z7+q9n9r1mMHt8UMUpt8zzUu2HP alepercq@z2r6p2.le-101.fr
#  avatar -> settings -> ssh keys

# REFERENCES:
# [Gitlab Readme]         Omnibus GitLab: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md
# [last release: 11.10.4] gitlab-ce release: https://gitlab.com/gitlab-org/gitlab-ce/tags
# [last debian package]   gitlab-ce package: https://packages.gitlab.com/gitlab/gitlab-ce/
# [file system read-only] configuration fails, sysctl key is read-only: https://gitlab.com/gitlab-org/omnibus-gitlab/issues/893
# [nginx https]           NGINX settings: https://docs.gitlab.com/omnibus/settings/nginx.html
# [ssl certificat]        Create CSR using OpenSSL Without Prompt: https://www.shellhacks.com/create-csr-openssl-without-prompt-non-interactive/
# [ssh connection]        GitLab and SSH: https://docs.gitlab.com/ee/ssh/
