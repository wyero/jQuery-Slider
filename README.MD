```
--- HTML ---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery Slider</title>
    <link rel="stylesheet" href="style/css/style.css">
</head>
<body>
    <header>
        <h1>jQuery Slider</h1>
        <ion-icon name="moon-outline" class="icon"></ion-icon>
    </header>
    <div id="sliders"></div>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script type="module" src="https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.esm.js"></script>
    <script>
        $( document ).ready(function() {
            var options = {
                showBullets: true,
                startIndex: 1
            };
            var slider = $("#sliders").imageSlider(modelData, options);
            slider.on("slide", function (event, val) {
                console.log("slided to image num: " + val);
            });
        });
    </script>
    <script src="demoData.js"></script>
    <script src="jquery/slider.js"></script>
</body>
</html>
```
```
--- SCSS ---
*{
    margin: 0;
    padding: 0;
}
body{
    background: whitesmoke;
}
header{
    display: flex;
    align-items: center;
    margin: 10px auto;
    h1{
        margin: auto;
    }
    .icon{
        width: 50px;
        height: 30px;
        position: relative;
        right: 20px;
        cursor: pointer;
    }
}
#sliders {
    width: 700px;
    height: 450px;
    margin: auto;
}
.dark-mode{
    background: #0a0a0a;
    color: whitesmoke;
}
@media screen and (max-width: 600px){
    #sliders{
        width: 500px;
        height: 450px;
    }
}
```
```
--- JQUERY ---
(function( $ ) {
    $.fn.imageSlider = function(data, options) {
        if (!data || !data.length) {
            console.error(error);
            return this;
        }
        var settings = $.extend({
            showBullets: false,
            startIndex: 1
        }, options);
        var elem = this, slideWidth, slideHeight, ulElem,
            singleImgFlag = (data.length <= 1)? true: false;
        this.setActiveBulletStyles = function (classDef) {
            this.activeBulletClass = classDef;
            _injectClasses('.activeBullet {' + classDef + '}');
        };
        this.css({
            position: 'relative',
            overflow: 'hidden'
        });
        var leftArrow = $("<a/>", {class: "left-arrow"}).html("&#11164");
        var rightArrow = $("<a/>", {class: "right-arrow"}).html("&#11166").css({'border-radius': '2px 0 0 2px', right: '0'});
        this.append(leftArrow).append(rightArrow);
        this.find('a').css({userSelect: 'none'});
        $("a.left-arrow, a.right-arrow").css({
            position: 'absolute',
            top: '40%',
            'z-index': '999',
            display: 'block',
            color: '#ffffff',
            'font-weight': 'bold',
            'font-size': '25px',
            cursor: 'pointer',
            'opacity':'0.9',
            margin: '0 5px',
        });
        var ul = $("<ul/>").css({
            position: 'relative',
            margin: '0',
            padding: '0',
            'list-style': 'none',
        });
        _buildSlides(settings.startIndex);
        function _buildSlides(startIndex) {
            var i, len = data.length;
            for (i = startIndex-1; i < len; i++) {
                ul.append(_getSlide(i));
            }
            for (i = 0; i < startIndex-1; i++) {
                ul.append(_getSlide(i));
            }
            ul.appendTo(elem);
            ulElem = elem.find('ul');
            elem.find("li").css({
                position: 'relative',
                display: 'block',
                'float': 'left',
                width: elem.width(),
                height: elem.height()
            });
            slideWidth = elem.width();
            slideHeight = elem.height();
            var slideCount = elem.find('ul li').length,
                sliderUlWidth = slideCount * slideWidth;
            elem.children("ul").css({ width: sliderUlWidth, marginLeft: (singleImgFlag? 0 : -slideWidth)});
            elem.find('ul li:last-child').prependTo(ulElem);
        }
        function _getSlide (index) {
            var li = $("<li/>", { value: index+1 }).css({
                'background-image': "url('" + data[index].src + "')",
                'background-repeat': 'no-repeat',
                'background-size': 'cover',
                'background-position': 'center'
            })
            return li;
        }
        this.moveLeft = function() {
            var slidedIndex;
            ulElem.animate({
                left: + slideWidth
            }, 200, function () {
                elem.find('ul li:last-child').prependTo(ulElem);
                ulElem.css('left', '');
                slidedIndex = elem.find( "ul li:nth-child(2)" ).val();
                $(elem).trigger("slide", slidedIndex);
                settings.showBullets && _setActiveBullet(slidedIndex);
            });
            return this;
        };
        this.moveRight = function() {
            var slidedIndex;
            ulElem.animate({
                left: - slideWidth
            }, 200, function () {
                elem.find('ul li:first-child').appendTo(ulElem);
                ulElem.css('left', '');
                slidedIndex = elem.find( "ul li:nth-child(2)" ).val();
                $(elem).trigger("slide", slidedIndex);
                settings.showBullets && _setActiveBullet(slidedIndex);
            });
            return this;
        };
        $(this).find('a.left-arrow').click(function () {
            !singleImgFlag && elem.moveLeft();
        });
        $(this).find('a.right-arrow').click(function () {
            !singleImgFlag && elem.moveRight();
        });
        if (settings.showBullets) {
            _addBullets();
        }
        function _injectClasses(rule) {
            var div = $("<div />", {
                html: '&shy;<style>' + rule + '</style>'
            }).appendTo("body");
        }
        function _addBullets() {
            var bulletsHolder=
                $('<div/>', {id: 'bulletsHolder'}).css({
                    position: 'absolute',
                    bottom: '30px',
                    'text-align': 'center',
                    'z-index': '999',
                    width: '100%',
                });
            for (var i = 0; i < data.length; i++) {
                bulletsHolder.append($('<div/>', {value: i+1}).css({
                    'background-color': 'silver',
                    display: 'inline-block',
                    height: '10px',
                    width: '10px',
                    margin: '0 4px 0 4px',
                    'border-radius': '50%',
                    cursor: 'pointer'
                }).data('index', i+1));
            }
            elem.append(bulletsHolder);
            _setActiveBullet(settings.startIndex);     
        }
        function _setActiveBullet(slidedIndex) {
            $('#bulletsHolder *').removeClass('activeBullet');
            $('#bulletsHolder').find('div[value=' + slidedIndex + ']').addClass('activeBullet');
        }
        $('#bulletsHolder div').click(function() {
            var index = $(this).data().index;
            ulElem.empty();
            _buildSlides(index);
            _setActiveBullet(index);
        });
        return this;
    };
    //----//
    $(".icon").click(function(){
        $("body").toggleClass("dark-mode")
        if($("body").hasClass("dark-mode")){
            $(".icon").attr("name", "sunny-outline")
        }else{
            $(".icon").attr("name", "moon-outline")
        }
    })
}( jQuery ));
```