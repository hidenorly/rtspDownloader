# rtspDownloader

This is downloader for rtsp:// stream as .mp4.
This is intended to store security camera's video stream as .mp4.
Usually multiple security cameras are installed, then this tool supports multiple download.


# Requirements

```
$ sudo apt install ffmpeg
```

# Storage

You need to prepare high endurance storage such as HDD.

```/media/data/ftproot/``` is example path in the following ```config.json```

```
$ cd /media/data
$ sudo chown ubuntu:ubuntu ftproot/ -R
```

You need to ensure the access permission if you want to run as "ubuntu" as same as the following ```config.json``` example.


# Config

```config.json
{ # You config multiple cameras as follows:
	"camera1":{
		"url":"rtsp://192.168.1.100/11",			# usually security camera /11:1st stream /12:2nd stream (down scaled)
		"user": "admin",							# you can set "" if no user authentication
		"password": "password",						# you can set "" if no user authentication
		"runas": "ubuntu",							# set as-user to run ffmpeg. if you want to run same user of this, you can remove this line or set ""
		"options": "-c copy",						# try "-c:v copy" if error on audio
		"duration": 300,							# 300sec = 5min
		"output": "/media/data/ftproot/camera1",	# file place
		"fileFormat": "camera1-%Y%m%d-%H%M%S.mp4",	# file format
		"keep": 4032,								# keep 4032 files then 300sec*4032/3600/24=14days
		"errorRetry": "enable",						# re-execute ffmpeg if error happened
		"errorSleep": 5,							# wait the sec when ffmpeg error happened
		"errorRetyCount": 100,						# error retry count if exceed, give up to retry
		"log": "/dev/null"							# if ffmpeg output is necessary, you need to set the file name
	},
	"camera2":{
		"url":"rtsp://192.168.1.101/11",			# usually security camera /11:1st stream /12:2nd stream (down scaled)
		"user": "admin",							# you can set "" if no user authentication
		"password": "password",						# you can set "" if no user authentication
		"runas": "ubuntu",							# set as-user to run ffmpeg. if you want to run same user of this, you can remove this line or set ""
		"options": "-c:v copy",						# try "-c:v copy" if error on audio. May add  "-rtsp_transport tcp -stimeout 15000000"
		"duration": 300,							# 300sec = 5min
		"output": "/media/data/ftproot/camera2",	# file place
		"fileFormat": "camera2-%Y%m%d-%H%M%S.mp4",	# file format
		"keep": 4032,								# keep 4032 files then 300sec*4032/3600/24=14days
		"errorRetry": "enable",						# re-execute ffmpeg if error happened
		"errorSleep": 5,							# wait the sec when ffmpeg error happened
		"errorRetyCount": 100,						# error retry count if exceed, give up to retry
		"log": "/dev/null"							# if ffmpeg output is necessary, you need to set the file name
	} # end of config should not have ","
}
```

# Install (for Ubuntu)

```
$ sudo ./install.sh
```

# Start

```
$ sudo systemctrl start rtsp_downloader
```

# Check

```
$ sudo systemctrl status rtsp_downloader
```

# Restart

```
$ sudo systemctrl restart rtsp_downloader
```

# Optional

```
$ sudo vim /etc/systemd/system/rtsp_downloader.service
```

```/etc/systemd/system/rtsp_downloader.service
ExecStart=/usr/bin/ruby /opt/rtspdownloader/rtspDownloader.rb -c /var/opt/rtspdownloader/config.json -r "2 5 * * *"
```

```
$ systemctl enable rtsp_downloader
$ systemctl restart rtsp_downloader
```

# TODO

* [x] Add ubuntu's service to manage with systemctl
* [x] Add installer
* [x] Add keep files (disk quota) to enable cyclic store
* [x] Add killer & retry if ffmpeg's download is failed
* [] Upload to youtube?
