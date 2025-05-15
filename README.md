
# Asterisk 18 Docker Setup

This guide explains how to run Asterisk 18 using the Docker image `andrius/asterisk` with custom configuration files.

---

## Prerequisites

- Docker installed on your machine
- Configuration files prepared:
  - `sip.conf`
  - `extensions.conf`
  - `rtp.conf`
  - `modules.conf`
- Sound files located in a folder named `sounds`

---

## Configuration example for `sip.conf`

```ini
[general]
context=ivr
bindport=5060
bindaddr=0.0.0.0
disallow=all
allow=ulaw
allow=alaw
allow=wav
language=ru
localnet=192.168.0.112/255.255.255.0
nat=force_rport,comedia

[kurbanov]
type=friend
secret=1234
host=dynamic
context=ivr
nat=force_rport,comedia
canreinvite=no
qualify=yes
dtmfmode=rfc2833
```

Make sure to add your external IP in `localnet` if you want to use Asterisk behind NAT.

---

## Running Asterisk in Docker

Run the following command from the folder where your `config` folder and `sounds` folder are located:

```bash
docker run -d \
  --name asterisk \
  -p 5060:5060/udp \
  -p 5060:5060/tcp \
  -p 10000-10100:10000-10100/udp \
  -v $(pwd)/config/sip.conf:/etc/asterisk/sip.conf \
  -v $(pwd)/config/extensions.conf:/etc/asterisk/extensions.conf \
  -v $(pwd)/config/rtp.conf:/etc/asterisk/rtp.conf \
  -v $(pwd)/config/modules.conf:/etc/asterisk/modules.conf \
  -v $(pwd)/sounds:/var/lib/asterisk/sounds/ \
  andrius/asterisk
```

---

## Checking logs and connecting

- To view logs and console output:

```bash
docker logs -f asterisk
```

- To connect to the running Asterisk CLI:

```bash
docker exec -it asterisk asterisk -rvvv
```

---

## Notes

- The RTP port range `10000-10100` is required for audio streams.
- Make sure the `sounds` directory contains your audio prompts in the correct format (e.g., `.wav`, `.gsm`, `.slin`).
- Adjust `localnet` and `nat` settings in `sip.conf` to match your network setup.
- If you use an external IP address or a public network, ensure your firewall/router forwards ports 5060 and 10000-10100 UDP.

---

If you encounter any issues, check the Asterisk logs and ensure your configuration files are valid.

