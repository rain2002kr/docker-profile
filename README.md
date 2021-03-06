# docker-profle

# DOCKER 명령어

    FROM busybox
    ENV FOO=/bar
    WORKDIR ${FOO}   # WORKDIR /bar
    ADD . $FOO       # ADD . /bar
    COPY \$FOO /quux # COPY $FOO /quux

    ADD
    COPY
    ENV
    EXPOSE
    FROM
    LABEL
    STOPSIGNAL
    USER
    VOLUME
    WORKDIR
    ONBUILD (when combined with one of the supported instructions above)

# .dockerignore file

# FROM 명령어

    FROM [--platform=<platform>] <image> [AS <name>]
    FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
    FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]

    ARG VERSION=latest
    FROM busybox:$VERSION
    ARG VERSION
    RUN echo $VERSION > image_version

# RUN 명령어

    RUN has 2 forms 폼:
    1. RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
    2. RUN ["executable", "param1", "param2"] (exec form)

    두줄 명령어
    RUN /bin/bash -c 'source $HOME/.bashrc; \
    echo $HOME'

    한줄 명령어
    RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'

    이런 형태도 가능
    RUN ["/bin/bash", "-c", "echo hello"]

# CMD 명령어

    The CMD instruction has 3 forms 폼:
    CMD 명령어는 마지막 한줄만 실행된다.

    1. CMD ["executable","param1","param2"] (exec form, this is the preferred form)
    2. CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
    3. CMD command param1 param2 (shell form)

    이런 형태
    FROM ubuntu
    CMD echo "This is a test." | wc -

    이런 형태도 가능
    FROM ubuntu
    CMD ["/usr/bin/wc","--help"]

# LABEL 명령어 <META DATA>

    LABEL <key>=<value> <key>=<value> <key>=<value> ...
    LABEL "com.example.vendor"="ACME Incorporated"
    LABEL com.example.label-with-value="foo"
    LABEL version="1.0"
    LABEL description="This text illustrates"

    이렇게 포멧 옵션으로 작성 가능
    docker image inspect --format='' myimage
    {
    "com.example.vendor": "ACME Incorporated",
    "com.example.label-with-value": "foo",
    "version": "1.0",
    "description": "This text illustrates that label-values can span multiple lines.",
    "multi.label1": "value1",
    "multi.label2": "value2",
    "other": "value3"
    }

# EXPOSE 명령어 <도커 콘테이너 포트 및 TCP/UDP/ 세팅>

EXPOSE <port> [<port>/<protocol>...]

    한개만 사용시
        EXPOSE 80/udp

    두개다 사용시
        EXPOSE 80/tcp
        EXPOSE 80/udp

docker run -p 80:80/tcp -p 80:80/udp ...

# ENV 명령어 <컨테이너 환경설정>

ENV <key>=<value> ...

    ENV MY_NAME="John Doe"
    ENV MY_DOG=Rex\ The\ Dog
    ENV MY_CAT=fluffy

    ENV MY_NAME="John Doe" MY_DOG=Rex\ The\ Dog \
    MY_CAT=fluffy

    예시
    ARG DEBIAN_FRONTEND=noninteractive
    RUN apt-get update && apt-get install -y ...

# ADD 명령어

두가지 형태
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]

    ADD --chown=55:mygroup files* /somedir/
    ADD --chown=bin files* /somedir/
    ADD --chown=1 files* /somedir/
    ADD --chown=10:11 files* /somedir/

# COPY 명령어

    COPY [--chown=<user>:<group>] <src>... <dest>
    COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]

    COPY --chown=55:mygroup files* /somedir/
    COPY --chown=bin files* /somedir/
    COPY --chown=1 files* /somedir/
    COPY --chown=10:11 files* /somedir/

# ENTRYPOINT 명령어

    두가지 형태
    ENTRYPOINT ["executable", "param1", "param2"]
    ENTRYPOINT command param1 param2

# VOLUME

    VOLUME ["/data"]
    VOLUME 명령은 지정된 이름으로 마운트 지점을 만들고 기본 호스트 또는 기타 컨테이너에서 외부 적으로 마운트 된 볼륨을 보유하는 것으로 표시합니다.
    값은 JSON 배열, VOLUME [ "/ var / log /"] 또는 VOLUME / var / log 또는 VOLUME / var / log / var / db와 같이 여러 인수가있는 일반 문자열 일 수 있습니다.
    Docker 클라이언트를 통한 자세한 정보 / 예제 및 마운트 지침은 볼륨을 통한 디렉토리 공유 문서를 참조하십시오.

    docker run 명령은 기본 이미지 내의 지정된 위치에있는 모든 데이터로 새로 생성 된 볼륨을 초기화합니다. 예를 들어 다음 Dockerfile 스 니펫을 고려하십시오.

    FROM ubuntu
    RUN mkdir /myvol
    RUN echo "hello world" > /myvol/greeting
    VOLUME /myvol

# USER

    USER 명령은 사용자 이름 (또는 UID)과 선택적으로 이미지를 실행할 때 사용할 사용자 그룹
    (또는 GID)을 설정하고 Dockerfile에서 그 뒤에 오는 RUN, CMD 및 ENTRYPOINT 명령에 사용할 수 있습니다.

    FROM microsoft/windowsservercore
    # Create Windows user in the container
    RUN net user /add patrick
    # Set it for subsequent commands
    USER patrick

# WORKDIR

    WORKDIR /path/to/workdir

    WORKDIR 명령어는 Dockerfile에서 뒤 따르는 RUN, CMD, ENTRYPOINT, COPY 및 ADD 명령어에 대한 작업 디렉토리를 설정합니다.
    WORKDIR이 없으면 후속 Dockerfile 명령에서 사용되지 않더라도 생성됩니다.
    WORKDIR 명령어는 Dockerfile에서 여러 번 사용할 수 있습니다. 상대 경로가 제공되면 이전 WORKDIR 명령어의 경로에 상대적입니다. 예를 들면 :

    WORKDIR /a
    WORKDIR b
    WORKDIR c
    RUN pwd

    ENV DIRPATH=/path
    WORKDIR $DIRPATH/$DIRNAME
    RUN pwd

# ARG 
    ARG 명령어는 Default 값을 포함 할수 있습니다. 

    ARG <name>[=<default value>]

    기본값이 없는 경우는 아래의 값이 전달된다. 
    FROM busybox
    ARG user1=someuser
    ARG buildno=1
    # ...


    $ docker build --build-arg user=what_user .
    
    다음과 같이 값을 입력 할수도 있습니다. 
    FROM busybox
    USER ${user:-some_user}
    ARG user
    USER $user
    # ...

    ARG 는 변수로 사용 가능합니다. 

    FROM ubuntu
    ARG CONT_IMG_VER
    ENV CONT_IMG_VER=v1.0.0
    RUN echo $CONT_IMG_VER

    $ docker build --build-arg CONT_IMG_VER=v2.0.1 .

    이렇게도 세팅 가능 합니다. 
    ARG명령어 와 달리 ENV값은 항상 빌드 된 이미지에 유지됩니다. --build-arg플래그가 없는 도커 빌드를 고려하십시오 
    
    FROM ubuntu
    ARG CONT_IMG_VER
    ENV CONT_IMG_VER=${CONT_IMG_VER:-v1.0.0}
    RUN echo $CONT_IMG_VER


