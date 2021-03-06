# 简单文件系统的实现

##  设计目的和内容要求

### 内容要求

- 在内存中开辟一个虚拟磁盘空间作为文件存储分区，在其上实现一个简单的基于多级目录的单用户单任务系统中的文件系统。在退出该文件系统的使用时，应将该虚拟文件系统以一个文件的方式保存到磁盘上，以便下次可以再将它恢复到内存的虚拟磁盘空间中。
- 文件存储空间的分配可采用显式链接分配或其他的办法。
- 空闲磁盘空间的管理可选择位示图或其他的办法。如果采用位示图来管理文件存储空间，并采用显式链接分配方式，那么可以将位示图合并到 FAT 中。
- 文件目录结构采用多级目录结构。为了简单起见，可以不使用索引结点，其中的每个目录项应包含文件名、物理地址、长度等信息，还可以通过目录项实现对文件的读和写的保护。
- 要求提供以下操作命令：
    * my_format：对文件存储器进行格式化，即按照文件系统的结构对虚拟磁盘空间进行布局，并在其上创建根目录以及用于管理文件存储空间等的数据结构。
    * my_mkdir：用于创建子目录。
    * my_rmdir：用于删除子目录。
    * my_ls：用于显示目录中的内容。
    * my_cd：用于更改当前目录。
    * my_create：用于创建文件。
    * my_open：用于打开文件。
    * my_close：用于关闭文件。
    * my_write：用于写文件。
    * my_read：用于读文件。
    * my_rm：用于删除文件。
    * my_exitsys：用于退出文件系统。


### 思考

- 我们的数据结构中的文件物理地址信息是使用 C 语言的指针类型、还是整型，为什么？
- 如果引入磁盘索引结点，上述实现过程需要作哪些修改？
- 如果设计的是一个单用户多任务文件系统，则系统需要进行哪些扩充（尤其要考虑读写指针问题）？如果设计的是一个多用户文件系统，则又要进行哪些扩充？

### FAT 文件系统介绍 

FAT 文件系统是以他的文件组织方式——文件分配表（file allocation table，FAT）命名的，文件分配表的每个表项中存放某文件的下一个盘块号，而该文件的起始盘块号则保存在它的文件控制块 FCB 中。在文件分配表中，一般用FFFF 来标识文件的结束；用 0000 来标识某个逻辑块未被分配，即是空闲块。为了提高文件系统的可靠性，在逻辑磁盘上通常设置两张文件分配表，它们互为备份。

虽然不同文件系统的文件控制块的内容和格式不完全相同，但通常都包括以下三类信息：

基本信息、存取控制信息和使用信息。

- 基本信息。包括文件名、用户名、文件类型、文件的物理地址、文件长度、文件的逻辑结构和物理结构等。
- 存取控制信息。一般分别给出文件主、伙伴用户、一般用户的存取权限。
- 使用信息。包括文件的建立日期及时间、上次存取文件的日期及时间、当前的使用信息等。
