---
title: 磁力链接是如何实现下载的？
date: 2015-07-03 21:08:00
updated: 2015-07-04 09:25:41
tags: 
- windows
- vim
- powerline
categories: 
- linux

---
一直很好奇磁力链接是如何工作的，只通过简单的一串字符就能下载任何文件，这确实是很神奇的一件事。而简单的背后，往往蕴含着非常复杂的逻辑和巧妙的设计，磁力链接正是如此。通过一段时间的学习，对磁力链接及相关的概念有了一定的了解，Magnet、Torrent、P2P、DHT、KAD等等等等，在这里做个小结。

## 一、磁力链接与种子文件

磁力链接并不是一个新概念，早在2002年，相关的标准草稿就已经制定了。但直到2012年海盗湾为规避版权问题删除了站点上的所有Torrent文件停止了下载服务，并将之前所有的种子转换为磁力链接之后，磁力链接技术才真正的飞速发展起来。说起磁力链接，不能不提BT；而说起BT，则不能不提P2P。在很早的网络时代，下载都是简单的集中式客户端/服务器模式，一个或多个服务器支撑成千上万的客户端连接下载，不仅带宽遇到了瓶颈，而且太容易出现单点故障。这时P2P被提出来解决这个问题，P2P也不是一经提出就完美无缺的，在经过了Napster到Gnutella到BitTorrent三代的P2P技术的发展才渐进成熟。（这里有对P2P的[科普](http://blog.kakarott.net/archives/category/p2p)） 维基百科上对BT做了详细的说明：


<!--more-->


> 根据BitTorrent协议，文件发布者会根据要发布的文件生成提供一个.torrent文件，即种子文件，也简称为“种子”。
> 种子文件本质上是文本文件，包含Tracker信息和文件信息两部分。Tracker信息主要是BT下载中需要用到的Tracker服务器的地址和针对Tracker服务器的设置，文件信息是根据对目标文件的计算生成的，计算结果根据BitTorrent协议内的Bencode规则进行编码。它的主要原理是需要把提供下载的文件虚拟分成大小相等的块，块大小必须为2k的整数次方（由于是虚拟分块，硬盘上并不产生各个块文件），并把每个块的索引信息和Hash验证码写入种子文件中；所以，种子文件就是被下载文件的“索引”。
> 下载者要下载文件内容，需要先得到相应的种子文件，然后使用BT客户端软件进行下载。
> 下载时，BT客户端首先解析种子文件得到Tracker地址，然后连接Tracker服务器。Tracker服务器回应下载者的请求，提供下载者其他下载者（包括发布者）的IP。下载者再连接其他下载者，根据种子文件，两者分别告知对方自己已经有的块，然后交换对方所没有的数据。此时不需要其他服务器参与，分散了单个线路上的数据流量，因此减轻了服务器负担。
> 下载者每得到一个块，需要算出下载块的Hash验证码与种子文件中的对比，如果一样则说明块正确，不一样则需要重新下载这个块。这种规定是为了解决下载内容准确性的问题。
> 一般的HTTP/FTP下载，发布文件仅在某个或某几个服务器，下载的人太多，服务器的带宽很易不胜负荷，变得很慢。而BitTorrent协议下载的特点是，下载的人越多，提供的带宽也越多，下载速度就越快。同时，拥有完整文件的用户也会越来越多，使文件的“寿命”不断延长。

综上所述，可以看出Tracker服务器在BT网络中充当着非常重要的作用，和传统的客户端/服务器模式一样，Tracker服务器同样会存在单点故障问题。所以在BT技术的基础上，后来又衍生出DHT网络和磁力链接技术，DHT全称为分布式哈希表（Distributed Hash Table），是一种分布式存储方法。DHT网络是Tracker-less的，不依赖于其他的Tracker服务器。在这种情况下，每个客户端负责一个小范围的路由，并负责存储一小部分数据，从而实现整个DHT网络的寻址和存储。使用支持该技术的BT下载软件，用户无需连上Tracker就可以下载，因为软件会在DHT网络中寻找下载同一文件的其他用户并与之通讯，开始下载任务。 在网络中定位资源最简单的方法是URL（统一资源定位符），它是通过资源的位置来进行定位。而在DHT网络中，则是使用URN（统一资源名称）来进行定位，磁力链接就是基于文件内容的散列函数值来链接到特定文件，生成一个唯一的文件识别符，从而在DHT网络中定位并下载文件。 一个最简单的磁力链接格式如下：
`magnet:?xt=urn:btih:51df6808c739174c8f264701ba94460c5238d6ce`
其中`urn`为统一资源名称，btih是BitTorrent Info Hash的缩写，是BitTorrent使用的Hash函数。除了`btih`还可以是其他类型的Hash函数，但不如btih用的多。这一串长度为40的字符串正是文件内容的Hash，BT下载工具就根据这个Hash在DHT网络中定位下载文件。

## 二、libtorrent实现磁力链接转BT种子

处理磁力链接最简单的库莫过于Python中的libtorrent，它封装了几乎所有的BitTorrent相关的内容，以至于你可以在10分钟内使用libtorrent写出一个自己的BT下载工具。libtorrent中的`add_magnet_uri`方法是用来解析磁力链接的，该方法非常简单是完全透明的，不用关心磁力链接或DHT协议之类的细节，方法返回一个句柄，在磁力链接未成功解析之前，这个句柄的`has_metadata`方法会一直返回False，一旦成功解析到磁力链接，根据这个句柄调用`get_torrent_info`方法即可获取种子信息了。示例代码如下：

    handle = lt.add_magnet_uri(sess, link, params)
     
    # waiting for metadata
    while (not handle.has_metadata()):
        time.sleep(5)    
     
    # create a torrent
    torinfo = handle.get_torrent_info()
    torfile = lt.create_torrent(torinfo)

注意，在使用libtorrent处理磁力链接之前，首先需要记得调用`add_dht_router`和`start_dht`启用DHT，这就是所谓的bootstrap node。如果不启用`DHT`，可能要很久都不能解析磁链。如下：

    sess = lt.session()
    sess.add_dht_router('router.bittorrent.com', 6881)
    sess.add_dht_router('router.utorrent.com', 6881)
    sess.add_dht_router('router.bitcomet.com', 6881)
    sess.add_dht_router('dht.transmissionbt.com', 6881)
    sess.start_dht();

常见的`bootstrap node`有`bittorrent`、`utorrent`、`bitcomet`、`transmissionbt`等，端口都是6881。完整的代码在[这里](https://github.com/aneasystone/btool/blob/master/transformor.py)。

## 三、BT种子转磁力链接

上面讲了这么多，大家会发现磁力链接的下载原理其实就是先根据磁力链接获取种子文件，然后再根据种子文件进一步下载。这是因为种子文件才有分片信息，文件大小，文件名等必要的信息，所以种子是必不可少的。那么既然有了种子文件，我们为什么还需要磁力链接呢？这是因为现在对BT的封锁太严重，传播种子并不是那么简单的事了，动不动就被和谐了，所以磁链的作用就是便于传输，因为磁链就是一个小小的文本，而种子确是一个文件。所以总结一下他们的优缺点：

 - A. 种子：稳定性高，信息多，不便于传播扩散

 - B. 磁力链接：仍需要先获得种子，不稳定，可能在某时刻不能获取，但是很便于扩散。

使用libtorrent将种子转换为磁链简单的不能再简单了。另外也可以使用bencode来进行解析，这里有[代码示例](http://www.au92.com/archives/P-y-t-h-o-n-jiang-B-T-zhong-zi-wen-jian-zhuan-huan-wei-ci-li-lian-de-liang-zhong-fang-fa.html)。

    def torrent2magnet(torrent_file):
        info = lt.torrent_info(torrent_file)
        link = "magnet:?xt=urn:btih:%s&dn=%s" % (info.info_hash(), info.name())
        return link

四、种子站点的缓存

使用libtorrent将磁力链接转换成种子文件，实际上是效率很低下的工作，运行上面的Python脚本，可以发现转换一个磁力链接至少也要花费5到10秒的时间，有的磁力链接更要将近一分钟才能转换。那么如何提高转换速度呢？其实，大多数的下载工具在下载磁力链接时会将种子文件保留在服务器上，所以我们在转换之前先判断下种子服务器上是否已经存在该种子，如果存在则直接下载，这将比通过DHT网络转换磁力链接快的多。常见的种子服务器有：（网上很多种子服务器都已经被墙，或者已关闭，或者提供了验证码不能直接下载了）

   

 1. http://bt.box.n0808.com

    这是迅雷的种子库，种子文件地址格式为：http://bt.box.n0808.com/HASH值最前面两位/HASH值最后面两位/HASH值.torrent

 2. http://magnet2torrent.com/
    实际上调的是torcache的接口，格式为：https://torcache.net/torrent/HASH值.torrent

 3. http://torrent-cache.bitcomet.org:36869/
    这是BitComet的服务器，格式为：http://torrent-cache.bitcomet.org:36869/get_torrent?info_hash=HASH值&key;=KEY值。关于KEY值的计算可以参考[这篇文章](http://www.cnblogs.com/UMU618/articles/2568579.html)。

 4. http://magnet.vuze.com/
    这是Vuze的种子库，格式为：http://magnet.vuze.com/magnetLookup?hash=KEY值，其中KEY值是Hash的base32编码。
参考

 1. [P2P技术科普](http://blog.kakarott.net/archives/category/p2p)
 2. [磁力链接 -
    维基百科](http://zh.wikipedia.org/wiki/%E7%A3%81%E5%8A%9B%E9%93%BE%E6%8E%A5) 
 3. [BitTorrent (协议) -
    维基百科](http://zh.wikipedia.org/wiki/BitTorrent_(%E5%8D%8F%E8%AE%AE))

