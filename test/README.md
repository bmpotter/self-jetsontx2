### README.md
Readme for testing the Intu AI project deployment pattern. Intu/Self along with its associated edge deep learning workloads, can be executed on a Jetson TX2 using the script intu_pattern.sh

#### Requirements:
- NVIDIA Jetson TX2, webcam, USB Sound Card and Mic/Speaker (not yet tested on TX1)
- TX2 with latest JetPack3.1 (L4T28.1), and kernel rebuilt with the instructions in this [wiki](https://github.com/open-horizon/cogwerx-jetson-tx2/wiki)
- For `intu_pattern.sh`, you'll need to build [umlauete's v4l2loopback](https://github.com/umlaeute/v4l2loopback) ke
rnel module on the native system.  (requirement for 'vdealer', our little video brokering container)
- For Intu/Self AI: Subscribe to IBM Cloud services (STT, TTS, Natural Language Processing...), and include these credentials in a bootstrap.json file. Copy the bootstrap.json file to the directory: self-jetsontx2/test/config/self.  (An example has been provided for you there)

#### Execution:
0. Configure your Jetson TX2 according to the requirements above
1. Test v4l2loopback on your Jetson TX2 to confirm it works (see examples at link)
2. Clone this repo on your Jetson TX2 in the directory of your choice
3. Navigate to the dir: self-jetsontx2/test
4. Change permissions of the script `chmod +x intu_pattern.sh`
5. Copy over your bootstrap.json file, containing IBM Cloud service credentials, to self-jetsontx2/test/config/self
6. Run the script with `./intu_pattern.sh start`. Intu and its related deep learning services will load.  (docker will pull workload containers as-needed the first time. This will take a while... some containers are 6GB+)
7. Browse to Intu's dashboard at http://<your TX2's local IP>:9443/www/dashboard#/ from another PC, or http://localhost:9443/www/dashboard#/ from the Jetson to interact with Intu.
7. Stop the workload pattern with `./intu_pattern.sh stop`.  Docker containers will be shut down.

#### Tips and Troubleshooting:
- To monitor resource usage, docker container startup, and video device brokering, use the following commands in a terminal window:

   `htop`                      # Device resources  (device RAM and GPU RAM are lumped together)
   `sudo ~/tegrastats`         # RAM, CPU, GPU (GPU % load shows as 'GR3D', only when running with 'sudo')
   `watch -n 1 ls /dev/video*` # Watch video devices come up and down as 'vdealer' allocates them
   `watch -n 1 docker stats`   # Watch docker containers come up and down, and resource utilzation

- Intu and face_classification will your webcam, which should show up as /dev/video1  (let us know if this turns out differently for you)
