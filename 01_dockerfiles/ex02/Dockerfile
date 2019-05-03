FROM ruby:2.3

# update & install required packages
RUN apt-get update && apt-get install -y nodejs && apt-get clean
RUN gem install rails --version 4.2.4

# copy app files to container directory /opt/app
ONBUILD RUN mkdir /opt/app
ONBUILD COPY app /opt/app
ONBUILD WORKDIR /opt/app

# expose ft-rails port
ONBUILD EXPOSE 3000

# install specific app files
ONBUILD RUN bundle install

# migrate app db
ONBUILD RUN rake db:migrate

# population app db
ONBUILD RUN rake db:seed


# FREE container & image:
# docker container rm --force $(docker container ls -aq)
# docker rmi $(docker images -aq)

# BUILD: docker build -t ft-rails:on-build .

# BUILD & RUN:
# cd validation
# docker build -t ft-rails:valid .
# docker run -it -p 3000:3000 --name rails101 ft-rails:valid

# VALIDATE: 
# http: http://0.0.0.0:3000
# bash: docker exec -it rails101 /bin/bash

# REFERENCES:
# [ruby:2.3]               Ruby 2.4/4 stack level too deep: https://stackoverflow.com/questions/41504106/ruby-2-4-and-rails-4-stack-level-too-deep-systemstackerror
# [sqlite3 1.3.13]         Gem::LoadError: Specified 'sqlite3': https://stackoverflow.com/questions/24336809/gemloaderror-specified-sqlite3-for-database-adapter-but-the-gem-is-not-loa
# [app: "rails new blog"]  Sample project in ruby on rails: https://medium.com/@khandelwal12nidhi/sample-project-in-ruby-on-rails-d2fd35c2ec9c
