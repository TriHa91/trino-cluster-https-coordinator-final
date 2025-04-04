# Config /etc/hosts to listen coordinator and workers for all instances
sudo nano /etc/hosts
x.x.x.x trino-coordinator
x.x.x.x trino-coordinator

# Create keystore and truststore on coordinator instance and copy that keys to other worker instances
sudo apt install openjdk-21-jre-headless

## Generate new self-signed certificate with appropriate SANs
keytool -genkeypair -alias trino -keyalg RSA -keysize 2048 \
  -keystore keystore.jks -validity 365 -storepass hanhattri \
  -dname "CN=trino-coordinator" \
  -ext SAN=DNS:trino-coordinator,DNS:trino-worker,DNS:localhost,IP:x.x.x.x,IP:x.x.x.x,IP:127.0.0.1, IP:172.18.0.2 *using docker network inspect to find the docker ip of coordinator contrainer "docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' trino_coordinator" 

## Export and create truststore
keytool -exportcert -alias trino -keystore keystore.jks -file trino.crt -storepass hanhattri
keytool -importcert -alias trino -keystore truststore.jks -file trino.crt -storepass hanhattri -noprompt

# Then copy that keys to trino/etc
