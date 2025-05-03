# rta-ctf-containers-writeup
Writeup - RTA "Introduction to Containers" Challenge

**Env:** Win / PowerShell 7.5.1

**Summary**

- Challenge 1 - lawyer
    - Manually override container entrypoint
- Challenge 2 - 
    - Offline container file extraction
- Challenge 3 - luser
    - Offline container file extraction
- Challenge 4 - professor
    - History inspection
- Challenge 5 - version
    - Inspect additional tags / Old version exposure
- Challenge 6 - onion
    - Image layer data exposure
- Challenge 7 - foreman
    - Create new minimal image using a layer from another image



## Intro

Run this container to get started:

```
docker run -it --rm XXXXXXXX.container-registry.com/introduction/challenge:latest
```

Welcome to the RTA "Introduction to Containers" challenges!

These challenges will test your knowledge of running containers and manipulating their images.

Each challenge will require you to download and run a container image. Each image contains a "secret.txt" file (typically in `/root`) that contains the next challenge's credentials. It'll also contain a brief description of what to expect.

To download and run each image, you will run the following commands:

- Login: `docker login [REGISTRY] -u [USER] -p [PASSWORD]`
- Download: `docker pull [IMAGE]`
- Run: `docker run -it --rm [IMAGE]`.

_NOTE: I recommend you run your containers with `-ti` and `--rm`._

---
```powershell
PowerShell 7.5.1
~/Downloads ❯ docker run -it --rm XXXXXXXX.container-registry.com/introduction/challenge:latest
```

```powershell
Unable to find image 'XXXXXXXX.container-registry.com/introduction/challenge:latest' locally
latest: Pulling from introduction/challenge
f18232174bc9: Pull complete
c0b334d3701d: Pull complete
Digest: sha256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Status: Downloaded newer image for XXXXXXXX.container-registry.com/introduction/challenge:latest

Welcome to the RTA "Introduction to Containers" challenges!

These challenges will test your knowledge of running containers and manipulating their images.

Each challenge will require you to download and run a container image. Each image contains a "secret.txt" file (typically in `/root`) that contains the next challenge's credentials. It'll also contain a brief description of what to expect.

To download and run each image, you will run the following commands:
Login: docker login [REGISTRY] -u [USER] -p [PASSWORD]
Download: `docker pull [IMAGE]`
Run: `docker run -it --rm [IMAGE]`.
NOTE: I recommend you run your containers with`-ti` and `--rm`.

----------

Challenge 1 - lawyer

Login command: docker login XXXXXXXX.container-registry.com/lawyer -u "XXXXXX" -p "XXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/lawyer/challenge:latest

Container images are often designed to run commands when a container is started. Depending on how this is configured, the command can accept arguments.
```

## Challenge 1 - lawyer


Login command: `docker login XXXXXXXX.container-registry.com/lawyer -u "XXXXXX" -p "XXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/lawyer/challenge:latest`

Hint: `Container images are often designed to run commands when a container is started. Depending on how this is configured, the command can accept arguments.`

### Login

```ps
~/Downloads ❯ docker login XXXXXXXX.container-registry.com/lawyer -u "XXXXXXXX" -p "XXXXXXXX"

WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

### Pull

```ps
~/Downloads ❯ docker pull XXXXXXXX.container-registry.com/lawyer/challenge:latest

latest: Pulling from lawyer/challenge
f18232174bc9: Already exists
45c8df668516: Pull complete
Digest: sha256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Status: Downloaded newer image for XXXXXXXX.container-registry.com/lawyer/challenge:latest
XXXXXXXX.container-registry.com/lawyer/challenge:latest
```

### Manually overriding the container entrypoint to extract the secret file

```ps
~/Downloads ❯ docker run -it --rm --entrypoint cat XXXXXXXX.container-registry.com/lawyer/challenge:latest /root/secret.txt

Challenge 2 - conquer
Login command: docker login XXXXXXXX.container-registry.com/conquer -u "XXXXXXXX" -p "XXXXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/conquer/challenge:latest

What are you options if your container doesn't accept arguments?
```

## Challenge 2 - conquer

Login command: `docker login XXXXXXXX.container-registry.com/conquer -u "XXXXXXXX" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/conquer/challenge:latest`

Hint: `What are you options if your container doesn't accept arguments?`

### Offline container file extraction

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker run -it --rm XXXXXXXX.container-registry.com/conquer/challenge:latest cat /root/secret.txt
Challenge 3 - luser
Login command: docker login XXXXXXXX.container-registry.com/luser -u "XXXXXXXX" -p "XXXXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/luser/challenge:latest

Can't access the file if you don't have permission.
```

## Challenge 3 - luser

Login command: `docker login XXXXXXXX.container-registry.com/luser -u "XXXXXXXX" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/luser/challenge:latest`

Hint: `Can't access the file if you don't have permission.`

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker run -it --rm XXXXXXXX.container-registry.com/luser/challenge:latest cat /root/secret.txt

/bin/sh: can't open 'cat': No such file or directory
```

