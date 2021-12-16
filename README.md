# ffmpeg



#'''HLS TO Facebook'''

ffmpeg -re -y -i https://feeds.intoday.in/aajtak/api/aajtakhd/master.m3u8 -c:a copy -ac 1 -ar 44100 -b:a 96k -vcodec libx264 -pix_fmt yuv420p  -r 30 -g 60 -tune zerolatency  -maxrate 2000k -preset veryfast  -f flv rtmps://live-api-s.facebook.com:443/rtmp/FB-996056144310758-0-AbziA61opBf49Av6

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

ffmpeg -i udp://@224.2.2.2:2000  -i udp://@224.2.2.2:2003  -i udp://@224.2.2.2:2001 -i udp://@224.2.2.2:2004 -i udp://@224.2.2.2:2005 -i udp://@224.2.2.2:2006  
-filter_complex " nullsrc=size=960x480 [base];
[0:v] setpts=PTS-STARTPTS, scale=320x240 [video1]; 
[1:v] setpts=PTS-STARTPTS, scale=320x240 [video2];
[2:v] setpts=PTS-STARTPTS, scale=320x240 [video3];
[3:v] setpts=PTS-STARTPTS, scale=320x240 [video4]; 
[4:v] setpts=PTS-STARTPTS, scale=320x240 [video5]; 
[5:v] setpts=PTS-STARTPTS, scale=320x240 [video6];
[base][video1] overlay=shortest=1 [tmp1]; 
[tmp1][video2] overlay=shortest=1:x=320 [tmp2];
[tmp2][video3] overlay=shortest=1:x=640 [tmp3];
[tmp3][video4] overlay=shortest=1:y=240 [tmp4]; 
[tmp4][video5] overlay=shortest=1:x=320:y=240 [tmp5];
[tmp5][video6] overlay=shortest=1:x=640:y=240" 
-c:v libx264 -c:a aac  -f mpegts "udp://225.2.1.1:1234?pkt_size=1316" 


#MOSAIC OUT -9
ffmpeg   -i udp://@224.2.2.2:2000?fifo_size=1000000 -i udp://@224.2.2.2:2001?fifo_size=1000000 -i udp://@224.2.2.2:2002?fifo_size=1000000 -i udp://@224.2.2.2:2003?fifo_size=1000000 -i udp://@224.2.2.2:2004?fifo_size=1000000 -i udp://@224.2.2.2:2005?fifo_size=1000000 -i udp://@224.2.2.2:2006?fifo_size=1000000 -i udp://@224.2.2.2:2007?fifo_size=1000000 -i udp://@224.2.2.2:2008?fifo_size=1000000  -vn -acodec pcm_s16le -ac 8000 -f segment -segment_time 3 -reset_timestamps 1 -strftime 1   
-filter_complex " nullsrc=size=1440x810  [base]; 
[0:v] setpts=PTS-STARTPTS, scale=480x270 [video1];
[1:v] setpts=PTS-STARTPTS, scale=480x270 [video2];
[2:v] setpts=PTS-STARTPTS, scale=480x270 [video3]; 
[3:v] setpts=PTS-STARTPTS, scale=480x270 [video4]; 
[4:v] setpts=PTS-STARTPTS, scale=480x270 [video5]; 
[5:v] setpts=PTS-STARTPTS, scale=480x270 [video6]; 
[6:v] setpts=PTS-STARTPTS, scale=480x270 [video7]; 
[7:v] setpts=PTS-STARTPTS, scale=480x270 [video8]; 
[8:v] setpts=PTS-STARTPTS, scale=480x270 [video9];
[base][video1] overlay=shortest=0 [tmp1];
[tmp1][video2] overlay=shortest=0:x=480 [tmp2];
[tmp2][video3] overlay=shortest=0:x=960 [tmp3];
[tmp3][video4] overlay=shortest=0:y=270 [tmp4]; 
[tmp4][video5] overlay=shortest=0:x=480:y=270 [tmp5];
[tmp5][video6] overlay=shortest=0:x=960:y=270 [tmp6]; 
[tmp6][video7] overlay=shortest=0:y=540 [tmp7]; 
[tmp7][video8] overlay=shortest=0:x=480:y=540 [tmp8];
[tmp8][video9] overlay=shortest=0:x=960:y=540 "
-c:v libx264  -an -f flv rtmp://localhost:1935/live/stream



