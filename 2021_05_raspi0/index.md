# about
I had a raspberry pi running 24/7 collecting data from my router and exposing a VPN.

I logged in a few days ago and influxdb was using a lot of CPU. I decided to reboot it, but it never came back up.

I will recreate my setup, but I will try to do it in a way that will allow me re-do it in an automatice way.

# image creation
I downloaded the [rpi imager](https://www.raspberrypi.org/software/) and burned the [Raspberry Pi OS Lite](https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2021-03-25/2021-03-04-raspios-buster-armhf-lite.zip) from the [downloads page](https://www.raspberrypi.org/software/operating-systems/).

# initial login
I plugged the sim card of and then on again, and my laptop remounted the partitions. I then followed [these instructions](https://web.archive.org/web/20210508073430/https://zedt.eu/tech/linux/how-to-pre-configure-raspberry-pi-for-remote-ssh-and-wifi/) to setup wifi and ssh, by creating the
files:
```
ssh
```

and
```
wpa_supplicant.conf
```
with content:

```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="NETWORKNAME"
    psk="NETWORKPASSWORD"
}

```

I was then able to connect to hostname
```
raspberrypi.local
```

using the default username:password pi:raspberry

```
ssh pi@raspberrypi.local
```
I run the raspi-config tool and set the hostname to rpi0.

I added my ssh key to the authorized_keys (from ~/.ssh/id_rsa.pub):

```bash
mkdir .ssh
chmod 700 .ssh
vi .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
```

I also changed the pi password.

# puppet
I do not want to keep re-burning the image every time I make a change (like I would in the cloud), as I do not want to have to manually access the box every time.

I chose to manage the pi once it is setup via puppet. I chose Puppet because infrastructure people I know use either Puppet or Chef. I can revisit this decision in the future.

I found [some instructions for that](https://web.archive.org/web/20210508073545/http://frederickvandenbosch.be/?p=1843).



TODO:

I want a masterless puppet setup

 - https://janikvonrotz.ch/2018/12/08/puppet-masterless-project-setup-guide/

 - https://github.com/danieldreier/puppet-push


# next steps
## custom images
I also found some manual [instructions](https://www.instructables.com/Raspbian-Pi-Customized-Unattended-Installation/) for creating a custom image.

[docker pi](https://github.com/lukechilds/dockerpi) emulates a raspberry pi

I found some good [instructions](https://disconnected.systems/blog/custom-rpi-image-with-github-travis/) about using existing tools to create a new image from a repo with configuration. The build is still a little manual.

[Here ](https://github.com/TheSin-/rpi-img-builder) is a repo that can build the image inside docker.

## other links

 - https://github.com/pyavitz/rpi-img-builder

 - https://github.com/ptr1337/arm-img-builder-docker
