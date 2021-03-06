# magic-api
**magic-api** is a Magic: The Gathering card database and API for PHP.

## Abstract
Popular MTG card references, such as Gatherer, feature human-readable information but do not provide a machine-readable version. **magic-api** requests cards by name from the official Gatherer database, strips the page down to the core data, and caches it in a MySQL database. Once a card is in the SQL database, it provides the cached version whenever that card is requested.

##Features
* Search for cards by name
* Returns JSON object of card (see API)
* 2-10 seconds to initially fetch a card, relying on Wizards' Gatherer
* 0.01 seconds to return a card from database once cached

##Setup
**Requires PHP5 with `allow_url_fopen`**  
Put your database connection information in `db.php` and install all the files together in a directory. `index.php` links all the files together, so you can see the required files there.

##API
For demonstration, my installation is kept at [http://stegriff.co.uk/host/magic](http://stegriff.co.uk/host/magic).
You can try it out using the [magic-api interface](http://stegriff.co.uk/host/magic/interface).
If you are building an app using **magic-api** as a server, I recommend hosting your own instance. 

###Parameters
`magic?name=black lotus` or `magic?name="black lotus"` to search for a card.  
`magic?name=boros recruit&metrics=true` to find out whether that card was from the cache or whether its cache insertion was successful.

###Returned
**magic-api** returns a JSON object, with the following possible fields. Fields with no value will not be in the returned object.

	name,
	mana_cost,
	converted_mana_cost,
	types,
	card_text,
	flavor_text,
	watermark,
	power_toughness,
	expansion,
	rarity,
	card_number,
	artist,
	from_cache^,
	into_cache^,
	request_time,
	error

^Fields only returned when metrics is turned on in the query string.

###Card encoding
####Icons
Icons are encoded with {Braces}, like mana costs (`{2}{U}`), and the tap icon,
`{Tap}`. Single coloured mana is shortened to W/U/B/R/G as is standard
(U is blue). Multi-coloured mana is represented like `{Red or White}`. These values are automatically extracted from the `alt` attribute of any images inside a field on Gatherer, and then mana symbols are shortened. `{Variable Colorless}` is also  shortened, to `{X}`.

####Line breaks
Lines of card text are ended by an underscore flanked by spaces, for example:

	"card_text": "{Tap}: Exile target land card from a graveyard. Add one mana of any color to your mana pool. _ {Black}, {Tap}: Exile target instant or sorcery card from a graveyard. Each opponent loses 2 life. _ {Green}, {Tap}: Exile target creature card from a graveyard"
####Escaping
* Single and double quotes appear with a backslash.
* Carriage returns (`\r`) in flavor text are replaced with a space.
* The em-dash in creature type is a Unicode character but should be rendered correctly as an em-dash by your JSON parser. I recommend JSONview for Firefox or Chrome to view the returned values from **magic-api**.

-----
Stephen Griffiths - @SteGriff - github@stegriff.co.uk