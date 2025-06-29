## dùng FFmpeg để merge video/audio thành 1 luồng và xuất ra STDOUT: (WINDOWS syntax) 
```
./ffmpeg -i "http://example.com/video.mp4" \
       -i "http://example.com/audio.mp3" \
       -c:v copy \
       -c:a copy \
       -map 0:v:0 \
       -map 1:a:0 \
       -f mpegts pipe:1|'/c/Program Files/VideoLAN/VLC/vlc.exe' -
```
>[!WARNING]
>KHÔNG DECODE VIDEO/AUDIO

>[NOTE]
>-map = chỉ định rõ bạn muốn lấy stream nào từ các input
>-map [file_index]:[stream_type]:[stream_index]
>0:v:0 = file đầu vào thứ 0, video stream số 0
>1:a:0 = file đầu vào thứ 1, audio stream số 0
## Get format tốt nhất mà có cả video và audio (pre-merged format)
```
yt-dlp -f best -g "https://www.youtube.com/watch?v=dPzT0oFX3AE"
```
