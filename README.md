Building a DIY CCTV System with Raspberry Pi and ESP32 Camera Modules

From https://medium.com/@ebinx7/building-a-diy-cctv-system-with-raspberry-pi-and-esp32-camera-modules-7dbba7e13129

Introduction
Start a DIY security project that protects your space both effectively and affordably. Imagine a CCTV system using Raspberry Pi and ESP32 camera modules that capture details and can communicate with you. Discover MotionEye OS, a smart way to boost your setup with easy motion detection and a simple interface, making your CCTV system a watchful guardian. It works with Telegram to turn your messaging app into a quick alert system. You’ll get instant notifications on your phone with a short video clip when motion is detected, which is a proactive way to enhance home security. Join me in exploring MotionEye OS, an open-source tool that improves motion detection and the user interface of your DIY CCTV system, making it a vigilant and communicative security solution.

Hardware required
Raspberry Pi: Model 3B+ or a later version for optimal performance.
ESP32 Camera Modules: I am using ESP32-CAM AI-Thinker, get one or more ESP32 camera modules.
Heat sink and Cooling Fan: Include heat sinks and a cooling fan to regulate temperature and enhance the longevity of your Raspberry Pi.
MicroSD card: 16GB or larger to host the operating system and a card reader.
Power Supplies: Acquire suitable power supplies for both the Raspberry Pi and ESP32 modules. 5V, 2A power supply is sufficient. Include connecting wires to establish power connections from the power supply to the Raspberry Pi and ESP32 cameras.
Network Connection: Choose either an Ethernet cable or a compatible Wi-Fi network for the Raspberry Pi and ESP32 modules.
Camera Housing: Consider protective housing for the cameras based on the installation environment.
Breadboard: Utilize a breadboard for prototyping and ESP32 programming.
FTDI Interface: Integrate an FTDI interface for programming and connecting the ESP32.
Tools required
Laptop: Get a laptop for tasks such as downloading software, configuring the Raspberry Pi, and programming the ESP32 module.
Internet Connection: Ensure an active internet connection for downloading software updates and dependencies.
Router: To connect the Raspberry Pi and ESP32 cameras to your local network.
Screwdriver, Glue, and Mounting Equipment: If applicable, use a screwdriver and mounting equipment for securing the cameras in specific locations.
Smartphone with Telegram App: Have a smartphone with the Telegram app installed to receive instant notifications.
HDMI Cable and Monitor (optional): Use an HDMI cable and monitor for the initial setup and configuration of the Raspberry Pi.
Network architecture

In our DIY CCTV system, think of the Wi-Fi router as the superhero bringing all the gadgets together. The Raspberry Pi and ESP32 cameras connect to this router through Wi-Fi / Ethernet, forming a team that talks and works together seamlessly. The Raspberry Pi is like the leader, guiding the cameras to detect motion and share videos. You can check what’s happening through Motion Eye OS on your Wi-Fi network, and your smartphone gets quick alerts with the Telegram app. The Wi-Fi router also links everything to the internet. So, in simple terms, it’s like our CCTV team having a secret meeting place (the router) where they share info and keep everything in check.

Setting up the ESP32 Cam board
I utilize the Arduino IDE for programming the ESP32 module. To flash the program, you’ll require a laptop, FTDI interface, and jumper wires/breadboard. You can find the program I employed on my GitHub; feel free to download it from there.


ESP32-CAM AI-Thinker
The flashing process

Establish a connection between the FTDI interface and the ESP32 camera following the provided diagram, and verify that the camera is appropriately connected to the board if it is not already.

FTDI connection diagram
Connect the FTDI interface to your laptop using an appropriate USB connector.
Launch the Arduino IDE and open the code obtained from the GitHub link provided earlier.
Ensure that you have updated the code with the correct SSID and password for your router in this file.
// ===========================
// Enter your WiFi credentials
// ===========================
const char* ssid = "<YOUR_SSID>";
const char* password = "<YOUR_PASSWORD>";
Transfer the program to the ESP32 module; for further information, consult this page.
Testing the Camera

Detach the camera module from the FTDI connector, and supply power to the module by connecting it to a 5V power supply through the VCC and GND pins.
Access your router configuration, navigate to the connected devices screen, where you’ll observe a new entry labeled “esp32-XXXXXX.” Additionally, you will find an IP address linked to this device, as illustrated in the image below.

Router config screen
Access the IP address (in my case, it’s 192.168.1.101; yours may be different) in your browser to view the following screen.

