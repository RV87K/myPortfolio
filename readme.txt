1. Установить Node JS - программа интерпритатор кода JS, длятого чтоб код читался на компе локально (без браузера)
2. Установка Gulp глобально: npm i --global gulp-cli;     Либо обновление: npm rm -- global gulp 
3. Установка проекта: npm init. Данные о проекте добавяться в файл package.json
4. Установка непосредственно в папку проекта: npm i --save-dev gulp  

Переименовать папку Gulp-start в название проекта и затем: npm i  // установка пакетов node_modules
Когда проект готов к продакшену: gulp build  //сборка проекта в папку dist
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
.src     // source
.dest    // destination
.require // метод запрашивающий данные определенного класса
.pipe    // метод позволяющий делать церочки из потоков (выход одного потока идёт на вход другому образующий цепочки)
exports  // реестр




const { src, dest, watch, parallel, series } = require('gulp');   // передача константам возможностей плагина 'gulp'  в начало gulpfile.js




Установка scss/saas terminal: npm install sass gulp-sass --save-dev  
gulpfile.js: const scss = require('gulp-sass')(require('sass')); //передача константе возможностей плагина 'gulp-sass'
function styles() {
  return src('app/scss/style.scss')     // исходный файл
        .pipe(scss())			// конвертация
        .pipe(dest('app/css'))		// место переноса
}
exports.styles = styles;
Terminal: gulp styles




Установка конкатинации(для сжатия файлов) Terminal: npm i --save-dev gulp-concat
gulpfile.js: const concat = require('gulp-concat');
function styles() {
  return src('app/scss/style.scss')
        .pipe(scss({outputStyle: 'compressed'}))  // сжатие(конкатинация)          .pipe(scss({outputStyle: 'expanded'})) - расжатие файла 
        .pipe(concat('style.min.css'))		//конктинация всех файлов в один 'style.min.css'
        .pipe(dest('app/css'))
}
exports.styles = styles;
Terminal: gulp styles 





Функция следящая за изменением файлов
const watch = require('gulp');
function watching() {
  watch(['app/scss/**/*.scss'], styles);			//при изменении файла в [] сработает функция после запятой т.е. styles
  watch(['app/js/**/*.js','!app/js/main.min.js'], scripts);
  watch(['app/*.html']).on('change',browserSync.reload)
}




Установка плагина BrowserSync: npm i --save-dev browser-sync  
Создаем переменную const browserSync = require('browser-sync').create();
а затем функцию: function browsersync() {
  browserSync.init({
    server: {
      baseDir: 'app/'
  }
  });
}







Установка плагина для минификации JavaScript: npm i --save-dev gulp-uglify-es
функция: function scripts() {
  return src([
    'node_modules/jquery/dist/jquery.js', //иходные файлы
    'app/js/main.js'
  ])
  .pipe(concat('main.min.js')) // конкотинируем файл
  .pipe(uglify())	       // минифицируем файл
  .pipe(dest('app/js'))        // помещаем файл в данную директрорию
  .pipe(browserSync.stream())  // обновляем страницу
}
в функции watching добавляем: 
function watching() {
  watch(['app/js/main.js','!app/js/main.min.js'], scripts);    // при изменении файла ['app/js/**/*.js', кроме '!app/js/main.min.js'], запускается функция scripts
}
в exports добавляем: 
exports.scripts = scripts;				      
exports.default = paralel(scripts, watching, browsersync);    // паралельный запуск всех функций







Установка jQuery: npm i --save-dev jquery  // добавляет папку в node_modules






Установка плагина autoprefixer (корректировки для старых версий браузеров): npm install --save-dev gulp-autoprefixer
 const autoprefixer = require('gulp-autoprefixer');   // добавляем константу и присваиваем данные из папки node_modules
 function styles() {				      // в уже существующей функции после минификации файла 
  return src('app/scss/style.scss')
        .pipe(scss({outputStyle: 'compressed'}))
        .pipe(concat('style.min.css'))
        .pipe(autoprefixer({
          overrideBrowserslist: ['last 10 version'],  // добавляем строку где указываем для 10 последних версий браузеров
          grid: true				      // для гридов соответственно
        }))
        .pipe(dest('app/css'))
        .pipe(browserSync.stream())
}







Сборка с помощью функции build  //  перенос файлов из 'app' в 'dist'
function build() {
  return src([			// источник
    'app/css/style.min.css',
    'app/fonts/**/*',
    'app/js/main.min.js',
    'app/*.html'
  ], {base: 'app'})
  .pipe(dest('dist'))		// dest(перенос) в dist
}
exports.build       = build;
в терминале запускаем: gulp build






Установка плагина для минификации картинок:  npm i gulp-imagemin@7.1.0
function images() {
  return src('app/images/**/*')
  .pipe(imagemin([
    imagemin.gifsicle({interlaced: true}),
    imagemin.mozjpeg({quality: 75, progressive: true}),
    imagemin.optipng({optimizationLevel: 5}),
    imagemin.svgo({
      plugins: [
        {removeViewBox: true},
        {cleanupIDs: false}
      ]
    })
  ]))
  .pipe(dest('dist/images'))
}
exports.images = images
Terminal: gulp images




Установка плагина для удаления ненужных(старых) файлов: npm i del --save-dev
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ГОТОВАЯ МИНИМАЛЬНАЯ СБОРКА

const { src, dest, watch, parallel, series } = require('gulp');

const scss         = require('gulp-sass')(require('sass'));
const concat       = require('gulp-concat');
const browserSync  = require('browser-sync').create();
const uglify       = require('gulp-uglify-es').default;
const autoprefixer = require('gulp-autoprefixer');
const imagemin    = require('gulp-imagemin');
const del         = require('del');

function browsersync() {
  browserSync.init({
    server: {
      baseDir: 'app/'
  }
  });
}

function cleanDist() {
  return del('dist');
}

function images() {
  return src('app/images/**/*')
  .pipe(imagemin([
    imagemin.gifsicle({interlaced: true}),
    imagemin.mozjpeg({quality: 75, progressive: true}),
    imagemin.optipng({optimizationLevel: 5}),
    imagemin.svgo({
      plugins: [
        {removeViewBox: true},
        {cleanupIDs: false}
      ]
    })
  ]))
  .pipe(dest('dist/images'))
}

function scripts() {
  return src([
    'node_modules/jquery/dist/jquery.js',
    'app/js/main.js'
  ])
  .pipe(concat('main.min.js'))
  .pipe(uglify())
  .pipe(dest('app/js'))
  .pipe(browserSync.stream())

}

function styles() {
  return src('app/scss/style.scss')
        .pipe(scss({outputStyle: 'compressed'}))
        .pipe(concat('style.min.css'))
        .pipe(autoprefixer({
          overrideBrowserslist: ['last 10 version'],
          grid: true
        }))
        .pipe(dest('app/css'))
        .pipe(browserSync.stream())
}

function build() {
  return src([
    'app/css/style.min.css',
    'app/fonts/**/*',
    'app/js/main.min.js',
    'app/*.html'
  ], {base: 'app'})
  .pipe(dest('dist'))
}

function watching() {
  watch(['app/scss/**/*.scss'], styles);
  watch(['app/js/**/*.js','!app/js/main.min.js'], scripts);
  watch(['app/*.html']).on('change',browserSync.reload)
}

exports.styles      = styles;
exports.watching    = watching;
exports.browsersync = browsersync;
exports.scripts     = scripts;
exports.images      = images;
exports.cleanDist   = cleanDist;

exports.build   = series(cleanDist, images, build);
exports.default = parallel(styles, scripts, browsersync, watching);