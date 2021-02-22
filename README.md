# ffmpeg

#HLS TO YOUTUBE

ffmpeg -re -i "hlsinput" -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k  -g 60 -c:a aac -b:a 128k -ar 44100 -f flv " rtmp://output "






