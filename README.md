# VPN portforwarding with Mullvad for Docker containers

This guide will show you how to use Mullvad to access your docker containers from your other Mullvad connected devices. 

Everything here will be done via CLI in Ubuntu Server.

## Installing Mullvad
First let's install Mullvad 
```
wget --content-disposition https://mullvad.net/download/deb/latest
```
If this command doesn't work then you can run the following command. Please verify you have the latest version.

On this page https://mullvad.net/en/download/#linux right click on "Download.deb" copy link location to get the latest release

You can also check out their releases here https://github.com/mullvad/mullvadvpn-app/releases/
```
get --content-disposition https://mullvad.net/media/app/MullvadVPN-XXXX.X_amd64.deb
sudo apt-get -y install gdebi-core && sudo gdebi MullvadVPN-XXXX.X_amd64.deb
```
Next we want to login
```
mullvad account set MULLVADACCOUNTNUMBER
```
Once logged in we need to pick a location

List locations
```
mullvad relay list
```
Set location with a country and city of your choice
```
mullvad relay set location <country> <city>
```
We want to be able to see our network devices such as our host machine running docker. If this isn't set before connecting mullvad then we won't be able to ssh into the server.
```
mullvad lan set allow
```
Let's connect
```
mullvad connect
```
Verify your connection
```
mullvad status
```
Or alternatively check if you are using mullvad with
```
curl https://am.i.mullvad.net/connected
```
## Turning on Wireguard
Now we can enable Wireguard in Mullvad

Check if you need a key
```
mullvad tunnel wireguard key check
```
If you do not have a key then generate one
```
mullvad tunnel wireguard key regenerate
```
Enable wireguard
```
mullvad relay set tunnel wireguard any
```
## Setting up a port for Docker
All that needs to be done now is to create a port and a docker container to listen to that port

1. Login to your Mullvad account in a web browser

2. Click on "Manage ports and WireGuard keys" or click this link https://mullvad.net/en/account/#/ports

3. Click the green plus button on the public key that matches the machine Mullvad is installed on

4. Remember we can check our key with ``` mullvad tunnel wireguard key check ```

5. Create a docker container listening to that port

6. For this example I used Bitwarden
```
docker pull bitwardenrs/server
docker run -d --name bitwarden -v /bw-data/:/data/ -p MULLVADPORT:80 bitwardenrs/server:latest
```

7. Connect to this container from other Mullvad connected devices using the ip from ``` curl https://am.i.mullvad.net/connected ``` along with the MULLVADPORT

## Conclusion
Now we easily have remote access to our docker container from any of our other devices connected to Mullvad. To some this might be dead simple but for me this took me way too long to figure out and hope this information is useful to someone.
Personally I would have liked to find a guide like this. 

Find more information on the official mullvad documentation below!

* https://mullvad.net/en/help/install-mullvad-app-linux/
* https://mullvad.net/en/help/how-use-mullvad-cli/
* https://mullvad.net/en/help/cli-command-wg/
