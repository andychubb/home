+++
title = 'Using ffmpeg'
date = 2022-02-16T17:32:57Z
draft = true
tags = ['FFMpeg', 'Notes']
+++

Just a few notes here on common video processing tasks that can be achieved with the awesome ffmpeg package.

#### Get the first frame of a video as a jpg

```bash
ffmpeg -i absolute-website-design-4x2.mp4 -vframes 1 test.jpg
```

#### Make a video smaller dimensionally

```bash
ffmpeg -i input.mp4 -s 1600x800 -c:a copy output.mp4
```

#### Crunch down mp4 videos

```bash
ffmpeg -i input.mp4 -vcodec libx264 -crf 20 output.mp4
```

#### Generate webm files

```bash
ffmpeg -i The1s.mp4 -c:v libvpx -crf 10 -b:v 1M -c:a libvorbis output.webm
```

#### Get the first frame of a video as a jpg

```bash
ffmpeg -i absolute-website-design-4x2.mp4 -vframes 1 test.jpg
```

#### Make a video smaller dimensionally

```bash
ffmpeg -i input.mp4 -s 1600x800 -c:a copy output.mp4
```

#### Crunch down mp4 videos

```bash
ffmpeg -i input.mp4 -vcodec libx264 -crf 20 output.mp4
```

#### Generate webm files

```bash
ffmpeg -i The1s.mp4 -c:v libvpx -crf 10 -b:v 1M -c:a libvorbis output.webm
```
