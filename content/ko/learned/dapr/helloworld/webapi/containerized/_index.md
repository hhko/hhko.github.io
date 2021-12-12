---
title: WebAPI 2. Containerized
#linkTitle: OpenSearch 링크 제목
description: Containerized WebAPI Daprized 
type: docs
weight: 3
#no_list: true
#main_menu: true
#content_type: concept
---

- 소스 : [링크](./MyBackEnd)

## WebAPI C# 프로젝트
- # WebAPI 프로젝트 생성
  ```shell
  dotnet new webapi -o MyBackEnd --no-https
  cd MyBackEnd
  ```
- `Dockerfile` 생성
  ```dockerfile
  FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
  WORKDIR /app
  EXPOSE 80
  
  FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
  WORKDIR /src
  COPY ["MyBackEnd/MyBackEnd.csproj", "MyBackEnd/"]
  RUN dotnet restore "MyBackEnd/MyBackEnd.csproj"
  COPY . .
  WORKDIR "/src/MyBackEnd"
  RUN dotnet build "MyBackEnd.csproj" -c Release -o /app/build
  
  FROM build AS publish
  RUN dotnet publish "MyBackEnd.csproj" -c Release -o /app/publish
  
  FROM base AS final
  WORKDIR /app
  COPY --from=publish /app/publish .
  ENTRYPOINT ["dotnet", "MyBackEnd.dll"]
  ```
- `docker-compose.yml` 파일 생성
  ```yaml
  version: '3.4'
  
  services:
    mybackend:
      image: mybackend
      build:
        context: .
        dockerfile: ./Dockerfile
      ports:
        - "52000:50001"
  
    mybackend-dapr:
      image: "daprio/daprd:1.5.1"
      command: [ "./daprd", "-app-id", "MyBackEnd", "-app-port", "80" ]
      depends_on:
        - mybackend
      network_mode: "service:mybackend"
  ```

























.NET Core Web App
--no-https


MyFrontEnd 
DaprMultiContainer 

Install-Package Dapr.AspNetCore

Program.cs
builder.Services.AddDaprClient();

WeatherForecast.cs
namespace MyFrontEnd;

public class WeatherForecast
{
    public DateTime Date { get; set; }

    public int TemperatureC { get; set; }

    public int TemperatureF { get; set; }

    public string Summary { get; set; } = string.Empty;
}

index.cshtml
using Dapr.Client;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace MyFrontEnd.Pages;

public class IndexModel : PageModel
{
    private readonly DaprClient _daprClient;

    public IndexModel(DaprClient daprClient)
    {
        _daprClient = daprClient;
    }

    public async Task OnGet()
    {
        var forecasts = await _daprClient.InvokeMethodAsync<IEnumerable<WeatherForecast>>(
            HttpMethod.Get,
            "MyBackEnd",
            "weatherforecast");

        ViewData["WeatherForecastData"] = forecasts;
    }
}
- OnGet메서드는 사용자가 홈 페이지를 방문할 때마다 호출 됩니다.

@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
    @foreach (var forecast in (IEnumerable<WeatherForecast>)ViewData["WeatherForecastData"]!)
    {
        <p>The forecast for @forecast.Date is @forecast.Summary!</p>
    }
</div>


Docker-compose.ci.build.yml
version: '3.4'

services:
myfrontend:
    image: ${DOCKER_REGISTRY-}myfrontend
    build:
    context: .
    dockerfile: MyFrontEnd/Dockerfile


Dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["MyFrontEnd/MyFrontEnd.csproj", "MyFrontEnd/"]
RUN dotnet restore "MyFrontEnd/MyFrontEnd.csproj"
COPY . .
WORKDIR "/src/MyFrontEnd"
RUN dotnet build "MyFrontEnd.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "MyFrontEnd.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MyFrontEnd.dll"]



.NET Core Web API
MyBackEnd 

Dockerfile 


Docker-compose.ci.build.yml
version: '3.4'

services:
  myfrontend:
    image: ${DOCKER_REGISTRY-}myfrontend
    build:
      context: .
      dockerfile: MyFrontEnd/Dockerfile

  mybackend:
    image: ${DOCKER_REGISTRY-}mybackend
    build:
      context: .
      dockerfile: MyBackEnd/Dockerfile



Docker-compose.ci.build.yml
version: '3.4'

services:
  myfrontend:
    image: ${DOCKER_REGISTRY-}myfrontend
    build:
      context: .
      dockerfile: MyFrontEnd/Dockerfile
    ports:
      - "51000:50001"

  myfrontend-dapr:
    image: "daprio/daprd:latest"
    command: [ "./daprd", "-app-id", "MyFrontEnd", "-app-port", "80" ]
    depends_on:
      - myfrontend
    network_mode: "service:myfrontend"

  mybackend:
    image: ${DOCKER_REGISTRY-}mybackend
    build:
      context: .
      dockerfile: MyBackEnd/Dockerfile
    ports:
      - "52000:50001"

  mybackend-dapr:
    image: "daprio/daprd:latest"
    command: [ "./daprd", "-app-id", "MyBackEnd", "-app-port", "80" ]
    depends_on:
      - mybackend
    network_mode: "service:mybackend"

daprio/daprd:latest -> 
gRPC 50001?