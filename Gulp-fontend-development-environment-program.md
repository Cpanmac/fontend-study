#Gulp安装及配合组件构建前端开发一体化
---
## [gulp官网地址](http://www.gulpjs.com.cn/)

###gulp依赖环境：[nodejs](https://nodejs.org/en/) 和 [ruby](https://www.ruby-lang.org/zh_cn/)

>注意：nodejs和ruby对应的版本

###	安装好nodejs和ruby开始安装gulp

1.	全局安装 gulp：

		npm install --global gulp
2.	作为项目的开发依赖（devDependencies）安装：
		
		npm install --save-dev gulp

3.	安装前端开发需要使用的gulp插件

前端项目需要的功能：
	1、图片（压缩图片支持jpg、png、gif）
	2、样式 （支持sass 同时支持合并、压缩、重命名）
	3、javascript （检查、合并、压缩、重命名）
	4、html （压缩）
	5、客户端同步刷新显示修改
	6、构建项目前清除发布环境下的文件（保持发布环境的清洁）
	
	通过gulp plugins，寻找对于的gulp组件
	gulp-imagemin: 压缩图片
	gulp-ruby-sass: 支持sass
	gulp-minify-css: 压缩css
	gulp-jshint: 检查js
	gulp-uglify: 压缩js
	gulp-concat: 合并文件
	gulp-rename: 重命名文件
	gulp-htmlmin: 压缩html
	gulp-clean: 清空文件夹
	gulp-livereload: 服务器控制客户端同步刷新（需配合chrome插件LiveReload及tiny-lr）
	gulp-connect：	自动刷新浏览器
	rimraf：清空文件夹
	gulp-autoprefixer：自动填补css3前缀
	gulp-cssnano：优化css的插件如空白转换、css写法优化
	gulp-css-spriter：图片雪碧图
	gulp-cache：图片压缩缓存
	imagemin-pngquant：图片深度压缩
	gulp-imagemin：图片压缩
	gulp-htmlmin：html压缩

	...等等其他的gulp插件请关注gulp官网...

>批量gulp插件安装命令如下：


		npm install gulp-htmlmin gulp-util gulp-cache gulp-css-spriter gulp-sass gulp-imagemin imagemin-pngquant gulp-autoprefixer gulp-ruby-sass gulp-cssnano gulp-minify-css gulp-jshint gulp-uglify gulp-rename gulp-concat gulp-clean gulp-livereload tiny-lr rimraf gulp-connect --save-dev

