# 源文件
~~~ JavaScript

<!doctype html>
<html>

	<head>
		<meta charset="utf-8">
		<title></title>
	</head>

	<body>
		<script type="text/javascript">
			mui.init();

			// 做饭
			function cook() {
				console.log('开始做饭..');
				var p = new Promise(function(resolve, reject) {
					setTimeout(function() {
						console.log('做饭完毕！！！！');
						resolve('鸡蛋炒饭');
					}, 1000);
				});
				return p;
			}
			// 吃饭
			function eat(data) {
				console.log('开始吃饭....' + data);
				var p = new Promise(function(resolve, reject) {
					setTimeout(function() {
						console.log(data + ' 吃完了!!!!!!!!!!');
						resolve('一双筷子和一个碗。');
					}, 1000);
				});
				return p;
			}
			// 洗碗
			function wash(data) {
				console.log('开始洗碗 ' + data);
				var p = new Promise(function(resolve, reject) {
					setTimeout(function() {
						console.log(data + ' 洗完了。');
						resolve('干净的一个碗和一双筷子');
					}, 1000);
				});
				return p;
			}
			// 使用方式一
			cook().then(function(data) {
				return eat(data);
			}).then(function(data) {
				return wash(data);
			}).then(function(data) {
				console.log(data);
			});
			// 使用方式二
			cook().then(eat).then(wash).then(function(data) {
				console.log(data);
			});
			
			
			// reject 方法
			function cook1(){
				console.log('开始做饭.....');
				var p = new Promise(function(resolve,reject){
					setTimeout(function(){
						console.log('做饭失败。');
						reject('饭烧焦了');
					},10000)
				});
				return p;
			}
			cook1().then(eat,function(error){
				console.log(error +'无法吃！');
			});

			// catch 方法
			function cook2(){
				console.log('开始做饭。');
				var p = new Promise(function(resolve,reject){
					setTimeout(function(){
						console.log('做饭失败。');
						return reject('饭烧焦了');
					})
				});
				return p;
			}
			
			cook2().then(eat).catch(function(data){
				console.log(data);
			})
			
			cook().then(function(data){
				throw Error('不小打翻了饭');
				eat(data);
			}).catch(function(data){
				console.log(data);
			});
			
			
			// all 方法
			
			// 切菜
			function cut(){
				console.log('开始切菜。');
				var p = new Promise(function(resolve,reject){
					setTimeout(function(){
						console.log('切菜完毕。');
						resolve('切好的菜');
					},1000);
				});
				return p;
			}
			
			// 烧水
			function boil(){
				console.log('开始烧水。');
				var p = new Promise(function(resolve,reject){
					setTimeout(function(){
						console.log('烧水完毕。');
						resolve('烧好的水');
					},1000);
				});
				return p;
			}
			
			Promise.all([cut(),boil()]).then(function(result){
				console.log('准备工作完毕!!');
				console.log( result );
			});

			// race
			Promise.race([cut(),boil()]).then(function(result){
				console.log('准备工作完毕！');
				console.log(result);
			})
			
		</script>
	</body>

</html>


~~~