WHEN  USE ULTRAFAST PRESET ITS USE LESS CPU


#MOSAIC OUT -12
ffmpeg   -i udp://@224.2.2.2:2000?fifo_size=1000000 -i udp://@224.2.2.2:2001?fifo_size=1000000 -i udp://@224.2.2.2:2002?fifo_size=1000000 -i udp://@224.2.2.2:2003?fifo_size=1000000 -i udp://@224.2.2.2:2004?fifo_size=1000000 -i udp://@224.2.2.2:2005?fifo_size=1000000 -i udp://@224.2.2.2:2006?fifo_size=1000000 -i udp://@224.2.2.2:2007?fifo_size=1000000 -i udp://@224.2.2.2:2008?fifo_size=1000000 -i udp://@224.2.2.2:2009?fifo_size=1000000 -i udp://@224.2.2.2:2010?fifo_size=1000000 -i udp://@224.2.2.2:2011?fifo_size=1000000      
-filter_complex "nullsrc=size=1920x1080 [base]; 
[0:v] setpts=PTS-STARTPTS, scale=480x360 [video1];
[1:v] setpts=PTS-STARTPTS, scale= 480x360 [video2]; 
[2:v] setpts=PTS-STARTPTS, scale=480x360 [video3]; 
[3:v] setpts=PTS-STARTPTS, scale=480x360 [video4];
[4:v] setpts=PTS-STARTPTS, scale= 480x360 [video5];
[5:v] setpts=PTS-STARTPTS, scale=480x360 [video6];
[6:v] setpts=PTS-STARTPTS, scale=480x360 [video7]; 
[7:v] setpts=PTS-STARTPTS, scale= 480x360 [video8];
[8:v] setpts=PTS-STARTPTS, scale=480x360 [video9]; 
[9:v] setpts=PTS-STARTPTS, scale=480x360 [video10];
[10:v] setpts=PTS-STARTPTS, scale= 480x360 [video11]; 
[11:v] setpts=PTS-STARTPTS, scale=480x360 [video12]; 
[base][video1] overlay=shortest=0 [tmp1];
[tmp1][video2] overlay=shortest=0:x= 480[tmp2]; 
[tmp2][video3] overlay=shortest=0:x=960 [tmp3]; 
[tmp3][video4] overlay=shortest=0:x=1440 [tmp4];
[tmp4][video5] overlay=shortest=0:y=360 [tmp5]; 
[tmp5][video6] overlay=shortest=0:x=480:y=360 [tmp6]; 
[tmp6][video7] overlay=shortest=0:x=960:y=360 [tmp7];
[tmp7][video8] overlay=shortest=0:x=1440:y=360 [tmp8];
[tmp8][video9] overlay=shortest=0:y=720 [tmp9]; 
[tmp9][video10] overlay=shortest=0:x=480:y=720 [tmp10]; 
[tmp10][video11] overlay=shortest=0:x=960:y= 720 [tmp11]; 
[tmp11][video12] overlay=shortest=0:x=1440:y=720" 
-c:v libx264  -b:v 2M -minrate 2M -maxrate 2M  -preset ultrafast  -an -f mpegts "udp://225.2.1.1:1234?pkt_size=1316"


# 12 CHANNEL MOSAIC WITH AUDIO BAR 

