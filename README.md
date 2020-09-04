# Commonly recommended ffmpeg arguments for video conversion using x264

Feel free to contribute and send pull requests!

[Recommended arguments for CPU conversion](#Recommended-arguments-for-CPU-conversion)

[Recommended arguments for NVIDIA GPUs (faster than CPU)](#Recommended-arguments-for-NVIDIA-GPUs)


[mp4 format note](#mp4-format-note)

[HLS format arguments (m3u8 + ts)](#hls-format-arguments)

- Tweak the video bitrate and audio bitrate simply by changing the value of 2M or 128Kbps to another appropriate value.
- If you are targeting 1080p type quality, 2M video and 128k audio is probably fine using 1920x1080 resolution.
- If you are targeting 720p type quality, 1M video and 128k audio is probably fine using 1280x720 resolution.

-----------------------------------------------------

# Recommended arguments for CPU conversion

This will convert the source video to a 1080p, 30fps, 2Mbps video, 128Kbps audio, output. It will strip the metadata, data streams, and subtitle streams.

|Argument|Description|
|--|--|
|-i "input.mp4"|Source file|
|-c:v libx264|Encode the video using libx264 (CPU based)|
|-pix_fmt yuv420p|The 420p is misleading - this is just a standard pixel format|
|-filter:v fps=fps=30|Not a typo, filters the video for 30 fps|
|-b:v 2M|Set a bitrate of 2 Mbps for video|
|-b:a 128k|Set a bitrate of 128 Kbps for audio|
|-map_metadata -1|Strip metadata from source|
|-map 0:v:0|Use the first video stream from the source|
|-map 0:a:0|Use the first audio stream from the source|
|-map -0:d|Strip any data streams from the source|
|-map -0:s|Strip any subtitle streams from the source|
|-s 1920x1080|Set the resolution to 1920x1080 (1080p)|
|-c:a aac|Use aac format for audio (very standard)|
|-ac 2|Convert to stereo audio (in case from 5.1, etc, MUST use for HLS format)|
|-threads 6|Use 6 threads with ffmpeg|
|"output.mp4"|Destination file|

    ffmpeg -hwaccel cuda -i "input.mp4" -c:v h264_nvenc -pix_fmt yuv420p -filter:v fps=fps=30 -b:v 2M -b:a 128k -map_metadata -1 -map 0:v:0 -map 0:a:0 -map -0:d -map -0:s -s 1920x1080 -c:a aac -threads 6 "output.mp4"
    
# Recommended arguments for NVIDIA GPUs
### If you have an NVIDIA GPU, this is a faster way to encode vs the CPU

This will convert the source video to a 1080p, 30fps, 2Mbps video, 128Kbps audio, output. It will strip the metadata, data streams, and subtitle streams.

|Argument|Description|
|--|--|
|-hwaccel cuda|CUDA Hardware Acceleration (use if possible and no errors arise)|
|-i "input.mp4"|Source file|
|-c:v h264_nvenc|Encode the video using the NVIDIA NVENC encoder. Fast, great, but only if you have an NVIDIA graphics card)|
|-pix_fmt yuv420p|The 420p is misleading - this is just a standard pixel format|
|-filter:v fps=fps=30|Not a typo, filters the video for 30 fps|
|-b:v 2M|Set a bitrate of 2 Mbps for video|
|-b:a 128k|Set a bitrate of 128 Kbps for audio|
|-map_metadata -1|Strip metadata from source|
|-map 0:v:0|Use the first video stream from the source|
|-map 0:a:0|Use the first audio stream from the source|
|-map -0:d|Strip any data streams from the source|
|-map -0:s|Strip any subtitle streams from the source|
|-s 1920x1080|Set the resolution to 1920x1080 (1080p)|
|-c:a aac|Use aac format for audio (very standard)|
|-ac 2|Convert to stereo audio (in case from 5.1, etc, MUST use for HLS format)|
|-threads 6|Use 6 threads with ffmpeg|
|"output.mp4"|Destination file|

    ffmpeg -hwaccel cuda -i "input.mp4" -c:v h264_nvenc -pix_fmt yuv420p -filter:v fps=fps=30 -b:v 2M -b:a 128k -map_metadata -1 -map 0:v:0 -map 0:a:0 -map -0:d -map -0:s -s 1920x1080 -c:a aac -threads 6 "output.mp4"

# mp4 format note
Using the above examples, it will convert the source video to an mp4 file.

# HLS format arguments

|Argument|Description
|--|--|
|-start_number 0|Start at ts number 0|
|-hls_time 10|Approx segment length (ts file)|
|-hls_list_size 0|Keep all segments in the playlist|
|-f hls|HLS format|
|"output.m3u8"|Output file (use m3u8 extension, it will create ts files)

**Remove "output.mp4" from an above example and append this:**

    -start_number 0 -hls_time 10 -hls_list_size 0 -f hls "output.m3u8"
