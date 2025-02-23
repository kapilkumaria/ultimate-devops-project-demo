# syntax=docker/dockerfile:1

################################
# 1. Build Stage (SDK Image)   #
################################
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /app

# Copy everything from the repo root into /app
COPY . .

# Make sure the proto directory exists
RUN mkdir -p src/accounting/proto

# Copy demo.proto into accounting/proto
RUN cp pb/demo.proto src/accounting/proto/demo.proto

# Move into the accounting folder
WORKDIR /app/src/accounting

# Restore .NET dependencies
RUN dotnet restore

# Publish the project in Release mode to a folder named 'out'
RUN dotnet publish -c Release -o /app/out

#################################
# 2. Runtime Stage (Slim Image) #
#################################
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

# Set environment variable for Kafka address (update the value as needed)
ENV KAFKA_ADDR=kafka:9092

# Copy the published output from the build stage
COPY --from=build /app/out ./

# Optional: If it’s an ASP.NET Core app that listens on a port
# EXPOSE 80

# Run the Accounting DLL
ENTRYPOINT ["dotnet", "Accounting.dll"]
