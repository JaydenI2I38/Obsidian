


https://github.com/sakitam-fdd/wind-layer
使用windlayer中的@sakitam-gis/maplibre-wind绘制粒子流
@sakitam-gis/maplibre-wind@2.0.3（截至2025/07/22 最新版）未对maplibre@5 以上进行适配，主要问题是project相关

maplibre@5开始支持设置type: "globe"，3D显示


使用
maplibre-gl@4.7.1
@sakitam-gis/maplibre-wind@2.0.3
可以对瓦片数据源进行展示
project只能是EPSG:3857
且缩放时会有一小部分区域短时间内展示过多粒子


![[Pasted image 20250722170937.png]]

瓦片数据是将风的uv数据分别写入rg通道
然后通过

```typescript

function addWindLayer() {
	const tileSource = new maplibreWind.TileSource("wind", {
		tileSize: 256,
		minZoom: 0,
		maxZoom: 6,
		roundZoom: true,
		// 这里还不会给图像写入信息，使用不了maplibreWind.DecodeType.imageWithExif
		// 所以需要同时设置decodeType+dataRange
		decodeType: maplibreWind.DecodeType.image,
		dataRange: [[-20, 20], [-20, 20]],
		url: "/geoserver/gwc/service/wmts?layer=tutorial%3Auv&style=&tilematrixset=WebMercatorQuad&Service=WMTS&Request=GetTile&Version=1.0.0&Format=image%2Fpng&TileMatrix={z}&TileCol={x}&TileRow={y}",
	});

	const layer = new maplibreWind.Layer("wind", tileSource, {
		styleSpec: {
			"fill-color": ["interpolate", ["step", 1], ["get", "value"], 0, "#fff", 104, "#fff"],
			"opacity": 0.7,
			"numParticles": [
				"interpolate",
				["exponential", 0.5],
				["zoom"],
				0,
				65535 / 8,
				1,
				65535 / 16,
			], // 粒子数量
			"speedFactor": ["interpolate", ["exponential", 0.5], ["zoom"], 0, 1, 15, 0], // 粒子速度
			"fadeOpacity": 0.93,
			"dropRate": 0.003,
			"dropRateBump": 0.002,
		},
		renderFrom: maplibreWind.RenderFrom.rg, // rg通道存放着uv数据
		displayRange: [-15, 25],
		renderType: maplibreWind.RenderType.particles,
	});

	map?.addLayer(layer);
}

```

![[Pasted image 20250722171108.png]]


```

declare enum RenderFrom {
    /**
     * 标量值
     * 一般需要配合数据配置中的 dataRange 字段解析原始值；
     * 当然也可以从图像的 `Exif` 信息中提取
     */
    r = "r",
    /**
     * 矢量值，常见于气象要素的风速和风向等描述大小和方向的数据
     * 一般需要配合数据配置中的 dataRange 字段解析原始值；
     * 当然也可以从图像的 `Exif` 信息中提取
     */
    rg = "rg",
    /**
     * 一般用于浮点值（需要在 glsl 中做 rgba2float 转换）
     */
    rgba = "rgba",
    /**
     * 浮点值，一般存储在 r 通道或者使用 `LUMINANCE` 通道（精度最高）
     */
    float = "float"
}


```

- [ ] 研究下float这个


