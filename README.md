centos7_lamp_stack
Apache &amp; EXIM | php5.6 | redis | mariadb | Supervisor | root ssh | varioius utilities | phpMyAdmin | Drush |NodeJS | Networking|Angular


Clone Repo:

git clone https://github.com/prasadgopale/centos7_lamp_stack.git .

For cenos and php7 stack go to dir:

cd centos7_lamp_stack_php7

create Dir:

mkdir -p project/html project/database

Build Image:

docker build  -t centos7 .

Run Docker image:

docker run -d --name smt_ui -p 8080:80 -p 8443:443 -p 8022:22 -p 6379:6379 -v `pwd`/html:/var/www/html -v `pwd`/database:/var/lib/phpMyAdmin/upload -t centos7:latest


