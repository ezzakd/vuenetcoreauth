FROM mcr.microsoft.com/dotnet/core/aspnet:2.1-stretch-slim AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.1-stretch AS build
WORKDIR /src
COPY ["authExample/authExample.csproj", "authExample/"]
RUN dotnet restore "authExample/authExample.csproj"
COPY . .
WORKDIR "/src/authExample"
RUN dotnet build "authExample.csproj" -c Release -o /app

FROM node:latest AS node-build
WORKDIR /src
COPY . .
RUN rm -rf authexampleui/node_modules
RUN cd authexampleui/ && npm install
RUN cd authexampleui/ && npm run-script build

FROM build AS publish
RUN dotnet publish "authExample.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
COPY --from=node-build /src/authExample/wwwroot /app/wwwroot
ENTRYPOINT ["dotnet", "authExample.dll"]