###在项目根目录下创建一个名为 gulpfile.js 的文件：

		// 引入 gulp及组件
		var gulp = require('gulp'), //基础库
		    htmlmin = require('gulp-htmlmin'), //HTML压缩
		    imagemin = require('gulp-imagemin'), //图片压缩
		    pngquant = require('imagemin-pngquant'), //图片深度压缩
		    cache = require('gulp-cache'), //图片压缩缓存
		    spriter = require('gulp-css-spriter'), //图片雪碧图
		    sass = require('gulp-sass'), //sass
		    minifycss = require('gulp-minify-css'), //css压缩
		    cssnano = require('gulp-cssnano'),//优化css的插件如空白转换、css写法优化
		    autoprefixer = require('gulp-autoprefixer'), //自动填补css3前缀
		    jshint = require('gulp-jshint'), //js检查
		    uglify = require('gulp-uglify'), //js压缩
		    rename = require('gulp-rename'), //重命名
		    concat = require('gulp-concat'), //合并文件
		    rimraf = require('rimraf'), //清空文件夹
		    connect = require('gulp-connect'); //自动刷新浏览器
		// HTML处理
		gulp.task('html', function() {
		    var htmlSrc = 'src/*.html',
		        htmlDst = 'assets/';
		    var options = {
		        removeComments: false, //清除HTML注释
		        collapseWhitespace: false, //压缩HTML
		        collapseBooleanAttributes: false, //省略布尔属性的值 <input checked="true"/> ==> <input />
		        removeEmptyAttributes: true, //删除所有空格作属性值 <input id="" /> ==> <input />
		        removeScriptTypeAttributes: true, //删除<script>的type="text/javascript"
		        removeStyleLinkTypeAttributes: true, //删除<style>和<link>的type="text/css"
		        minifyJS: false, //压缩页面JS
		        minifyCSS: false //压缩页面CSS
		    };
		    gulp.src(htmlSrc)
		        .pipe(htmlmin(options))
		        .pipe(gulp.dest(htmlDst))
		        .pipe(connect.reload());
		});
		
		// 样式处理
		gulp.task('sass', function() {
		    var cssSrc = 'src/dist/scss/*.scss',
		        cssDst = 'assets/dist/css',
		        timestamp = +new Date();
		    gulp.src(cssSrc)
		        .pipe(sass())
		        .pipe(gulp.dest(cssDst))
		        .pipe(rename({
		            suffix: '.min'
		        }))
		        .pipe(spriter({
		            // 生成的spriter的位置
		            'spriteSheet': 'assets/dist/img/sprite.png',
		            // 生成样式文件图片引用地址的路径
		            // 如下将生产：backgound:url(.images/sprite20324232.png)
		            'pathToSpriteSheetFromCSS': '../img/sprite.png',
		            spritesmithOptions: {
		                padding: 40 //雪碧图的间距
		            }
		        }))
		        .pipe(autoprefixer("last 10 version", "ie 8", "ie 7"))
		        // .pipe(cssnano({
		        // 	discardComments: {
		        // 		removeAll: true
		        // 	}
		        // }))
		        .pipe(gulp.dest(cssDst))
		        .pipe(connect.reload());
		});
		
		gulp.task('css', function() {
		    var cssSrc = 'src/dist/css/**/*.css',
		        cssDst = 'assets/dist/css';
		    //timestamp = +new Date();
		    gulp.src(cssSrc)
		        .pipe(spriter({
		            // 生成的spriter的位置
		            'spriteSheet': 'assets/dist/img/sprite.png',
		            // 生成样式文件图片引用地址的路径
		            // 如下将生产：backgound:url(.images/sprite20324232.png)
		            'pathToSpriteSheetFromCSS': '../img/sprite.png',
		            spritesmithOptions: {
		                padding: 40 //雪碧图的间距
		            }
		        }))
		        // .pipe(autoprefixer({
		        // 	browsers: ['last 10 versions']
		        // }))
		        // .pipe(cssnano({
		        // 	discardComments: {
		        // 		removeAll: true
		        // 	}
		        // }))
		        .pipe(gulp.dest(cssDst))
		        .pipe(connect.reload());
		});
		
		// 图片处理(未开启)
		gulp.task('images', function() {
		    var imgSrc = 'src/dist/img/*.+(jpeg|jpg|png)',
		        imgDst = 'assets/dist/img';
		    gulp.src(imgSrc)
		        // .pipe(imagemin({
		        // 	optimizationLevel: 5, //类型：Number  默认：3  取值范围：0-7（优化等级）
		        // 	progressive: true, //类型：Boolean 默认：false 无损压缩jpg图片
		        // 	interlaced: true, //类型：Boolean 默认：false 隔行扫描gif进行渲染
		        // 	multipass: true, //类型：Boolean 默认：false 多次优化svg直到完全优化
		        // 	svgoPlugins: [{
		        // 		removeViewBox: false
		        // 	}], //不要移除svg的viewbox属性
		        // 	use: [pngquant()] //使用pngquant深度压缩png图片的imagemin插件
		        // }))
		        .pipe(gulp.dest(imgDst))
		        .pipe(connect.reload());
		});
		
		// js处理
		gulp.task('js', function() {
		    var jsSrc = 'src/dist/js/**/*.js',
		        jsDst = 'assets/dist/js';
		    gulp.src(jsSrc)
		        //.pipe(jshint('.jshintrc'))
		        //.pipe(jshint.reporter('default'))
		        //.pipe(concat('main.js'))
		        .pipe(gulp.dest(jsDst))
		        .pipe(rename({
		            suffix: '.min'
		        }))
		        .pipe(uglify({
		            mangle: false, //类型：Boolean 默认：true 是否修改变量名
		            compress: false //类型：Boolean 默认：true 是否完全压缩
		        }))
		        .pipe(gulp.dest(jsDst))
		        .pipe(connect.reload());
		});
		
		// 清空图片、样式、js
		gulp.task('clean', function(cb) {
		    rimraf('assets/dist', cb);
		});
		
		// 默认任务 清空图片、样式、js并重建 运行语句 gulp
		gulp.task('default', ['clean'], function() {
		    gulp.start('html', 'css', 'js', 'images');
		});
		
		//使用connect启动一个Web服务器
		gulp.task('connect', function() {
		
		    connect.server({
		        root: 'assets',
		        port: 8000,
		        livereload: {
		            port: 35729,
		        }
		    });
		});
		
		// 监听任务 运行语句 gulp watch
		gulp.task('watch', ['default', 'connect'], function() {
		
		    // 监听html
		    gulp.watch(['src/*.html'], ['html']);
		
		    // 监听css
		    //gulp.watch(['src/dist/scss/*.scss'], ['sass']);
		
		    // 监听css
		    gulp.watch(['src/dist/css/*.css'], ['css']);
		
		    // 监听images(暂未开启)
		    gulp.watch(['src/dist/img/*.+(jpeg|jpg|png)'], ['images']);
		
		    // 监听js
		    gulp.watch(['src/dist/js/*.js'], ['js']);
		
		});


###运行 gulp(例如监控文件的变化的命令)：

		gulp watch
###运行结果截图:
![gulp-watch](http://7xsfn1.com1.z0.glb.clouddn.com/gulp-watch-success.png)
	
	