ffmpeg   -i udp://@224.2.2.2:2000?fifo_size=1000000 -i udp://@224.2.2.2:2001?fifo_size=1000000 -i udp://@224.2.2.2:2002?fifo_size=1000000 -i 
udp://@224.2.2.2:2003?fifo_size=1000000 -i udp://@224.2.2.2:2004?fifo_size=1000000 -i udp://@224.2.2.2:2005?fifo_size=1000000 -i 
udp://@224.2.2.2:2006?fifo_size=1000000 -i udp://@224.2.2.2:2007?fifo_size=1000000 -i udp://@224.2.2.2:2008?fifo_size=1000000 -i 
udp://@224.2.2.2:2009?fifo_size=1000000 -i udp://@224.2.2.2:2010?fifo_size=1000000 -i udp://@224.2.2.2:2011?fifo_size=1000000       
-filter_complex  "color=c=black:size=1280x720 [base]; 
[0:v] setpts=PTS-STARTPTS, scale=244x192, drawtext=text='TESTO1'[video1]; 
[0:a] showvolume=b=1:h=10:w=200:o=v [audio1];
[1:v] setpts=PTS-STARTPTS, scale= 244x192 [video2]; 
[1:a] showvolume=b=1:h=10:w=200:o=v [audio2];
[2:v] setpts=PTS-STARTPTS, scale=244x192 [video3];
[2:a] showvolume=b=1:h=10:w=200:o=v [audio3]; 
[3:v] setpts=PTS-STARTPTS, scale=244x192 [video4];
[3:a] showvolume=b=1:h=10:w=200:o=v [audio4]; 
[4:v] setpts=PTS-STARTPTS, scale= 244x192 [video5]; 
[4:a] showvolume=b=1:h=10:w=200:o=v [audio5];
[5:v] setpts=PTS-STARTPTS, scale=244x192 [video6];
[5:a] showvolume=b=1:h=10:w=200:o=v [audio6]; 
[6:v] setpts=PTS-STARTPTS, scale=244x192 [video7];
[6:a] showvolume=b=1:h=10:w=200:o=v [audio7]; 
[7:v] setpts=PTS-STARTPTS, scale= 244x192 [video8];
[7:a] showvolume=b=1:h=10:w=200:o=v [audio8]; 
[8:v] setpts=PTS-STARTPTS, scale=244x192 [video9]; 
[8:a] showvolume=b=1:h=10:w=200:o=v [audio9];
[9:v] setpts=PTS-STARTPTS, scale=244x192 [video10]; 
[9:a] showvolume=b=1:h=10:w=200:o=v [audio10];
[10:v] setpts=PTS-STARTPTS, scale= 244x192 [video11]; 
[10:a] showvolume=b=1:h=10:w=200:o=v [audio11];
[11:v] setpts=PTS-STARTPTS, scale=244x192 [video12]; 
[11:a] showvolume=b=1:h=10:w=200:o=v [audio12];
[base][video1]  overlay=shortest=0:x=50:y=10 [stop1];
[stop1][audio1] overlay=shortest=0:x=0:y=0    [tmp1]; 
[tmp1][video2]  overlay=shortest=0:x=370:y=10 [stop2];
[stop2][audio2] overlay=shortest=0:x=320:y=0  [tmp2]; 
[tmp2][video3]  overlay=shortest=0:x=690:y=10 [stop3]; 
[stop3][audio3] overlay=shortest=0:x=640:y=0  [tmp3];
[tmp3][video4]  overlay=shortest=0:x=1010:y=10 [stop4]; 
[stop4][audio4] overlay=shortest=0:x=960:y=0   [tmp4];
[tmp4][video5]  overlay=shortest=0:x=50:y=250 [stop5]; 
[stop5][audio5] overlay=shortest=0:x=0:y=240  [tmp5];
[tmp5][video6]  overlay=shortest=0:x=370:y=250 [stop6]; 
[stop6][audio6] overlay=shortest=0:x=320:y=240 [tmp6];
[tmp6][video7]  overlay=shortest=0:x=690:y=250 [stop7]; 
[stop7][audio7] overlay=shortest=0:x=640:y=240 [tmp7];
[tmp7][video8]  overlay=shortest=0:x=1010:y=250 [stop8]; 
[stop8][audio8] overlay=shortest=0:x=960:y=240 [tmp8];
[tmp8][video9]  overlay=shortest=0:x=50:y=490 [stop9]; 
[stop9][audio9] overlay=shortest=0:x=0:y=480  [tmp9]; 
[tmp9][video10] overlay=shortest=0:x=370:y=490 [stop10];
[stop10][audio10] overlay=shortest=0:x=320:y=480 [tmp10];
[tmp10][video11]  overlay=shortest=0:x=690:y=490 [stop11];
[stop11][audio11] overlay=shortest=0:x=640:y=480  [tmp11];
[tmp11][video12]  overlay=shortest=0:x=1010:y=490 [stop12]; 
[stop12][audio12]  overlay=shortest=0:x=960:y=480 "
 -c:v libx264  -preset superfast -an -f mpegts "udp://225.2.1.1:1234?pkt_size=1316"
 
 # 16-ch mosaic 
 
 ffmpeg  -i udp://@224.2.2.2:2000?fifo_size=1000000 -i udp://@224.2.2.2:2001?fifo_size=1000000 -i 
