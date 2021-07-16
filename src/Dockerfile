FROM ubuntu:20.04

LABEL maintainer="Paul Steinlechner"

ENV TIMEZONE=Europe/Vienna \
    DEBIAN_FRONTEND=noninteractive \
    CONANEXILES_MASTERSERVER=1 \
    CONANEXILES_Game_RconPlugin_RconEnabled=1 \
    CONANEXILES_Game_RconPlugin_RconPassword=Password \
    CONANEXILES_Game_RconPlugin_RconPort=25575 \
    CONANEXILES_Game_RconPlugin_RconMaxKarma=60 \
    CONANEXILES_Engine_Core.Log_LogStreaming=Warning \
    CONANEXILES_Engine_Core.Log_LogModController=Warning \
    CONANEXILES_Engine_Core.Log_LoglevelActorContainer=Warning \
    CONANEXILES_Game_/Script/ConanSandbox.ConanGameMode_PeriodicBackupInterval=900

RUN apt-get update \
    && apt-get install -y crudini python3-pip redis-tools software-properties-common supervisor unzip curl xvfb wget rsync net-tools \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Wine
RUN wget -qO - https://dl.winehq.org/wine-builds/winehq.key | apt-key add \
    && dpkg --add-architecture i386 \
    && apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ focal main' \
    && apt update \
    && apt install wine64 wine32 -y \
    && apt install screen xvfb -y \
    && pip3 install python-valve \
    && apt install --install-recommends winehq-stable -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# Install winetricks
ADD winetricks /usr/bin/winetricks
RUN chmod +x /usr/bin/winetricks

RUN apt-get update \
    && apt-get install -y x11vnc strace cabextract \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY . ./

RUN ln -snf /usr/share/zoneinfo/$TIMEZONE /etc/localtime \
    && echo $TIMEZONE > /etc/timezone \
    && chmod +x /entrypoint.sh \
    && cd /usr/bin/ \
    && chmod +x conanexiles_controller rconcli steamcmd_setup

EXPOSE 7777/udp 25575 27015/udp 27016/udp 37015/udp 37016/udp 5900

VOLUME ["/conanexiles"]

ENTRYPOINT ["/entrypoint.sh"]
CMD ["supervisord"]
