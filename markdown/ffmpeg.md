- win配置`ffmpeg -hwaccel qsv -c:v h264_qsv -noautorotate -i input.mp4 -vcodec h264_qsv -preset veryslow -crf 1 -c:a copy -b:v 3000k -profile:v high  -f mp4  out.mp4`
    - -crf 18~28是一个合理的范围。18被认为是视觉无损的
    - -preset指定的编码速度越慢，获得的压缩效率就越高
    - -c:a copy 将音频数据从源文件中以原有编码格式直接拷入目标文件
    - -preset  slow 与 medium相比提升了5%~10%；slower 与 slow相比提升了5%；veryslow 与 slower相比提升了3%，与 veryslow相比，placebo以极高的编码时间为代价，只换取了大概1%的视频质量提升
    - 解码也指定硬件解码： -c:v h264_qsv
    - 编码使用硬件: -vcodec h264_qsv
    - 指定硬件加速qsv：-hwaccel qsv
    - 指定视频导出格式： -f mp4
    - -noautorotate：视频带有旋转信息
    - profile
        - H.264有四种画质级别,分别是baseline, extended, main, high
    - ffmpeg -i Clean.mp4 -i spanish.ass -i english.ass -c:s mov_text -c:v copy -c:a copy -map 0:v -map 0:a -map 1 -map 2 -metadata:s:s:0 language=spa -metadata:s:s:1 language=eng With2CC.mp4
    - 拼接片头和片尾
        - 使用ts拼接
            - 先将 mp4 转化为同样编码形式的 ts 流，因为 ts流是可以 concate 的，先()把 mp4 封装成 ts ，然后 concate ts 流， 最后再把 ts 流转化为 mp4。
            - ```javascript
# 将 mp4 文件封装为 ts 格式
ffmpeg -i a1.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 1.ts
ffmpeg -i a2.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 2.ts
ffmpeg -i a3.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 3.ts
ffmpeg -i a4.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 4.ts
# 拼接 ts 并导出最终 mp4 文件
ffmpeg -i "concat:1.ts|2.ts|3.ts|4.ts" -acodec copy -vcodec copy -absf aac_adtstoasc output.mp4
# 删除过程中生成的 ts 文件```
