# Linux screen recording and encoding with ffmpeg

### Hardware

- CPU: AMD Ryzen 7, 5800X
- GPU: AMD Radeon 5700XT
- Memory: 32gb

### Terminology

- `hwdownload`: "Download" data from the GPU to system memory
- `hwupload`: "Upload" data from system memory to GPU


## Hardware acceleration

- <https://trac.ffmpeg.org/wiki/Hardware/VAAPI>


# "Record the screen"

## Audio

- <https://trac.ffmpeg.org/wiki/Encode/HighQualityAudio>

This might work: `-c:a libopus`

Bitrate: `-b:a 192k`

## Get `-crtc_id`:

```shell
modetest | less
```

Look for "Encoders", map `crtc` to corresponding "encoder" value for the display ("Connector") you want to use.


## Hardware encoding for v4l2loopback

You need to be root to use `kmsgrab` or add the capability `cap_sys_admin+ep` to ffmpeg binary:

```shell
sudo setcap cap_sys_admin+ep /usr/bin/ffmpeg
```

```shell
ffmpeg -y -device /dev/dri/card0 -f kmsgrab -framerate 60 -i - -vf 'hwmap=derive_device=vaapi,scale_vaapi=w=1920:h=1080:format=nv12,hwdownload' -vcodec rawvideo -pix_fmt yuv420p -f v4l2 /dev/video2

# Might also need `-device /dev/dri/[card] for this
ffmpeg -framerate 30 -f kmsgrab -crtc_id 77 -thread_queue_size 64 -i - -vaapi_device /dev/dri/renderD128 -vf 'hwmap=derive_device=vaapi,scale_vaapi=format=nv12,hwdownload' -vcodec rawvideo -pix_fmt yuv420p -bufsize 0 -f_strict experimental -syncpoints none -f v4l2 /dev/video2
```

Record to file:

```shell
ffmpeg -f kmsgrab -crtc_id 77 -thread_queue_size 64 -i - -vaapi_device /dev/dri/renderD128 -vf 'hwmap=derive_device=vaapi,scale_vaapi=format=nv12,hwupload' -c:v h264_vaapi -qp 28 -profile:v 100 -bufsize 0 -f_strict experimental -syncpoints none -f mpegts recording_ffmpeg.mp4

