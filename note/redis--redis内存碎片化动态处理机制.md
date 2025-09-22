> Redis 内存碎片化是指已分配的内存无法被有效利用，导致内存浪费的现象。Redis 提供了几种机制来动态处理内存碎片化问题：
>
> 1. ### 内存碎片率监控
>
>    > Redis 通过 info memory 命令可以查看内存碎片情况： redis-cli info memory | grep ratio 返回结果中的 memfragmentationratio 表示内存碎片率： 1.0 左右表示几乎没有碎片 1.0-1.5 之间是合理范围 1.5 表示碎片较多 <1.0 表示Redis使用了交换空间(swap)
>
> 2. ###  动态内存回收机制
>
>    - 自动内存回收 Redis 4.0 及以上版本引入了自动内存碎片整理功能，通过以下配置控制：
>
>    - 启用自动内存碎片整理
>
>      > -  activedefrag yes 
>      > -  active-defrag-ignore-bytes 100mb  # 内存碎片率超过此阈值时开始整理 
>      > -   active-defrag-threshold-lower 10  active-defrag-threshold-upper 100  意味着当内存碎片率达到 **100%** 时，Redis 的自动碎片整理机制会开始**尽最大努力**去减少内存碎片
>      > -  active-defrag-cycle-min 5 active-defrag-cycle-max 75 # 整理过程占用CPU的最小和最大比例 
>
>    - 手动执行内存碎片整理 redis-cli memory purge
>
> 3. ### 关键配置参数
>
>    - 当碎片率达到该值时开启碎片整理(百分比)
>
>    -  active-defrag-threshold-lower 10 # 当碎片率达到该值时全力整理(百分比) 
>
>    - active-defrag-threshold-upper 100 # 碎片整理至少节省的内存大小 
>
>    - active-defrag-ignore-bytes 100mb # 碎片整理占用CPU时间比例范围 
>
>    - active-defrag-cycle-min 5 active-defrag-cycle-max 75 # 碎片整理扫描的set/hash/zset/list元素最大数量 active-defrag-max-scan-fields 1000
>
> 4. ### 减少碎片化的最佳实践
>
>    - 使用适当的数据结构：根据场景选择最合适的数据结构 
>
>    - 避免大量小键：合并小键或使用hash结构存储 
>
>    - 合理设置过期时间：避免大量键同时过期 
>
>    - 使用32位Redis：如果内存小于4GB，32位Redis更节省内存 
>
>    - 升级到Redis 4.0+：利用自动碎片整理功能 
>
>    - 监控碎片率：设置报警监控mem_ragmentation_ratio
>
> 5. ### 碎片整理工作原理
>
>    - Redis 主线程周期性检查内存碎片率 
>
>    - 当达到阈值时，启动后台线程进行整理 
>
>    - 通过移动数据来合并空闲内存块 
>
>    - 整理过程是渐进式的，避免影响正常请求 
>
>    - 根据CPU使用率动态调整整理速度
>
> 6. #### 注意事项 
>
>    - 碎片整理会增加CPU使用率
>
>    - 在低峰期进行大规模整理 
>
>    - 对于大内存实例，整理可能需要较长时间 
>
>    - 生产环境建议先测试再调整参数 
>
>    - 通过合理配置和监控，可以有效地动态管理Redis内存碎片问题，保持Redis的高效运行。