### Offline container file extraction

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker create --name tempcontainer XXXXXXXX.container-registry.com/luser/challenge:latest
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker cp tempcontainer:/root/secret.txt .  
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ cat .\secret.txt

Challenge 4 - professor
Login command: docker login XXXXXXXX.container-registry.com/professor -u "XXXXXXXXr" -p "XXXXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/professor/challenge:latest

Would it be helpful if you could see the commands I ran? Well, I'm not gonna show you.
```

## Challenge 4 - professor

Login command: `docker login XXXXXXXX.container-registry.com/professor -u "XXXXXXXXr" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/professor/challenge:latest`

Hint: `Would it be helpful if you could see the commands I ran? Well, I'm not gonna show you.`

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker run -it --rm XXXXXXXX.container-registry.com/professor/challenge:latest

uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
```

### Secrets in docker history

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker history --no-trunc XXXXXXXX.container-registry.com/professor/challenge:latest

IMAGE                                                                     CREATED CREATED BY       SIZE      COMMENT
sha256:XXXXXXXXXXXXXXXXXXXXXXXX   16 hours ago   CMD["id"] 0B        buildkit.dockerfile.v0
<missing>                                                                 16 hours ago   RUN /bin/sh -c zip --password "XXXXXX" -m /root/secret.zip /root/secret.txt # buildkit   429B      buildkit.dockerfile.v0
<missing>                                                                 16 hours ago   COPY secret.txt /root/secret.txt # buildkit                                                    337B      buildkit.dockerfile.v0
<missing>                                                                 16 hours ago   RUN /bin/sh -c apk add zip # buildkit                                                          3.13MB    buildkit.dockerfile.v0
<missing>                                                                 2 months ago   CMD ["/bin/sh"]                                                                                0B        buildkit.dockerfile.v0
<missing>                                                                 2 months ago   ADD alpine-minirootfs-3.21.3-x86_64.tar.gz / # buildkit                                        7.83MB    buildkit.dockerfile.v0
```

### Offline container file extraction

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker create --name temp2 XXXXXXXX.container-registry.com/professor/challenge:latest

8d49c8c3b93909bb365092a6e1c9dab788fac1eae14ff1d56f739a0031847a85
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker cp temp2:/root/secret.zip .

Successfully copied 2.05kB to C:\Users\user\Downloads\CTF_RTA_DOCKER\.
```
- USE PASSWORD IN ```docker history``` command: ```XXXXXX```, to access the ```secret.txt``` file inside the ```secret.zip```

## Challenge 5 - version

Login command: `docker login XXXXXXXX.container-registry.com/version -u "XXXXXXXX" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/version/challenge:v8`

Hint: `It took me a few tries, but I think I've finally uploaded an image that doesn't have secrets in them.`


```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker run -it --rm XXXXXXXX.container-registry.com/version/challenge:v8

/ # cat /root/secret.txt
Keep trying
```

### Old version exposure

I almost got stuck here and greped every file on the system.....

Eventually I tried the other versions of the image as hinted in the challenge name.

Even then v5 was the last one I checked...

```ps
docker run -it --rm XXXXXXXX.container-registry.com/version/challenge:v5
```
```bash
/ # cat /root/secret.txt
Challenge 6 - onion
Login command: docker login XXXXXXXX.container-registry.com/onion -u "XXXXXXXX" -p "XXXXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/onion/challenge:latest

Okay, I've actually removed the secret this time. Definitely isn't there. No chance./ #
```

## Challenge 6 - onion

Login command: `docker login XXXXXXXX.container-registry.com/onion -u "XXXXXXXX" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/onion/challenge:latest`

Hint: `Okay, I've actually removed the secret this time. Definitely isn't there. No chance./ #`

```ps
docker pull XXXXXXXX.container-registry.com/onion/challenge:latest
```

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker history --no-trunc XXXXXXXX.container-registry.com/onion/challenge:latest
IMAGE                                                                     CREATED        CREATED BY                                                SIZE      COMMENT
sha256:XXXXXXXXXXXXXXXXXXXXXXXX   17 hours ago   ENTRYPOINT ["/bin/sh"]                                    0B        buildkit.dockerfile.v0
<missing>                                                                 17 hours ago   RUN /bin/sh -c touch /etc/surprise.txt # buildkit         0B        buildkit.dockerfile.v0
<missing>                                                                 17 hours ago   RUN /bin/sh -c touch /root/test.txt # buildkit            0B        buildkit.dockerfile.v0
<missing>                                                                 17 hours ago   WORKDIR /tmp                                              0B        buildkit.dockerfile.v0
<missing>                                                                 17 hours ago   RUN /bin/sh -c rm /root/secret.txt # buildkit             0B        buildkit.dockerfile.v0
<missing>                                                                 17 hours ago   COPY secret.txt /root/secret.txt # buildkit               456B      buildkit.dockerfile.v0
<missing>                                                                 2 months ago   CMD ["/bin/sh"]                                           0B        buildkit.dockerfile.v0
<missing>                                                                 2 months ago   ADD alpine-minirootfs-3.21.3-x86_64.tar.gz / # buildkit   7.83MB    buildkit.dockerfile.v0
```

### Image layer data exposure

```ps
docker create --name onion-temp XXXXXXXX.container-registry.com/onion/challenge:latest
2d2e8ba6893d986e54ef016da14b90b1d2f968ee6f944cd56c97c81175807ebf
```

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker export onion-temp > onion-export.tar
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ mkdir onion-fs
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker save XXXXXXXX.container-registry.com/onion/challenge:latest -o onion-image.tar
```