# With audio, using pulse and the interface (`-i`) `alsa_output.usb-Focusrite_Scarlett_Solo_USB_Y7KQXJC19168CF-00.analog-stereo.monitor`
ffmpeg -f pulse -thread_queue_size 64 -i alsa_output.usb-Focusrite_Scarlett_Solo_USB_Y7KQXJC19168CF-00.analog-stereo.monitor -ac 2 -f pulse -thread_queue_size 64 -i rnnoise_source -f kmsgrab -thread_queue_size 64 -crtc_id 77 -framerate 60 -i - -vaapi_device /dev/dri/renderD128 -vf 'hwmap=derive_device=vaapi,scale_vaapi=format=nv12,hwupload' -c:v hevc_vaapi -qp 32 -bufsize 0 -f_strict experimental -syncpoints none -f mpegts recording_ffmpeg.mp4
```

Crop and/or record a specific region of the screen with:

```shell
'hwmap=derive_device=vaapi,crop=[width]:[heigh]:[start_x]:[start_y],scale_vaapi=w=[width]:h=[height]'
```

## `wf-recorder`

```shell
wf-recorder -g "$(slurp)" -p format='nv12,hwupload' --codec=hevc_vaapi -d /dev/dri/renderD128
```

VAAPI codecs: `h264_vaapi`,`hevc_vaapi`.

Change framerate with `-F fps=[fps]`. 

> VAAPI requires `hwupload,scale_vaapi=nv12` to work.

So to change framerate when using vaapi: `--filter-string fps=30,hwupload,scale_vaapi=nv12`

# Encoding

h264 hardware encoding using `ffmpeg` with `kmsgrab`:

- *Very high quality*:
  ```shell
  ffmpeg -f pulse -thread_queue_size 64 -i alsa_output.usb-Focusrite_Scarlett_Solo_USB_Y7KQXJC19168CF-00.analog-stereo.monitor -ac 2 -f pulse -thread_queue_size 64 -i rnnoise_source -f kmsgrab -thread_queue_size 64 -crtc_id 77 -framerate 60 -i - -vaapi_device /dev/dri/renderD128 -vf 'hwmap=derive_device=vaapi,scale_vaapi=format=nv12,hwupload' -c:v h264_vaapi -qp 1 -bufsize 0 -f_strict experimental -syncpoints none -f mpegts recording_ffmpeg.mp4
  ```

  ```shell
  ffmpeg -f kmsgrab -crtc_id 77 -thread_queue_size 64 -framerate 60 -i - -vaapi_device /dev/dri/renderD128 -vf 'hwmap=derive_device=vaapi,scale_vaapi=format=nv12,hwupload' -c:v h264_vaapi -qp 1 -profile:v 100 -bufsize 0 -f_strict experimental -syncpoints none recording_ffmpeg.mp4
  ```

## VP9

- <https://streaminglearningcenter.com/codecs/encoding-vp9-in-ffmpeg-an-update.html#The_Recommended_Command_String>
- <https://trac.ffmpeg.org/wiki/Encode/VP9>

This first command seems to produce smaller output with the same perceived quality.

```shell
# Two-pass
ffmpeg -i recording_ffmpeg.mp4  -c:v libvpx-vp9 -b:v 0 -crf 30 -pass 1 -an -f null /dev/null && \
ffmpeg -i recording_ffmpeg.mp4 -c:v libvpx-vp9 -b:v 0 -crf 30 -pass 2 -c:a libopus output.webm
```

Other options. Might be better:

```shell
# Two-pass
ffmpeg -y -i recording_ffmpeg.mp4 -c:v libvpx-vp9 -pass 1 -b:v 2000K -g 48 \
-keyint_min 48 -sc_threshold 0 -threads 8 -speed 4 -row-mt 1 \
-tile-columns 4 -f webm /dev/null &&\
ffmpeg -i recording_ffmpeg.mp4 -c:v libvpx-vp9 -pass 2 -b:v 2000K -minrate 2000K \
-maxrate 4000K -g 48 -keyint_min 48 -sc_threshold 0 -row-mt 1 -threads 8 \
-speed 2 -tile-columns 4 output.webm
```

## AV1

Seems really slow (1-10fps encoding). Atleast on AMD hardware, and no hardware acceleration with a 5700XT.

Generates invalid files?

```
[ffmpeg/video] av1: Missing Sequence Header.
Error while decoding frame!
```

```
ffmpeg version n5.1.2
  libavutil      57. 28.100 / 57. 28.100
  libavcodec     59. 37.100 / 59. 37.100
  libavformat    59. 27.100 / 59. 27.100
  libavdevice    59.  7.100 / 59.  7.100
  libavfilter     8. 44.100 /  8. 44.100
  libswscale      6.  7.100 /  6.  7.100
  libswresample   4.  7.100 /  4.  7.100
  libpostproc    56.  6.100 / 56.  6.100
```

- <https://trac.ffmpeg.org/wiki/Encode/AV1>
- <https://gitlab.com/AOMediaCodec/SVT-AV1/-/blob/master/Docs/Ffmpeg.md>

```shell
# Two-pass
ffmpeg -i recording_ffmpeg.mp4  -c:v libaom-av1 -b:v 2M -pass 1 -an -f null /dev/null && \
ffmpeg -i recording_ffmpeg.mp4  -c:v libaom-av1 -b:v 2M -pass 2 -c:a libopus output.mkv
```

```shell
ffmpeg -i recording_ffmpeg.mp4  -c:v libsvtav1 -preset 5 -crf 32 -g 240 -pix_fmt yuv420p10le -svtav1-params tune=0 -c:a copy output.mkv
```

(Might work, idk. Check the internet or something for better options/presets..)

# Convert

Copy subtitle tracks with `-c:s copy`. And specific tracks with `-c:s:0 copy`.

## x264 10-bit to HEVC

- <https://trac.ffmpeg.org/wiki/Hardware/VAAPI#FullExamples>

Without hardware acceleration (`libx265`):

```shell
ffmpeg -i input.mkv -c:v libx265 -vtag hvc1 -c:a copy -c:s copy output.mkv
```

With hardware acceleration (`hevc_vaapi`):

```shell
ffmpeg -vaapi_device /dev/dri/renderD128 -i input.mkv -vf 'format=p010,hwupload' -c:a copy -c:v hevc_vaapi -profile:v main10 output.mkv
```
