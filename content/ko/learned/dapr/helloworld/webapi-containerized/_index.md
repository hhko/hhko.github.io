dotnet new sln -o DaprMultiContainer

dotnet new webapp --no-https -o MyFrontEnd
dotnet sln add MyFrontEnd/

dotnet new webapi --no-https -o MyBackEnd
dotnet sln add MyBackEnd/


dapr list
dapr dashboard

dapr run --app-id mybackend --app-port 5000 --dapr-http-port 3500 dotnet run
ℹ️  Starting Dapr with id mybackend. HTTP Port: 3500. gRPC Port: 33797
...

== APP == Building...
ℹ️  Updating metadata for app command: dotnet run
✅  You're up and running! Both Dapr and your app logs will appear here.

== APP == info: Microsoft.Hosting.Lifetime[14]
== APP ==       Now listening on: http://localhost:5223
== APP == info: Microsoft.Hosting.Lifetime[0]
== APP ==       Application started. Press Ctrl+C to shut down.
== APP == info: Microsoft.Hosting.Lifetime[0]
== APP ==       Hosting environment: Development
== APP == info: Microsoft.Hosting.Lifetime[0]
== APP ==       Content root path: /github/DaprMultiContainer/MyBackEnd/



http://localhost:5223/WeatherForecast
http://localhost:3500/v1.0/invoke/mybackend/method/WeatherForecast


MyBackEnd - Dockerfile
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


docker-compose.yml
version: '3.4'

services:
  mybackend:
    image: mybackend
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


1.5.1
1.5.1-linux-amd64
1.5.1-windows-amd64

Compose file format : 3.4
Docker Engine release : 17.09.0+



DaprMultiContainer 

.NET Core Web App
--no-https


MyFrontEnd 


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