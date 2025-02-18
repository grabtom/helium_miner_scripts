# Helium miner update script
This script will automatically detect if that latest docker image is running and if this is not the case it will update to the latest docker image.

# For the Validator scripts, see the /Validator directory

# If this guide/script helped you please consider donating HNT the following address
```
13UcQB9B867b6Fp6cwtKgzxfwrr61sbxMpPsXChqPYRr9iFoTar
```

## Setup

```
$ git clone https://github.com/Wheaties466/helium_miner_scripts
$ cd helium_miner_scripts/
```
Then run any of the scripts.

## Updating the repo/script to get the lastest version of the script

```
$ cd helium_miner_scripts/
$ git pull
```
after you're in the same directory as the script and do a git pull. It will let you know if it is up to date or not. 

# Dependencies
Following dependencies shall be met:

- Curl
- jq
```
$ sudo apt install curl jq -y
```
Make sure the user that is running the script is allowed to run docker. Either add the user to the docker group or run the scipt as root. See: https://docs.docker.com/engine/install/linux-postinstall/

# Configuration
After cloning this repository make the script executable:

```
$ chmod +x miner_latest.sh
$ sudo touch /var/log/miner_latest.log

```

## Change architecture 
Currently it is setup to use arm64 version, but it can be easily modified to use the amd64 docker image build by modifying the ARCH variable to equal ``amd`` instead of ``arm``.

## Change region
By default the script is configured for US915 frequencies. To use the miner in other regios like EU868 the script shall be changed by adding the `` --env  REGION_OVERRIDE=EU868`` to the line with ``docker run``:

```
docker run -d --env REGION_OVERRIDE=EU868 --restart always --publish 1680:1680/udp --publish 44158:44158/tcp --name miner --mount type=bind,source=/home/pi/miner_data,target=/var/data quay.io/team-helium/mi$
```

# Script useage

```
$ ./miner_latest.sh
```

This can be called from a cron or ran in a shell.

## Using cron
Add the following lines to your crontab using ``sudo crontab -e`` to run the script daily at 1 o clock at night:
The second line updates this repo on a regular basis.

```
# Check for updates on miner image every night at 1 o clock

# Use whatever path you have your repo setup with.
# If you need to test your cron you can use the following site and add "&& curl -s 'https://webhook.site/#!/~'" to the end of your cron and it will make a web request to your specific url when it completes.
0 */4 * * * /home/pi/helium_miner_scripts/miner_latest.sh 2>&1 >> /var/log/miner_latest.log
```

# Extra

Check if your miner is running and receiving data from your gateway:
```
$ docker exec miner tail -f /var/log/miner/console.log | grep "lora"
```

Check progress of your miner on the blockchain:
```
$ docker exec miner miner info height
```

Check connectivity of your miner:
```
$ docker exec miner miner peer book -s
```
Check the current running miner version
```
$ docker container inspect -f '{{.Config.Image}}' miner | awk -F: '{print $2}'
```

Test update...
