---
layout: defaults/page
permalink: index.html
narrow: true
title: 系统整理
---

### 横剖面分析
<h1>一、决策分析算法</h1>

<ul>
    <li id="crossSection"><font style="color: red">横剖面分析</font></li>
    <img src="../theme/img/20.png" width="100%"/><br>
    <img src="../theme/img/21.png" width="100%"/><br>
    <li>操作逻辑：点击菜单栏功能之后，使用鼠标在地图上拖拽，会显示辅助线，放开鼠标，显示辅助线与管线交点的横剖面图</li>
    <li>ksdofoxhhg0237497 剖面分析菜单按下的事件处理代码</li>
    <li>fml,bkg98618237 鼠标左键按下的处理代码</li>
    <li>skncjfjsie86293 鼠标移动时的处理代码</li>
    <li>sjdlfjaoxxue 鼠标左键释放的处理代码</li><br>
    <li><h5>横剖面分析算法 dkjfkbxihfd98346</h5></li>
    <ul>
        <font style="color: green">
        <li>1.需要数据库对象（用于获取要素属性数据）、地图对象、临时图形对象</li>
        <font style="color: #84613d"><li>2.通过地图对象与临时图形求出与临时图形相交的要素（通过arcgis接口实现） ISpatialFilter空间数据查询对象</li>
            ISpatialFilter pSFilter = new SpatialFilterClass();<br>
            pSFilter.Geometry = pGeometry;<br>
            pSFilter.WhereClause = whereClause;<br>
            IFeatureCursor pFCursor = pLyr.FeatureClass.Search(pQuerFilter, false);<br>
            IFeature pfeature = pFCursor.NextFeature();<br>
        </font>
        <li>3.求出临时线段的起点与终点</li>
            <font style="color: #84613d"><li>4.求出临时线段与要素相交的点（使用ArcGisMap接口进行计算的）</li>
            IPolyline pLine = pFeature.Shape as IPolyline;<br>
            ITopologicalOperator pTopo = pPolyline as ITopologicalOperator;<br>
            IGeometry pGeo = pTopo.Intersect(pLine as IGeometry, esriGeometryDimension.esriGeometry0Dimension);<br>
            IPoint pPoint = (pGeo as IPointCollection).get_Point(0);<br>
            </font>
            <li>5.起点地面高程=管线起点管顶高程+起点埋深</li>
            <li>6.终点地面高程=管线终点管顶高程+终点埋深</li>
            如果埋设方式为直埋，则断面类型为圆管，否则为方管。<br>
            圆管直径=圆管垂直管径*0.001<br>
            方管管径去字段值的第一个数字值*0.001<br>
            <li>求管径的方法</li>
            if (LSGOGisComFun.IsDoubleNumeric(tGxAnaInfo.Ds) == true)<br>
            {<br>
            tGxAnaInfo.Ds1 = double.Parse(tGxAnaInfo.Ds);<br>
            sngDs = tGxAnaInfo.Ds1 * 0.001;<br>
            }<br>
            else<br>
            {<br>
            //dkhnd9762650shctri<br>
            tGxAnaInfo.Ds2 = double.Parse(GetSquPipeDia(tGxAnaInfo.Ds));<br>
            sngDs = tGxAnaInfo.Ds2 * 0.001;<br>
            }<br>
            <h5>排水: xkbkfhiughdf9871265h</h5>
            <font style="color: #9c27b0">
            <li>管线起点管底高程=起点管顶高程（起点高程）</li>
            <li>管线终点管底高程=终点管顶高程（终点高程）</li>
            <li>管线起点管顶高程=管线起点管底高程+直径</li>
            <li>管线终点管顶高程=管线终点管底高程+直径</li>
                tGxAnaInfo.BtmH1 = tGxAnaInfo.TopH1;<br>
                tGxAnaInfo.BtmH2 = tGxAnaInfo.BtmH1 + sngDs;<br>
                tGxAnaInfo.BtmH2 = tGxAnaInfo.TopH2;<br>
                tGxAnaInfo.TopH2 = tGxAnaInfo.BtmH2 + sngDs;<br>
                <br>
                //tGxAnaInfo.BtmH1 = tGxAnaInfo.TopH1;<br>
                //tGxAnaInfo.BtmH2 = tGxAnaInfo.TopH2;<br>
                //tGxAnaInfo.TopH1 = tGxAnaInfo.BtmH1 + sngDs;<br>
                //tGxAnaInfo.TopH2 = tGxAnaInfo.BtmH2 + sngDs;<br>
            </font>
            <h5>非排水：</h5>
            <li>7.管线起点管底高程=管线起点管顶高程-直径</li>
            <li>8.管线终点管底高程=管线终点管顶高程-直径</li><br>
            <li>9.管线起点中心高程=(管线起点管顶高程+管线起点管底高程)/2</li>
            <li>10.管线终点中心高程=(管线终点管顶高程+管线终点管底高程)/2</li>


            格式说明：<br>

            ***横断面分析：***<br>

            城市名称，分析类型<br>
            断面线起点坐标<br>
            断面线终点坐标<br>
            管线代码，管线颜色(RGB)，地面高程，中心高程，管径，离起点距离，所在道路<br>


            ***正断面分析：***<br>

            城市名称，分析类型<br>
            X，Y，管线代码，地面高程，中心高程，管径，所在道路<br>


            <li>11.起点到交点的距离</li>
            CrsToP1 = Math.Sqrt(Math.Pow(tGxAnaInfo.CrsX - dX1, 2) + Math.Pow(tGxAnaInfo.CrsY - dY1, 2));
            <li>12.两个交点之间的距离</li>
            P1ToP2 = Math.Sqrt(Math.Pow(dX1 - dX2, 2) + Math.Pow(dY1 - dY2, 2));
            <li>13.管线断点处中心高程 根据距离比插值计算得出</li>
            tGxAnaInfo.CrsCenH = (tGxAnaInfo.CenH2 - tGxAnaInfo.CenH1) * CrsToP1 / P1ToP2 + tGxAnaInfo.CenH1;
            <li>14.断点处管顶高程=断点中心点高程+半径</li>
            tGxAnaInfo.CrsTopH = tGxAnaInfo.CrsCenH + sngDs * 0.5;
            <li>15.断点处管底高程=断点中心点高程-半径</li>
            tGxAnaInfo.CrsBtmH = tGxAnaInfo.CrsCenH - sngDs * 0.5;
            <li>16.管线断点处地面高程 根据距离比插值计算得出</li>
            tGxAnaInfo.CrsSurfH = (tGxAnaInfo.SurfH2 - tGxAnaInfo.SurfH1) * CrsToP1 / P1ToP2 + tGxAnaInfo.SurfH1;
            <li>17.管线断点处中心埋深=管线断点处地面高程-管线断点处中心高程</li>
            tGxAnaInfo.CrsCenDeep = tGxAnaInfo.CrsSurfH - tGxAnaInfo.CrsCenH;
            <li>18.管线断点处管顶埋深=管线断点处地面高程-管线断点处管顶高程</li>
            tGxAnaInfo.CrsTopDeep = tGxAnaInfo.CrsSurfH - tGxAnaInfo.CrsTopH;
            <li>19.管线断点处管底埋深=管线断点处地面高程-管线断点处管底高程</li>
            tGxAnaInfo.CrsBtmDeep = tGxAnaInfo.CrsSurfH - tGxAnaInfo.CrsBtmH;
            <li>20.管线断点至拉线起点的距离</li>
            tGxAnaInfo.CrsToStart = Math.Sqrt(Math.Pow(tGxAnaInfo.CrsX - pStart.X, 2) + Math.Pow(tGxAnaInfo.CrsY - pStart.Y, 2));
            <li>21.写入Cross.txt文件，使用DMFX.exe进行横剖面分析</li>
            写入每个交点的内容：<br>
            管线类型代码（小类）、管线类型名称（小类）、要素颜色、管线断点处地面高程、管线断点处中心高程、管径（取自字段值）、管线断点至拉线起点的距离、道路名称<br>
        </font>
    </ul>



    <li id="longiSection"><font style="color: red">纵剖面分析</font></li>
    <li> <img src="../theme/img/22.png" width="100%"/><br>
    <li> <img src="../theme/img/23.png" width="100%"/><br>
    <li>鼠标点击按下事件 选择地图要素（要求是连续的管线） jfnhxxjuenh873424hxid</li>
    pEnv = _mapCtrl.TrackRectangle(); 等待式鼠标拖动画矩形框选<br>
    Program.UPGISFacade.LSGOMenuAnalysis.SelectPipeLine(pEnv, _mapCtrl); 根据框选结果选择管线<br>
    <li>鼠标点击右键释放事件 进行纵剖面分析 jhdnfhe09734hxcbve9023</li>
    <h5>纵剖面算法分析</h5>
    <ul>
        <li>1.通过统计所选管线点号单点数量来确定所选管线是否连续（如果是连续管线的话，有且只有两个单点） djgcyhfudfj86234681hchn</li>
        <li>2.统计相同点号重复的最大数量，最大数量不能大于2，因为无法处理三通以及多通管线</li>
        <font style="color: #9c27b0">
        <li>3.判断是否属于排水管道，分别进行计算处理(计算管线中心高程)</li>
        通过代码字符常量 /YS/WS/HS/ 判断是否是排水 sjdfhioxhgdksdjfh<br>
        if ("/YS/WS/HS/".IndexOf(arVertPipePnt[i].SubTypeCode) != -1)<br>
        </font>
        <font style="color: #9c27b0">
        <li>4.求管径</li>
        if (LSGOGisComFun.IsDoubleNumeric(arVertPipePnt[i].Ds))<br>
        sngDs = Convert.ToDouble(arVertPipePnt[i].Ds) * 0.001;<br>
        else<br>
        sngDs = Convert.ToDouble(GetSquPipeDia(arVertPipePnt[i].Ds)) * 0.001;<br>
        </font>
        <li>5.处理流程</li>
        依次获取管线节点，记录相关信息，形成数组<br>
        截面点数量=选择管段数量+1<br>

    </ul>


    <li id="fireEmergency"><font style="color: red">火灾抢险分析</font></li>
    <ul>
        <li>菜单栏点击事件 jbfbvuhf87237y4gd73</li>
        <li>鼠标左键点击事件 sldjfasbnxoheori</li>
        <li>弹出输入半径对话框</li>
        <li>代码查询范围为给水</li>
        <font style="color: #9c27b0">
        List&#060;string&#062; subLyer = AppPubVar.DicGXLayerControl["给水"];<br>
        条件设定：附属物='消防栓' or 附属物='消火栓' or 附属物='消防井<br>
        </font>
        分图层寻找范围内管点<br>
    </ul>
    <font style="color: blue">Label: sldjfasbnxoheori</font><br>
    1.找出所有满足条件的点（消防栓、搜索半径）<br>
    ISpatialFilter pSFilter = new SpatialFilterClass();<br>
    pSFilter.Geometry = Geometry;<br>
    pSFilter.SpatialRel = SpatialRelEnum;<br>
    string whereClause = "[附属物] = '消防栓'";<br>
    pSFilter.WhereClause = whereClause;<br>
    pSFilter.GeometryField = FeatureLayer.FeatureClass.ShapeFieldName;<br>
    pSFilter.SubFields = "*";<br>
    IFeatureCursor pFCursor = FeatureLayer.FeatureClass.Search(pSFilter, false);<br><br>
    2.求出每个点到事故点的最短距离<br>
    IProximityOperator pProximity = (IProximityOperator)pGeometry;<br>
    //Returns the minimum distance between two geometries.<br>
    double distance = pProximity.ReturnDistance(pGeometry2);<br><br>



    <li id="pipeCrack"><font style="color: red">爆管关阀分析</font></li>
    <li>菜单按钮点击事件 dkfhbxcvdf234794kqls,mx</li>
    <li>鼠标点击事件处理代码 xjdfkxnvojdf182368162399
    <li>查询条件</li>
    <li>鼠标点击位置</li>
    "附属物='阀门井' or 附属物='阀门'"<br>
    <font style="color: blue">Label: jjhwich89erhx;'x</font><br>
    1.找到事故点所在管段的相关信息<br>
    2.找到管段相关管线所在图层，找到相关管点所在图层<br>
    3.找到事故点所在管段的起始点号与终止点号<br>
    4.从事故点管段开始向两边遍历寻找最近的阀门和通往阀门的管段<br>
    注：仅仅在给水、热力、燃气图层上进行搜索阀门井、阀门,进行爆管分析<br><br>





    <li id="vertialDis"><font style="color: red">垂直净距分析</font></li>
    <font style="color: blue">Label: xnxbgdjfh812387rvxqyh MouseDown事件</font><br>
    private void MapCtrlMain_OnMouseDown(object sender, IMapControlEvents2_OnMouseDownEvent e)<br>
    pPolygon = _mapCtrl.TrackPolygon(); 阻塞式绘制临时图形，绘制完成再进行后续处理<br>
    Program.UPGISFacade.LSGOMenuAnalysis.PopAnalyseVerDis(_mapCtrl, pPolygon as IGeometry, ref axGrid, 1);<br>
    <font style="color: blue">Label: m,.9jsgtcz4</font><br>
    /// <param name="AnalysisType">分析类型,0为覆土深度分析,1为垂直净距分析</param><br>
    public void PopAnalyseVerDis(MapControl mapControl, IGeometry pPolyline, ref  Grid aGrid, short AnalysisType)<br>
    <font style="color: blue">Label: ddnxbcfi0986w2jdk</font><br>
    pFeaArr = PlineEntGet(mapControl, pPolyline);//获得框选到的所有管线<br>
    计算所选管线基本参数<br>
    arrGxAnaInfo = GetFeaturesVal(pFeaArr, false, pPolyline as IPolyline);<br>

    1.QDMS（起点埋深） Deep1（起点埋深）<br>
    2.ZDMS（终点埋深） Deep2（终点埋深）<br>
    3.QDGC（起点高程） TopH1（管线起点管顶高程）<br>
    4.ZDGC（终点高程） TopH2（管线终点管顶高程）<br>
    5.SurfH1（起点地面高程）=Deep1+TopH1<br>
    6.SurfH2（终点地面高程）=Deep2+TopH2<br>
    给水<br>
    7.BtmH1（管线起点管底高程）=TopH1-sngDs（直径)<br>
    8.BtmH2（管线终点管底高程）=TopH2-sngDs<br><br>
    设置垂直净距分析 uiddjfoier2394896gdf023<br>
    SetGridB(mapControl, pFeaArr, arrGxAnaInfo, pPolyline, ref aGrid)<br>
    遍历所选管线，依次求出管线与其他所有管线的所有交点<br>
    查询前置条件：不同小类管线（燃气类管线可以在本类型中进行分析）<br>
    <font style="color: #9c27b0">交点不能在第一条线的两个端点 kxnfejncm,wl2984<br></font>
    (pPntColl.get_Point(0).X != pLine1.FromPoint.X && pPntColl.get_Point(0).X != pLine1.ToPoint.X)<br>
    DataGrid表格初始化 一个交点一行<br>
    计算相关属性<br>
    GetCrossFeaVal(arrGxAnaInfoList[i], pLine1, pPntColl.get_Point(0), false, null); dhcjdjsdf82349hsdgf<br>
    <font style="color: green">
    净垂距=管线断点处管顶埋深-管线断点处管底埋深  小于规范值时，提示不规范<br>
    </font>


    <li id="horizonDis"><font style="color: red">水平净距分析</font></li>
    <ul>
        <li>dkfjnvifhiok 水平净距菜单栏点击事件</li>
        单例设置窗口（全局变量）<br>
        单例选择集对象（全局变量）<br>
        <li>fhfnxmfhihsql;;.9384 MouseDown事件</li>
        <li>nxcvbfhj39848sdfh383hgkx MouseMove事件</li>
        <li>dfnvbfh2347802hjsdhfei MouseUp事件</li>
        根据临时图元进行管线选择<br>
        根据所选图元进行水平净距分析,记录当前选择的管线，放到全局数组<br>
        第二次选择的管线要与第一次的一些属性一致，包括大类、小类、管径、埋设方式、管线压力<br>
        <font style="color: #9c27b0">
        <li>求两个几何对象的最小距离</li>
        double shortestDistance = (pPolyLine1 as IProximityOperator).ReturnDistance(pPolyLine2 as IGeometry);<br>
        </font>
        相交管线不能进行水平净距分析(最小距离为0)<br>
        判断所选管线是否满足进行水平净距分析的条件<br>
        int temp = ISGoodLines(pFeatureList); kdfkxnvk234987gq<br>
        <li>fjcnvhnohf293480hsdhrf 右键点击事件</li>
        弹出水平净距设置对话框<br>
        分两次选择<br>
        第二次选择之后点击确定按钮，会求两次选择的几何对象之间的最短距离<br>
        从数据库中获取标准值<br>
        HSTANDARD xnvljfgoj234078<br>
        计算值标准值时，水平净距不符合规范 dkhcn1203874<br>

    </ul>
    <font style="color: blue">Label: xkdhfahsoer34nbvb  水平净距分析对话框</font><br>





    <li><font style="color: red">空间三维</font></li>
    <font style="color: blue">Label: ksdhixufie934979669-khg 开始</font><br>
    <font style="color: blue">Label: sdhfihxiuyre1029965569 所选数据存入3ds文件中的代码</font><br>
    如果所选管线存在起始点坐标与终点坐标重合的话，三维分析界面功能不可用<br>
    /* X1,Y1,Z1,X2,Y2,Z2,Color,管径,管线代码,起点点号,终点点号,管径,材质,起点高程,终点高程,起点埋深,终点埋深,年代,权属单位,备注*/<br>


    <li id="DeepAnalyse"><font style="color: red">覆土深度分析</font></li>
    <ul>
        <li>nvjweoslwxkcvni123 菜单按钮的点击事件</li>
        <li>hbvqxrevxkkf8264sbh 鼠标左键点击事件</li>
        <li>mc,h.tgpe402yvzcw29 鼠标移动事件</li>
        <li>ncjgkr8723gxhd823hachd83 鼠标左键释放事件</li>
        获取框选管线数据库属性数据<br>
        获取计算相关数据<br>
        管点埋深即覆土深度<br>
    </ul>


    <li id="pieplePlant"><font style="color: red">管线规划设计</font></li>
    <ul>
        <li>hddiuncvbe893sl;wkxjhw892 菜单按钮的点击事件</li>
        <li>kjorincn9873461i2g398dfb94 新对话框的确定按钮事件</li>
        输入管线起始坐标以及埋深<br>
        获取输入值<br>
        获取相交的管线(通过arcMap接口)<br>
        获取相交管线属性值以及计算与规划管线的交点信息 jdhikjncbqi2894jzklfgm.bjk<br>
        arrGxAnaInfo = GetFeaturesVal(pFeaArr, true, pPolyline);
        生成表格，行为相交的管线以及交点信息，列为属性字段<br>
        覆土深度=管顶埋深<br>
        实际值与规范值值的是垂直净距<br>
        select 给水 from VSTANDARD where 上面管类 = '给水' and 1=1   获取规范值<br>
    </ul>

    <li id="roadExpand"><font style="color: red">道路扩建分析</font></li>
    <ul>
        <li>dsjfjxhfiehr9264 菜单栏点击事件</li>
        <li>jdjgieor7973491023jsdhf 鼠标点击事件</li>
        <li>djn9023bndvh23n 鼠标移动事件</li>
        <li>jdjcnbkf[g;'j823jhdh3 鼠标释放事件</li>
        仅限于 交通设施-面 这个图层<br>
    </ul>

    <li id="boundaryBreak"><font style="color: red">范围拆迁分析</font></li>
    <ul>
        <li>fjxkncjf294850sjkx.cvmaje 菜单栏点击事件</li>
        <li>jhchdeiueyhzn298371j1kude 鼠标点击确定按钮的事件</li>
        <li>jxhdjfnh3948hhffu1-2-=5904kc 鼠标释放事件</li>
        <li>njfjdfj3948jfjh0i3493jcey 进行图形剪裁</li>
        <li>jcjeyhcer983udu38 剪裁内容存放到临时数据库中</li>
        <li>jjhcdjeie837gcvbwj828 剪裁预览窗口</li>
    </ul>

    <li id="pipleLine3d"><font style="color: red">管线三维分析</font></li>
    <ul>
        <li>jdjicjeir862hdi8i2h2 菜单栏的点击事件</li>
        <li>nndkdjfh92731hxu934hfu234 鼠标左键点击事件</li>
        <li>kcjjfiooejni3948jhd89du3 空间三维分析算法</li>
        <li>sdhfihxiuyre1029965569 判断是否为排水</li>
        if ("/雨水/污水/雨污合流/".IndexOf(SubTypeName) == -1)<br>
        {<br>
        Z1 = H1 + sngR;<br>
        Z2 = H2 + sngR;<br>
        }<br>
        else<br>
        {<br>
        Z1 = H1 - sngR;<br>
        Z2 = H2 - sngR;<br>
        }<br>
        <li>写入三维分析程序参数文件</li>
        /* X1,Y1,Z1,X2,Y2,Z2,Color,管径,管线代码,起点点号,终点点号,管径,材质,起点高程,终点高程,起点埋深,终点埋深,年代,权属单位,备注*/<br>
        string path = LSGOGisSystemProperty.WorkDirectory+@"\3DAnalyse\VisualPipeLine.3ds";  c,.elwk849hsof93 写入管线数据<br>
    </ul>

    <li id="dx3d"><font style="color: red">地形三维分析</font></li>
    <ul>
        <li>djkrj390850hzofjjvgnpq 鼠标左键的释放事件</li>
        <li>kcjjfiooejni3948jhd89du3 分析算法一致</li>
    </ul>

    <li id="sptial3d"><font style="color: red">空间三维分析</font></li>
    <ul>
        <li>jcjjnjfkqp,vmpr9234u 鼠标左键的释放事件</li>
        <li>分析算法一致</li>
    </ul>


</ul>

    <h1>二、数据统计</h1>
    <ul>
        <li id="WholeDBPipelineLength"><font style="color: red">1.全库管线长度统计</font></li>
         <img src="../theme/img/1.png" width="100%"/><br>
        管线大类<br>
        选择管线子类：要进行统计的图层<br>
        分类属性:对每个图层进行分组统计的属性<br>
        现有计算：使用线表中的管段间距作为管线长度来统计（sql语句统计）<br>
        重新计算：使用x,y,高程来计算管线长度（sql语句统计）<br>
        一层循环：遍历图层->使用Sql语句进行分组统计查询<br>
        <br>
        jhnckemfjr178236gsydf723y 菜单栏点击事件<br>
        FrmStatGdNumOrGxLen 参数设置界面<br>
        传入数据： IType = 0（0代表管线 1代表管点） 和地图控件 _mapCtrl<br>
        调用方法A：public StatInfo StatByCondition(ILSGOMisDbControlStructure MisDBControlStructure, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, string CorrLayerName)<br>
        jfnvjr893472jd893uiihao 开始统计<br>
        每个图层单独统计，统计完一个显示一个 jncnbfkmadfnnfhcbqui1u774hc612hsx92<br>
        现有计算:select [材质] As F1,Sum([管段间距]) as F2 from [JSL] group by [材质]<br>
        获得表名称："Select Name From TableInfo Where [IsMetaDataSet]='True'"<br>
        重新计算：select A.材质 as F1,Sum(sqr((A.起点高程-A.终点高程)*(A.起点高程-A.终点高程)+(B.Y-C.Y)*(B.Y-C.Y)+(B.X-C.X)*(B.X-C.X))) AS F2 from (JSL as A inner join JSP as B on A.起始点号=B.物探点号) inner join JSP as C on A.终止点号=C.物探点号 Group BY A.材质<br>
        <font style="color: blue">
            注：1.管线长度使用的是x,y,点高程(没有区分是否为排水),计算长度的话只要高程位置类型一致即可，长度是一样的
        </font>

        <li id="WholeDBPipePointCount"><font style="color: red">2.全库管点数量统计</font></li>
         <img src="../theme/img/2.png" width="100%"/><br>
        管线大类<br>
        选择管线子类：要进行统计的图层<br>
        分类属性:对每个图层进行分组统计的属性<br>
        统计：对每个图层按属性分组统计管点数量(sql语句统计)<br>
        一层循环：遍历图层->使用Sql语句进行分组统计查询<br>
        <br>
        hcheie938hxaoxjeu28 菜单栏点击事件<br>
        jfnvjr893472jd893uiihao 开始统计（与全库管线统计用的同一个界面）<br>
        FrmStatGdNumOrGxLen 参数设置界面<br>
        IType = 1<br>
        调用方法A：public StatInfo StatByCondition(ILSGOMisDbControlStructure MisDBControlStructure, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, string CorrLayerName)<br>
        每个图层单独统计，统计完一个显示一个 jncnbfkmadfnnfhcbqui1u774hc612hsx92<br>
        xdkcnakl398djed93jx01k<br>
        Select 特征 As F1,Count(*) As F2 From JSP Group By 特征<br>


        <li id="WholeDBPipeLineSortStatic"><font style="color: red">3.全库管线分类统计</font></li>
         <img src="../theme/img/3.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类：需要统计的图层<br>
        分类属性：需要分类统计的属性<br>
        现有计算：使用管段间距字段来计算管段长度,使用arcmap接口来查找管段<br>
        重新计算：使用x,y,高程来计算长度,使用arcmap接口来查找管段<br>
        三层循环：遍历图层->遍历分类属性->遍历属性值->进行统计<br>
        <br>
        cjjdhj28337883yd82jdjhaie 菜单栏点击事件<br>
        hcjdnfjf9347hsd89h3 开始统计（与全库管线/管点统计用的不是同一个界面）<br>
        FrmStatByClass 参数设置界面<br>
        IType = 0<br>
        枚举出分类属性的所有值 xjjefj29311x0cvje03<br>
        通过图层名称获得表名称，再获得字段值<br>
        通过IQueryDef查找字段所有值枚举  DISTINCT(材质)<br>
        <font style="color: blue">当属性值为DBNull时，设置为未分类类别，这个类别是无法在数据库中查找到记录<br></font>
        调用方法B：public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>
        通过SpatialFilterClass.whereClouse进行管线查询<br>
        现有计算，对 管段间距 求和<br>
        重新计算,tmpLng = System.Math.Sqrt((H1(起点高程) - H2(终点高程)) * (H1 - H2) + pPolyline.Length * pPolyline.Length);  jjcjvhifuy9234976xgbcv0q3-ejco99u20M<br>
        <font style="color: blue">
            注:<br>
            在arcgis几何长度基础之上，增加了高程，进而求出管线的空间长度<br>
            使用的是线表点高程，但没有区分给水与排水<br>
        </font>


        <li id="WholeDBPipePointSortStatic"><font style="color: red">4.全库管点分类统计</font></li>
         <img src="../theme/img/4.png" width="100%"/><br>
        选择管线大类<br>
        选择管线子类：需要统计的图层<br>
        分类属性：需要分类统计的属性<br>
        使用arcmap接口来查找管点,进而统计管点数量<br>
        三层循环：遍历图层->遍历分类属性->遍历属性值->进行统计<br>
        <br>
        jhxhb98dvjkbhb3r09k1kcb3 菜单栏点击事件<br>
        统计界面与 全库管线分类统计 用的同一个界面<br>
        FrmStatByClass 参数设置界面<br>
        IType = 1<br>
        调用方法B：public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>
        对每个图层应用IQueryFilter进行统计查询(特征 = '未分类)<br>
        通过统计类型切换界面元素的显示与隐藏，管点分类统计没有现有计算<br>


        <li id="WholeDBPipeMaterialsSortStatic"><font style="color: red">5.按管线材质统计</font></li>
         <img src="../theme/img/5.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类:选择需要进行统计的图层<br>
        自动分类:有问题，应该设置为不可修改，固定的某个分类属性<br>
        输入或选择材质:选择要进行统计的分类属性值<br>
        现有计算:通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算：通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">两层循环:遍历图层->遍历所选属性值->进行统计(And 组合选中的属性值)   实际不应该如此，一层循环即可<br></font>

        <br>

         <img src="../theme/img/6.png" width="100%"/><br><br>
        nxdfj2389dyv298d93 菜单栏点击事件<br>
        FrmStatOneStep 参数设置对话框 IStatType = 1<br>
        jxnldfkcn23868xcg2387gaoqlznf 开始统计事件<br>
        调用方法B：public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>
        通过管线子类名称与"-管线"进行拼接，从而得到图层名称，通过图层名称获得相应的图层<br>
        使用WhereClause进行统计 "材质 = '钢' And ([材质]='钢')"<br>
        现有计算，对 管段间距 求和<br>
        重新计算,tmpLng = System.Math.Sqrt((H1(起点高程) - H2(终点高程)) * (H1 - H2) + pPolyline.Length * pPolyline.Length);  jjcjvhifuy9234976xgbcv0q3-ejco99u20M<br>
        点完查询，参数设置界面隐藏<br>

        <li id="WholeDBPipeDiameterSortStatic"><font style="color: red">6.按管线管径统计</font></li>
         <img src="../theme/img/16.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类:选择需要进行统计的图层<br>
        自动分类:有问题，应该设置为不可修改，固定的某个分类属性<br>
        输入或选择材质:选择要进行统计的分类属性值<br>
        现有计算:通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算：通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">两层循环:遍历图层->遍历所选属性值->进行统计(And 组合选中的属性值)   实际不应该如此，一层循环即可<br></font>
        <br>
         <img src="../theme/img/17.png" width="100%"/><br><br>
        jjixnfj2391hsdfh92384 菜单栏点击事件<br>
        FrmStatOneStep 参数设置对话框 IStatType = 2<br>
        同上，调用同一个参数设置界面，IStatType值不同仅仅对界面选项进行设置，算法一致<br>

        <li id="WholeDBPipeAgeSortStatic"><font style="color: red">7.按建设年代统计</font></li>
         <img src="../theme/img/18.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类:选择需要进行统计的图层<br>
        自动分类:有问题，应该设置为不可修改，固定的某个分类属性<br>
        输入或选择材质:选择要进行统计的分类属性值<br>
        现有计算:通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算：通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">两层循环:遍历图层->遍历所选属性值->进行统计(And 组合选中的属性值)   实际不应该如此，一层循环即可<br></font>
        <br>
         <img src="../theme/img/19.png" width="100%"/><br><br>
        jjxi39xc2330kqlx,fbn29 菜单栏点击事件<br>
        FrmStatOneStep 参数设置对话框 IStatType = 3<br>
        同上，调用同一个参数设置界面，IStatType值不同仅仅对界面选项进行设置，算法一致<br>

        <li id="WholeDBPipeWorkSortStatic"><font style="color: red">8.按权属单位统计</font></li>
         <img src="../theme/img/24.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类:选择需要进行统计的图层<br>
        自动分类:有问题，应该设置为不可修改，固定的某个分类属性<br>
        输入或选择材质:选择要进行统计的分类属性值<br>
        现有计算:通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算：通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">两层循环:遍历图层->遍历所选属性值->进行统计(And 组合选中的属性值)   实际不应该如此，一层循环即可<br></font>
        <br>
         <img src="../theme/img/25.png" width="100%"/><br><br>
        hjcjhdb38fh8j3hc912bf 菜单栏点击事件<br>
        FrmStatOneStep 参数设置对话框 IStatType = 4<br>
        同上，调用同一个参数设置界面，IStatType值不同仅仅对界面选项进行设置，算法一致<br>

        <li id="WholeDBPipeRoadrSortStatic"><font style="color: red">9.按所在道路统计</font></li>
         <img src="../theme/img/26.png" width="100%"/><br><br>
        选择管线大类<br>
        选择管线子类:选择需要进行统计的图层<br>
        自动分类:有问题，应该设置为不可修改，固定的某个分类属性<br>
        输入或选择材质:选择要进行统计的分类属性值<br>
        现有计算:通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算：通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">两层循环:遍历图层->遍历所选属性值->进行统计(And 组合选中的属性值)   实际不应该如此，一层循环即可<br></font>
        <br>
        <img src="../theme/img/27.png" width="100%"/><br><br>
        bxcbv2330912nv023jfa;ls92 菜单栏点击事件<br>
        FrmStatOneStep 参数设置对话框 IStatType = 5<br>
        同上，调用同一个参数设置界面，IStatType值不同仅仅对界面选项进行设置，算法一致<br>

        <li id="WholeDBSimpleConditionStatic"><font style="color: red">10.全库简单条件统计</font></li>
         <img src="../theme/img/7.png" width="100%"/><br><br>
        管线大类<br>
        管线子类:要进行统计的图层<br>
        统计类型:要进行统计的类型，管线还是管点<br>
        具有属性:进行分类统计的属性类别<br>
        属性值:进行统计的属性值<br>
        现有计算：通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算:通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        通过参数设置界面敲定需要统计的语句参数，哪个图层，哪个属性，属性中哪个值<br>
        <font style="color: blue">一层循环：对属性值的遍历，实际上应该去掉<br></font>
        <br>
         <img src="../theme/img/8.png" width="100%"/><br><br>
        hixidf893bc93dasf3 菜单栏的点击事件<br>
        FrmStatSimple 参数设置对话框<br>
        产生查询条件 "[材质]='钢'"<br>
        xidhcjh38910jsdhf8234h 进行统计<br>
        调用方法B:public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>

        <li id="WholeDBComplexConditionStatic"><font style="color: red">11.全库复合条件统计</font></li>
         <img src="../theme/img/9.png" width="100%"/><br><br>
        选择管线子类:要进行统计的图层<br>
        查询类型:要进行统计的类别，管线还是管点<br>
        基本条件:设置查询条件<br>
        查询表达式：管线查询的条件<br>
        自动分类:用作枚举属性所有值<br>
        现有计算：通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算:通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">一层循环：对属性值的循环，不正确，arcgis Map的SpatialFilterClass接口使用有问题并没有达到and的效果<br></font>
        <br>
         <img src="../theme/img/10.png" width="100%"/><br><br>
        jhcb29dscb109df330dha 菜单栏的点击事件<br>
        FrmStatComplex 参数设置对话框<br>
        jldkfkjnvid923047hc9823y 开始统计<br>
        生成WhereClause条件<br>
        调用方法B:statInfor = Program.UPGISFacade.LSGOMenuStat.StatByCondition(mapCtrl, lyrName, cmbFld.Text, richBExpresion.Text.Trim(), statType, iType, pGN);<br>

        <li id="WholeDBRegionPipleLineStatic"><font style="color: red">12.区域内管线长度统计</font></li>
         <img src="../theme/img/11.png" width="100%"/><br><br>
         <img src="../theme/img/12.png" width="100%"/><br><br>
        区域选择:划定区域，通过arcgis接口获得区域内管线<br>
        选择管线大类<br>
        选择管线子类：选择要进行统计的图层<br>
        分类属性:选择要进行统计查询的属性<br>
        现有计算：通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算:通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        两层循环：遍历图层->遍历属性值->统计管线长度<br>
        <br>
        hci82jnjc91kfg;'xckh 菜单栏的点击事件<br>
        FrmInputByCoordinate 区域选择对话框<br>
        hxubasfbeurbxh82349hc83 鼠标点击事件M<br>
        FrmStatGdNumOrGxLen 区域管线长度统计参数设置对话框<br>
        Program.UPGISFacade.FormRunList.FrmStatGdNumOrGxLen.IType = 2;<br>
        调用方法B：public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>
        通过 SpatialFilterClass 来查询要统计的区域以及查询条件，长度单独计算<br>


        <li id="WholeDBRegionPiplePointcountStatic"><font style="color: red">13.区域内管点数量统计</font></li>
         <img src="../theme/img/11.png" width="100%"/><br><br>
         <img src="../theme/img/13.png" width="100%"/><br><br>
        区域选择:划定区域，通过arcgis接口获得区域内管线<br>
        选择管线大类<br>
        选择管线子类：选择要进行统计的图层<br>
        分类属性:选择要进行统计查询的属性<br>
        两层循环：遍历图层->遍历属性值->统计管点数量<br>
        <br>
        hu892c921h0d=ac023 菜单栏的点击事件<br>
        FrmInputByCoordinate 区域选择对话框<br>
        hxubasfbeurbxh82349hc83 鼠标点击事件<br>
        FrmStatGdNumOrGxLen 区域管线长度统计参数设置对话框<br>
        Program.UPGISFacade.FormRunList.FrmStatGdNumOrGxLen.IType = 3;<br>
        调用方法B：public StatInfo StatByCondition(MapControl MapCtrl, string LayerName, string StatField, string WhereClause, lsgoStatType StatType, short CaculateType, IGeometry Geometry)<br>
        通过 SpatialFilterClass 来查询要统计的区域以及查询条件，长度单独计算<br>

        <li id="WholeDBRegionComplexConditionStatic"><font style="color: red">14.区域内复合条件统计</font></li>
         <img src="../theme/img/14.png" width="100%"/><br><br>
         <img src="../theme/img/15.png" width="100%"/><br><br>
        区域选择:划定区域，通过arcgis接口获得区域内管线<br>
        选择管线子类:要进行统计的图层<br>
        查询类型:要进行统计的类别，管线还是管点<br>
        基本条件:设置查询条件<br>
        查询表达式：管线查询的条件<br>
        自动分类:用作枚举属性所有值<br>
        现有计算：通过管段间距来计算长度,通过arcmap接口来查找条件管线<br>
        重新计算:通过x,y,高程来计算长度,通过arcmap接口来查找条件管线<br>
        <font style="color: blue">一层循环：对属性值的循环，不正确，arcgis Map的SpatialFilterClass接口使用有问题并没有达到and的效果<br></font>
        <br>

        hc9c823hpcj=rgp3JJ9023H 菜单栏的点击事件<br>
        FrmStatSelectControl 区域选择对话框<br>
        jjxjdi23ijd8203jcc-1=-1jf 鼠标点击事件<br>
        拖动绘制统计区域,然后弹出参数设置界面<br>
        FrmStatComplex 参数设置界面<br>
        传参<br>
        Program.UPGISFacade.FormRunList.FrmStatComplex.IType = 1;<br>
        Program.UPGISFacade.FormRunList.FrmStatComplex.MapCtrl = _mapCtrl;<br>
        Program.UPGISFacade.FormRunList.FrmStatComplex.PGN = pPolygon as IPolygon;<br>
        jldkfkjnvid923047hc9823y 进行统计<br>

    </ul>




<h1>三、其他相关算法</h1>
<ul>
    <li><font style="color: red">管线数据入库流程，用从点线表里取到的x,y值生成arcmap中的几何对象</font></li>
    <font style="color: blue">Label: a;ldkfjklasnd</font><br>
    pLine = new PolylineClass();<br>
    pSPt = new PointClass();<br>
    pEPt = new PointClass();<br>
    pSPt.PutCoords(Convert.ToDouble(dr["b." + LSGOGisPubVar.X]), Convert.ToDouble(dr["b." + LSGOGisPubVar.Y]));<br>
    pEPt.PutCoords(Convert.ToDouble(dr["c." + LSGOGisPubVar.X]), Convert.ToDouble(dr["c." + LSGOGisPubVar.Y]));<br>
    object obj = Type.Missing;<br>
    pLine.AddPoint(pSPt, ref obj, ref obj);<br>
    pLine.AddPoint(pEPt, ref obj, ref obj);<br><br>
    <li><font style="color: red">管线长度计算,全库管线长度统计</font></li>
    select A.材质 as F1,Sum(sqr((A.起点高程-A.终点高程)*(A.起点高程-A.终点高程)+(B.Y-C.Y)*(B.Y-C.Y)+(B.X-C.X)*(B.X-C.X))) AS F2 from (CSL as A inner join CSP as B on A.起始点号=B.物探点号)
    inner join CSP as C on A.终止点号=C.物探点号 Group BY A.材质<br>
    据猜测，z=点高程-埋深<br>
    验证map成图的几何数据
</ul>
