<!DOCTYPE HTML>
<html>
<head>
	<meta http-equiv="Content-Type"  content="text/html; charset=gbk">
	<title>输入提示后查询</title>
	<style type="text/css">
		body{
			margin:0;
			height:100%;
			width:100%;
			position:absolute;
			font-size:12px;
		}
		#mapContainer{
			position: absolute;
			top:0;
			left: 0;
			right:0;
			bottom:0;
		}
		
	</style>
</head>
<body>
    <div id="mapContainer" ></div>
   
	<script type="text/javascript" src="http://webapi.amap.com/maps?v=1.3&key=22a97cd08070dd8c3dd470ae99db88b7"></script>
	<script type="text/javascript">
	
		var windowsArr = [];
		var marker = [];
		var mapObj = new AMap.Map("mapContainer", {
			resizeEnable: true,
	        view: new AMap.View2D({
	        	resizeEnable: true,
	            center:new AMap.LngLat(113.950373,22.543984),//地图中心点
	            zoom:13//地图显示的缩放级别
	        }),
	        keyboardEnable:false
	    });
mapObj.plugin(["AMap.Scale"],function(){
    var scale = new AMap.Scale();
    mapObj.addControl(scale);  
});
		autoSearch();	
	
		function autoSearch() {
		   var keywords="深圳市";
			  var auto;
		    //加载输入提示插件
		        AMap.service(["AMap.Autocomplete"], function() {
		        var autoOptions = {
		            city: "" //城市，默认全国
		        };
		        auto = new AMap.Autocomplete(autoOptions);
		       auto.search("深圳市", function(status, result){
		            	autocomplete_CallBack(result);
		            });
		   });
	}
		 
		//输出输入提示结果的回调函数
		function autocomplete_CallBack(data) {
		    var resultStr = "";
		    var tipArr = data.tips;
		
			selectResult(0);
 resultStr += "<div id='divid1' onmouseover='openMarkerTipById(1,this)' onclick='selectResult(0)' onmouseout='onmouseout_MarkerStyle(1,this)' style=\"font-size: 13px;cursor:pointer;padding:5px 5px 5px 5px;\"" + "data=" + tipArr[0].adcode + ">" + tipArr[0].name + "<span style='color:#C1C1C1;'>"+ tipArr[0].district + "</span></div>";
		}

		//从输入提示框中选择关键字并查询
		function selectResult(index) {
			  var  text="深圳市盐田区海山街道";
			 mapObj.plugin(["AMap.PlaceSearch"], function() {       
		        var msearch = new AMap.PlaceSearch();  //构造地点查询类
		        AMap.event.addListener(msearch, "complete", placeSearch_CallBack); //查询成功时的回调函数
				//msearch.setCity(cityCode);
				 msearch.search(text);
				 });
        }
	
		//输出关键字查询结果的回调函数
		function placeSearch_CallBack(data) {
		    //清空地图上的InfoWindow和Marker
		    windowsArr = [];
		    marker     = [];
		  //  mapObj.clearMap();
		    var resultStr1 = "";
		    var poiArr = data.poiList.pois;
		    var resultCount = poiArr.length;
		    for (var i = 0; i < resultCount; i++) {
				if(i>1){
		        resultStr1 += "<div id='divid" + (i + 1) + "' onmouseover='openMarkerTipById1(" + i + ",this)' onmouseout='onmouseout_MarkerStyle(" + (i + 1) + ",this)' style=\"font-size: 12px;cursor:pointer;padding:0px 0 4px 2px; border-bottom:1px solid #C1FFC1;\"><table><tr><td><img src=\"http://webapi.amap.com/images/" + (i + 1) + ".png\"></td>" + "<td><h3><font color=\"#00a6ac\">名称: " + poiArr[i].name + "</font></h3>";
		            resultStr1 += TipContents(poiArr[i].type, poiArr[i].address, poiArr[i].tel) + "</td></tr></table></div>";
		            addmarker(i, poiArr[i]);
				break;
				}
		    }
		    mapObj.setFitView();
		}
		
		//添加查询结果的marker&infowindow   
		function addmarker(i, d) {
		    var lngX = d.location.getLng();
		    var latY = d.location.getLat();
		    var markerOption = {
		        map:mapObj,
		        icon:"http://webapi.amap.com/images/" + (i + 1) + ".png",
		        position:new AMap.LngLat(lngX, latY)
		    };
		    var mar = new AMap.Marker(markerOption);         
		    marker.push(new AMap.LngLat(lngX, latY));
		 
		    var infoWindow = new AMap.InfoWindow({
		        content:"<h3><font color=\"#00a6ac\">  " + (i + 1) + ". " + d.name + "</font></h3>" + TipContents(d.type, d.address, d.tel),
		        size:new AMap.Size(300, 0),
		        autoMove:true, 
		        offset:new AMap.Pixel(0,-30)
		    });
		    windowsArr.push(infoWindow);
		    var aa = function (e) {infoWindow.open(mapObj, mar.getPosition());};
		    AMap.event.addListener(mar, "mouseover", aa);
		}
		 
		//infowindow显示内容
		function TipContents(type, address, tel) {  //窗体内容
		    var str = "  地址：" + address||"暂无" + "<br />  电话：" + tel||"暂无" + " <br />  类型：" + type||"暂无";
		    return str;
		}
	
	</script>
</body>
</html>						

	