udp://@224.2.2.2:2002?fifo_size=1000000 -i udp://@224.2.2.2:2003?fifo_size=1000000 -i udp://@224.2.2.2:2004?fifo_size=1000000 -i 
udp://@224.2.2.2:2005?fifo_size=1000000 -i udp://@224.2.2.2:2006?fifo_size=1000000 -i udp://@224.2.2.2:2007?fifo_size=1000000 -i 
udp://@224.2.2.2:2008?fifo_size=1000000 -i udp://@224.2.2.2:2009?fifo_size=1000000 -i udp://@224.2.2.2:2010?fifo_size=1000000 -i 
udp://@224.2.2.2:2011?fifo_size=1000000  -i udp://@224.2.2.2:2012?fifo_size=1000000  -i udp://@224.2.2.2:2013?fifo_size=1000000 -i 
udp://@224.2.2.2:2014?fifo_size=1000000 -i udp://@224.2.2.2:2015?fifo_size=1000000  
 -filter_complex " nullsrc=size=1280x720 [base]; 
 [0:v] setpts=PTS-STARTPTS, scale=320x180 [video1]; 
 [1:v] setpts=PTS-STARTPTS, scale=320x180 [video2];
 [2:v] setpts=PTS-STARTPTS, scale=320x180 [video3];
 [3:v] setpts=PTS-STARTPTS, scale=320x180 [video4]; 
 [4:v] setpts=PTS-STARTPTS, scale=320x180 [video5];
 [5:v] setpts=PTS-STARTPTS, scale=320x180 [video6]; 
 [6:v] setpts=PTS-STARTPTS, scale=320x180 [video7]; 
 [7:v] setpts=PTS-STARTPTS, scale=320x180 [video8];
 [8:v] setpts=PTS-STARTPTS, scale=320x180 [video9]; 
 [9:v] setpts=PTS-STARTPTS, scale=320x180 [video10];
 [10:v] setpts=PTS-STARTPTS, scale=320x180 [video11]; 
 [11:v] setpts=PTS-STARTPTS, scale=320x180 [video12];
 [12:v] setpts=PTS-STARTPTS, scale=320x180 [video13];
 [13:v] setpts=PTS-STARTPTS, scale=320x180 [video14]; 
 [14:v] setpts=PTS-STARTPTS, scale=320x180 [video15]; 
 [15:v] setpts=PTS-STARTPTS, scale=320x180 [video16];
 [base][video1] overlay=shortest=0 [tmp1];
 [tmp1][video2] overlay=shortest=0:x=320 [tmp2];
 [tmp2][video3] overlay=shortest=0:x=640 [tmp3]; 
 [tmp3][video4] overlay=shortest=0:x=960 [tmp4];
 [tmp4][video5] overlay=shortest=0:y=180 [tmp5]; 
 [tmp5][video6] overlay=shortest=0:x=320:y=180 [tmp6]; 
 [tmp6][video7] overlay=shortest=0:x=640:y=180 [tmp7]; 
 [tmp7][video8] overlay=shortest=0:x=960:y=180 [tmp8];
 [tmp8][video9] overlay=shortest=0:y=360 [tmp9]; 
 [tmp9][video10] overlay=shortest=0:x=320:y=360 [tmp10]; 
 [tmp10][video11] overlay=shortest=0:x=640:y=360 [tmp11];
 [tmp11][video12] overlay=shortest=0:x=960:y=360 [tmp12];
 [tmp12][video13] overlay=shortest=0:y=540 [tmp13]; 
 [tmp13][video14] overlay=shortest=0:x=320:y=540 [tmp14]; 
 [tmp14][video15] overlay=shortest=0:x=640:y=540 [tmp15]; 
 [tmp15][video16] overlay=shortest=0:x=960:y=540 "  
 -c:v libx264  -preset superfast -an -f mpegts udp://225.2.1.1:1234?pkt_size=1316
 
 
 16-ch with audio bar
 
 ffmpeg -i udp://@224.2.2.2:2000?fifo_size=1000000 -i udp://@224.2.2.2:2001?fifo_size=1000000 -i udp://@224.2.2.2:2002?fifo_size=1000000 
