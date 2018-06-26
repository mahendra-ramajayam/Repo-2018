# Raspberry Pi3 Model B for IoT Project - Ubuntu Core  

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/rasp_pin_assignment.JPG>  

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/Raspberry_run.png>  

<b>1 - Plug and Play</b>  

Open "Disks" and find path to SDCard.  
Download Ubuntu Core Image: http://cdimage.ubuntu.com/ubuntu-core/16/stable/current/ubuntu-core-16-pi3.img.xz  
Flash it with Etcher to SDCard: https://etcher.io/  

Insert SDCard into Raspberry.  

Back to your computer, generate private and public key.

```
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa -b 4096
```  
Copy key code and paste into Ubuntu One:  

```
ssh-rsa ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345ABCDEF12345 user@Dell
```

Turn on Raspberry:

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/raspberry_OK%20(1).png>  

Configure network and setup administrator account connected to Ubuntu One. SSH into your Raspberry. 

```
sudo apt-get install openssh-server
sudo ufw allow 22
sudo service ssh start
ssh your_user@191.168.15.XXX
```  

```
ssh-keygen -R 192.168.15.XXX
cd .ssh
rm *
cp id_rsa ~/.ssh/id_rsa
cp id_rsa.pub ~/.ssh/id_rsa.pub
ssh your_user@191.168.15.XXX
```  

Your login will change:
```
rubens@localhost:~$
```  

You're in ! Install snap classic.  

```
sudo snap install classic --edge --devmode
sudo classic
Creating classic environment
Parallel unsquashfs: Using 4 processors
11111 inodes (11975 blocks) to write

[===========================================================/] 11975/11975 100%

(classic)rubens@localhost:~$ sudo apt update
sudo apt install snapcraft build-essential git
```

<b>2 - Collect CPU Temperature and Connect Raspberry to AWS IoT</b>  

```
import shlex
import subprocess
def measure_temp():
        temp = subprocess.Popen(shlex.split('sensors'),
                                stdout=subprocess.PIPE,
                                bufsize=10, universal_newlines=True)
        return temp.communicate()[0]
```  

```
export LC_ALL=C
source .bashrc
pip install setuptools
pip install AWSIoTPythonSDK
```  

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/temp_run.png>

Create a blank file, copy and paste contents of AWS_Send_0.py via vim, then ESC:wq ENTER:  

```
touch AWS_Send_0.py
vi AWS_Send_0.py
```

```
python AWS_Send_0.py -e a23312345.iot.us-east-1.amazonaws.com -r rootCA.pem -c 123412345-certificate.pem.crt -k 12345-private.pem.key -id arn:aws:iot:us-east-1:1123112345:thing/CPUUbuntu -t 'Teste'
```  

Now you can ping AwS IoT Core:  

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/AWS_Raspberry.png>  

Copy certificates to your Raspberry:  

```
lsblk

NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    1  58.2G  0 disk 
`-sda1        8:1    1    32G  0 part 
loop0         7:0    0  70.9M  0 loop /snappy
loop1         7:1    0  85.6M  0 loop 
loop2         7:2    0  70.9M  1 loop 
loop3         7:3    0  85.6M  1 loop 
loop4         7:4    0   5.9M  1 loop 
loop5         7:5    0  73.4M  1 loop 
loop6         7:6    0 128.8M  1 loop 
loop7         7:7    0     4K  1 loop 
mmcblk0     179:0    0   7.4G  0 disk 
|-mmcblk0p1 179:1    0   128M  0 part 
`-mmcblk0p2 179:2    0   7.3G  0 part /home

sudo mkdir /mnt/usb
sudo mount /dev/sda1 /mnt/usb
sudo cp /mnt/usb/27aa12345-public.pem.key 27aa12345-public.pem.key
```  
Remember to copy rootCA.pem + *.crt + *private.pem.key + *.public.pem.key

<b>4 - Send Data to AWS</b>  

Run AWS_Send_0.py at rubens@localhost:~$

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/AWS_running_Raspberry.png>  

<img src=https://github.com/RubensZimbres/Repo-2018/blob/master/Raspberry%20Pi3%20IoT-Project/Pictures/Raspberry_AWS_Success.png>

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python-pip python-dev
sudo pip install RPi.GPIO
```  

```
import RPi.GPIO as GPIO # Import Raspberry Pi GPIO library
from time import sleep # Import the sleep function from the time module

GPIO.setwarnings(False) # Ignore warning for now
GPIO.setmode(GPIO.BOARD) # Use physical pin numbering
GPIO.setup(8, GPIO.OUT, initial=GPIO.LOW) # Set pin 8 to be an output pin and set initial value to low (off)

while True: # Run forever
    GPIO.output(8, GPIO.HIGH) # Turn on
    sleep(1) # Sleep for 1 second
    GPIO.output(8, GPIO.LOW) # Turn off
    sleep(1) # Sleep for 1 second
 ```
<b>4 - Save data into Dynamo DB</b>  

<b>5 - Add sensor</b>  

<b>6 - Load saved Machine Learning model for anomaly detection</b>  

<b>7 - Dashboard</b>  

LogOff:
```
sudo halt -h
```