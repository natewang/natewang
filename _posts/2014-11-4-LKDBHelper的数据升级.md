---
layout: post
title: LKDBHelper的数据升级
---
在IOS项目中我们经常会用到数据库，sqlite，去存储一些繁杂的数据,随着项目的进度，我们一定会遇到数据升级问题，数据库的升级是需要慎重的事，可能会升级数据失败，更有可能app崩溃无法使用。
在我的项目中使用的基于fmdb的LKDBHelper，这次说一说如何升级。
数据库的四大操作，增删改查，一般我们数据结构的升级只涉及到增加和删除，一般不需要的数据在app上也不要删除，会引起一些关联的错误，所以这里只涉及到增加。

**首先**，加入新的字段,如下	
`
@property (nonatomic, strong) NSNumber *isPushOn; // 2 第二版本修改
`

**第二步**，更新数据库表版本，用于升级判断	

	+(int)getTableVersion
	{
    	    return 2;//第二版本，一次向上增加
	}	

**第三步**，在代理方法种进行数据库升级，插入数据库语句    

    +(LKTableUpdateType)tableUpdateForOldVersion:(int)oldVersion newVersion:(int)newVersion
    {
    	if(oldVersion == 1 && newVersion == 2){
        	[[LKDBHelper getUsingLKDBHelper] executeSQL:@"alter table user add column isPushOn NOT NULL default 1" arguments:nil];
    	}
    return LKTableUpdateTypeCustom;
	}
程序发布记得覆盖升级一次，查看数据是否完全
