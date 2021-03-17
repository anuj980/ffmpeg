# ffmpeg

#'''HLS TO YOUTUBE'''

ffmpeg -re -i "hlsinput" -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k  -g 60 -c:a aac -b:a 128k -ar 44100 -f flv " rtmp://output "


# '''MULTIPLE INPUT MULTIPLE OUT ( HLS)'''
ffmpeg -re -i "INPUT 1 " -i "INPUT 2" -map_metadata 0  -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100 -map 0 record.mp4 
-framerate 30 -video_size 720x480 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100 -map 1 record2.mp4


#'''LOGO ADD ON STREAM'''

ffmpeg  -i https://live-indiatvnews.akamaized.net/indiatv-origin/liveabr/indiatv-origin/live2/chunks.m3u8 -i C:\Users\transcoder\Videos\star1.png  -filter_complex "overlay=x=main_w-overlay_w-(main_w*0.01):y=main_h*0.01" -framerate 30 -video_size 1280x720 -vcodec libx264 -preset veryfast -maxrate 1984k -bufsize 3968k -g 60 -c:a aac -b:a 128k -ar 44100  "record.mp4 "



#'''UDP OUT WITH MPEG-4 (VBR)'''

ffmpeg   -i input -vcodec h264_nvenc -preset medium -profile high -pix_fmt yuv420p -b 2000K -f mpegts "udp://225.2.1.1:1234?pkt_size=1316"



#'''UDP OUT MPEG 4(CBR)'''

ffmpeg   -i INPUT  -vcodec h264_nvenc -preset medium -profile high -pix_fmt yuv420p -rc 8 -b 2000K -f mpegts "udp://225.2.1.1:1234?pkt_size=1316"

#'''UDP OUT MPEG-2 '''

ffmpeg -i udp://@224.2.2.2:2007 -c:v mpeg2video -pix_fmt yuv420p -me_method epzs -threads 4 -r 30.000030 -g 45 -bf 2 -trellis 2 -cmp 2 -subcmp 2 -s 960x720 -b 2500k -bt 300k -async 1  -f mpegts udp://225.2.1.1:1234?pkt_size=1316


#RTP OUT WITH AUDIO & VIDEO

ffmpeg  -i udp://@224.2.2.2:2007 -vcodec h264_nvenc -preset medium -profile high -pix_fmt yuv420p -rc 8 -b 2000K  -f rtp_mpegts  rtp://192.168.0.212:5005

#mosaic out 

ffmpeg -i udp://@224.2.2.2:2000  -i udp://@224.2.2.2:2003  -i udp://@224.2.2.2:2001 -i udp://@224.2.2.2:2004 -i udp://@224.2.2.2:2005 -i udp://@224.2.2.2:2006  -filter_complex " nullsrc=size=960x480 [base]; [0:v] setpts=PTS-STARTPTS, scale=320x240 [video1]; [1:v] setpts=PTS-STARTPTS, scale=320x240 [video2]; [2:v] setpts=PTS-STARTPTS, scale=320x240 [video3]; [3:v] setpts=PTS-STARTPTS, scale=320x240 [video4]; [4:v] setpts=PTS-STARTPTS, scale=320x240 [video5]; [5:v] setpts=PTS-STARTPTS, scale=320x240 [video6]; [base][video1] overlay=shortest=1 [tmp1]; [tmp1][video2] overlay=shortest=1:x=320 [tmp2]; [tmp2][video3] overlay=shortest=1:x=640 [tmp3]; [tmp3][video4] overlay=shortest=1:y=240 [tmp4]; [tmp4][video5] overlay=shortest=1:x=640:y=240 [tmp5]; [tmp5][video6] overlay=shortest=1:x=640:y=240" -c:v libx264 -c:a aac  -f mpegts "udp://225.2.1.1:1234?pkt_size=1316" 




