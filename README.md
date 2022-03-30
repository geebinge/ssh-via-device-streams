# ssh-via-device-streams
How-to setup ssh connectivity up to 100 sessions to an IoT Device via IoT-Hub and [Azure Device Streams](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-streams-overview). 


## create an IoT device

You can today use this service, as long it is in preview only dedicated [IoT-Hub regions](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-streams-overview#regional-availability). Go to your IoT-Hub in the right region, and create a new device. If your device is already registered, you can use also the existing registration. If you are working with an IoT-Edge device, you need an additional registration, with a new device id, as a simple device. 

For later, pls note somewhere the Primary Connection String.

## installation on a Linux device

Proceed with the following steps on your Linux device 

```bash
git clone -b DeviceStreaming-PersistentProxy https://github.com/geebinge/azure-iot-sdk-c.git

cd azure-iot-sdk-c
git submodule update --init

mkdir cmake
cd cmake

# in case OpenSSL lib is missing. 
apt-get install libssl-dev 

# in case curl lib is missing. 
apt-get install libcurl4-openssl-dev

cmake ..  

#in case of "uuid/uuid.h: No such file or directory"
apt-get install uuid-dev

make -j 1 

cd ~/azure-iot-sdk-c/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
vi iothub_client_c2d_streaming_proxy_sample.c 

```

go to line 70 (in vi you can do this by pres `:70`) and edit 
```cpp 
static const char* connectionString = "[device connection string]";
```
and update this with your Primary Connection String you have noted in section ["create an IoT device"](#create-an-IoT-device)

```bash
cd ~/azure-iot-sdk-c/cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
make -j 1 
./iothub_client_c2d_streaming_proxy_sample
```

now you are able to connect with the [client](#-setup-the-client-proxy) for test purpose

## if you like to run it as a services 

```bash
mkdir ~/bin 
cp ./iothub_client_c2d_streaming_proxy_sample ~/bin/iot-ssh-client
sudo vi /etc/systemd/system/iot-ssh-client.service
```

insert into `/etc/systemd/system/iot-ssh-client.service`


	[Unit]
	Description=IoT-ssh-Client
	
	[Service]
	ExecStart=/home/<youruserid>/bin/iot-ssh-client
	
	[Install]
	WantedBy=multi-user.target

and run the following steps to install the demon

```bash
sudo systemctl daemon-reload
sudo systemctl start iot-ssh-client.service
sudo systemctl enable  iot-ssh-client.service
systemctl status iot-ssh-client.service
```

## setup the client proxy

to connect to your IoT device via port 22, you have to run a small service on your local machine or a machine inside your local network. Microsoft provided some example code for node.js and C#. With that, you are able to connect with the Service SDK via the IoT Hub to your device. 

This section describes how to do this with node.js. As a first step download and install the latest version of https://nodejs.org, or install nodejs with your Linux installer. 

download the [device streams service package](https://github.com/geebinge/ssh-via-device-streams/blob/main/device-streams-service/device-streams-service.zip) and extract it where ever you like. 

go to your IoT Hub and choose in the left menu "Shared access policies" in the "Security settings" section and choose below "Manage shared access policies" the "service" Policy Name and copy the primary connection string. 

### for Windows 

create a small script like `open_proxy.cmd`

	SET IOTHUB_CONNECTION_STRING=<your service primary connection string> 
	SET STREAMING_TARGET_DEVICE=<the device id from the device you like to connect> 
	SET PROXY_PORT=<choose a port, what ever you like e.g. 2225> 

	node <path2proxy.js>\proxy.js

### for Linux 

create a small script like `open_proxy.sh`

	#!/bin/bash 
	
	export IOTHUB_CONNECTION_STRING="<your service primary connection string>"
	export STREAMING_TARGET_DEVICE="<the device id from the device you like to connect>"
	export PROXY_PORT="<choose a port, what ever you like e.g. 2225>" 

	node <path2proxy.js>/proxy.js

When you start your `open_proxy.cmd` or `open_proxy.sh` this will open the PROXY_PORT on this machine which you can then access with [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), or what every ssh client you like. 

if you using Linux, you may be considered running the local proxy also as a [service](#if-you-like-to-run-it-as-a-services) in a similar way.











