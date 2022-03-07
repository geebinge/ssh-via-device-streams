# ssh-via-device-streams
HowDo setup ssh connectivity up to 100 sessions to an IoT Device via IoT Hub and [Azure Device Streams](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-streams-overview)


## create an IoT devices

You can today use this serice, as long it is in preview only dedicated [IoTHub regions]. 



## installation on a linux client. 

git clone -b DeviceStreaming-PersistentProxy  https://github.com/geebinge/azure-iot-sdk-c.git


cd azure-iot-sdk-c
git submodule update --init


mkdir cmake
cd cmake

cmake ..
make -j 1 


cd ~/azure-iot-sdk-c/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
vi iothub_client_c2d_streaming_proxy_sample.c 

# edit line 70 "static const char* connectionString = "
# static const char* connectionString = "HostName=IoThub-20201127SSFTest.azure-devices.net;DeviceId=iiot-lab-edge-vm-ds;SharedAccessKey=hoM1F6VNNc2L69+mEWSoBEdF6/RNcjqmMXGFe/W7q0M=";
# Gerhard: HostName=IoThub-20201127SSFTest.azure-devices.net;DeviceId=iiot-lab-edge-vm-ds-gerhard;SharedAccessKey=VRpkheMNVRngYUYUNyzs9gp2Xwn3rTB3KZ44guVaE14=
# demouser: HostName=IoThub-20201127SSFTest.azure-devices.net;DeviceId=iiot-lab-edge-vm-ds-demouser;SharedAccessKey=XO1wl3V9xxgoA11JfFLWQ1pPngxlMcdR02LfPE3+nvc=



cd ~/azure-iot-sdk-c/cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
make -j 1 
./iothub_client_c2d_streaming_proxy_sample



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
