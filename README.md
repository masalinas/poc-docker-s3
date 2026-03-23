# Description
PoC Docker with AWS S3. We will use the [rclone docker plugin](https://hub.docker.com/r/rclone/docker-volume-rclone)

## Steps 01: Install FUSE (requirement for rclone)
```
$ sudo apt update && sudo apt install fuse3 -y
```
## Steps 02: Create the required plugin directories
```
$ sudo mkdir -p /var/lib/docker-plugins/rclone/config
$ sudo mkdir -p /var/lib/docker-plugins/rclone/cache
```

## Steps 03: Create the Rclone Config
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

## Steps 04: Install the Rclone Plugin
```
$ docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  args="-v"
```

## Steps 05: Create a bucket in AWS/Region
![AWS Bucket](captures/aws_bucket.png "AWS Bucket")

We can test that we our credentials we see our index.html file uploaded to the bucket using the AWS CLI

```
$ AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY \
AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY \
aws s3 ls s3://miweb-924628188769-us-east-1-an

2026-03-23 16:45:20         22 index.html
```

## Steps 06: Create the Docker Volume
```
$ docker volume create miweb \
  -d rclone \
  -o remote=mys3:miweb-924628188769-us-east-1-an \
  -o allow_other=true \
  -o vfs_cache_mode=full
```

## Steps 07: Start the Container
```
$ docker run -d \
  --name nginx-s3 \
  -p 8080:80 \
  -v miweb:/usr/share/nginx/html \
  nginx
```

![Docker Container](captures/container.png "Docker Container")

## Some links

- [rclone doc](https://rclone.org/docker/): official documentation