# https://hub.docker.com/_/microsoft-dotnet-core
FROM mcr.microsoft.com/dotnet/sdk:5.0.403-focal-amd64 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY telepathy-test.sln .
COPY telepathy-test/*.csproj ./telepathy-test/

RUN dotnet restore

# copy everything else and build app
COPY telepathy-test ./telepathy-test/

WORKDIR /source/telepathy-test
RUN dotnet publish -c release -o /app

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0.12-focal-amd64
ENV ASPNETCORE_URLS http://0.0.0.0:5000
WORKDIR /app

RUN apt-get update ; apt-get install -y git build-essential gcc make yasm autoconf automake cmake libtool checkinstall libmp3lame-dev pkg-config libunwind-dev zlib1g-dev

RUN apt-get update \
    && apt-get clean \
    && apt-get install -y --no-install-recommends libavutil-dev libc6-dev libgdiplus wget software-properties-common

RUN apt install libavcodec-dev -y
RUN apt install libavformat-dev -y

COPY --from=build /app ./
COPY ffprobe-53.tar.gz ./
RUN tar -xzf ffprobe-53.tar.gz; rm -r ffprobe-53.tar.gz

RUN cd ./ffprobe-53; ./configure --enable-gpl --enable-libmp3lame --enable-decoder=mjpeg,png --enable-encoder=png --enable-openssl --enable-nonfree


# RUN cd ./ffprobe-53; make
# RUN cd ./ffprobe-53; make install

ENTRYPOINT ["dotnet", "telepathy-test.dll"]