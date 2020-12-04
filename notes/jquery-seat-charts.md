#### 一、[Seat-Charts](https://github.com/mateuszmarkowski/jQuery-Seat-Charts)插件讲解

##### 1、座位ID以及LABEL自定义
+ 默认的座位渲染示例：
```
[
    'aaa___aaa',
    'aaaa_aaaa',
    'aaaa_aaaa'
]
```

+ 可自定义座位信息的渲染示例：
```
[
    'a[ID,LABEL]a[ID2,LABEL2]a___a[JUST_ID1]aa',
    'aaaa_aaaa[,JUST_LABEL1]',
    'aaaa_aaaa'
]
```

##### 2、左侧行数信息自定义
+ 如果用户不进行行数自定义，那么行数信息将默认从1开始。
+ 左侧行数信息自定义渲染示例：
```
naming: {
	top: false,
	rows: ['A', 'B', '', 'C']
}
```

#### 二、完整示例

##### 1、HTML代码
```html
<div id="main">
  <div class="demo">
    <div class="movie-details">
      <p>影片：
        <span>星际穿越</span>
      </p>
      <p>时间：
        <span>11月14日 21:00</span>
      </p>
      <div id="legend"></div>
    </div>
    <div class="myScroll">
      <div id="seat-map">
        <div class="front">屏幕</div>
      </div>
    </div>
    <div class="minimap">
      <div id="minimap-indicator"></div>
    </div>
    <div class="booking-details">
      <p>座位：</p>
      <ul id="selected-seats" class="clearfix"></ul>
      <p>票数：
        <span id="counter">0</span>
      </p>
      <p>总计：
        <b>￥
          <span id="total">0</span>
        </b>
      </p>
      <button class="checkout-button">确定购买</button>
    </div>
  </div>
</div>
```

##### 2、CSS代码
```css
@charset "UTF-8";

.clearfix:before,
.clearfix:after {
  content: '';
  display: table;
}

.clearfix:after {
  clear: both;
}

.clearfix {
  zoom: 1;
  clear: both;
}

ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
html,
body {
  height: 100%;
  overflow: hidden;
  margin: 0px;
  padding: 0px;
}
ul,
p {
  margin: 0;
  padding: 0;
}
#main {
  height: 100%;
}
.demo {
  position: relative;
  width: 100%;
  height: 100%;
}
.movie-details {
  height: 120px;
}
.seatCharts-legendList:before,
.seatCharts-legendList:after {
  content: '';
  display: table;
}

.seatCharts-legendList:after {
  clear: both;
}

.seatCharts-legendList {
  zoom: 1;
  clear: both;
}
.seatCharts-legendItem {
  float: left;
  width: 90px;
  line-height: 2;
}
.myScroll {
  position: absolute;
  z-index: 1;
  top: 120px;
  bottom: 48px;
  left: 0;
  width: 100%;
  min-height: 160px;
  overflow: hidden;
}
div.seatCharts-container {
  width: 400px;
  padding: 20px;
}
.front {
  width: 90%;
  margin: 5% auto;
  background-color: #f0f0f0;
  color: #666;
  text-align: center;
  padding: 3px;
  border-radius: 5px;
}
div.seatCharts-row {
  height: 35px;
}
div.seatCharts-row:before,
div.seatCharts-row:after {
  content: '';
  display: table;
}

div.seatCharts-row:after {
  clear: both;
}

div.seatCharts-row {
  zoom: 1;
  clear: both;
}
div.seatCharts-cell {
  float: left;
  color: #182c4e;
  height: 25px;
  width: 25px;
  line-height: 25px;
  margin: 3px;
  text-align: center;
  outline: none;
  font-size: 13px;
}
div.seatCharts-seat {
  color: #fff;
  cursor: pointer;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}
div.seatCharts-seat.available {
  background-color: #b9dea0;
}

div.seatCharts-seat.focused {
  background-color: #76b474;
  border: none;
}

div.seatCharts-seat.selected {
  background-color: #e6cac4;
}

div.seatCharts-seat.unavailable {
  background-color: #472b34;
  cursor: not-allowed;
}
.minimap {
  position: absolute;
  z-index: 1;
  width: 33%;
  height: 80px;
  top: 120px;
  right: 0;
  background-color: #ccc;
}
#minimap-indicator {
  position: absolute;
  z-index: 1;
  border: 1px solid #fe0;
  box-shadow: 0 0 5px #000;
  background: rgba(255, 255, 255, 0.15);
  -webkit-transform: translateZ(0);
  -moz-transform: translateZ(0);
  -ms-transform: translateZ(0);
  -o-transform: translateZ(0);
  transform: translateZ(0);
}
.booking-details {
  position: absolute;
  bottom: 0;
  width: 100%;
  background-color: #ccc;
  z-index: 99;
}
#selected-seats li {
  float: left;
}

```

##### 3、JS代码

```javascript
var price = 38 //票价

function recalculateTotal(sc) {
  var total = 0
  sc.find('selected').each(function() {
    total += price
  })

  return total
}

$(document).ready(function() {
  var $cart = $('#selected-seats'), //座位区
    $counter = $('#counter'), //票数
    $total = $('#total') //总计金额

  var myScroll = new IScroll('.myScroll', {
    // click: true,
    disableMouse: false,
    freeScroll: true,
    scrollbars: false,
    scrollX: true,
    scrollY: true,
    zoom: true,
    zoomStart: 0,
    zoomMin: 0,
    mouseWheel: true,
    wheelAction: 'zoom',
    indicators: {
      el: '.minimap',
      interactive: true
    }
  })

  console.dir(myScroll.options)

  var sc = $('#seat-map').seatCharts({
    map: [
      //座位图
      'a[A_1,1]a[A_2,2]a[A_3,3]a[A_4,4]a[A_5,5]a[A_6,6]a[A_7,7]a[A_8,8]a[A_9,9]a[A_10,10]a[A_11,11]',
      'a[B_1,1]a[B_2,2]a[B_3,3]a[B_4,4]a[B_5,5]a[B_6,6]a[B_7,7]a[B_8,8]a[B_9,9]a[B_10,10]a[B_11,11]',
      'a[C_1,1]a[C_2,2]a[C_3,3]a[C_4,4]a[C_5,5]a[C_6,6]a[C_7,7]a[C_8,8]a[C_9,9]a[C_10,10]a[C_11,11]',
      'a[D_1,1]a[D_2,2]a[D_3,3]a[D_4,4]a[D_5,5]a[D_6,6]a[D_7,7]a[D_8,8]a[D_9,9]a[D_10,10]a[D_11,11]',
      '___________',
      'a[E_1,1]a[E_2,2]a[E_3,3]_____a[E_4,4]a[E_5,5]a[E_6,6]',
      'a[F_1,1]a[F_2,2]a[F_3,3]a[F_4,4]a[F_5,5]___a[F_6,6]a[F_7,7]a[F_8,8]',
      'a[G_1,1]a[G_2,2]a[G_3,3]____a[G_4,4]a[G_5,5]a[G_6,6]a[G_7,7]'
    ],
    naming: {
      top: false,
      rows: ['A', 'B', 'C', 'D', '', 'E', 'F', 'G']
    },
    legend: {
      //定义图例
      node: $('#legend'),
      items: [['a', 'available', '可选座'], ['a', 'unavailable', '已售出']]
    },
    click: function() {
      //点击事件
      if (this.status() == 'available') {
        //可选座
        $('<li>' + this.settings.id.split('_')[0] + '排' + this.settings.id.split('_')[1] + '座</li>')
          .attr('id', 'cart-item-' + this.settings.id)
          .data('seatId', this.settings.id)
          .appendTo($cart)
        $counter.text(sc.find('selected').length + 1)
        $total.text(recalculateTotal(sc) + price)
        return 'selected'
      } else if (this.status() == 'selected') {
        //已选中
        //更新数量
        $counter.text(sc.find('selected').length - 1)
        //更新总计
        $total.text(recalculateTotal(sc) - price)
        //删除已预订座位
        $('#cart-item-' + this.settings.id).remove()
        //可选座
        return 'available'
      } else if (this.status() == 'unavailable') {
        //已售出
        return 'unavailable'
      } else {
        return this.style()
      }
    }
  })
  //已售出的座位
  sc.get(['C_6', 'C_7', 'C_8']).status('unavailable')

  $('.checkout-button').on('click', function(event) {
    event.preventDefault()

    var seatSelected = sc.find('selected')
    var seatSelectedIds = seatSelected.seatIds
    var isEffective = true
    $.each(seatSelectedIds, function(index, item) {
      var seatSelectedPrveNode = seatSelected.seats[index].node().prevAll()
      var seatSelectedNextNode = seatSelected.seats[index].node().nextAll()
      var prevSeatsStatus = []
      var nextSeatsStatus = []
      if (seatSelectedPrveNode.length != 0) {
        $.each(seatSelectedPrveNode, function(index2, item2) {
          try {
            var seatStatus = sc.get([item2.id]).seats[0].settings.status
            prevSeatsStatus.push(seatStatus)
          } catch (e) {
            prevSeatsStatus.push('noSeat')
          }
        })
      }
      if (seatSelectedNextNode.length != 0) {
        $.each(seatSelectedNextNode, function(index3, item3) {
          try {
            var seatStatus = sc.get([item3.id]).seats[0].settings.status
            nextSeatsStatus.push(seatStatus)
          } catch (e) {
            nextSeatsStatus.push('noSeat')
          }
        })
      }
      console.log(prevSeatsStatus)
      console.log(nextSeatsStatus)

      if (prevSeatsStatus.length == 1) {
        if (prevSeatsStatus[0] == 'available') {
          isEffective = false
          return false
        } else {
          isEffective = true
        }
      } else {
        if (prevSeatsStatus[0] == 'available' && prevSeatsStatus[1] != 'available') {
          isEffective = false
          return false
        } else {
          isEffective = true
        }
      }

      if (nextSeatsStatus.length == 1) {
        if (nextSeatsStatus[0] == 'available') {
          isEffective = false
          return false
        } else {
          isEffective = true
        }
      } else {
        if (nextSeatsStatus[0] == 'available' && nextSeatsStatus[1] != 'available') {
          isEffective = false
          return false
        } else {
          isEffective = true
        }
      }
      // var seatSelectedPrveNode = seatSelected.seats[index].node().prev();
      // var seatSelectedPrve2Node = seatSelected.seats[index].node().prev().prev();
      // var seatSelectedNextNode = seatSelected.seats[index].node().next();
      // var seatSelectedNext2Node = seatSelected.seats[index].node().next().next();
      // var LeftSileSeat1 = seatSelectedPrveNode[0].id;
      // var LeftSileSeat2 = '';
      // if (LeftSileSeat1 != '' && LeftSileSeat1 != null && LeftSileSeat1 != undefined) {
      //     LeftSileSeat2 = seatSelectedPrve2Node[0].id;
      // }
      // var rightSileSeat1 = seatSelectedNextNode[0].id;
      // var rightSileSeat2 = '';
      // if (rightSileSeat1 != '' && rightSileSeat1 != null && rightSileSeat1 != undefined) {
      //     rightSileSeat2 = seatSelectedNext2Node[0].id;
      // }
      // console.log("左边第一位：" + LeftSileSeat1);
      // console.log("左边第二位：" + LeftSileSeat2);
      // console.log("右边第一位：" + rightSileSeat1);
      // console.log("右边第二位：" + rightSileSeat2);
      // var seatSelectedIdRow = item.split('_')[0];
      // var seatSelectedIdCol = item.split('_')[1];
      // console.log('自己的坐标：' + item);
      // console.log('自己的行坐标' + seatSelectedIdRow);
      // console.log('自己的列坐标' + seatSelectedIdCol);
      // var seatSelectedIdColL1 = 0, seatSelectedIdColL2 = 0, seatSelectedIdColR1 = 0, seatSelectedIdColR2 = 0;
      // var seatSelectedLeft2 = '', seatSelectedRight2 = '';
      // var seatSelectedLeft2Status = [], seatSelectedRight2Status = [];
      // 选中座位的左边座位状态
      // if ((LeftSileSeat1 != '' && LeftSileSeat1 != null && LeftSileSeat1 != undefined) && (LeftSileSeat2 == '' || LeftSileSeat2 == null || LeftSileSeat2 == undefined)) {
      //     seatSelectedIdColL1 = parseInt(seatSelectedIdCol) -1;
      //     seatSelectedLeft2 = sc.get([seatSelectedIdRow + '_' + seatSelectedIdColL1]).seats;
      // }else{
      //     seatSelectedIdColL1 = parseInt(seatSelectedIdCol) -1;
      //     seatSelectedIdColL2 = parseInt(seatSelectedIdCol) -2;
      //     seatSelectedLeft2 = sc.get([seatSelectedIdRow + '_' + seatSelectedIdColL1, seatSelectedIdRow + '_' + seatSelectedIdColL2]).seats;
      // }
      // for (var i = 0; i < seatSelectedLeft2.length; i++) {
      //     seatSelectedLeft2Status.push(seatSelectedLeft2[i].settings.status);
      // }
      // console.log('左侧位置个数：' + seatSelectedLeft2Status.length);
      // console.log('左侧两个位置的状态：' + seatSelectedLeft2Status);
      // 判断选中座位的右边座位状态
      // if((rightSileSeat1 != '' && rightSileSeat1 != null && rightSileSeat1 != undefined) && (rightSileSeat2 == '' || rightSileSeat2 == null || rightSileSeat2 == undefined)){
      //     seatSelectedIdColR1 = parseInt(seatSelectedIdCol) + 1;
      //     seatSelectedRight2 = sc.get([seatSelectedIdRow + '_' + seatSelectedIdColR1]).seats;
      //     console.log([seatSelectedIdRow + '_' + seatSelectedIdColR1]);
      // }else{
      //     seatSelectedIdColR1 = parseInt(seatSelectedIdCol) +1;
      //     seatSelectedIdColR2 = parseInt(seatSelectedIdCol) +2;
      //     seatSelectedRight2 = sc.get([seatSelectedIdRow + '_' + seatSelectedIdColR1, seatSelectedIdRow + '_' + seatSelectedIdColR2]).seats;
      // }
      // for (var j = 0; j < seatSelectedRight2.length; j++) {
      //     seatSelectedRight2Status.push(seatSelectedRight2[j].settings.status);
      // }
      // console.log('右侧位置个数：' + seatSelectedRight2Status.length);
      // console.log('右侧两个位置的状态：' + seatSelectedRight2Status);
      // console.log("----------------------分割线-----------------------");
      // var seatSelectedLeftLength = seatSelectedLeft2Status.length;
      // var seatSelectedRightLength = seatSelectedRight2Status.length;
      // switch(seatSelectedLeftLength + '_' + seatSelectedRightLength){
      //     case '0_1' :
      //         if (seatSelectedRight2Status[0] == 'available') {
      //             alert('No.1:选择的座位无效');
      //         }
      //         break;
      //     case '0_2' :
      //         if (seatSelectedRight2Status[0] == 'available' && seatSelectedRight2Status[1] == 'selected') {
      //             alert('No.2:选择的座位无效');
      //         }
      //         break;
      //     case '1_0' :
      //         if (seatSelectedLeft2Status[0] == 'available') {
      //             alert('No.3:选择的座位无效');
      //         }
      //         break;
      //     case '1_1' :
      //         if (seatSelectedLeft2Status[0] == 'available' || seatSelectedRight2Status[0] == 'available') {
      //             alert('No.4:选择的座位无效');
      //         }
      //         break;
      //     case '1_2' :
      //         if (seatSelectedLeft2Status[0] == 'available' || (seatSelectedRight2Status[0] == 'available' && seatSelectedRight2Status[1] == 'selected') || (seatSelectedRight2Status[0] == 'selected' && seatSelectedRight2Status[1] == 'available')) {
      //             alert('No.5:选择的座位无效');
      //         }
      //         break;
      //     case '2_0' :
      //         if ((seatSelectedLeft2Status[0] == 'available' && seatSelectedLeft2Status[1] == 'selected') || (seatSelectedLeft2Status[0] == 'selected' && seatSelectedLeft2Status[1] == 'available')) {
      //             alert('No.6:选择的座位无效');
      //         }
      //         break;
      //     case '2_1' :
      //         if (seatSelectedRight2Status[0] == 'available' || (seatSelectedLeft2Status[0] == 'available' && seatSelectedLeft2Status[1] == 'selected') || (seatSelectedLeft2Status[0] == 'selected' && seatSelectedLeft2Status[1] == 'available')) {
      //             alert('No.7:选择的座位无效');
      //         }
      //         break;
      //     case '2_2' :
      //         if ((seatSelectedLeft2Status[0] == 'available' && seatSelectedLeft2Status[1] == 'selected') || (seatSelectedLeft2Status[0] == 'selected' && seatSelectedLeft2Status[1] == 'available') || (seatSelectedRight2Status[0] == 'available' && seatSelectedRight2Status[1] == 'selected') || (seatSelectedRight2Status[0] == 'selected' && seatSelectedRight2Status[1] == 'available')) {
      //             alert('No.8:选择的座位无效');
      //         }
      //         break;
      //     default :
      //         alert('default:选择的座位有效');
      // }
    })

    if (isEffective) {
      console.log('-----------结果显示----------')
      console.log('座位选择有效')
    } else {
      console.log('-----------结果显示----------')
      console.log('座位选择无效')
    }
  })
})
```