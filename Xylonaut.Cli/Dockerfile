FROM mcr.microsoft.com/dotnet/runtime:8.0 AS base
USER $APP_UID
WORKDIR /app

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
# Install dependencies required for Native AOT (clang and zlib)
RUN apt-get update && apt-get install -y \
    clang \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy project file and restore
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Xylonaut.Cli.csproj", "./"]
RUN dotnet restore "Xylonaut.Cli.csproj"

# Copy the rest of the source code and build
COPY . .
WORKDIR "/src/"
RUN dotnet build "./Xylonaut.Cli.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Xylonaut.Cli.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:SelfContained=false

# Final stage
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Xylonaut.Cli.dll"]
