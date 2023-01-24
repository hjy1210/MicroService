This project id followed [Tutorial: Containerize a .NET app](https://learn.microsoft.com/en-us/dotnet/core/docker/build-container?view=vs-2022&tabs=windows)

`dotnet new console -o App -n DotNet.Docker`

`cd app`

edit program.cs as follows:

```
var counter = 0;
var max = args.Length != 0 ? Convert.ToInt32(args[0]) : -1;
while (max == -1 || counter < max)
{
    Console.WriteLine($"Counter: {++counter}");
    await Task.Delay(TimeSpan.FromMilliseconds(1_000));
}
```

`dotnet run`

`dotnet publish -c Release`

Create `Dockerfile` file with contents:

```
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build-env
WORKDIR /App

# Copy everything
COPY . ./
# Restore as distinct layers
RUN dotnet restore
# Build and publish a release
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:7.0
WORKDIR /App
COPY --from=build-env /App/out .
ENV DOTNET_EnableDiagnostics=0
ENTRYPOINT ["dotnet", "DotNet.Docker.dll"]
```

`docker build -t counter-image -f Dockerfile .`

`docker images`

`docker create --name core-counter counter-image`

`docker ps -a`

`docker start core-counter`

`docker stop core-counter`

`docker start core-counter`

`docker attach --sig-proxy=false core-counter`

`docker rm core-counter`

`docker ps -a`

`docker run -it --rm counter-image`

```
docker run -it --rm --entrypoint "bash" counter-image
ls
dotnet DotNet.Docker.dll 7
```

# Docker Essential commands 的連結
[docker build](https://docs.docker.com/engine/reference/commandline/build/)

docker run

docker ps

docker stop

docker rm

docker rmi


docker image