
# ** EN CONSTRUCCION **

# Opción 2: DIY - Instalación de un coordinador ZigBee en una RasPi

- Una alternativa a disponer de un gateway ZigBee comercial, es la instalación en una raspberry Pi de un coordinador con un modulo Xbee.
- Para ello deberemos instalar uno de los siguientes modulos hardware o USB en las RasPi: <https://www.zigbee2mqtt.io/information/supported_adapters.html>.

- Instalaremos zigbee2mqtt como coordinador:

 sudo git clone <https://github.com/Koenkk/zigbee2mqtt.git> /opt/zigbee2mqtt
 sudo chown -R pi:pi /opt/zigbee2mqtt
 cd /opt/zigbee2mqtt
 npm install
  
Cuando termine la instalación, tendremos que configurar el fichero `/opt/zigbee2mqtt/data/configuration.yaml`:

 homeassistant: true
 permit_join: true
 mqtt:
   base_topic: zigbee2mqtt
zigbee2mqtt  server: 'mqtt://localhost'
  user: usuario
  password: contraseña
 serial:
   port: /dev/ttyACM0
 advanced:
  log_level: debug
  network_key: [1, 3, 5, 7, 9, 11, 13, 15, 0, 2, 4, 6, 8, 10, 12, 13]

Para generar una nueva clave `network_key` aleatoria:

dd if=/dev/urandom bs=1 count=16 2>/dev/null | od -A n -t u1 | awk '{printf "["} {for(i = 1; i< NF; i++) {printf "%s, ", $i}} {printf "%s]\n", $NF}'
    
Desconfigurar el <permit_join=false> una vez emparejados todos los dispostivos.

Reiniciamos zigbee2mqtt:

  sudo systemctl start zigbee2mqtt

Podemos mirar el log de zigbee2mqtt así:

sudo journalctl -u zigbee2mqtt.service -f

Flashear dispositivo CC26X2R1 usando uniflash:
   - URL uniflash: <http://www.ti.com/tool/UNIFLASH>
   - Firmware: <https://github.com/Koenkk/Z-Stack-firmware/tree/master/coordinator/Z-Stack_3.x.0/bin>



###Instalación HomeAssistant###
  
- <https://www.home-assistant.io/hassio/>
- Instalación con containers. Ejemplo:
   
sudo docker run -d --name="home-assistant" -v /PATH_TO_YOUR_CONFIG:/config -v /etc/localtime:/etc/localtime:ro --net=host homeassistant/home-assistant:stable

- Una vez instalado podemos acceder al entorno con la URL <http://localhost:8123/onboarding.html>.
