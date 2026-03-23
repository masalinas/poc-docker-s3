# Description
PoC Docker with AWS S3

## Steps 01: Install FUSE (requirement for rclone)
```
$ sudo apt update && sudo apt install fuse3 -y
```
## Steps 02: Create the required plugin directories
```
$ sudo mkdir -p /var/lib/docker-plugins/rclone/config
$ sudo mkdir -p /var/lib/docker-plugins/rclone/cache
```

## Steps 03: Create the required plugin directories
```
$ sudo mkdir -p /var/lib/docker-plugins/rclone/config
$ sudo mkdir -p /var/lib/docker-plugins/rclone/cache
```

## Steps 04: Create the Rclone Config
```
$ sudo nano /var/lib/docker-plugins/rclone/config/rclone.conf

[mys3]
type = s3
provider = AWS
access_key_id = YOUR_ACCESS_KEY
secret_access_key = YOUR_SECRET_KEY
region = us-east-1
endpoint = s3.us-east-1.amazonaws.com
```

## Steps 05: Install the Rclone Plugin
```
$ docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  args="-v"
```

## Steps 06: Create a bucket in AWS/Region
![AWS Bucket](captures/aws_bucket.png "AWS Bucket")

## Steps 07: Create the Docker Volume
```
$ docker volume create miweb \
  -d rclone \
  -o remote=mys3:miweb-924628188769-us-east-1-an \
  -o allow_other=true \
  -o vfs_cache_mode=full
```

## Steps 08: Start the Container
```
$ docker run -d \
  --name nginx-s3 \
  -p 8080:80 \
  -v miweb:/usr/share/nginx/html \
  nginx
```

![Docker Container](captures/container.png "Docker Container")
