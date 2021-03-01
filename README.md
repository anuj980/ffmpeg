# ffmpeg

#HLS TO YOUTUBE

ffmpeg -re -i "hlsinput" -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k  -g 60 -c:a aac -b:a 128k -ar 44100 -f flv " rtmp://output "


# MULTIPLE INPUT MULTIPLE OUT ( HLS)
ffmpeg -re -i "INPUT 1 " -i "INPUT 2" -map_metadata 0  -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100 -map 0 record.mp4 
-framerate 30 -video_size 720x480 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100 -map 1 record2.mp4


#LOGO ADD ON STREAM
ffmpeg  -i https://live-indiatvnews.akamaized.net/indiatv-origin/liveabr/indiatv-origin/live2/chunks.m3u8 -i C:\Users\transcoder\Videos\star1.png  -filter_complex "overlay=x=main_w-overlay_w-(main_w*0.01):y=main_h*0.01" -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100  record.mp4 



#UDP OUT WITH MPEG-4 (VBR)
ffmpeg   -i input -vcodec libx264 -x264-params keyint=250 -preset medium -profile high -pix_fmt yuv420p -tune zerolatency -b:v 2000K -f mpegts udp://225.2.1.1:1234?pkt_size=1316



#UDP OUT MPEG 4(CBR)
ffmpeg   -i INPUT  -vcodec h264_nvenc -preset medium -profile high -pix_fmt yuv420p -rc 8 -b 2000K -f mpegts udp://225.2.1.1:1234?pkt_size=1316