This step is pivotal in configuring the camera module. It’s crucial to set a static IP within the local network because the IP linked to the camera module may undergo changes upon router restarts. The method for configuring static IP binding can vary based on your router, so please refer to your router’s manual and set it up accordingly.
Likewise, program all your ESP32 cam modules and make a record of their respective local static IPs, as they will be needed later.
Setting up the Raspberry Pi
I’m utilizing the Raspberry Pi 3 B+ model, which acts as the core of the CCTV system we’re assembling. Now, let’s delve into the configuration process for the Pi in this project.


Raspberry Pi 3B+
Download a Raspberry Pi OS image, with a preference for the Lite variant from the official website. As of writing this document, I’m utilizing the latest version, Debian version: 12 (bookworm).
Launch your preferred OS flashing tool; I recommend Raspberry Pi Imager software. Proceed to flash the OS image onto a microSD card. In my setup, I’m using a 64GB SanDisk microSD card.

Flashing the microSD card.
When using the RPi Imager, additional configurations, such as setting up SSH and user credentials during the flashing process, can be included. This way, the need for an HDMI cable and monitor for the initial configuration can be bypassed.
Disconnect the card from your laptop and insert it into the Raspberry Pi, then power it up.
Identify the IP address in a manner similar to what was done for the ESP32 cam, and proceed to establish an SSH connection to the Pi using the credentials provided during the OS flashing.
ssh ebin@192.168.1.100
Upon accessing the Pi shell, install the necessary packages by running the provided shell script in install.sh.
MotionEye OS is now operational on the Raspberry Pi within a Docker container, accessible on port 8765. Open your browser and visit your Pi’s IP address followed by “:8765” — in my case, it is http://192.168.1.100:8765. Now you will see a screen like this.

Login screen of MotionEye
Enter “admin” as the username and leave the password field blank, then proceed to log in.
You’ll encounter a notification that says, ‘You have not configured any camera yet. Click here to add one…’, Simply click on this notification to add your cameras. Below, you can view screenshots of my configuration.

Add a new Network Camera.

You will get this new camera widget after adding the camera.
Integrating the Telegram bot
I’ve shared the script on the GitHub repository for sending video clips through the Telegram app. To use it effectively, we should set up the script to run whenever the MotionEye application generates a new video file. We can configure when new files are created, such as recording only when motion is detected or continuous recording. Additionally, we have the option to determine the duration of each video file.

Currently, the Telegram bot supports a maximum of 50 MB files. As a result, I’ve set the video duration to approximately 7 minutes to ensure the file size remains below this limit. You can see the specific video and storage configurations in the following images.

# Command to upload video clips to Telegram
/home/uploader.sh "%f" "%d/%m/%Y, %H:%M:%S"
The command above is employed to initiate the Telegram bot, enabling the upload of video files to your mobile phone. It’s important to ensure that you’ve correctly inserted your Telegram bot information into this file and relocated the uploader.sh, file to the /home directory of the MotionEye docker container.

Update the Telegram bot credentials like this.
# Replace with your Telegram bot token and chat ID
BOT_TOKEN="<BOT_TOKEN>"
CHAT_ID="<CHAT_ID>"
Move the uploader.sh, file into the MotionEye docker container using the following docker cp command.
# Copy the TG uploader file into docker container
docker cp uploader.sh motioneye:/home/

Video file configurations in MotionEye

Receiving CCTV footage on Telegram
Expert Tip: Make the MotionEye dashboard accessible on the internet.
I utilize a free tool called ‘remote.it’ to make my Raspberry Pi accessible over the internet. The initial step involves registering your Raspberry Pi with remote.it, is a straightforward process well-documented on their website. After registration, within the device menu, select the recently registered Raspberry Pi. Then, under the services section, add a new service specifically for HTTP. Set the URL to http://127.0.0.1:8765, which corresponds to your MotionEye dashboard’s local URL.

By employing remote.it and clicking the “connect” button, this URL becomes accessible through the remote.it application, providing you with a temporary URL. This method allows you to access your dashboard over the internet whenever needed. It’s worth noting that in the paid version of remote.it, the URLs remain static, offering a more convenient option for those who prefer not to regenerate URLs repeatedly.

This approach simplifies the process of accessing your MotionEye dashboard remotely, making it a practical choice for users seeking internet connectivity for their Raspberry Pi devices.


Remote.it HTTP configuration to expose MotionEye over the internet.
