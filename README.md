# srt-live-server
Dockerized version of Edward Wu's SRT Live Server (SLS) and Haivision's SRT SDK
All credit should go to the original tool author(s) - this is just a dockerization for convenience and portability.

Requirements:
* Docker
* Access to Dockerhub, for either the prebuilt image or base layers

To build:
`docker build -t friendly_image_name_goes_here .`

To run from dockerhub:
`docker run -d -p 1935:1935/udp ravenium/srt-live-server`

Notes on the sls.conf (the config for srt-live-server)
* Set port to default of 1935/UDP.  Note that SRT doesn't technically have a default protocol port, so you will have to explicitly call this out in stream URLs (see below).  1935/TCP is what RTMP uses, so this makes it simpler to remember.
* Set default latency to 200ms. (Nimble Streamer recommendeds no lower than 120ms no matter what, Haivision can do lower on hardware)  If your streams are getting "confetti" you may want to set this higher, but I've found this to be a safe default in using OBS and Larix SRT streams over a reasonable internet connection. Think of this as a "safety buffer" for connection burps.
* There are two "endpoints", a publisher and an application.  Publishing is for sending, application is for recieving. They are "input/live" and "output/live", respectively - I changed them from the author's defaults to make them a bit less confusing.


Example Sending of SRT in OBS:
* In the setup menu under "stream", select "Custom..."  leave the Key field blank.
* Put the following url to send to your docker container: `srt://your.server.ip:1935?streamid=input/live/yourstreamname`

Example of Receiving of SRT in OBS:
* Add a Media Source
* Put the following url to receive: `srt://your.server.ip:1935?streamid=output/live/yourstreamname`

Errata, thoughts, future:
* There are some things in the config that don't seem to work right yet, e.g. the on_event and status_url directives.  If anyone gets these working, please let me know!
* The container builds on the latest SRT SDK at build time (1.4.1 as of this writing) and srt-live-server (1.4.8) so try rebuilding the container if you need a new feature in either that isn't here yete.  No guarantee future things won't break it.
* You can map a docker volume so you can change your SLS config, then reload the container.  Add the following to your docker run line: `-v /path/to/your/local/sls.conf:/etc/sls/sls.conf`
* I'm looking for a way to monitor log output and parse the results for feed start/stop to make a stats server similar to nginx-rtmp, so if any bash/python/grep ninjas want to give it a whirl, please submit a PR!


References:

https://github.com/Edward-Wu/srt-live-server

https://blog.wmspanel.com/2019/06/srt-latency-maxbw-efficient-usage.html

https://www.haivision.com/blog/all/how-to-configure-srt-settings-video-encoder-optimal-performance/


