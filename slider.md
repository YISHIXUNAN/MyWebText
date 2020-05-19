function SliderVerification(dataJson){
    if(!dataJson){
        throw new Error('需要传入初始化块ID！')
    }
    this.container = document.querySelector('#'+dataJson.containerId)?document.querySelector('#'+dataJson.containerId):null;
    console.log(this.container)
    this._init(dataJson);
    this._bindEvent();
}

SliderVerification.prototype._init = function(dataJson){
    // 初始化布局和样式
    this.container.innerHTML = [
        "<div class='bgColor'>"+
        "</div><div class='txt' >向右滑动验证身份</div>"+
        "<div class='slider'><i class='iconfont icon-double-right'></i></div>"
    ]
    // 获取传入值的数值
    const getWidth = dataJson.width;
    const getHeight = dataJson.height?dataJson.height:parseInt(this.container.offsetWidth)*0.12+'px';
    const getBorderRadius = parseInt(getHeight)/2+'px';
    const getFontSize = parseInt(getHeight)/3.2+'px';
    this.getIconWidth = parseInt(getHeight)*1.25+'px';
    this.IconLeft = parseInt(this.getIconWidth)/2;
    this.bgColor = this.container.querySelector('.bgColor');
    this.txt = this.container.querySelector('.txt');
    this.slider = this.container.querySelector('.slider');
    this.icon = this.container.querySelector(".slider>i");

    this.slider.style.cssText = "position:absolute;left:0;top:0;width:"+this.getIconWidth+";height:"+getHeight+";background:url('"+dataJson.iconUrl+"');background-size:cover;text-align:center;cursor:move;"
    this.container.style.cssText = "position:relative;width:"+getWidth+";height:"+getHeight+";border-radius:"+getBorderRadius+";margin:0 auto;margin-top:10px;background-color:#d0eaf2"    
    this.bgColor.style.cssText = "padding:0px;position:absolute;left:1px;top:0px;width:"+this.IconLeft+"px;height:"+getHeight+";border-top-left-radius:"+getBorderRadius+";border-bottom-left-radius:"+getBorderRadius+";background-color:#4EDED2;"
    this.txt.style.cssText = "position:absolute;width:100%;height:"+getHeight+";line-height:"+getHeight+";font-size:"+getFontSize+";text-align:center;border-radius:"+getBorderRadius+";color:#888;"
    this.icon.style.cssText = "position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);"
    // 初始化必要变量
    this.successMoveDistance = this.container.offsetWidth- this.slider.offsetWidth;//解锁需要滑动的距离
    this.downX = null;//用于存放鼠标按下时的位置
    this.isSuccess = false;//是否解锁成功的标志，默认不成功
}

SliderVerification.prototype._bindEvent = function(){
    var self = this;
    this.slider.addEventListener('touchstart', function(e) {
        self._mousedownHandler(e);
    });
}

SliderVerification.prototype._mousedownHandler = function(e){
    e.preventDefault()
    this.slider.ontouchend = null;
    var self = this;
    this.bgColor.style.transition = "";
    this.slider.style.transition = "";
    var e = e || window.event || e.which;
    this.downX = e.touches[0].clientX;
    //在鼠标按下时，分别给鼠标添加移动和松开事件
    this.slider.addEventListener('touchmove', function(e) {
        self._mousemoveHandler(e);
    });
    this.slider.addEventListener('touchend', function() {
        self._mouseupHandler();
    });
};

SliderVerification.prototype._mouseupHandler = function(){
    if(!this.isSuccess){
        this.txt.innerHTML = "向右滑动验证身份";
        this.bgColor.style.width = this.IconLeft + "px";
        this.slider.style.left = 0 + "px";
        this.bgColor.style.transition = "width 0.8s linear";
        this.slider.style.transition = "left 0.8s linear";
    }
    this.slider.ontouchend = null;
};

SliderVerification.prototype._mousemoveHandler = function(e){
    this.txt.innerHTML = "";
    var self = this;
    var e = e || window.event || e.which;
    var moveX = e.touches[0].clientX;
    var offsetX = self._getOffsetX(moveX - this.downX,0,this.successMoveDistance);
    this.bgColor.style.width = (offsetX+this.IconLeft) + "px";
    this.slider.style.left = offsetX + "px";
    if(offsetX == this.successMoveDistance){
        self._success();
    }
    //如果不设置滑块滑动时会出现问题（目前还不知道为什么）
    e.preventDefault();
}

SliderVerification.prototype._getOffsetX = function(offset,min,max){
    if(offset < min){
        offset = min;
    }else if(offset > max){
        offset = max;
    }
    return offset;
}

SliderVerification.prototype._success = function(){
    this.isSuccess = true;
    this.txt.innerHTML = "验证成功！";
    this.txt.style.color = "#ffffff";
    this.bgColor.style.backgroundColor ="#4EDED2";
    this.slider.className = "slider active";
    this.icon.className = "iconfont icon-xuanzhong";
    //滑动成功时，移除鼠标按下事件和鼠标移动事件
    this.slider.ontouchend = null;
    // this.slider.ontouchmove = null;

}

SliderVerification.prototype._getState = function(){
    return this.isSuccess;
}

