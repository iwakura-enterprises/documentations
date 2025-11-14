<show-structure depth="2"/>

# AmiAmi API Reference

I have not found AmiAmi API documentation online, so I decided to make my own based on my findings.

<warning>
This may be incomplete and/or inaccurate. Use at your own risk!
</warning>

## Servers

- `https://api.amiami.com/api/v1.0` - The base API url for AmiAmi API
- `https://img.amiami.com` - The base URL for AmiAmi images; usually you can append the image path returned by the API
  to this URL to get the full image URL.

## Endpoints

By default, all responses from AmiAmi are compressed using gzip; make sure your HTTP client can handle gzip-compressed responses.

Furthermore, you should include header `x-user-key: amiami_dev` in your requests to authenticate with the API. Otherwise,
you will get either a cloudflare error page or a 400 Bad Request response with ErrorMessage `Invalid access.` Other than
that, you don't have to include any other headers. Cloudflare should let you pass.

### GET `/items`

Searches thru AmiAmi's item database. Prices are in JPY!

#### Parameters {id="parameters__items"}

| Name                            | In    | Description                                                       | Required | Example      |
|---------------------------------|-------|-------------------------------------------------------------------|----------|--------------|
| `pagemax`                       | query | Number of items per page (min 0, max 50)                          | Yes      | `50`         |
| `lang`                          | query | Language                                                          | No       | `eng`, `cn`  |
| `s_keywords`                    | query | Search keywords. Not required for getting all items.              | No       | `cirno fumo` |
| `s_st_list_preorder_available`  | query | Filter by pre-order availability                                  | No       | `1`          |
| `s_st_list_backorder_available` | query | Filter by back-order availability                                 | No       | `1`          |
| `s_st_list_newitem_available`   | query | Filter by new availability                                        | No       | `1`          |
| `s_st_condition_flg`            | query | Filter by pre-owned availability                                  | No       | `1`          |
| `s_st_list_store_bonus`         | query | Filter by w/AmiAmi Bonus                                          | No       | `1`          |
| `s_st_saleitem`                 | query | Filter by sale items                                              | No       | `1`          |
| `s_cate1`                       | query | Filter by category ID (1st level)                                 | No       | `398`        |
| `s_cate2`                       | query | Filter by category ID (2nd level)                                 | No       | `398`        |
| `s_cate3`                       | query | Filter by category ID (3rd level)                                 | No       | `918`        |
| `s_cate4`                       | query | Filter by category ID (4th level)                                 | No       | `9688`       |
| `s_cate_tag`                    | query | Filter by category tag ID                                         | No       | `10`         |
| `s_charaname_search_id`         | query | Filter by character name ID                                       | No       | `4613`       |
| `s_maker_id`                    | query | Filter by brand ID (e.g. SQUARE ENIX)                             | No       | `97`         |
| `s_seriestitle_id`              | query | Filter by series title ID (e.g. Touhou Plush Series)              | No       | `9619`       |
| `s_originaltitle_id`            | query | Filter by original title ID (e.g. Touhou Project)                 | No       | `211`        |
| `s_sortkey`                     | query | Sorts by specified key (see [table below](#sort-keys) for values) | No       | `regtimed`   |

You may combine multiple filters to narrow down your search results.

##### Sort keys

| Sort Key       | Description                               |
|----------------|-------------------------------------------|
| `regtimed`     | Recently Updated Items                    |
| `recommend`    | Shop Recommendation (whatever that means) |
| `releasedated` | Latest Release Date                       |

<tabs>
<tab title="200 OK">

Successful response for item search.

```json
{
   "RSuccess":true,
   "RValue":null,
   "RMessage":"OK",
   "search_result":{
      "total_results":43
   },
   "items":[
      {
         "gcode":"GOODS-04700694",
         "gname":"[Bonus] Touhou Plush Series 103 Clownpiece FumoFumo Piece.",
         "thumb_url":"/images/product/main/254/GOODS-04700694.jpg",
         "thumb_alt":"GOODS-04700694.jpg",
         "thumb_title":"[Bonus] Touhou Plush Series 103 Clownpiece FumoFumo Piece.",
         "min_price":6600,
         "max_price":6600,
         "c_price_taxed":6600,
         "maker_name":"Gift",
         "saleitem":0,
         "condition_flg":0,
         "list_preorder_available":1,
         "list_backorder_available":0,
         "list_store_bonus":0,
         "list_amiami_limited":0,
         "instock_flg":0,
         "order_closed_flg":0,
         "element_id":null,
         "salestatus":null,
         "salestatus_detail":null,
         "releasedate":"2026-06-30 00:00:00",
         "jancode":"4580731044420",
         "preorderitem":1,
         "saletopitem":0,
         "resale_flg":0,
         "preowned_sale_flg":0,
         "for_women_flg":0,
         "genre_moe":1,
         "cate6":null,
         "cate7":null,
         "buy_flg":0,
         "buy_price":0,
         "buy_remarks":null,
         "stock_flg":1,
         "image_on":1,
         "image_category":"254/",
         "image_name":"GOODS-04700694",
         "metaalt":null
      }
   ],
   "_embedded":{
      "category_tags":[
         {
            "id":10,
            "name":"Character Goods",
            "count":42
         },
         {
            "id":12,
            "name":"Toys",
            "count":24
         },
         {
            "id":37,
            "name":"Plush Dolls",
            "count":24
         },
         {
            "id":42,
            "name":"Fashion",
            "count":1
         },
         {
            "id":43,
            "name":"Men\u0027s Fashion",
            "count":1
         },
         {
            "id":44,
            "name":"Ladies\u0027 Fashion",
            "count":1
         }
      ],
      "makers":null,
      "series_titles":null,
      "original_titles":null,
      "character_names":null,
      "elements":null
   }
}
```

</tab>

<tab title="400 Bad Request">

Bad request - missing required parameters or invalid values.

```json
{
    "RSuccess": false,
    "RValue": {
        "Error": [
            {
                "RequiredParameter": "pagemax",
                "ErrorMessage": "Please enter pagemax in [0] or [50] below.",
                "ErrorCode": "10022",
                "Fields": [
                    "pagemax"
                ]
            }
        ]
    },
    "RMessage": "Invalid Request\r\nPlease enter pagemax in [0] or [50] below.\r\n"
}
```

</tab>
</tabs>

### GET `/item`

Get detailed information about a specific item using its gcode. Prices are in JPY!

#### Parameters

| Name    | In    | Description                                   | Required | Example    |
|---------|-------|-----------------------------------------------|----------|------------|
| `gcode` | query | The gcode of the item to retrieve details for | Yes      | `CGD-9540` |
| `lang`  | query | Language                                      | No       | `eng`      |

<tabs>
<tab title="200 OK">

Successful response for item details.

```json
{
   "RSuccess":true,
   "RValue":null,
   "RMessage":"OK",
   "item":{
      "gcode":"GOODS-04700693",
      "scode":"GOODS-04700693",
      "gname":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma.",
      "sname":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma.(Pre-order)",
      "gname_sub":"",
      "sname_simple":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma.",
      "sname_simple_j":"【特典】東方ぬいぐるみシリーズ 102 小悪魔 ふもふもこあくま。",
      "main_image_url":"/images/product/main/254/GOODS-04700693.jpg",
      "main_image_alt":"GOODS-04700693.jpg",
      "main_image_title":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma. [Gift]",
      "image_comment":"*Actual product may differ from photos. \u003Cbr /\u003E",
      "youtube":null,
      "list_price":6000,
      "c_price_taxed":6600,
      "price":6600,
      "point":66,
      "salestatus":"Pre-order",
      "releasedate":"Jun-2026",
      "period_from":null,
      "period_to":"2025-11-25T14:59:00.000Z",
      "cart_type":8,
      "max_cartin_count":3,
      "include_instock_only_flg":0,
      "remarks":"*This is the same product that was sold at 12th Shuuki Reitaisai Plush Maker Gift Booth.\n*This product cannot be shipped to the following area: Japan",
      "size_info":null,
      "watch_list_available":1,
      "jancode":"4580731044970",
      "maker_name":"Gift",
      "modeler":"",
      "modelergroup":"",
      "spec":"Size: Approx. H20cm\n\n[Set Contents]\nTouhou Plush Series 102 Koakuma FumoFumo Koakuma.\n[Gift Limited Purchase Bonus]\nTin badge Koakuma",
      "memo":"",
      "copyright":"",
      "saleitem":0,
      "condition_flg":0,
      "preorderitem":1,
      "backorderitem":0,
      "store_bonus":0,
      "amiami_limited":0,
      "instock_flg":1,
      "order_closed_flg":0,
      "preown_attention":0,
      "producttypeattention":0,
      "agelimit":0,
      "customs_warning_flg":0,
      "preorderattention":"",
      "preorder_bonus_flg":0,
      "domesticitem":0,
      "metadescription":"",
      "metawords":"",
      "releasechange_text":"",
      "cate1":[
         168
      ],
      "cate2":[
         1299
      ],
      "cate3":[
         10118
      ],
      "cate4":null,
      "cate5":null,
      "cate6":null,
      "cate7":null,
      "salestalk":"",
      "buy_flg":0,
      "buy_price":0,
      "buy_remarks":null,
      "end_flg":0,
      "disp_flg":0,
      "onsale_flg":1,
      "handling_store":null,
      "salestatus_detail":"",
      "stock":1,
      "newitem":0,
      "saletopitem":0,
      "resale_flg":0,
      "preowned_sale_flg":0,
      "big_title_flg":0,
      "soldout_flg":1,
      "inc_txt1":0,
      "inc_txt2":34,
      "inc_txt3":18,
      "inc_txt4":34,
      "inc_txt5":0,
      "inc_txt6":0,
      "inc_txt7":0,
      "inc_txt8":0,
      "inc_txt9":0,
      "inc_txt10":0,
      "image_on":1,
      "image_category":"254/",
      "image_name":"GOODS-04700693",
      "metaalt":"",
      "image_reviewnumber":1,
      "image_reviewcategory":"254/",
      "price1":6600,
      "price2":0,
      "price3":0,
      "price4":0,
      "price5":6600,
      "discountrate1":0,
      "discountrate2":0,
      "discountrate3":0,
      "discountrate4":0,
      "discountrate5":0,
      "sizew":"\u003Cdiv class=\u0022icon_preorder\u0022\u003E\u003C/div\u003E",
      "colorw":"\u003Cfont style=\u0022color:red;font-weight:bold;\u0022\u003E6,600JPY\u003C/font\u003E",
      "thumb_url":"/images/product/thumb300/254/GOODS-04700693.jpg",
      "thumb_alt":"GOODS-04700693.jpg",
      "thumb_title":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma.",
      "thumb_agelimit":0
   },
   "_embedded":{
      "review_images":[
         {
            "image_url":"/images/product/review/254/GOODS-04700693_01.jpg",
            "thumb_url":"/images/product/rthumb/254/GOODS-04700693_01.jpg",
            "alt":"GOODS-04700693_01.jpg",
            "title":"[Bonus] Touhou Plush Series 102 Koakuma FumoFumo Koakuma.[Gift]"
         }
      ],
      "bonus_images":[
         
      ],
      "related_items":[
         {
            "gcode":"GOODS-04700694",
            "gname":"[Bonus] Touhou Plush Series 103 Clownpiece FumoFumo Piece.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700694.jpg",
            "thumb_alt":"GOODS-04700694.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 103 Clownpiece FumoFumo Piece.",
            "thumb_agelimit":0
         },
         {
            "gcode":"GOODS-04700695",
            "gname":"[Bonus] Touhou Plush Series 104 Tsukasa Kudamaki FumoFumo Tsukasa.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700695.jpg",
            "thumb_alt":"GOODS-04700695.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 104 Tsukasa Kudamaki FumoFumo Tsukasa.",
            "thumb_agelimit":0
         },
         {
            "gcode":"GOODS-04700696",
            "gname":"[Bonus] Touhou Plush Series 105 Megumu Iizunamaru FumoFumo Megumu.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700696.jpg",
            "thumb_alt":"GOODS-04700696.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 105 Megumu Iizunamaru FumoFumo Megumu.",
            "thumb_agelimit":0
         },
         {
            "gcode":"GOODS-04700690",
            "gname":"[Bonus] Touhou Plush Series 98 Daiyousei FumoFumo Daiyousei.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700690.jpg",
            "thumb_alt":"GOODS-04700690.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 98 Daiyousei FumoFumo Daiyousei.",
            "thumb_agelimit":0
         },
         {
            "gcode":"GOODS-04700691",
            "gname":"[Bonus] Touhou Plush Series 100 Reimu Hakurei (Green ver.) FumoFumo Reimu. Phantasmagoria of Dim.Dream. Green Version.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700691.jpg",
            "thumb_alt":"GOODS-04700691.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 100 Reimu Hakurei (Green ver.) FumoFumo Reimu. Phantasmagoria of Dim.Dream. Green Version.",
            "thumb_agelimit":0
         },
         {
            "gcode":"GOODS-04700692",
            "gname":"[Bonus] Touhou Plush Series 101 Marisa Kirisame (White ver.) FumoFumo Marisa. Phantasmagoria of Dim.Dream. White Version.",
            "thumb_url":"/images/product/thumb300/254/GOODS-04700692.jpg",
            "thumb_alt":"GOODS-04700692.jpg",
            "thumb_title":"[Bonus] Touhou Plush Series 101 Marisa Kirisame (White ver.) FumoFumo Marisa. Phantasmagoria of Dim.Dream. White Version.",
            "thumb_agelimit":0
         }
      ],
      "other_items":[
         
      ],
      "makers":[
         {
            "id":97,
            "name":"Gift"
         }
      ],
      "series_titles":[
         {
            "id":9619,
            "name":"FumoFumo Series"
         },
         {
            "id":403,
            "name":"Touhou Plush Series"
         }
      ],
      "original_titles":[
         {
            "id":211,
            "name":"Touhou Project"
         }
      ],
      "character_names":null
   }
}
```

</tab>

<tab title="400 Bad Request">

Bad request - unknown/invalid gcode/scode .

```json
{
   "RSuccess":false,
   "RValue":{
      "Error":[
         {
            "RequiredParameter":"",
            "ErrorMessage":"21",
            "ErrorCode":"error_code_21",
            "Fields":[]
         }
      ]
   },
   "RMessage":"Invalid Request\r\n21\r\n"
}
```

</tab>
</tabs>

## Miscellaneous

I think `ransu` query parameter is for authentication.

Currently dunno the difference between `scode` and `gcode`. Both are labeled as "Shop Code".

## Categories?

You may get all categories by making a GET request to `https://www.amiami.com/js/cate_tag_t.json`.
They'll probably not change often. But it seems this is not these are not the exact categories used in `s_cate3`.

```json
[
{  "ersid" : "1",  "cate_tagname_e" : "Figures Categories",  "cate_tagname_c" : "手办"  },
{  "ersid" : "14", "cate_tagname_e" : "Bishoujo Figures",  "cate_tagname_c" : "美少女手办"  },
{  "ersid" : "15", "cate_tagname_e" : "Character Figures",  "cate_tagname_c" : "人物手办"  },
{  "ersid" : "16", "cate_tagname_e" : "Foreign Figures",  "cate_tagname_c" : "海外角色手办（兵人）"  },
{  "ersid" : "2",  "cate_tagname_e" : "Dolls",  "cate_tagname_c" : "娃娃"  },
{  "ersid" : "4",  "cate_tagname_e" : "Gundam Toys",  "cate_tagname_c" : "高达模型"  },
{  "ersid" : "5",  "cate_tagname_e" : "Robots",  "cate_tagname_c" : "机器人"  },
{  "ersid" : "6",  "cate_tagname_e" : "Tokusatsu Toys",  "cate_tagname_c" : "特摄玩具"  },
{  "ersid" : "3",  "cate_tagname_e" : "Scale Models",  "cate_tagname_c" : "比例模型"  },
{  "ersid" : "17", "cate_tagname_e" : "Scale/Military",  "cate_tagname_c" : "军事比例模型"  },
{  "ersid" : "19", "cate_tagname_e" : "Car Models",  "cate_tagname_c" : "车模"  },
{  "ersid" : "21", "cate_tagname_e" : "Train Models",  "cate_tagname_c" : "火车模型"  },
{  "ersid" : "18", "cate_tagname_e" : "Car Plastic Model Kits",  "cate_tagname_c" : "汽车塑料模型"  },
{  "ersid" : "36", "cate_tagname_e" : "Plastic Models",  "cate_tagname_c" : "塑料模型"  },
{  "ersid" : "22", "cate_tagname_e" : "Tools / Paints / Material",  "cate_tagname_c" : "工具 /涂料 / 材料"  },
{  "ersid" : "39", "cate_tagname_e" : "Tools",  "cate_tagname_c" : "工具"  },
{  "ersid" : "40", "cate_tagname_e" : "Paints",  "cate_tagname_c" : "涂料"  },
{  "ersid" : "41", "cate_tagname_e" : "Element",  "cate_tagname_c" : "材料"  },
{  "ersid" : "10", "cate_tagname_e" : "Character Goods",  "cate_tagname_c" : "角色周边"  },
{  "ersid" : "23", "cate_tagname_e" : "Trading Figures",  "cate_tagname_c" : "盒蛋/扭蛋"  },
{  "ersid" : "42", "cate_tagname_e" : "Fashion",  "cate_tagname_c" : "时尚"  },
{  "ersid" : "43", "cate_tagname_e" : "Men's Fashion",  "cate_tagname_c" : "男士时装"  },
{  "ersid" : "44", "cate_tagname_e" : "Ladies' Fashion",  "cate_tagname_c" : "女士时装"  },
{  "ersid" : "45", "cate_tagname_e" : "Cosplay",  "cate_tagname_c" : "角色扮演"  },
{  "ersid" : "8",  "cate_tagname_e" : "Books/Mangas",  "cate_tagname_c" : "书 杂志"  },
{  "ersid" : "9",  "cate_tagname_e" : "Video Games",  "cate_tagname_c" : "主机游戏"  },
{  "ersid" : "7",  "cate_tagname_e" : "Videos & Music",  "cate_tagname_c" : "动画光盘・CD"  },
{  "ersid" : "25", "cate_tagname_e" : "Blu-ray",  "cate_tagname_c" : "蓝光"  },
{  "ersid" : "26", "cate_tagname_e" : "DVDs",  "cate_tagname_c" : "DVD"  },
{  "ersid" : "27", "cate_tagname_e" : "CDs",  "cate_tagname_c" : "CD"  },
{  "ersid" : "11", "cate_tagname_e" : "Trading Cards",  "cate_tagname_c" : "卡片游戏"  },
{  "ersid" : "29", "cate_tagname_e" : "Character Trading Cards",  "cate_tagname_c" : "动漫角色卡片"  },
{  "ersid" : "30", "cate_tagname_e" : "Idols / Talents",  "cate_tagname_c" : "偶像 /艺人交换卡"  },
{  "ersid" : "31", "cate_tagname_e" : "Sports",  "cate_tagname_c" : "运动"  },
{  "ersid" : "28", "cate_tagname_e" : "Card Games",  "cate_tagname_c" : "卡牌游戏"  },
{  "ersid" : "32", "cate_tagname_e" : "Card Holders",  "cate_tagname_c" : "卡套"  },
{  "ersid" : "12", "cate_tagname_e" : "Toys",  "cate_tagname_c" : "玩具"  },
{  "ersid" : "34", "cate_tagname_e" : "Kid's Toys",  "cate_tagname_c" : "儿童玩具"  },
{  "ersid" : "48", "cate_tagname_e" : "Board Games",  "cate_tagname_c" : "棋盘游戏"  },
{  "ersid" : "35", "cate_tagname_e" : "Jigsaw Puzzles",  "cate_tagname_c" : "益智拼图"  },
{  "ersid" : "37", "cate_tagname_e" : "Plush Dolls",  "cate_tagname_c" : "毛绒娃娃"  },
{  "ersid" : "46", "cate_tagname_e" : "Household Goods",  "cate_tagname_c" : "家庭用品"  },
{  "ersid" : "38", "cate_tagname_e" : "Stationery",  "cate_tagname_c" : "文具"  },
{  "ersid" : "47", "cate_tagname_e" : "Sports / Outdoors",  "cate_tagname_c" : "运动的・户外用品"  },
{  "ersid" : "33", "cate_tagname_e" : "Calendars",  "cate_tagname_c" : "日历"  },
{  "ersid" : "13", "cate_tagname_e" : "Age Restricted Products",  "cate_tagname_c" : "成人商品"  }
]
```

### Extracted categories

Here are extracted categories from their static page.

```json
{
  "bishoujo": {
    "name": "Bishoujo Figures",
    "icon": "https://img.amiami.com/images/genre/icon/1001.png",
    "url": "/eng/c/bishoujo/",
    "s_cate3": {
      "9713": "Completed Figures",
      "1202": "Nendoroid",
      "1209": "figma",
      "9709": "Plastic Kit / Plastic Models",
      "1123": "Action Figures",
      "9711": "Garage Kits"
    }
  },
  "characterfigure": {
    "name": "Character Figures",
    "icon": "https://img.amiami.com/images/genre/icon/1002.png",
    "url": "/eng/c/characterfigure/",
    "s_cate3": {
      "10080": "Action Figures",
      "9726": "Completed Figures",
      "1223": "Nendoroid (General)",
      "9723": "figma",
      "9722": "Plastic Kit / Plastic Models",
      "1033": "Garage Kits",
      "9728": "Scenery Set/Accessories",
      "1212": "Others"
    }
  },
  "foreignfigure": {
    "name": "Foreign Figures",
    "icon": "https://img.amiami.com/images/genre/icon/1003.png",
    "url": "/eng/c/foreignfigure/",
    "s_cate3": {
      "9727": "Military Action Figures",
      "1094": "TV Series",
      "8510": "Video Game Characters",
      "1110": "Other Anime Titles",
      "1101": "Other American Comics",
      "1108": "Other Movies",
      "1102": "Musicians/Celebrities",
      "1084": "Alien/Predator",
      "1086": "Star Wars",
      "1090": "Star Trek",
      "1096": "X-MEN",
      "1097": "Spider-Man",
      "1099": "Batman",
      "9731": "Iron Man",
      "1366": "Other Genres"
    }
  },
  "dolls": {
    "name": "Dolls",
    "icon": "https://img.amiami.com/images/genre/icon/1004.png",
    "url": "/eng/c/dolls/",
    "s_cate3": {
      "9626": "Furniture",
      "41": "Azone",
      "1042": "Pullip",
      "1043": "Blythe",
      "1367": "momoko DOLL",
      "46": "CUTIES",
      "1461": "Display Stands",
      "1041": "Non-Character Bodies",
      "1044": "Other Character Bodies",
      "1329": "Foreign Manufacturers",
      "1312": "Other Character Dolls",
      "48": "Other Groove Companies",
      "9744": "Tokyo Doll"
    }
  },
  "gundam": {
    "name": "Gundam Toys",
    "icon": "https://img.amiami.com/images/genre/icon/2001.png",
    "url": "/eng/c/gundam/",
    "s_cate4": {
      "10100": "Action Figures",
      "938": "Plastic Model Kits",
      "942": "Other"
    }
  },
  "robots": {
    "name": "Robots",
    "icon": "https://img.amiami.com/images/genre/icon/2002.png",
    "url": "/eng/c/robots/",
    "s_cate4": {
      "9687": "Plastic Models (Main Body)",
      "9721": "Plastic Models (Option Parts)",
      "9688": "Complete Figures",
      "1005": "Garage Kits"
    }
  },
  "tokusatsu": {
    "name": "Tokusatsu Toys",
    "icon": "https://img.amiami.com/images/genre/icon/2003.png",
    "url": "/eng/c/tokusatsu/",
    "s_cate4": {
      "971": "Figures",
      "969": "Sentai Series",
      "976": "Kamen (Masked) Rider",
      "953": "Ultraman",
      "968": "Godzilla/Gamera, etc.",
      "9725": "Action Figures",
      "984": "Other"
    }
  },
  "scale": {
    "name": "Scale/Military",
    "icon": "https://img.amiami.com/images/genre/icon/3001.png",
    "url": "/eng/c/scale/",
    "s_cate3": {
      "8488": "Plastic Model Kits",
      "945": "Radio Control",
      "950": "Wooden Models",
      "8489": "Other Completed Models",
      "973": "Sci-Fi Movies"
    }
  },
  "carmodels": {
    "name": "Car Models",
    "icon": "https://img.amiami.com/images/genre/icon/3003.png",
    "url": "/eng/c/carmodels/",
    "s_cate3": {
      "947": "Choro-Q",
      "9921": "TOMICA",
      "9923": "TOMICA Limited",
      "9932": "KYOSHO",
      "9935": "GOOD SMILE RACING",
      "9917": "Wit's",
      "9919": "EBBRO",
      "9925": "RAI'S",
      "9930": "Spark",
      "9931": "ixo",
      "9934": "WELLY",
      "9918": "GREENLIGHT",
      "9920": "AUTOart",
      "9924": "Interallied",
      "9933": "Make Up",
      "9936": "Slot Cars",
      "9927": "Motorcycle Models",
      "9928": "Others"
    }
  },
  "trainmodels": {
    "name": "Train Models",
    "icon": "https://img.amiami.com/images/genre/icon/3005.png",
    "url": "/eng/c/trainmodels/",
    "s_cate3": {
      "9806": "Other sizes",
      "9792": "N Gauge",
      "9817": "HO/16.5mm",
      "9805": "Z Gauge",
      "9832": "Train Toys",
      "9808": "Structures",
      "9881": "Scenery",
      "9829": "Other Goods",
      "9841": "Books",
      "9815": "Tools",
      "9816": "Others"
    }
  },
  "tools": {
    "name": "Tools / Paints / Material",
    "icon": "https://img.amiami.com/images/genre/icon/3006.png",
    "url": "/eng/c/tools/",
    "s_cate3": {
      "9592": "Paints / Solvent",
      "9566": "Painting Tools",
      "9590": "Adhesives / Putty",
      "9588": "Polishing",
      "9576": "Craft Tools",
      "9585": "Material",
      "9571": "Decal",
      "10138": "Other"
    }
  },
  "carplasticmodel": {
    "name": "Car Plastic Model Kits",
    "icon": "https://img.amiami.com/images/genre/icon/3002.png",
    "url": "/eng/c/carplasticmodel/",
    "s_cate3": {}
  },
  "tradingfigure": {
    "name": "Trading Figures",
    "icon": "https://img.amiami.com/images/genre/icon/4001.png",
    "url": "/eng/c/tradingfigure/",
    "s_cate2": {
      "230": "Bishoujo",
      "397": "General",
      "398": "Tokusatsu (SFX) / Live Action",
      "405": "Robots",
      "403": "Family Characters",
      "399": "Miniatures",
      "611": "Talents / Sports",
      "401": "Vehicles, Military",
      "406": "Animals",
      "407": "Other"
    }
  },
  "charactergoods": {
    "name": "Character Goods",
    "icon": "https://img.amiami.com/images/genre/icon/4002.png",
    "url": "/eng/c/charactergoods/?tab=1",
    "s_cate2": {
      "413": "Bishoujo",
      "414": "Anime",
      "1462": "Robot Anime",
      "639": "Games",
      "418": "Tokusatsu (SFX)",
      "415": "Family Characters",
      "185": "Hugging Pillows / Cushions",
      "10037": "Prepaid Cards",
      "417": "Movies / Talents",
      "8516": "History / Sengoku Era",
      "416": "Other"
    }
  },
  "plush": {
    "name": "Plush Dolls",
    "icon": "https://img.amiami.com/images/genre/icon/8001.png",
    "url": "/eng/c/plush/",
    "s_cate2": {}
  },
  "fashion": {
    "name": "Fashion",
    "icon": "https://img.amiami.com/images/genre/icon/1004.png",
    "url": "/eng/c/fashion/",
    "s_cate2": {
      "9902": "Men's Fashion",
      "9883": "Ladies' Fashion",
      "508": "Cosplay Goods",
      "9890": "Goods",
      "9897": "Accessories"
    }
  },
  "books": {
    "name": "Books/Mangas",
    "icon": "https://img.amiami.com/images/genre/icon/5001.png",
    "url": "/eng/c/books/",
    "s_cate3": {
      "204": "Manga (single volume)",
      "9836": "Novels",
      "193": "Artbooks",
      "203": "Idols Photobooks",
      "158": "Anime Magazines / Mooks",
      "165": "Figure / Hobby Magazines",
      "223": "Other Magazines",
      "205": "Game Guidebooks",
      "160": "Card Games",
      "167": "TRPG",
      "166": "Other Books"
    }
  },
  "videogames": {
    "name": "Video Games",
    "icon": "https://img.amiami.com/images/genre/icon/5002.png",
    "url": "/eng/c/videogames/",
    "s_cate3": {
      "10054": "Nintendo Switch",
      "10146": "Nintendo Switch 2",
      "9772": "PlayStation 4",
      "10108": "PlayStation 5",
      "9730": "PS Vita",
      "9748": "Nintendo Wii U",
      "9676": "Nintendo 3DS",
      "1450": "PlayStation 3",
      "1412": "PSP",
      "9868": "Xbox One",
      "10120": "Xbox Series X|S",
      "1446": "Nintendo Wii",
      "1349": "Nintendo DS",
      "1445": "Xbox 360",
      "1404": "PlayStation 2",
      "10035": "Prepaid Cards",
      "159": "PC Games (General)",
      "164": "Other"
    }
  },
  "blu-ray": {
    "name": "Blu-ray Discs",
    "icon": "https://img.amiami.com/images/genre/icon/6002.png",
    "url": "/eng/c/blu-ray/",
    "s_cate3": {
      "9997": "Anime",
      "10006": "Tokusatsu (SFX)",
      "10008": "Japanese Films",
      "10009": "Foreign Films",
      "10005": "Female Idols",
      "10004": "Other"
    }
  },
  "media": {
    "name": "DVDs",
    "icon": "https://img.amiami.com/images/genre/icon/6001.png",
    "url": "/eng/c/media/",
    "s_cate3": {
      "1373": "Anime",
      "1392": "Tokusatsu (SFX)",
      "1397": "Japanese Films",
      "1374": "Foreign Films",
      "1403": "Female Idols",
      "1382": "Other"
    }
  },
  "cd": {
    "name": "CDs",
    "icon": "https://img.amiami.com/images/genre/icon/6003.png",
    "url": "/eng/c/cd/",
    "s_cate3": {
      "8472": "Anime / Manga",
      "8475": "Games",
      "8473": "Tokusatsu (SFX)",
      "8477": "Voice Actresses / Artists (Female)",
      "8511": "Voice Actors / Artists (Male)",
      "8476": "Other"
    }
  },
  "cardgames": {
    "name": "Card Games",
    "icon": "https://img.amiami.com/images/genre/icon/7001.png",
    "url": "/eng/c/cardgames/",
    "s_cate2": {
      "83": "Yu-Gi-Oh!OCG",
      "86": "Pokemon Card Game",
      "113": "Magic: The Gathering",
      "30": "Weiss Schwarz",
      "33": "Battle Spirits",
      "904": "Lycee",
      "54": "Duel Masters",
      "9733": "Cardfight!! Vanguard",
      "10105": "Future Card Buddyfight",
      "9672": "Precious Memories",
      "10013": "WIXOSS TCG",
      "10106": "Z/X -Zillions of enemy X-",
      "10107": "TCG Fire Emblem Cipher",
      "1325": "Monster Collection TCG",
      "9674": "Arcade Card Related",
      "1114": "Other",
      "10041": "Single Cards"
    }
  },
  "tradingcard": {
    "name": "Trading Cards",
    "icon": "https://img.amiami.com/images/genre/icon/7002.png",
    "url": "/eng/c/tradingcard/",
    "s_cate2": {
      "136": "Character Trading Cards",
      "131": "Sports",
      "121": "Idols / Talents",
      "215": "Supplies (Card Holders / Tools)"
    }
  },
  "cardsupply": {
    "name": "Card Supplies",
    "icon": "https://img.amiami.com/images/genre/icon/7003.png",
    "url": "/eng/c/cardsupply/",
    "s_cate3": {
      "10099": "Play Mat",
      "910": "Sleeves",
      "916": "Deck Cases",
      "909": "Storage Boxes",
      "216": "Binders",
      "907": "Others"
    }
  },
  "kidstoy": {
    "name": "Kid's Toys",
    "icon": "https://img.amiami.com/images/genre/icon/8001.png",
    "url": "/eng/c/kidstoy/",
    "s_cate3": {
      "10118": "Plush Dolls",
      "8495": "Boys Toys",
      "8507": "Varieties / Gadgets",
      "176": "Playing Cards / Board Games",
      "1467": "Magic Tricks",
      "1332": "Toy Guns",
      "9952": "Cap gun",
      "10104": "Science Kits",
      "8505": "Educational Toys",
      "1032": "Other"
    }
  },
  "householdgoods": {
    "name": "Household Goods",
    "icon": "https://img.amiami.com/images/genre/icon/8001.png",
    "url": "/eng/c/householdgoods/",
    "s_cate2": {
      "10042": "Calendars",
      "10119": "Stationery",
      "92": "Miscellaneous goods",
      "10020": "Kitchen tools",
      "1087": "Sports / Outdoors",
      "10036": "Food & Drink",
      "10101": "Bath supplies / Toiletry goods"
    }
  },
  "stationery": {
    "name": "Stationery",
    "icon": "https://img.amiami.com/images/genre/icon/5001.png",
    "url": "/eng/c/stationery/",
    "s_cate3": {}
  },
  "boardgames": {
    "name": "Board Games",
    "icon": "https://img.amiami.com/images/genre/icon/7001.png",
    "url": "/eng/c/boardgames/",
    "s_cate3": {}
  },
  "jigsaw": {
    "name": "Jigsaw Puzzles",
    "icon": "https://img.amiami.com/images/genre/icon/8002.png",
    "url": "/eng/c/jigsaw/",
    "s_cate3": {
      "232": "Anime / Characters",
      "235": "Talents",
      "243": "Pets & Animals",
      "1293": "Sceneries",
      "201": "Art",
      "248": "Frames",
      "1335": "Other"
    }
  },
  "calendar": {
    "name": "Calendars",
    "icon": "https://img.amiami.com/images/genre/icon/4003.png",
    "url": "/eng/c/calendar/",
    "s_cate3": {}
  },
  "toys": {
    "name": "Toys",
    "icon": "https://img.amiami.com/images/genre/icon/.png",
    "url": "",
    "s_cate2": {
      "979": "Arsenal Toys",
      "1301": "Robots / Tokusatsu (SFX)",
      "172": "Play Blocks",
      "8500": "Cooking Toys",
      "10142": "Children's doll",
      "10143": "Children's cosmetics",
      "10144": "Sports & Action Toys",
      "1299": "General Toys",
      "200": "Jigsaw Puzzles"
    }
  },
  "mature": {
    "name": "Age Restricted Products",
    "icon": "https://img.amiami.com/images/genre/icon/9000.png",
    "url": "/eng/c/mature/",
    "tabs": {
      "1": "Figures",
      "2": "Character Goods",
      "4": "DVDs & Blu-ray Discs",
      "3": "Video Games"
    }
  }
}
```

{collapsible="true" collapsed-title="All categories (json)"}

| Category                                   | Icon URL                      | Category identifier | Sub categories                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|--------------------------------------------|-------------------------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bishoujo Figures (`bishoujo`)              | `/images/genre/icon/1001.png` | `s_cate3`           | `9713`: Completed Figures<br>`1202`: Nendoroid<br>`1209`: figma<br>`9709`: Plastic Kit / Plastic Models<br>`1123`: Action Figures<br>`9711`: Garage Kits                                                                                                                                                                                                                                                                                                                     |
| Character Figures (`characterfigure`)      | `/images/genre/icon/1002.png` | `s_cate3`           | `10080`: Action Figures<br>`9726`: Completed Figures<br>`1223`: Nendoroid (General)<br>`9723`: figma<br>`9722`: Plastic Kit / Plastic Models<br>`1033`: Garage Kits<br>`9728`: Scenery Set/Accessories<br>`1212`: Others                                                                                                                                                                                                                                                     |
| Foreign Figures (`foreignfigure`)          | `/images/genre/icon/1003.png` | `s_cate3`           | `9727`: Military Action Figures<br>`1094`: TV Series<br>`8510`: Video Game Characters<br>`1110`: Other Anime Titles<br>`1101`: Other American Comics<br>`1108`: Other Movies<br>`1102`: Musicians/Celebrities<br>`1084`: Alien/Predator<br>`1086`: Star Wars<br>`1090`: Star Trek<br>`1096`: X-MEN<br>`1097`: Spider-Man<br>`1099`: Batman<br>`9731`: Iron Man<br>`1366`: Other Genres                                                                                       |
| Dolls (`dolls`)                            | `/images/genre/icon/1004.png` | `s_cate3`           | `9626`: Furniture<br>`41`: Azone<br>`1042`: Pullip<br>`1043`: Blythe<br>`1367`: momoko DOLL<br>`46`: CUTIES<br>`1461`: Display Stands<br>`1041`: Non-Character Bodies<br>`1044`: Other Character Bodies<br>`1329`: Foreign Manufacturers<br>`1312`: Other Character Dolls<br>`48`: Other Groove Companies<br>`9744`: Tokyo Doll                                                                                                                                              |
| Gundam Toys (`gundam`)                     | `/images/genre/icon/2001.png` | `s_cate4`           | `10100`: Action Figures<br>`938`: Plastic Model Kits<br>`942`: Other                                                                                                                                                                                                                                                                                                                                                                                                         |
| Robots (`robots`)                          | `/images/genre/icon/2002.png` | `s_cate4`           | `9687`: Plastic Models (Main Body)<br>`9721`: Plastic Models (Option Parts)<br>`9688`: Complete Figures<br>`1005`: Garage Kits                                                                                                                                                                                                                                                                                                                                               |
| Tokusatsu Toys (`tokusatsu`)               | `/images/genre/icon/2003.png` | `s_cate4`           | `971`: Figures<br>`969`: Sentai Series<br>`976`: Kamen (Masked) Rider<br>`953`: Ultraman<br>`968`: Godzilla/Gamera, etc.<br>`9725`: Action Figures<br>`984`: Other                                                                                                                                                                                                                                                                                                           |
| Scale/Military (`scale`)                   | `/images/genre/icon/3001.png` | `s_cate3`           | `8488`: Plastic Model Kits<br>`945`: Radio Control<br>`950`: Wooden Models<br>`8489`: Other Completed Models<br>`973`: Sci-Fi Movies                                                                                                                                                                                                                                                                                                                                         |
| Car Models (`carmodels`)                   | `/images/genre/icon/3003.png` | `s_cate3`           | `947`: Choro-Q<br>`9921`: TOMICA<br>`9923`: TOMICA Limited<br>`9932`: KYOSHO<br>`9935`: GOOD SMILE RACING<br>`9917`: Wit's<br>`9919`: EBBRO<br>`9925`: RAI'S<br>`9930`: Spark<br>`9931`: ixo<br>`9934`: WELLY<br>`9918`: GREENLIGHT<br>`9920`: AUTOart<br>`9924`: Interallied<br>`9933`: Make Up<br>`9936`: Slot Cars<br>`9927`: Motorcycle Models<br>`9928`: Others                                                                                                         |
| Train Models (`trainmodels`)               | `/images/genre/icon/3005.png` | `s_cate3`           | `9806`: Other sizes<br>`9792`: N Gauge<br>`9817`: HO/16.5mm<br>`9805`: Z Gauge<br>`9832`: Train Toys<br>`9808`: Structures<br>`9881`: Scenery<br>`9829`: Other Goods<br>`9841`: Books<br>`9815`: Tools<br>`9816`: Others                                                                                                                                                                                                                                                     |
| Tools / Paints / Material (`tools`)        | `/images/genre/icon/3006.png` | `s_cate3`           | `9592`: Paints / Solvent<br>`9566`: Painting Tools<br>`9590`: Adhesives / Putty<br>`9588`: Polishing<br>`9576`: Craft Tools<br>`9585`: Material<br>`9571`: Decal<br>`10138`: Other                                                                                                                                                                                                                                                                                           |
| Car Plastic Model Kits (`carplasticmodel`) | `/images/genre/icon/3002.png` | N/A                 | None                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Trading Figures (`tradingfigure`)          | `/images/genre/icon/4001.png` | `s_cate2`           | `230`: Bishoujo<br>`397`: General<br>`398`: Tokusatsu (SFX) / Live Action<br>`405`: Robots<br>`403`: Family Characters<br>`399`: Miniatures<br>`611`: Talents / Sports<br>`401`: Vehicles, Military<br>`406`: Animals<br>`407`: Other                                                                                                                                                                                                                                        |
| Character Goods (`charactergoods`)         | `/images/genre/icon/4002.png` | `s_cate2`           | `413`: Bishoujo<br>`414`: Anime<br>`1462`: Robot Anime<br>`639`: Games<br>`418`: Tokusatsu (SFX)<br>`415`: Family Characters<br>`185`: Hugging Pillows / Cushions<br>`10037`: Prepaid Cards<br>`417`: Movies / Talents<br>`8516`: History / Sengoku Era<br>`416`: Other                                                                                                                                                                                                      |
| Plush Dolls (`plush`)                      | `/images/genre/icon/8001.png` | N/A                 | None                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Fashion (`fashion`)                        | `/images/genre/icon/1004.png` | `s_cate2`           | `9902`: Men's Fashion<br>`9883`: Ladies' Fashion<br>`508`: Cosplay Goods<br>`9890`: Goods<br>`9897`: Accessories                                                                                                                                                                                                                                                                                                                                                             |
| Books/Mangas (`books`)                     | `/images/genre/icon/5001.png` | `s_cate3`           | `204`: Manga (single volume)<br>`9836`: Novels<br>`193`: Artbooks<br>`203`: Idols Photobooks<br>`158`: Anime Magazines / Mooks<br>`165`: Figure / Hobby Magazines<br>`223`: Other Magazines<br>`205`: Game Guidebooks<br>`160`: Card Games<br>`167`: TRPG<br>`166`: Other Books                                                                                                                                                                                              |
| Video Games (`videogames`)                 | `/images/genre/icon/5002.png` | `s_cate3`           | `10054`: Nintendo Switch<br>`10146`: Nintendo Switch 2<br>`9772`: PlayStation 4<br>`10108`: PlayStation 5<br>`9730`: PS Vita<br>`9748`: Nintendo Wii U<br>`9676`: Nintendo 3DS<br>`1450`: PlayStation 3<br>`1412`: PSP<br>`9868`: Xbox One<br>`10120`: Xbox Series X\|S<br>`1446`: Nintendo Wii<br>`1349`: Nintendo DS<br>`1445`: Xbox 360<br>`1404`: PlayStation 2<br>`10035`: Prepaid Cards<br>`159`: PC Games (General)<br>`164`: Other                                   |
| Blu-ray Discs (`blu-ray`)                  | `/images/genre/icon/6002.png` | `s_cate3`           | `9997`: Anime<br>`10006`: Tokusatsu (SFX)<br>`10008`: Japanese Films<br>`10009`: Foreign Films<br>`10005`: Female Idols<br>`10004`: Other                                                                                                                                                                                                                                                                                                                                    |
| DVDs (`media`)                             | `/images/genre/icon/6001.png` | `s_cate3`           | `1373`: Anime<br>`1392`: Tokusatsu (SFX)<br>`1397`: Japanese Films<br>`1374`: Foreign Films<br>`1403`: Female Idols<br>`1382`: Other                                                                                                                                                                                                                                                                                                                                         |
| CDs (`cd`)                                 | `/images/genre/icon/6003.png` | `s_cate3`           | `8472`: Anime / Manga<br>`8475`: Games<br>`8473`: Tokusatsu (SFX)<br>`8477`: Voice Actresses / Artists (Female)<br>`8511`: Voice Actors / Artists (Male)<br>`8476`: Other                                                                                                                                                                                                                                                                                                    |
| Card Games (`cardgames`)                   | `/images/genre/icon/7001.png` | `s_cate2`           | `83`: Yu-Gi-Oh!OCG<br>`86`: Pokemon Card Game<br>`113`: Magic: The Gathering<br>`30`: Weiss Schwarz<br>`33`: Battle Spirits<br>`904`: Lycee<br>`54`: Duel Masters<br>`9733`: Cardfight!! Vanguard<br>`10105`: Future Card Buddyfight<br>`9672`: Precious Memories<br>`10013`: WIXOSS TCG<br>`10106`: Z/X -Zillions of enemy X-<br>`10107`: TCG Fire Emblem Cipher<br>`1325`: Monster Collection TCG<br>`9674`: Arcade Card Related<br>`1114`: Other<br>`10041`: Single Cards |
| Trading Cards (`tradingcard`)              | `/images/genre/icon/7002.png` | `s_cate2`           | `136`: Character Trading Cards<br>`131`: Sports<br>`121`: Idols / Talents<br>`215`: Supplies (Card Holders / Tools)                                                                                                                                                                                                                                                                                                                                                          |
| Card Supplies (`cardsupply`)               | `/images/genre/icon/7003.png` | `s_cate3`           | `10099`: Play Mat<br>`910`: Sleeves<br>`916`: Deck Cases<br>`909`: Storage Boxes<br>`216`: Binders<br>`907`: Others                                                                                                                                                                                                                                                                                                                                                          |
| Kid's Toys (`kidstoy`)                     | `/images/genre/icon/8001.png` | `s_cate3`           | `10118`: Plush Dolls<br>`8495`: Boys Toys<br>`8507`: Varieties / Gadgets<br>`176`: Playing Cards / Board Games<br>`1467`: Magic Tricks<br>`1332`: Toy Guns<br>`9952`: Cap gun<br>`10104`: Science Kits<br>`8505`: Educational Toys<br>`1032`: Other                                                                                                                                                                                                                          |
| Household Goods (`householdgoods`)         | `/images/genre/icon/8001.png` | `s_cate2`           | `10042`: Calendars<br>`10119`: Stationery<br>`92`: Miscellaneous goods<br>`10020`: Kitchen tools<br>`1087`: Sports / Outdoors<br>`10036`: Food & Drink<br>`10101`: Bath supplies / Toiletry goods                                                                                                                                                                                                                                                                            |
| Stationery (`stationery`)                  | `/images/genre/icon/5001.png` | N/A                 | None                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Board Games (`boardgames`)                 | `/images/genre/icon/7001.png` | N/A                 | None                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Jigsaw Puzzles (`jigsaw`)                  | `/images/genre/icon/8002.png` | `s_cate3`           | `232`: Anime / Characters<br>`235`: Talents<br>`243`: Pets & Animals<br>`1293`: Sceneries<br>`201`: Art<br>`248`: Frames<br>`1335`: Other                                                                                                                                                                                                                                                                                                                                    |
| Calendars (`calendar`)                     | `/images/genre/icon/4003.png` | N/A                 | None                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Toys (`toys`)                              | `/images/genre/icon/.png`     | `s_cate2`           | `979`: Arsenal Toys<br>`1301`: Robots / Tokusatsu (SFX)<br>`172`: Play Blocks<br>`8500`: Cooking Toys<br>`10142`: Children's doll<br>`10143`: Children's cosmetics<br>`10144`: Sports & Action Toys<br>`1299`: General Toys<br>`200`: Jigsaw Puzzles                                                                                                                                                                                                                         |
| Age Restricted Products (`mature`)         | `/images/genre/icon/9000.png` | `tabs`              | `1`: Figures<br>`2`: Character Goods<br>`4`: DVDs & Blu-ray Discs<br>`3`: Video Games                                                                                                                                                                                                                                                                                                                                                                                        |

## Character Names

I don't see any list of character names in their API. So I guess I will find some my favorite characters and list them
here later.