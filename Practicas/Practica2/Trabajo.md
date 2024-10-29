## Zuhaitz:
sudo snap install docker

sudo apt install ./code_1.95.0-1730153583_amd64.deb

git clone https://github.com/ferrarimarco/docker-pxe.git

docker run --cap-add=NET_ADMIN -it --rm --net=host ferrarimarco/pxe

docker exec -it <docker id> /bin/sh

docker run --cap-add=NET_ADMIN -it --rm --net=host -v /home/student/Desktop/mount/:/var/lib/tftpboot ferrarimarco/pxe


## Rubén:

## Olatz:
