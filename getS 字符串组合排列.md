function getS(totalS,curString){
    if(totalS.length === 0){// 如果已经是最后一层了
        console.log(curString);
        return 1;
    }
    var len = totalS.length;
    var tempArr = '';
    var num = 0;
    for(var i = 0;i < len;i++){
        if(tempArr.indexOf(totalS[i]) === -1) {
            tempArr += totalS[i];
            var newtotalS = totalS.substr(0,i) + totalS.substr(i+1);
            num += getS(newtotalS,curString+totalS[i]);
        }
    }
    return num;
 }
