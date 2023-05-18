
# 数据结构
数据保存在./static/data中，包含measure.json和method.json两个文件，可替换

+ measure：保存了度量信息，结构如下：

  ```json
  [
  	{
  	    'name':'measure1', //自定义的measure名称，可替换
           'data':[
              {'value':value1},{'value':value2},...
          ]//度量的值，数组长度为点的总数,要注意顺序上和method.json的数据的一一对应
  	},
      {
      	'name':'measure2',
          ...
      },
  	...
  ]
  ```

+ method.json：保存了图信息，结构如下：

  ```json
  [
  	{
  	    'name':'method1', //自定义的method名称，可替换
           'pos':[
              {'x':x1,
               'y':y1}
               ...
          ],//坐标的值，数组长度为点的总数,要注意顺序上和measure.json的数据的一一对应
  		'link':[
              [index173,index072],//边的两点的index，范围为[0,点的总数],要注意顺序上和measure.json的数据的一一对应
              ...
          ]
      },
      {
      	'name':'method2',
          ...
      },
  	...
  ]
  ```

  

# install dependencies
npm i

# start
npm start
