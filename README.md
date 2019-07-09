# PdfBook-imagePatch

**Based on the original MediaWiki PdfBook (by Aran Dunkley)**. The image scaling problem was fixed. 

The original extension can be found here: https://www.mediawiki.org/wiki/Extension:PdfBook

Though the PdfBook is far the best Pdf export tool available for Mediawiki, it has some annoing issues with image scaling. 

* Generally, all the images look larger than they are in the wiki page
* There are images that don't even fit. 
* There is a third problem, all the images have an approx. 40px indent, so they are not in line with the text. 

Here is my fix, that I've been using for a long time:


Put the following code between these lines: 
```PHP
113:     $text = preg_replace( "|(<img[^>]+?src=\"$imgpath)(/.+?>)|", "<img src=\"$wgUploadDirectory$2", $text );
                        ....fix goes here....
114: }
115: if( $nothumbs == 'true' ) $text = preg_replace( "|images/thumb/(\w+/\w+/[\w\.\-]+).*\"|", "images/$1\"", $text );
```


And here is the fix: 
```PHP
//If any image widther than this, the image will be resized to this size
$max_image_size=650;

//By default, every image in the PDF has a 40px indent. If this is true, the images will be 
//in line with the text
$remove_image_indent=true;

//Generally, the images in the PDF are larger than it was in the wiki page. If this flag is true, 
//all the images, that width weren't modified due to it was larger than 'max_image_size' will be resized 
//with the percentage defined in 'resize_percentage' variable. 
$resize_small_images=false;
$resize_percentage=80;


if ($remove_image_indent) {					
   $text    = preg_replace( "|<dl><dd>(<a .+><img.+\/><\/a>)<\/dd><\/dl>|", "$1", $text );
}

if ( preg_match_all("/<img[^>]+?width=\"([0-9]+)\"/", $text, $matches)) {

    for ($i = 0; $i < count($matches[1]); $i++) {
       
	$w=$matches[1][$i];

	if (preg_match("/<img[^>]+?width=\"$w\"\s+height=\"([0-9]+)\"/", $text, $matches2)) {
		$h=$matches2[1];

		if ($w > $max_image_size) {
			$w2=$max_image_size;
			$h2=round($h*($w2/$w));
			$text    = preg_replace( "|width=\"$w\"\s+height=\"$h\"|", "width=\"$w2\" height=\"$h2\"", $text );
		} else if ($resize_small_images) { 
			$w2=$w*($resize_percentage/100);
			$h2=round($h*($w2/$w));	
			$text    = preg_replace( "|width=\"$w\"\s+height=\"$h\"|", "width=\"$w2\" height=\"$h2\"", $text );
		}
	}
    }
}
```



# Push display adatok megjelenítése Schema

```

```

| Abstract            | Extensible | Status       | Identifiable | Custom Properties | Additional Properties | Defined In                                         |
| ------------------- | ---------- | ------------ | ------------ | ----------------- | --------------------- | -------------------------------------------------- |
| Can be instantiated | No         | Experimental | No           | Forbidden         | Permitted             | [pushDetails.schema.json](pushDetails.schema.json) |

# Push display adatok megjelenítése Properties

| Property                        | Type       | Required     | Nullable | Defined by                                      |
| ------------------------------- | ---------- | ------------ | -------- | ----------------------------------------------- |
| [attributes](#attributes)       | `object[]` | **Required** | No       | Push display adatok megjelenítése (this schema) |
| [creationDate](#creationdate)   | `string`   | **Required** | No       | Push display adatok megjelenítése (this schema) |
| [functionCode](#functioncode)   | `string`   | **Required** | No       | Push display adatok megjelenítése (this schema) |
| [templateType](#templatetype)   | `enum`     | **Required** | No       | Push display adatok megjelenítése (this schema) |
| [transactionId](#transactionid) | `string`   | **Required** | No       | Push display adatok megjelenítése (this schema) |
| `*`                             | any        | Additional   | Yes      | this schema _allows_ additional properties      |

## attributes

A templét belsejében megjelenő attribútumok listája

`attributes`

- is **required**
- type: `object[]`
- defined in this schema

### attributes Type

Array type: `object[]`

All items must be of the type: `object` with following properties:

| Property    | Type   | Required     |
| ----------- | ------ | ------------ |
| `labelCode` | string | Optional     |
| `params`    | object | **Required** |
| `type`      | string | **Required** |

#### labelCode

Annak a címkének a kódja, amit kiírunk a sor elejére az adott attribútum típus elé

`labelCode`

- is optional
- type: `string`

##### labelCode Type

`string`

#### params

Az attribútum paraméterei, melyek nevesített mezői a params univerzális objektumnak

`params`

- is **required**
- type: `object`

##### params Type

`object` with following properties:

| Property | Type | Required |
| -------- | ---- | -------- |


#### type

Az attribútum típusa. Ez határozza meg a megjelenítést

`type`

- is **required**
- type: `enum`

The value of this property **must** be equal to one of the [known values below](#attributes-known-values).

##### type Known Values

| Value                  | Description |
| ---------------------- | ----------- |
| `TEXT`                 |             |
| `MULTI_LINE_TEXT`      |             |
| `SORT_DATE`            |             |
| `DATE_WITH_WEEK_DAY`   |             |
| `DATE`                 |             |
| `SHORT_DATE_TIME`      |             |
| `PIECE`                |             |
| `SOURCE_ACCOUNT`       |             |
| `AMOUNT`               |             |
| `TARGET_GIRO_ACCOUNT`  |             |
| `TARGET_IBAN_ACCOUNT`  |             |
| `TARGET_PROXY_ACCOUNT` |             |
| `CARD`                 |             |
| `RECURRENCE`           |             |
| `PROVIDER`             |             |

## creationDate

A tranzakció létrehozásának az időpontja ISO 8601 formátumban.

`creationDate`

- is **required**
- type: `string`
- defined in this schema

### creationDate Type

`string`

## functionCode

A funkció kódja annak a folyamatnak amihez az MFA-t kaptuk. Ez fogja a címet meghatározni

`functionCode`

- is **required**
- type: `string`
- defined in this schema

### functionCode Type

`string`

## templateType

A templét típusa ami a képernyő struktúráját fogja meghatározni

`templateType`

- is **required**
- type: `enum`
- defined in this schema

The value of this property **must** be equal to one of the [known values below](#templatetype-known-values).

### templateType Known Values

| Value      | Description |
| ---------- | ----------- |
| `MODIFY`   |             |
| `TRANSFER` |             |
| `PURCHASE` |             |
| `LOGIN`    |             |

## transactionId

Az ID-ja annak a tranzakciónak, amihez a PUSH megerősítést kaptuk

`transactionId`

- is **required**
- type: `string`
- defined in this schema

### transactionId Type

`string`

