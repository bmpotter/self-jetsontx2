# Watson-Intu Self for Jetson TX2
[Self](https://github.com/watson-intu/self), an AI Middleware project from IBM, runs on various x86 platforms (Mac, Windows, Linux) in addition to Raspberry Pi 3.  This build extends Self for Jetson TX2, in Docker. 

## Jetson TX2 Setup
For initial Jetson TX2 setup from scratch, including SSD and Docker, follow this guide: https://github.com/open-horizon/cogwerx-jetson-tx1/wiki/Initial-setup:-Jetson-TX1-and-TX2
![NVIDIA Jetson TX1 devkit](http://images.nvidia.com/content/tegra/embedded-systems/images/jetson-tx1-devkit.jpg)

You'll need a microphone (required) and camera (optional) to interact with Self. 


#### Mic
Since many people don't have an I2S Mic set up on their Jetsons, this build supports a USB sound card and microphone (such as [this](https://amazon.com/Sabrent-External-Adapter-Windows-AU-MMSA/dp/B00IRVQ0F8)).  (ALSA will list the USB mic as card #2 or #3. This repo expects it will be #3, after a USB webcam.)
#### Camera
A USB Webcam such as the common Logitech C720 should work. We're working on enabling the onboard camera also.

## To Build
Clone this repo into a folder on your TX2

 `git clone https://github.com/open-horizon/self-jetsontx2.git`
 
 `cd self-jetsontx2`

Build Self docker image (this image will be ~6-7GB, ensure you have sufficient disk space)
 
 `docker build -t openhorizon/aarch64-tx2-intu-self:<version> .`

## Run
Visit IBM.com and register for IBM Cloud services (Speech-to-Text, Text-to-Speech, Natural Language Classification...)
Once you've obtained your credentials, you'll run the container and add them to the bootstrap-example.json file in your build directory. 

`cp  ./config/self/bootstrap-example.json  ./config/self/bootstrap.json`
`vim ./config/self/bootstrap.json          #fill in the required info`

Run the Self container in privileged mode, to ensure access to the cam (#1 for webcam) and mic (#3 for USB sound card), already defined in bootstrap-example.json and alsa.conf: 

`docker run --name intu-self -it --rm --privileged -p 9443:9443 -v $PWD/test/config/self:/configs openhorizon/aarch64-tx2-intu-self:edge /bin/bash -c "ln -s -f /configs/bootstrap.json bin/linux/etc/shared/; ln -s -f /configs/alsa.conf /usr/share/alsa/; ./bin/bash"`

Run Self

`/root/src/watson-intu/self/bin/linux/run_self.sh`

Visit the Self web UI:
- On your TX2 itself: http://localhost:9443/www/dashboard 
- Via some other computer on your LAN http://<TX2_IP_address>:9443/www/dashboard

Ask Self a question: "What is the weather today?"

Once your config is set, subsequent runs of Self may be done directly:

`docker run --name intu-self -it --rm --privileged -p 9443:9443 -v $PWD/test/config/self:/configs openhorizon/aarch64-tx2-intu-self:edge /bin/bash -c "ln -s -f /configs/bootstrap.json bin/linux/etc/shared/; ln -s -f /configs/alsa.conf /usr/share/alsa/; ./bin/linux/run_self.sh"`
