## 零碎笔记    
### 1. Linux命令 
* 批量删除指定文件    
  * 命令： find / -name XXX -exec rm -v {} \\;

### 2. Calcite 查看RelNode    
RelOptUtil.toString(RelNode)