- Extract `onion-image.tar` and navigate to `./onion-image/blobs/sha256/`
- USE the ```dive``` tool to identify a layer containing secret.txt before it was removed. 
- APPEND .tar to the end of ```XXXXXXXXXXXXXXXXXXXXXXXX```
- UNZIP and VIEW ```XXXXXXXXXXXXXXXXXXXXXXXX.tar``` / root / secret.txt
```
Challenge 7 - foreman
Login command: docker login XXXXXXXX.container-registry.com/foreman -u "XXXXXXXX" -p "XXXXXXXX"
Pull command: docker pull XXXXXXXX.container-registry.com/foreman/challenge:latest

This next challenge will require a bit of fair play from you. Instead of trying to find secret.txt, I want you to build a new image and copy secret.txt to it. Once you've done that, access the secret however you want.
```

## Challenge 7 - foreman

Login command: `docker login XXXXXXXX.container-registry.com/foreman -u "XXXXXXXX" -p "XXXXXXXX"`

Pull command: `docker pull XXXXXXXX.container-registry.com/foreman/challenge:latest`

Hint: `This next challenge will require a bit of fair play from you. Instead of trying to find secret.txt, I want you to build a new image and copy secret.txt to it. Once you've done that, access the secret however you want.`

```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker history --no-trunc XXXXXXXX.container-registry.com/foreman/challenge:latest
IMAGE                                                                     CREATED        CREATED BY                              SIZE      COMMENT
sha256:XXXXXXXXXXXXXXXXXXXXXXXX   17 hours ago   COPY secret.txt secret.txt # buildkit   316B      buildkit.dockerfile.v0
```
```ps
~/Downloads/CTF_RTA_DOCKER ❯ docker save -o foreman-image.tar XXXXXXXX.container-registry.com/foreman/challenge:latest
```

### Create new minimal image using a layer from another image

```ps
~/Downloads/CTF_RTA_DOCKER ❯ mkdir foreman-image
```

- APPEND `.tar` to the end of ```XXXXXXXXXXXXXXXXXXXXXXXX``` and inspects its contents.
- CREATE a `Dockerfile` in the root of the extracted layer above.
- ADD the following to the `Dockerfile`
```yaml
FROM alpine:latest
COPY XXXXXXXXXXXXXXXXXXXXXXXX/ /
CMD ["less", "/secret.txt"]
```
```ps
~/Downloads/CTF_RTA_DOCKER/foreman-new-image ❯ docker build --no-cache -t foreman-new .                 30% 19/63GB
[+] Building 1.3s (7/7) FINISHED                                                                     docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                 0.0s
 => => transferring dockerfile: 162B                                                                                 0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                                     1.0s
 => [internal] load .dockerignore                                                                                    0.0s
 => => transferring context: 2B                                                                                      0.0s
 => [internal] load build context                                                                                    0.0s
 => => transferring context: 181B                                                                                    0.0s
 => [1/2] FROM docker.io/library/alpine:latest@sha256:XXXXXXXXXXXXXXXXXXXXXXXX  0.1s
 => => resolve docker.io/library/alpine:latest@sha256:XXXXXXXXXXXXXXXXXXXXXXXX  0.0s
 => => sha256:XXXXXXXXXXXXXXXXXXXXXXXX88c 9.22kB / 9.22kB                       0.0s
 => => sha256:XXXXXXXXXXXXXXXXXXXXXXXX 1.02kB / 1.02kB                       0.0s
 => => sha256:XXXXXXXXXXXXXXXXXXXXXXXX 581B / 581B                           0.0s
 => [2/2] COPY XXXXXXXXXXXXXXXXXXXXXXXX/ /                                   0.0s
 => exporting to image                                                                                               0.1s
 => => exporting layers                                                                                              0.0s
 => => writing image sha256:XXXXXXXXXXXXXXXXXXXXXXXX                         0.0s
 => => naming to docker.io/library/foreman-new                                                                       0.0s
```
```ps 
~/Downloads/CTF_RTA_DOCKER/foreman-new-image ❯ docker run --rm foreman-new                              30% 19/63GB
Congrats! You've completed all of the container challenges!

While I don't have any more containers for you to download, I recommend you attempt to escape a privileged container! Use the following command to setup the container: `docker run -ti --rm --privileged ubuntu`

https://www.youtube.com/watch?v=XXXXXXXXXX
```