-i udp://@224.2.2.2:2003?fifo_size=1000000 -i udp://@224.2.2.2:2004?fifo_size=1000000 -i udp://@224.2.2.2:2005?fifo_size=1000000 
-i udp://@224.2.2.2:2006?fifo_size=1000000 -i udp://@224.2.2.2:2007?fifo_size=1000000 -i udp://@224.2.2.2:2008?fifo_size=1000000 
-i udp://@224.2.2.2:2009?fifo_size=1000000 -i udp://@224.2.2.2:2010?fifo_size=1000000 -i udp://@224.2.2.2:2011?fifo_size=1000000 
-i udp://@224.2.2.2:2012?fifo_size=1000000 -i udp://@224.2.2.2:2013?fifo_size=1000000 -i udp://@224.2.2.2:2014?fifo_size=1000000 
-i udp://@224.2.2.2:2015?fifo_size=1000000
 -filter_complex "color=c=black:size=1280x720 [base]; 
 [0:v] setpts=PTS-STARTPTS, scale=230x170 [video1];
 [0:a] showvolume=b=1:h=10:w=170:o=v [audio1]; 
 [1:v] setpts=PTS-STARTPTS, scale=230x170 [video2];
 [1:a] showvolume=b=1:h=10:w=170:o=v [audio2];
 [2:v] setpts=PTS-STARTPTS, scale=230x170 [video3];
 [2:a] showvolume=b=1:h=10:w=170:o=v [audio3];
 [3:v] setpts=PTS-STARTPTS, scale=230x170 [video4];
 [3:a] showvolume=b=1:h=10:w=170:o=v [audio4]; 
 [4:v] setpts=PTS-STARTPTS, scale=230x170 [video5];
 [4:a] showvolume=b=1:h=10:w=170:o=v [audio5];
 [5:v] setpts=PTS-STARTPTS, scale=230x170 [video6]; 
 [5:a] showvolume=b=1:h=10:w=170:o=v [audio6];
 [6:v] setpts=PTS-STARTPTS, scale=230x170 [video7]; 
 [6:a] showvolume=b=1:h=10:w=170:o=v [audio7];
 [7:v] setpts=PTS-STARTPTS, scale=230x170 [video8];
 [7:a] showvolume=b=1:h=10:w=170:o=v [audio8];
 [8:v] setpts=PTS-STARTPTS, scale=230x170 [video9]; 
 [8:a] showvolume=b=1:h=10:w=170:o=v [audio9];
 [9:v] setpts=PTS-STARTPTS, scale=230x170 [video10];
 [9:a] showvolume=b=1:h=10:w=170:o=v [audio10];
 [10:v] setpts=PTS-STARTPTS, scale=230x170 [video11]; 
 [10:a] showvolume=b=1:h=10:w=170:o=v [audio11];
 [11:v] setpts=PTS-STARTPTS, scale=230x170 [video12];
 [11:a] showvolume=b=1:h=10:w=170:o=v [audio12];
 [12:v] setpts=PTS-STARTPTS, scale=230x170 [video13];
 [12:a] showvolume=b=1:h=10:w=170:o=v [audio13];
 [13:v] setpts=PTS-STARTPTS, scale=230x170 [video14]; 
 [13:a] showvolume=b=1:h=10:w=170:o=v [audio14];
 [14:v] setpts=PTS-STARTPTS, scale=230x170 [video15]; 
 [14:a] showvolume=b=1:h=10:w=170:o=v [audio15];
 [15:v] setpts=PTS-STARTPTS, scale=230x170 [video16];
 [15:a] showvolume=b=1:h=10:w=170:o=v [audio16];
 [base][video1]  overlay=shortest=0:x=50:y=10 [stop1];
