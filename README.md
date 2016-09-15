# Website één.be scraping information
1. Fetch https://www.een.be/deze-week
2. Find all elements `div.js-video-teaser` and extract data fields:
   * image = `.card-teaser[data-image]`
   * video-id = `.card-teaser[data-video]`
   * video-type = `.card-teaser[data-type]`
   * date = `.card-teader[data-analytics]->date` (parse json)
   * name = `.card-teader[data-analytics]->program` (parse json)
   * title = `.card-teader[data-analytics]->epîsode` (parse json)
   * description = `div.card-teaser__text` or `div.image-teaser__text`

```html
<div class="card-teaser js-video-teaser"
     data-allowAutoplay="true"
     data-preroll="false"
     data-image="https://images.vrt.be/orig/2016/09/12/e00b1d9b-78b4-11e6-94b1-00163edf843f.jpg"
     data-analytics="{&quot;date&quot;:&quot;2016-09-12T00:00:00+0000&quot;,&quot;unix&quot;:1473638400,&quot;episode&quot;:&quot;Thuis: 22: 160912: volledige aflevering&quot;,&quot;program&quot;:&quot;Thuis&quot;,&quot;type_clip&quot;:1,&quot;whatson&quot;:&quot;333611808527&quot;}"
     data-video="mz-ast-ef070552-856d-465e-a750-b1633f4a04b2"
     data-type="mz">
<!-- ... -->
<div class="image-teaser__tag">Thuis</div>
<h3 class="image-teaser__title">Bekijk de aflevering van dinsdag 13 september 2016</h3>
<div class="image-teaser__text">
  <span class="image-teaser__time">eergisteren |</span>
  Nancy krijgt het hard te verduren. Jana zoekt Bob op. Simonne wil niet dat Waldek haar nog langer ontwijkt. Paulien doet haar best, maar het valt haar zwaar.
</div>
```

3. Find elements a.card-teaser or a.image-teaser and extract data fields from linked href page:
  * image = `meta[og:image]`
  * video-id = `.wieni-vrt-video__vamp[data-video]`
  * video-type = `.wieni-vrt-video__vamp[data-type]`
  * date = `.wieni-vrt-video__vamp[data-analytics]->date` (parse json)
  * name = `.wieni-vrt-video__vamp[data-analytics]->program` (parse json)
  * title = `.wieni-vrt-video__vamp[data-analytics]->episode` (parse json)
  * description = `div.paragraph__text`

```html
<meta property="og:image" content="https://images.vrt.be/canvas_2015_1200s630_j70/2016/09/12/5e2c89f7-78f0-11e6-94b1-00163edf843f.jpg" />
<!-- ... -->
<div class="wieni-vrt-video__vamp"
  data-allowAutoplay="false"
  data-type="mz"
  data-video="mz-ast-68fe073c-93c0-4101-b90d-6901ac8fcdfe"
  data-preroll="false"
  data-image=""
  data-analytics='{&quot;date&quot;:&quot;2016-09-13T00:00:00+0000&quot;,&quot;unix&quot;:1473724800,&quot;episode&quot;:&quot;Het goeie leven : 1: 160913: volledige aflevering&quot;,&quot;program&quot;:&quot;Het goeie leven &quot;,&quot;type_clip&quot;:1,&quot;whatson&quot;:&quot;331166214527&quot;}'>
</div>
<!-- ... -->
<section class="paragraph paragraph--text">
  <div class="paragraph__text"><p><strong>Na Wims televisie-oproep in de zomer van 2015 vormen zich een goeie 700 kweek- en kookduo’s.</strong></p>
    <p>De vijftig beste worden uitgenodigd naar Hotelschool Spermalie in Brugge. Op basis van hun oogstmand met ochtendverse eigen kweek én een koude bereiding kiest Wim samen met Jeroen Meus de top tien. In de volgende ronde op de weide in Drongen wacht hen de echte vuurproef, want het koken gebeurt zonder elektriciteit, met echt vuur. Back to basics.</p>
    <p>Aan het einde van de eerste kweek- en kookdag reiken Wim en Jeroen een riek uit aan de beste zes duo’s.</p>
    <p><em>Deze aflevering is beschikbaar tot dinsdag 20 september om 20.35 uur.</em></p>
  </div>
</section>
```

4. When requested to play we fetch url with stream and meta information based on video-id: https://mediazone.vrt.be/api/v1/een/assets/`video-id`.
```json
{
   "title": "Bekijk de aflevering van dinsdag 13 september 2016",
   "description": "Nancy krijgt het hard te verduren. Jana zoekt Bob op. Simonne wil niet dat Waldek haar nog langer ontwijkt. Paulien doet haar best, maar het valt haar zwaar.",
   "duration": 1504240,
   "aspectRatio": "16:9",
   "metaInfo": {
      "allowedRegion": "WORLD",
      "ageCategory": null,
      "whatsonId": "true"
   },
   "targetUrls": [
      {
         "type": "PROGRESSIVE_DOWNLOAD",
         "url": "http://download.stream.vrt.be/mediazone_een/2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video_1296.mp4"
      },
      {
         "type": "HLS",
         "url": "http://vod.stream.vrt.be/mediazone_een/_definst_/smil:2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video.smil/playlist.m3u8"
      },
      {
         "type": "HDS",
         "url": "http://vod.stream.vrt.be/mediazone_een/_definst_/smil:2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video.smil/manifest.f4m"
      },
      {
         "type": "RTMP",
         "url": "rtmp://vod.stream.vrt.be/mediazone_een/_definst_/mp4:2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video_1296.mp4"
      },
      {
         "type": "RTSP",
         "url": "rtsp://vod.stream.vrt.be/mediazone_een/_definst_/mp4:2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video_1296.mp4"
      }
   ],
   "subtitleUrls": [
      {
         "type": "CLOSED",
         "url": "http://vod.stream.vrt.be/mediazone_een/_definst_/2016/09/mz-ast-9a422a7a-fe16-430d-8908-9dd00f0ac982-2/video_sub_closed.vtt"
      }
   ],
   "posterImageUrl": "https://images.vrt.be/orig/2016/09/12/c7eca426-78ef-11e6-94b1-00163edf843f.jpg",
   "tags": []
}
```
