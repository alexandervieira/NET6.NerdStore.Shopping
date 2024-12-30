# Acesse https://aka.ms/customizecontainer para saber como personalizar seu contêiner de depuração e como o Visual Studio usa este Dockerfile para criar suas imagens para uma depuração mais rápida.

# Esta fase é usada durante a execução no VS no modo rápido (Padrão para a configuração de Depuração)
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443


# Esta fase é usada para compilar o projeto de serviço
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Services/AVS.NerdStoreShopping.BillingAPI/AVS.NerdStoreShopping.BillingAPI.csproj", "src/Services/AVS.NerdStoreShopping.BillingAPI/"]
RUN dotnet restore "./src/Services/AVS.NerdStoreShopping.BillingAPI/AVS.NerdStoreShopping.BillingAPI.csproj"
COPY . .
WORKDIR "/src/src/Services/AVS.NerdStoreShopping.BillingAPI"
RUN dotnet build "./AVS.NerdStoreShopping.BillingAPI.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Esta fase é usada para publicar o projeto de serviço a ser copiado para a fase final
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./AVS.NerdStoreShopping.BillingAPI.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Esta fase é usada na produção ou quando executada no VS no modo normal (padrão quando não está usando a configuração de Depuração)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "AVS.NerdStoreShopping.BillingAPI.dll"]