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
>[!WARNING]
>Tùy chọn này thường trả về định dạng video chất lượng rất thấp (ngang vietKTV) => nên tránh
```
yt-dlp -f best -g "https://www.youtube.com/watch?v=dPzT0oFX3AE"
```

## Get format tốt nhất của video và audio lại. (chính là video có ID cao nhất và audio có ID cao nhất)
>[!WARNING]
>nếu dùng FFmpeg để stream ra stdout bằng mpegts thì ko nên dung cách này vò mpegts chỉ hỗ trợ video  H.264, MPEG-2. audio:AAC, MP2
>FFPEG-mpegts không tương thích với video codect: VP8, VP9. audio codect: Opus, Vorbis
```
yt-dlp -f best -g "https://www.youtube.com/watch?v=dPzT0oFX3AE"
```
## Get format tốt nhất cho ffmpeg+mpegts (giới hạn độ phân giải tối đa 1080p)
>[!WARNING]
>nếu dùng FFmpeg để stream ra stdout bằng mpegts thì ko nên dung cách này vò mpegts chỉ hỗ trợ video  H.264, MPEG-2. audio:AAC, MP2
>FFPEG-mpegts không tương thích với video codect: VP8, VP9. audio codect: Opus, Vorbis
```
yt-dlp \
  -f "bv[ext=mp4][vcodec*=avc1][height<=1080]+ba[ext=m4a][acodec*=mp4a]" \
  --get-url \
  "https://www.youtube.com/watch?v=VIDEO_ID"
```


