``` 
#!/bin/bash

# Update Ubuntu
echo "Updating Ubuntu..."
sudo apt update && apt upgrade -y

# Install Podman
echo "Installing Podman container..."
sudo apt install podman -y

# Podman version
echo "Podman version..."
sudo podman --version

# Create secret key
echo "Creating SECRET_KEY = "
sudo openssl rand -base64 42

# Create Superset container
echo "Creating Superset container...."
#podman run -d -p 8088:8088 -e "SUPERSET_SECRET_KEY=bWKudcFGznRM7LvFYdfN/9ePRMSHzC7Xf0ECYfCd8RWavicEaugnjaiz" --name superset apache/superset
podman pull apache/superset
podman tag apache/superset superset:latest
podman run -d -p 8088:8088 -e "SUPERSET_SECRET_KEY=bWKudcFGznRM7LvFYdfN/9ePRMSHzC7Xf0ECYfCd8RWavicEaugnjaiz" --name superset superset:latest



# Create admin user
echo "Creating admin user...."
podman exec -it superset superset fab create-admin \
              --username admin \
              --firstname Superset \
              --lastname Admin \
              --email admin@superset.com \
              --password admin

# Update DB
echo "Updating DB....."
podman exec -it superset superset db upgrade

# Load Examples
echo "superset emaples....."
podman exec -it superset superset load_examples

# Setup roles
echo "setup roles..."
podman exec -it superset superset init
# Run on localhost
echo "Superset is running at http://localhost:8088/"


```
