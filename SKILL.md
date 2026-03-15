---
name: amap-maps
description: 高德地图 MCP 插件，支持路径规划（驾车、步行、骑行、公交）、地理编码、逆地理编码、周边搜索、打车唤醒、导航唤醒及天气查询。
allowed-tools: exec
---

# 高德地图 (AMap) MCP Skill

通过 `mcporter` 调用高德官方 MCP 服务，提供全面的地图和位置服务。

## 核心功能

1.  **路径规划**：
    *   驾车：`mcporter call amap.maps_direction_driving origin="经度,纬度" destination="经度,纬度"`
    *   步行：`mcporter call amap.maps_direction_walking origin="经度,纬度" destination="经度,纬度"`
    *   骑行：`mcporter call amap.maps_direction_bicycling origin="经度,纬度" destination="经度,纬度"`
    *   公交：`mcporter call amap.maps_direction_transit_integrated origin="经度,纬度" destination="经度,纬度" city="起点城市" cityd="终点城市"`

2.  **位置解析**：
    *   正地理编码（地址转坐标）：`mcporter call amap.maps_geo address="详细地址" city="城市"`
    *   逆地理编码（坐标转地址）：`mcporter call amap.maps_regeocode location="经度,纬度"`

3.  **搜索与详情**：
    *   关键字搜索：`mcporter call amap.maps_text_search keywords="关键词" city="城市"`
    *   周边搜索：`mcporter call amap.maps_around_search keywords="关键词" location="经度,纬度" radius="半径"`
    *   POI 详情：`mcporter call amap.maps_search_detail id="POI_ID"`

4.  **快捷工具**：
    *   打车唤醒：`mcporter call amap.maps_schema_take_taxi dlon="目的地经度" dlat="目的地纬度" dname="目的地名称"`
    *   导航唤醒：`mcporter call amap.maps_schema_navi lon="经度" lat="纬度"`
    *   天气查询：`mcporter call amap.maps_weather city="城市名称/adcode"`

## 使用指令

*   **搜索地址并导航**：先通过 `maps_geo` 获取坐标，再通过 `maps_schema_navi` 生成导航链接。
*   **规划路线**：使用对应的 `direction` 工具，并将返回的路径描述口语化转达给用户。
*   **直接展示链接**：对于 `maps_schema_*` 工具返回的 URI 或其他网页链接，请遵循下方的 **输出规范**。

## 输出规范

当你需要向用户提供具体的链接（包括普通网页 https 链接、以及 androidamap:// 等应用唤醒 URI）时，**必须**将链接独立出来，组装成 XML 卡片消息输出，供客户端渲染卡片。

**【基本格式要求】：**
1. **禁止代码块**：不要使用 ````xml`包裹卡片，必须作为纯文本直接输出！
2. **前缀标识符**：在 `<appmsg` 的最开头加入 `//n`。
3. **URL规范（致命点）**：
    * `&` 必须转义为 `&amp;`。
    * `<url>` 中**绝不能包含未编码的中文字符**！如果链接包含中文（例如起终点城市名），必须进行 URL Encode（如 `%E5%B9%BF%E5%B7%9E`），否则会导致发送失败！

**标准 XML 卡片模板（直接复制结构，仅替换内容）：**
//n<appmsg appid="" sdkver="0">
<title>填写卡片标题（例如：高德路线导航）</title>
<des>填写卡片描述（例如：全程约1255公里）</des>
<username></username>
<action>view</action>
<type>5</type>
<showtype>0</showtype>
<content></content>
<url>填入URL(中文必须URL Encode，&符号必须转为&amp;)</url>
<lowurl></lowurl>
<dataurl></dataurl>
<lowdataurl></lowdataurl>
<contentattr>0</contentattr>
<streamvideo>0</streamvideo>
<thumburl>https://p4.itc.cn/mpbp/pro/20200828/e4c4b5c7759c49bc8a091c75af351e7f.png</thumburl>
<appattach>
<totallen>0</totallen>
<attachid></attachid>
<emoticonmd5></emoticonmd5>
<fileext></fileext>
<cdnthumburl>https://p4.itc.cn/mpbp/pro/20200828/e4c4b5c7759c49bc8a091c75af351e7f.png</cdnthumburl>
<cdnthumbmd5></cdnthumbmd5>
<cdnthumblength>0</cdnthumblength>
<cdnthumbwidth>0</cdnthumbwidth>
<cdnthumbheight>0</cdnthumbheight>
<cdnthumbaeskey></cdnthumbaeskey>
<aeskey></aeskey>
<encryver>1</encryver>
</appattach>
<extinfo></extinfo>
<sourceusername></sourceusername>
<sourcedisplayname></sourcedisplayname>
<md5></md5>
</appmsg>

## 示例

### 1. 常见调用示例
*   **查广州塔天气**：`mcporter call amap.maps_weather city="广州"`
*   **搜索周边的咖啡馆**：`mcporter call amap.maps_around_search keywords="咖啡馆" location="113.32459,23.10668" radius="1000"`

### 2. 标准输出示例（包含普通回复与单行XML卡片）
当用户向您请求导航、搜索详情时，您应先像人一样自然回复描述，在末尾换行一次，输出单行 XML 即可：

这为您生成了从广州到重庆的自驾路线链接，总里程约1255公里。跨越多个省份，您可以点击下方卡片查看详细信息：

//n<appmsg appid="" sdkver="0"><title>广州 ➔ 重庆 驾车导航路线</title><des>全程约1255公里，预计耗时13.5小时。</des><username></username><action>view</action><type>5</type><showtype>0</showtype><content></content><url>https://uri.amap.com/navigation?from=113.264499,23.130061,广州市&amp;to=106.551787,29.562680,重庆市&amp;mode=car&amp;policy=1&amp;src=openclaw</url><lowurl></lowurl><dataurl></dataurl><lowdataurl></lowdataurl><contentattr>0</contentattr><streamvideo>0</streamvideo><thumburl>https://p4.itc.cn/mpbp/pro/20200828/e4c4b5c7759c49bc8a091c75af351e7f.png</thumburl><appattach><totallen>0</totallen><attachid></attachid><emoticonmd5></emoticonmd5><fileext></fileext><cdnthumburl>https://p4.itc.cn/mpbp/pro/20200828/e4c4b5c7759c49bc8a091c75af351e7f.png</cdnthumburl><cdnthumbmd5></cdnthumbmd5><cdnthumblength>0</cdnthumblength><cdnthumbwidth>0</cdnthumbwidth><cdnthumbheight>0</cdnthumbheight><cdnthumbaeskey></cdnthumbaeskey><aeskey></aeskey><encryver>1</encryver></appattach><extinfo></extinfo><sourceusername></sourceusername><sourcedisplayname></sourcedisplayname><md5></md5></appmsg>
