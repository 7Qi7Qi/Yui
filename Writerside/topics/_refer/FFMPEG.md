# FFMPEG

## [FFMPEG cheat sheet](https://gist.github.com/steven2358/ba153c642fe2bb1e47485962df07c730)

### Basic conversion
`ffmpeg -i in.mp4 out.avi`
### Remux(lossless) an MKV file into MP4
`ffmpeg -i in.mkv -c:v copy -c:a copy out.mp4`
### High-quality encoding
use the `crf`(Constant Rate Factor) parameter to control the output quality. 
The lower crf, the higher the quality(range：0-51). The default value is 23,
and visually lossless compression corresponds to `-crf 18`. Use the `preset`
parameter to control the speed of the compression process

`ffmpeg -i in.mp4 -preset slower -crf 18 out.mp4`
### Trimming
Without re-encoding:

`ffmpeg -ss [start] -i in,mp4 -t [duration] -c copy out.mp4`
- `-ss` start time e.g. `00:01:23.000` or `83`seconds
- `-t` the duration of the clip (same format) also can use `-to`
- `-c copy` copy file without re-encoding and make the command within seconds

With re-encoding

`ffmpeg -ss [start] -i in.mp4 -t [duration] -c:v libx264 -c:a aac -strict experimental -b:a 128 out.mp4`

Leave out `-c copy` option, ffmpeg will automatically re-encode the output video and audio
according to the format you choose
### Mux video and audio from another video
`ffmpeg -i in1.mp4 -i in2.mp4 -c copy -map 0:0 -map 1:1 -shortest out.mp4` 

- With`-c copy` the streams will be `stream copied`, not re-encoded, so there will 
  be no quality loss
- The `-shortest` option will cause the output duration to match the duration of the 
  shortest input stream
- See the `-map` [option documentation](http://ffmpeg.org/ffmpeg.html#Advanced-options) for more info
### Concat demuxer
First, make a text file
```
file 'in1.mp4'
file './in2.mp4'
file 'in3.mp4'
```
Then, run `ffmepg`

`ffmpeg -f concat -i list.txt -c copy out.mp4`

If you get `unsafe file name 'in1.mp4' list.txt: Operation not permitted`

Execute command `ffmpeg -f concat -safe 0 -i list.txt -c copy out.mp4`
### Delay audio/video
Delay video by 3.84 seconds:

`ffmpeg -i in.mp4 itsoffset 3.84 -i in.mp4 -map 1:v -map 0:a vcodec copy -acodec copy out.mp4`

Delay audio by 3.84 seconds:

`ffmpeg -i in.mp4 itsoffset 3.84 -i in.mp4 -map 0:v -map 1:a vcodec copy -acodec copy out.mp4`
### Burn subtitles
use the [libass](http://ffmpeg.org/ffmpeg.html#ass) library (make sure your ffmpeg install has the library in the configuration `--enable-libass`)

First convert the subtitles to .ass format

`ffmpeg -i sub.srt sub.ass`

Then add them using a video filter

`ffmpeg -i in.mp4 -vf ass=sub.ass out.mp4`
### Extract the frames from a video
To extract all frames from between 1 and 5 seconds, and also between 11 and 15 seconds

`ffmpeg -i in.mp4 -vf select='between(t,1,5)+between(t,11,15)' -vsync 0 out%d.png`

To extract one frame per seconds only

`ffmpeg -i in.mp4 -fps=1 -vsync 0 out%d.png`

### Rotate a video
Rotate 90 clockwise

`ffmpeg -i in.mov -vf "transpose=1" out.mov`

For the transpose parameter you can pass:
```
0 = 90CounterCLockwise and Vertical Flip (default)
1 = 90Clockwise
2 = 90CounterClockwise
3 = 90Clockwise and Vertical Flip
```
e.g. use `-vf "transpose=2,transpose=2` for 180 degrees.
### Download "Transport Stream" video streams
- Locate the playlist file, e.g. using Chrome > F12 > Network > Filter: m3u8
- Download and concatenate the video fragments:

`ffmpeg -i "path_to_playlist.m3u8" -c copy -bsf:a aac_adtstoasc out.mp4`

If you get a "Protocol 'https not on whitelist 'file, crypto'!" error, add the `protocol_whitelist` option

`ffmpeg -protocol_whitelist "file,http,https,tcp,tls" -i "path_to_playlist.m3u8" -c copy -bsf:a aac_adtstoasc out.mp4`

e.g. m3u8 file to video `ffmpeg -allowed_extensions ALL -protocol_whitelist "file,http,crypto,tcp" -i index.m3u8 -c copy out.mp4`

`ffmpeg -i https://xxx.com/index.m3u8 -c copy aaa.mp4`

### Mute some of the audio
To replace the first 90 seconds of audio with silence

`ffmpeg -i in.mp4 -vcodec copy -af "volumn=enable='lte(t, 90)':volumn=0" out.mp4`

To replace all audio between 1'20'' and 1'30'' with silence

`ffmpeg -i in.mp4 -vcodec copy -af "volumn=enable='lte(t, 80, 90)':volumn=0" out.mp4`

### Deinterlace(can't understand what is)
`ffmpeg -i in.mp4 -vf yadif out.mp4`

### Create a video slideshow from images
`ffmpeg -r 1/5 -i img%03d.png -c:v libx264 -vf fps=25 -pix-fmt yuv420p out.mp4`

Parameters:`-r` marks the image framerate(inverse time of each image); 
`-vf fps=25` marks the true framerate of the output 
### Extract images from a video
- All frames: `ffmpeg -i in.mp4 thumb%04d.jpg -hide_banner`
- A frame per second: `ffmpeg -i in.mp4 -vf fps=1 thumb%04d.jpg -hide_banner`
- Only one frame: `ffmpeg -i in.mp4 -ss 00.00.10.000 -vframes 1 thumb.jpg`
### Metadata: Change the title
`ffmpeg -i in.mp4 -map_metadata -1 -metadata title="My Title" -c:v copy -c:a copy out.mp4`


```
24 = 4 * 3 * 2
60 = 5 * 4 * 3
1000 = 5 * 5 * 5 * 4 * 2
Millseconds in one day = 5^5 * 4^4 * 3^3 * 2^2 * 1^1
```