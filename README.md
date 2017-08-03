# aio-docker-hass
All-in-one docker image for Home Assistant. Tested on Ubuntu Xenial (16.04.2).

Supports:
- v0.48.1+
  - zwave
  - hdmi cec
  - nmap scanner
  - ssh device tracker
  - wake on lan
  - curl
  - mysql recorder (requires [separate mysql server](#mysql))

## Example Usage

```bash
# home assistant
docker run -d --name=home-assistant --net=host --rm \
  --device=/dev/vchiq:/dev/vchiq --device=/dev/zwave:/dev/zwave \
  -v ~/hass-config:/config -v /etc/localtime:/etc/localtime:ro -v ~/.ssh:/ssh \
  sterlingw/rpi-aio-home-assistant:latest
```
#### Parameters Explained
- `--net=host` Optional, depending on your use case. You may expose specific ports or just use this for simplicity. This works well for things like Amazon Alexa/Emulated Hue.
- `--rm` Optional. Removes the container when it is stopped. For more stability, use `--restart=always`.
- `--device=/dev/vchiq:/dev/vchiq` Required to enable HDMI CEC components.
- `--device=/dev/zwave:/dev/zwave` Required to enable zwave devices. Use the device mapped to your zwave hub controller. Eg: `/dev/ttyACM0` or `/dev/ttyACM1` etc.
- `-v ~/hass-config:/config` Specifies the Home Assistant config directory.
- `-v /etc/localtime:/etc/localtime:ro` Sets the container's timezone to the host's timezone.
- `-v ~/.ssh:/ssh` Used to gain access to host's ssh keys for ssh-based components.

### Other Containers

#### MySQL
Following example taken from raspberry pi. Find the appropriate docker image for your machine.

```bash
# mysql
docker run -d --name=mysql -p 3306:3306 --rm \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -e MYSQL_DATABASE=homeassistant -e MYSQL_USER=hass -e MYSQL_PASSWORD=hass \
  -v ~/hass-config/db:/var/lib/mysql \
  hypriot/rpi-mysql
```
You may use another container to host the `recorder` component database (like MySQL, for example). With the above example, connections can be made at `mysql://hass:hass@127.0.0.1/homeassistant`. Don't use `localhost` because MySQL will try to connect via a unix socket instead of a TCP connection. 
