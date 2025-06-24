## dùng FFmpeg để merge video/audio thành 1 luồng và xuất ra STDOUT:
```
./ffmpeg -i "https://rr8---sn-42u-jmge.googlevideo.com/videoplayback?expire=" -i "https://rr8---sn-42u-jmge.googlevideo.com/" \
       -c:v copy -c:a copy -map 0:v:0 -map 1:a:0 \
       -f mp4 -movflags frag_keyframe+empty_moov pipe:1|'/c/Program Files/VideoLAN/VLC/vlc.exe' -
```
>[!WARNING]
>KHÔNG DECODE VIDEO/AUDIO