[stop1][audio1] overlay=shortest=0:x=20:y=0    [tmp1]; 
[tmp1][video2]  overlay=shortest=0:x=370:y=10 [stop2];
[stop2][audio2] overlay=shortest=0:x=340:y=0  [tmp2]; 
[tmp2][video3]  overlay=shortest=0:x=690:y=10 [stop3]; 
[stop3][audio3] overlay=shortest=0:x=660:y=0  [tmp3];
[tmp3][video4]  overlay=shortest=0:x=1010:y=10 [stop4]; 
[stop4][audio4] overlay=shortest=0:x=980:y=0   [tmp4];
[tmp4][video5]  overlay=shortest=0:x=50:y=190 [stop5]; 
[stop5][audio5] overlay=shortest=0:x=20:y=180  [tmp5];
[tmp5][video6]  overlay=shortest=0:x=370:y=190 [stop6]; 
[stop6][audio6] overlay=shortest=0:x=340:y=180 [tmp6];
[tmp6][video7]  overlay=shortest=0:x=690:y=190 [stop7]; 
[stop7][audio7] overlay=shortest=0:x=660:y=180 [tmp7];
[tmp7][video8]  overlay=shortest=0:x=1010:y=190 [stop8]; 
[stop8][audio8] overlay=shortest=0:x=980:y=180 [tmp8];
[tmp8][video9]  overlay=shortest=0:x=50:y=370 [stop9]; 
[stop9][audio9] overlay=shortest=0:x=20:y=360  [tmp9]; 
[tmp9][video10] overlay=shortest=0:x=370:y=370 [stop10];
[stop10][audio10] overlay=shortest=0:x=340:y=360 [tmp10];
[tmp10][video11]  overlay=shortest=0:x=690:y=370 [stop11];
[stop11][audio11] overlay=shortest=0:x=660:y=360  [tmp11];
[tmp11][video12]  overlay=shortest=0:x=1010:y=370 [stop12]; 
[stop12][audio12]  overlay=shortest=0:x=980:y=360  [tmp12];
[tmp12][video13]  overlay=shortest=0:x=50:y=550  [stop13];
[stop13][audio13]  overlay=shortest=0:x=20:y=540  [tmp13];
[tmp13][video14]  overlay=shortest=0:x=370:y=550 [stop14];
[stop14][audio14]  overlay=shortest=0:x=340:y=540  [tmp14];
[tmp14][video15]  overlay=shortest=0:x=690:y=550 [stop15];
[stop15][audio15]  overlay=shortest=0:x=660:y=540  [tmp15];
[tmp15][video16]  overlay=shortest=0:x=1010:y=550 [stop16];
[stop16][audio16]  overlay=shortest=0:x=980:y=540  "
 -c:v libx264  -preset superfast -an -f mpegts udp://225.2.1.1:1234?pkt_size=1316


