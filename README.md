# ssh-via-device-streams
HowDo setup ssh connectivity up to 100 sessions to an IoT Device via IoT-Hub and [Azure Device Streams](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-streams-overview){:target="_blank" rel="noopener"}


## create an IoT devices

You can today use this service, as long it is in preview only dedicated [IoT-Hub regions](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-streams-overview#regional-availability){:target="_blank" rel="noopener"}. Go to your IoT-Hub in the right region, and create a new device. If your device is already registered, you can use also the existing registration. If you are working with an IoT-Edge device, you need an additional registration, with a new device id, as a simple device. 

For later, pls note somewhere the Primary or Secondary Connection String


## installation on a linux client. 

Produce with the following steps on your Linux device 

```bash
git clone -b DeviceStreaming-PersistentProxy  https://github.com/geebinge/azure-iot-sdk-c.git

cd azure-iot-sdk-c
git submodule update --init


mkdir cmake
cd cmake

cmake ..
make -j 1 


cd ~/azure-iot-sdk-c/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
vi iothub_client_c2d_streaming_proxy_sample.c 

```

go to line 70 and edit 
```cpp 
static const char* connectionString = "[device connection string]";
```
and update this with your connection string you have noted 

```bash
cd ~/azure-iot-sdk-c/cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
make -j 1 
./iothub_client_c2d_streaming_proxy_sample
```

now you are able connect with the 


To run it as a services 

mkdir ~/bin 
cp ./iothub_client_c2d_streaming_proxy_sample ~/bin/iot-ssh-client

sudo vi /etc/systemd/system/iot-ssh-client.service

	[Unit]
	Description=IoT-ssh-Client
	
	[Service]
	ExecStart=/home/demouser/bin/iot-ssh-client
	
	[Install]
	WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl start iot-ssh-client.service
sudo systemctl enable  iot-ssh-client.service
systemctl status iot-ssh-client.service
![image](https://user-images.githubusercontent.com/32458010/157073198-b0807b72-d5b4-403a-95a1-a5cf461935f2.png)


setup the 


