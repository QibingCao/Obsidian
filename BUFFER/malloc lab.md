---

类型: 笔记
创建日期: 2024-06-17
修改日期: 2024-06-17
---
## 分离的空闲链表
维护多个空闲链表，其中每个链表中的块有大致相等的大小。
### 块内容
![[Pasted image 20240617172415.png]]

```c
typedef struct Block {
    size_t header;         // 块头信息，包含块大小和分配标志
    
    // 数据
    struct Block* prev;    // 前一节点指针
    struct Block* next;    // 后一节点指针
    char data[];           // 用于存储实际数据
    
    size_t footer;         // 块尾信息（与头部信息一致）
} Block;

```
### 空闲链表结构
![[Pasted image 20240617172430.png]]