---
title: "Mysql使用汇总表优化查询"
categories: [ "SQL" ]
tags: [ "mysql","optimize" ]
draft: false
slug: "mysql-use-summary-tables-to-optimize-query"
date: "2014-02-01 23:12:00"
---

## 1. 提出问题

根据视频的播放数实现排行榜。需要关联4张表：专辑表、视频表、专辑和视频的关联表以及视频的播放数日增表，日增表的日数据大概在2万条左右。出排行榜的sql如下：

	SELECT
		vts.TV_ALUMN_ID AS albumId, inc.tv_vid, inc.tv_id, vts.TV_NAME AS tvName,
		sum( inc.tv_count + inc.tv_count_app) AS realCount, sum( inc.tv_display_count + inc.tv_display_count_app) AS showCount
	FROM
		vrs2_playlist vp, vrs2_tv_playlist vtp, vrs_tv_set vts, t_vrs_video_day_inc inc
	WHERE
		vp.ID = vtp.TV_PLAYLIST_ID AND vtp.TV_ID = inc.tv_id AND vts.ID = vtp.TV_ID 
	AND vts.TV_ALUMN_ID = vp.id AND inc.tv_date BETWEEN ?  AND ? AND vp.IS_BD != 0 
	AND vp.TV_IS_INTREST != 1 AND vp.TV_EFFECTIVE = 1 AND vp.TV_IS_TEST = 0 AND vts.IS_BD != 0
	AND ( vts.TV_S_TYPE < 2 OR vts.TV_S_TYPE IS NULL)
	AND vp.TV_CATEGORY_ID = ?  GROUP BY inc.tv_vid ORDER BY showCount DESC LIMIT 100

> 如果是日榜，查询时间大约在8s左右，还可以接受。如果是周榜，大概需要1min，如果是月榜，耗时约2~3分钟。周榜和月榜的时间是令人难以接受的。


<!--more-->


## 2. 优化方案

我采取的方案是：在日增数据的基础上，进行一周和一月的数据的汇总，生成周增表和月增表，周榜和月榜直接从周增表和月增表里查询。

### 2.1 通过insert into select汇总数据

创建表结构之后（周增表和月增表的表结构和日增表一致），通过insert into TABLE select 语句可以将查询的结果写入指定的表中，完整的SQL语句为：

	INSERT INTO t_vrs_video_week_inc (tv_id,tv_vid,tv_count,tv_display_count,tv_count_app,tv_display_count_app,tv_date)	SELECT 
	tv_id, tv_vid, sum(tv_count) as tv_count, sum(tv_display_count) as tv_display_count, sum(tv_count_app) as tv_count_app, 
	sum(tv_display_count_app) as tv_display_count_app, SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+7 DAY) AS tv_date 
	FROM t_vrs_video_day_inc inc WHERE inc.tv_date BETWEEN SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+7 DAY) AND 
	SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+1 DAY) GROUP BY inc.tv_vid

但是，如果数据量稍大，这个操作会失败，MySql提示binlog太小了。binlog用来记录数据库发生变化的“事件"，如创建表或修改表中的数据，我们的INSERT INTO语句就会使用binlog，但是select语句和show语句因为仅仅是查询，不会使用binlog。所以得想别的办法。

### 2.2 通过load data从文件导数据

因为select语句不会受binlog的限制，也就是说，数据是可以查出来的，只是没法一下子写入表中。我们可以先将查出来的数据写入文件，然后通过MySql的load data local infile命令从文件中将数据导入表中。
查询数据存入文件：
	
	selectSql="SELECT tv_id, tv_vid, sum(tv_count) as tv_count, sum(tv_display_count) as tv_display_count, sum(tv_count_app) 
	as tv_count_app, sum(tv_display_count_app) as tv_display_count_app, SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+7 DAY) 
	AS tv_date FROM t_vrs_video_day_inc inc WHERE inc.tv_date BETWEEN SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+7 DAY) 
	AND SUBDATE(CURDATE(),INTERVAL WEEKDAY(CURDATE())+1 DAY) GROUP BY inc.tv_vid"

	mysql -h10.11.12.13 -uuser -ppasswd -Dvideo -s --skip-column-names -e "$selectSql" > $dataFilePath

从文件将数据load进表中：

	loadDataSql="load data local infile '$dataFilePath' into table t_vrs_video_week_inc (tv_id,tv_vid,tv_count,tv_display_count,
	tv_count_app,tv_display_count_app,tv_date)"

	mysql -h10.11.12.13 -uuser -ppasswd -Dvideo -s --skip-column-names -e "$loadDataSql"

## 3. 总结

优化之后，周榜和月榜的耗时大概在10s左右，与日榜差不多。整个系统的执行时间从30min左右降到10分钟左右，应该说效果还是比较明显的。
我的方案只是一种形式的优化，肯定不是最优的，比如多表联查的效率很低，这应该也是个优化点，还会继续探索。

### 参考

+ [5.2.4. The Binary Log](http://dev.mysql.com/doc/refman/5.7/en/binary-log.html)
