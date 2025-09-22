Typora +OSS +坚果云

1.  Typora 对MD的友好支持

2. 坚果云手机端的同步显示，但是坚果云对于图片的显示不友好，所以图片统一上传到OSS上面

3. Typora本地配置

   1. <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/note/20250909-125004-image-20250909125002022.png" alt="image-20250909125002022" style="zoom: 25%;float:left" />

   2. upload.sh 配置

       ```shell
       #!/bin/bash
       
       # 接收 Typora 传递的文件路径
       file_path="$1"
       
       # 配置 OSS 参数
       bucket="bucketName"
       endpoint="oss-cn-shanghai.aliyuncs.com"  # 替换为你的 Bucket 实际所在地域
       folder="img/"  # 设置 OSS 上的存储路径，可以留空或设置为其他路径
       
       # 生成 OSS 上的文件名（按日期时间重命名避免冲突）
       filename="${folder}$(date +%Y%m%d-%H%M%S)-$(basename "$file_path")"
       
       # 使用 ossutil 上传文件（假设 ossutil 已在 PATH 中）
       ossutil cp $file_path oss://${bucket}/${filename}
       
       # 检查上传是否成功
       if [ $? -eq 0 ]; then
           # 拼接文件的公网访问 URL（这里以公有读 Bucket 为例）
           echo "https://${bucket}.${endpoint}/${filename}"
       else
          # 上传失败，输出错误信息
           echo "Error: Upload failed with the following error:"
           cat "$temp_error"
           # 也可以同时输出ossutil的标准输出（如果有）
           if [ -s "$temp_output" ]; then
               echo "Output:"
               cat "$temp_output"
           fi
           # 以非零状态退出，让Typora知道失败
           exit 1
       fi
       
       # 清理临时文件
       rm -f "$temp_output" "$temp_error"
       ```
   
   3. 安装ossutil命令。具体参考阿里官网 https://account.aliyun.com/login/login.htm?oauth_callback=https://oss.console.aliyun.com/index

