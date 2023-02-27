# TODO list

___

本文档用来记载一些可能会需要实现但暂且无暇的想法。留待日后再做或者同伴帮忙做了。

## 服务端的包验证

当客户端连接到服务端遇到MD5失败时，考虑：

1. 服务端除了告知失败之外还告知客户端自己的打包属性，即自己启用了哪些包，包的URL和版本等。
2. 客户端根据信息禁用掉不需要的包，下载没有的包，更新启用的包，将需要的包都切换到服务器提供的版本。

___

## UI主题可拓展

考虑影响一下skin-bank.js，使其根据某个config的不同，将相应的值设为不同的路径。然后确保所有图片资源（关于页面和logo除外）都通过skin-bank.js访问所需图片。

由于所有拓展包都是只能通过init.lua访问，考虑为QmlBackend提供相应的Lua接口使其能够注册新的配置方案。配置文件本身的组织考虑JSON。

考虑在skin-bank.js中加入更多信息，例如各个组件的x, y, width, height等。因为影响到的都是Image所以设置这些应该是够了。

___

## 对话框可拓展

考虑劫营，严教等含有特殊交互框的例子。

可以通过类似fk.Backend:loadDialog的方式弹出对话框。在Lua文件中相应的地方影响client_callback这张全局表。但同时考虑到拓展包比server/client相关的Lua先加载，或许需要一些特殊的办法，比如弄一张全局表，然后client.lua初始回调函数列表的时候先把那个表的结果加进去之类的。

至于Lua如何与QML进行交互，毫无疑问的是通过JSON字符串。QML最后需要自己将数据发回给服务端，这里需要用到ClientInstance手动发送了，不能用RoomLogic.js。同样的需要有统一的方法初始化QML对话框的数据，考虑都传入JSON字符串，然后QML在Component.onComplete时候加载初始化数据。

skin-bank.js的话依然可以用相对位置进行加载，这个理论上应该是不会被影响到。

___

## 代码简洁化

目前FK的lua代码中仍有不少地方的代码重用度不高，典型的例子是fk_ex.lua。考虑在这里用local function将重复代码合并一下。

还有视为技/触发技/主动技这些能够被“发动”的技能（即继承于UsableSkill的基本），它们的技能生效环节都有很多重复，比如播放声音动画和具体生效等。考虑在某处用一个函数总结一下，至于具体生效部分可以包在一个函数里面`function() skill:onEffect(room, effect) end`然后作为参数传递到useskill函数中。如果后面要做SkillUsed之类的时机的话，这方面就更加重要了。