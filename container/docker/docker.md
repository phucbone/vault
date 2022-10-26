![[docker-presend.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-container/imgs-docker/docker-presend.png?raw=true)

---
# Giới thiệu

`Docker` là một `open platform` cung cấp các công cụ, serivce giúp đóng gói và chạy chương trình trên các môi trường khác nhau một cách nhanh, gọn, nhẹ.

`Docker` được viết bằng ngôn ngữ `Go`. 

`Docker` sẽ công cấp một không gian để làm việc là `containers`.

Giả sử có một project muốn gửi cho người khác ( ví dụ người mà không biết quá nhiều về lập trình) để chạy thử trên máy họ, người ta không cần biết bạn cài những công nghệ nào vào project để chạy, vì ta đã gói gọn các công nghệ trong `Docker` rồi, việc ta cần làm là đưa cho họ vài câu lệnh để chạy là xong, quả là tiện.

---
# Kiến trúc

Bao gồm 3 thành phần chính:  
+ `Client`: bao gồm các công cụ để ta tương tác với Docker Host như CLI, API hoặc Docker Desktop.  
+ `Docker` Host: bao gồm Docker Daemon là bộ não của Docker.  
+ `Registry`: nơi chứa Container Image.

![[docker-architecture.jpg]](https://github.com/phucbone/vault/blob/master/imgs/imgs-container/imgs-docker/docker-architecture.jpg?raw=true)

---
# Lợi ích

No | Benefit
---|---
1|Nhanh và nhẹ
2|Dễ dàng mở rộng ứng dụng
3|Giảm chi phí cơ sở hạ tầng
4|Tiện lợi hơn, tiết kiệm thời gian hơn trong quá trình cài đặt
5|Dễ dàng lựa chọn version của các công nghệ (mysql, php...). Tùy vào mục đích nhu cầu sử dụng bạn có thể cài đặt version bạn muốn
6|Cộng đồng phát triển lớn
7|Có thể tái sử dụng

---
# Các khái niệm cơ bản

![[docker-basic.png]](https://github.com/phucbone/vault/blob/master/imgs/imgs-container/imgs-docker/docker-basic.png?raw=true)

No | Benefit
---|---
1|Docker Image
2|Docker Container
3|Docker Hub
4|Dockerfile

## Docker Image

`Docker Image` có thể hiểu như là một khuôn mẫu để tạo ra `Container`, nó sẽ tạo ra `Container` khi chạy một `Docker Image` nào đó.

Một `Docker Image` sẽ định nghĩa môi trường và những thứ có bên trong môi trường đó (thư viện, biến môi trường hay các file cấu hình...).

Ta có thể tự tạo ra riêng một `Docker Image` của mình hoặc là lấy `Docker Image` của người khác về dùng cũng được nếu public.

## Docker Container

Người ta thường hay liên tưởng `Docker Image` và `Container` giống như trong lập trình hướng đối tượng.

Nếu `Docker Image` là class, thì `Container` chính là thực thể của class đó.

Từ một `Docker Image` chúng ta có thể tạo ra hàng chục `Container` khác nhau.

Ta có thể hiểu `Container` chính là một máy ảo, nhưng tốc độ của nó thì nhanh hơn rất nhiều.

Nếu như bật nhiều chương trình máy ảo như `VMware` lên thì tình trạng treo máy rất dễ xảy ra, nhưng khi chạy cả chục con `Container` lên thì máy vẫn bình thường.

Ta có thể start, stop, move, delete, create container thông qua việc sử dụng `Docker API` hoặc là `Docker CLI`.

## Docker Hub

Đơn giản `Docker Hub` là nơi dùng để lưu trữ các `Docker Image`, giống như `Github` hay `Gitlab` chứa source code vậy.

Ta có thể pull hoặc push `Docker Image` lên `Docker Hub` để lưu trữ cũng như public cho cộng đồng sử dụng.

## Dockerfile

`Dockerfile` là một file dạng text, không có đuôi, bên trong file này chứa một tập các câu lệnh để tạo ra `docker image`.

Một số lệnh cơ bản trong `Dockerfile`

Command|Description
---|---
`ARG`|Định nghĩa parameters (biến) được dùng trong quá trình build image
`FROM <DOCKER_IMAGE>:<IMAGE_TAG>`|Đây là lệnh bắt buộc trong `Dockerfile` nhằm để chỉ `Docker Image` này được build từ đầu ra (từ `Docker Image` gốc nào).
`RUN <COMMAND>`|Dùng để thực thi một câu lệnh nào đó trong quá trình build image, có thể có nhiều lệnh `RUN` trong một `Dockerfile`
`CMD <COMMAND>`|Trong `Dockerfile` chỉ tồn tại duy nhất một lệnh `CMD`, lệnh này dùng để thực thi câu lệnh trong quá trình bật `Container`
`LABEL`|Dùng để cung cấp metadata cho `Docker Image`, có thể dùng để thêm thông tin của maintainer. Để xem thông tin `LABEL` sử dụng lệnh `docker image inspect`
`EXPOSE`|Lệnh này để cho `Docker` biết `Container` sẽ lắng nghe trên các cổng mạng được chỉ định khi chạy
`ENV <KEY>=<VALUE>`|Được dùng để định nghĩa biến môi trường trong `Container`
`ADD <SOURCE> <DESTINATION>`|Dùng để copy một thư mục ở Local hoặc một remote file nào đó (định nghĩa bằng `<SOURCE>`) vào một thư mục nào đó trong `Container` (định nghĩa bằng `<DESTINATION>`)
`ENTRYPOINT <COMMAND>`|Định nghĩa những lệnh sẽ được thực thi trong quá trình `Container` chạy, những câu lệnh này thường được viết trong file `.sh`
`VOLUME`|Được sử dụng để mount thư mục từ Local vào trong `Container`
`WORKDIR`|Thiết lập thư mục đang làm việc cho các chỉ thị khác như `CMD`, `RUN`, `ENTRYPOINT`, `COPY`, `ADD`

### Tạo Dockerfile đơn giản

#### Khởi tạo

Tạo một file `Dockerfile` với nội dung

```Dockerfile
FROM ubuntu

MAINTAINER demo

WORKDIR /app

RUN apt-get update

COPY . .

CMD ["/script.sh"]
```

và một file `script.sh`

```bash
#! bin/sh

echo "This is Demo-SCRIPT"

```

Giải thích

Command|Description
---|---
`FROM ubuntu`|Định nghĩa Image gốc của Image mình sẽ tạo là ở môi trường nào (môi trường này đã phải được build thành image). Image ở đây sử dụng là phiên bản Offical `ubuntu` và lấy trên `Docker Hub`
`MAINTAINER demo`|Định nghĩa tác giả của Image
`WORKDIR /app`|Thư mục sẽ được tạo trong Image tên là `app` và chuyển phiên làm việc của mình vào thư mục này. Lệnh này tương đương với lệnh `mkdir /app && cd /app` trong `ubuntu`
`COPY . .`|Hai dấu `.` có nghĩa là copy tất cả code các kiểu ở trong thư mục hiện tại bạn đang làm việc ở Host vào bên trong thư mục `/app` của `Container`.
`CMD ["/script.sh"]`|Chạy lệnh `/script.sh` khi khởi tạo `Container` từ Image

### Build image

```bash
docker build -t demo-image:v1 .
```

Giải thích

What|Description
---|---
`docker build`|Lệnh để build `Docker Image`
`-t demo-image:v1`|Tạo `Docker Image` với tham số `IMAGE_NAME:IMAGE_TAG`=`demo-image:v1`
`.`|Tìm file `Dockerfile` ở folder hiện tại rồi build image

---
# Các khái niệm khác

Command|Description
---|---
`Docker Toolbox`|Là tool của `Docker` được sử dụng trên MacOS và WIndows
`Docker Engine`|Hoạt động như một ứng dụng client-server, `Docker Engine` cung cấp cho chúng ta function để có thể làm việc được với `Docker Image` `hay Docker Container`. Để sử dụng các function này chúng ta có thể sử dụng `Rest API` hoặc `CLI` của `Docker`. (`docker run ...`)
`Docker Trusted Registry`|Giống như `Docker Hub` nhưng là ở một phiên bản Private tuy nhiên chúng ta sẽ mất phí. Nếu công ty nào đó cần bảo mật, Private Image thì sẽ dùng đến registry này.
`Docker Registry`|Tương tự với `Docker Trusted Registry` nhưng là một phiên bản không mất phí, open source (Github registry...)
`Docker machine`|Là công cụ giúp cài đặt `Docker Engine` lên môi trường máy ảo một cách tự động.
`Docker Swarm`|Có thể hiểu là nó là một thằng trung gian giữa bạn và các `Docker Host`, nó sẽ tập trung các `Docker Host` về một khối. Khi làm việc ta sẽ làm việc với `Virtual Host` mà `Docker Swarm` tạo ra.
`Docker Cloud`|Là hệ thống PAAS cho phép bạn dễ dàng triển khai các APP của mình lên môi trường Cloud.
`Docker Daemon`|`Docker Daemon` chạy trên các máy Host. Người dùng sẽ không tương tác trực tiếp với các Daemon, mà thông qua `Docker Client`. `Docker` sử dụng kiến trúc client-server. `Docker Client` sẽ liên lạc với các `Docker Daemon`, các `Docker Daemon` sẽ thực hiện các tác vụ build, run và distribuing các `Docker Container`. Cả `Docker Client` và `Docker Daemon` có thể chạy trên cùng 1 máy, hoặc có thể kết nối theo kiểu `Docker Client` điều khiển các `Docker Daemon` giao tiếp với nhau thông qua socket hoặc RESTful API.
`Docker Client`|Là giao diện người dùng của `Docker`, nó cung cấp cho người dùng giao diện dòng lệnh và thực hiện phản hồi với các `Docker Daemon`.

---
# Docker CLI

## Run Containers

COMMAND | DESCRIPTION
---|---
`docker run IMAGE` | Start a new container
`docker run --name CONTAINER_NAME IMAGE` | Start a new container and set a name
`docker run -p HOST_PORT:CONTAINER_PORT IMAGE` | Start a new container with mapped ports
`docker run -P IMAGE` | Start a new container and map all ports

## Container Management

COMMAND | DESCRIPTION | DESCRIPTION (VN)
---|---|---
`docker create IMAGE` | Create a new container | Tạo mới 1 container từ IMAGE
`docker start CONTAINER` | Start a container | Start 1 container
`docker stop CONTAINER` | Graceful stop a container | Stop 1 container
`docker kill CONTAINER` | Kill (SIGKILL) a container | Kill 1 container
`docker restart CONTAINER` | Graceful stop and restart a container | Restart 1 container
`docker pause CONTAINER` | Suspend a container | Pause 1 container
`docker unpause CONTAINER` | Resume a container | Resume 1 container
`docker rm CONTAINER` | Destroy a container | Delete 1 container

## Container Bulk Management

COMMAND | DESCRIPTION | DESCRIPTION (VN)
---|---|---
`docker stop $(docker ps -q)` | To stop all the running containers | Stop tất cả các containers đang running
`docker stop $(docker ps -a -q)` | To stop all the stopped and running containers| Stop tất cả các containers đang running và stopped
`docker kill $(docker ps -q)` | To kill all the running containers | Kill tất cả các containers đang running
`docker kill $(docker ps -a -q)` | To kill all the stopped and running containers| Kill tất cả các containers đang running và stopped
`docker restart $(docker ps -q)` | To restart all running containers| Restart tất cả các containers đang running
`docker restart $(docker ps -a -q)` | To restart all the stopped and running containers| Restart tất cả các containers đang running và stopped
`docker rm $(docker ps -q)` | To destroy all running containers | Xóa tất cả các containers đang running
`docker rm $(docker ps -a -q)` | To destroy all the stopped and running containers | Xóa tất cả các containers đang running và stopped
`docker pause $(docker ps -q)` | To pause all running containers | Pause tất cả các containers đang running
`docker pause $(docker ps -a -q)` | To pause all the stopped and running containers | Pause tất cả các containers đang running và stopped
`docker start $(docker ps -q)` | To start all running containers | Start lại tất cả các containers đang running
`docker start $(docker ps -a -q)` | To start all the stopped and running containers | Start lại tất cả các containers đang running và stopped
`docker rm -vf $(docker ps -a -q)` | To delete all containers including its volumes use | Delete tất cả các containers bao gồm cả volumes của nó
`docker rmi -f $(docker images -a -q)` | To delete all the images | Delete tất cả các images
`docker system prune` | To delete all dangling and unused images, containers, cache and volumes | Delete tất cả các containers, cache, volumes, images không sử dụng
`docker system prune -a` | To delete all used and unused images | Delete tất cả các containers, cache, volumes, images đang sử dụng và không sử dụng
`docker system prune --volumes` | To delete all docker volumes | Delete tất cả docker volumes

## Inspect Containers

COMMAND | DESCRIPTION
---|---
`docker ps` | List running containers
`docker ps -a` | List all containers, including stopped
`docker logs CONTAINER` | Show a container output
`docker logs -f CONTAINER` | Follow a container output
`docker top CONTAINER` | List the processes running in a container
`docker diff` | Show the differences with the image (modified files)
`docker inspect` | Show information of a container (json formatted)

## Run Commands

COMMAND | DESCRIPTION
---|---
`docker attach CONTAINER` | Attach to a container
`docker cp CONTAINER:PATH HOSTPATH` | Copy files from the container
`docker cp HOSTPATH CONTAINER:PATH` | Copy files into the container
`docker export CONTAINER` | Export the content of the container (tar archive)
`docker exec CONTAINER` | Run a command inside a container
`docker exec -it CONTAINER /bin/bash` | Open an interactive shell inside a container (there is no bash in some images, use /bin/sh)
`docker wait CONTAINER` | Wait until the container terminates and return the exit code

## Images

COMMAND | DESCRIPTION
---|---
`docker images` | List all local images
`docker history IMAGE` | Show the image history
`docker inspect IMAGE` | Show information (json formatted)
`docker tag IMAGE TAG` | Tag an image
`docker commit CONTAINER IMAGE` | Create an image (from a container)
`docker import URL` | Create an image (from a tarball)
`docker rmi IMAGE` | Delete images
`docker pull REPO:[TAG]` | pull an image/repo from a registry
`docker push REPO:[TAG]` | push and image/repo to a registry
`docker search TEXT` | Search an image on the official registry
`docker login` | Login to a registry
`docker logout` | Logout from a registry
`docker save REPO:[TAG]` | Export an image/repo as a tarball
`docker load` | Load images from a tarball

## Volumes

COMMAND | DESCRIPTION
---|---
`docker volume ls` | List all vol1umes
`docker volume create VOLUME` | Create a volume
`docker volume inspect VOLUME` | Show information (json formatted)
`docker volume rm VOLUME` | Destroy a volume
`docker volume ls --filter="dangling=true"` | List all dangling volumes (not referenced by any container)
`docker volume prune` | Delete all volumes (not referenced by any container)

### Backup a container
Backup docker data from inside container volumes and package it in a tarball archive.
`docker run --rm --volumes-from CONTAINER -v $(pwd):/backup busybox tar cvfz /backup/backup.tar CONTAINERPATH`

### Restore container from backup
Restore the volume with a tarball archive.
`docker run --rm --volumes-from CONTAINER -v $(pwd):/backup busybox sh -c "cd CONTAINERPATH && tar xvf /backup/backup.tar --strip 1"`
## Networks

## Troubleshooting
### Networking
`docker run --name netshoot --rm -it nicolaka/netshoot /bin/bash`

