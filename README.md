# Shop Manager (adapted from [Purestring](https://github.com/blawson69/PurseStrings))
This [Roll20](http://roll20.net/) script handles currency and currency exchanges for characters in games using the default [SRD monetary system](https://roll20.net/compendium/dnd5e/Treasure#content). It manages a character's currency and will add/subtract appropriately when collecting loot, paying for goods/services, etc. This script also includes an Inventory system, allowing a dynamic Merchant experience. You can create any number of Merchant NPCs with an Inventory that is updated with every purchase.

This script is for use with the [5e Shaped Sheet](http://github.com/mlenser/roll20-character-sheets/tree/master/5eShaped) and the D&D 5th Edition OGL Sheet.

## Script Integration
For users of the 5e Shaped Sheet, PurseStrings will detect the presence of the [PotionManager (v0.4+)](https://github.com/blawson69/PotionManager) and/or [GearManager (v0.6+)](https://github.com/blawson69/GearManager) scripts, and can add purchased items directly into a characters offense or utility sections according to those scripts. The option to enable/disable this behavior is in the [config](#--config) dialog. The default is enabled.

## Coinage
PurseStrings is fairly flexible in the way it accepts coinage through the various commands. It can be sent as either a list of amounts & denominations (commas and spaces between numbers and denominations are accepted), or as a shorthand list with a colon separating the amounts in denominational order from smallest to largest (cp, sp, ep, gp, pp) with zeros for placeholders.

All of the examples below represent 30cp and 4gp:
- 30cp 4gp
- 30 cp 4 gp
- 30cp, 4gp
- 30:0:0:4:0

## Party Members
You can [add characters](#--party) to a Party Members list which persists across sessions. This makes the distribution of loot easy, as it eliminates the need for selecting multiple tokens. This only applies to the use of the `--dist` command [below](#--dist). Giving individual characters money still requires that the character(s) token(s) be selected. Characters must be [set up](#--setup) with PurseStrings before adding them to the Party Members list.

## Merchant Setup
Merchants are NPCs that have items or services to sell. A Merchant has two requirements:
1. It must be a **token** that contains the Merchant's Inventory (below) in the token's GM Notes, and
2. The token must represent a Character that has been [set up](#--setup) with PurseStrings.

As it is not required for the Merchant token to be the default token of the character, you have more flexibility and fewer character sheets to load. You can create a generic Character and use it with multiple tokens - they will all have different Inventory but use the same pool of money for transactions. If you wish to use a character as a Merchant, [see below](#characters-as-merchants).

You can allow a Merchant's Inventory to override the [default setting](#--config) by adding "show-stock" or "hide-stock" to the first Bar 1 box on the token. This allows you to mix up inventory "types" such as a restaurant menu and a shopkeeper's stock.

To create a Merchant, follow the instructions below.
1. Select and Edit a token you wish to represent your Merchant.
2. Add "show-stock" or "hide-stock" to the first Bar 1 box, if desired.
3. Edit the token's GM Notes field.
4. The first line of Inventory must be either "PurseStrings Inventory" or "PurseStrings Menu" and nothing else. The dialog displayed through the [`--invlist`](#--invlist) command will give the name of the Merchant token along with either "Inventory" or "Menu" as you provide here.
5. Enter each item on its own line in the following manner:  
    *Item Name|price|quantity|weight|prop|mod|desc|isService(0 or 1)|isTreasure(0 or 1)(buy for 0)*  

Price must be a single denomination, i.e. "2cp" or "1200gp" and not "2cp, 1200gp".
6. If you have multiple categories, you may add category headers by simply giving the category name its own line above the items of that category.
7. The PurseStrings inventory data *must* be the only data in the token's GM Notes field!
8. Click the 'Save Changes' button.

Here is an example GM Note for a small town blacksmith Merchant:
```
PurseStrings Inventory
Armes et Armures
Fronde|1sp|39|0,1|,Range|Item Type#Ranged Weapon,Damage# 1d4,Damage Type# Bludgeoning,Range# 30/120|Fronde|0|0
Bâton|2sp|39|2|Versatile|Item Type#Melee Weapon,Damage# 1d6,Damage Type# Bludgeoning,Alternate Damage# 1d8,Alternate Damage Type# Bludgeoning|Bâton|0|0
Gourdin|1sp|39|1|Light|Item Type#Melee Weapon,Damage# 1d4,Damage Type# Bludgeoning|Gourdin|0|0
Serpe|1gp|39|1|Light|Item Type#Melee Weapon,Damage# 1d4,Damage Type# Slashing|Serpe|0|0
Dague|2gp|39|0,5|Finesse,Light,Thrown,Range|Item Type#Melee Weapon,Damage# 1d4,Damage Type# Piercing,Range# 20/60|Dague|0|0
Consommables
Services
Investissements
Investissement|100gp|10|0||Item Type#Investissements|Investissement|0|0
Potions
Marchandises
Montures
Bateaux
Batiment
```
If a Merchant buys an item from a player that is not already in their inventory, it will be added to the list. It is assumed that Merchants will buy at half their selling cost, so the inventory price on new items will be double the amount at which it was purchased. Updates to inventory outside of purchases may be done by editing the GM Notes field. See the [`--buy` command](#--buy) for options and information.

**Advice on GM Notes**
The GM Notes field is a [WYSIWYG](https://en.wikipedia.org/wiki/WYSIWYG) editor, and cutting and pasting from the web or a word processor like Google Docs, Microsoft Word or Pages can carry over formatting which causes problems in processing your Inventory. If you are having problems with inventory showing in chat, try cutting from your original source (the web, word processor, etc.) and paste into a text editor *first*, or create the content in the text editor itself. Then cut from the text editor and past into the GM Notes field.

### Characters as Merchants
If you wish to use a character as a Merchant (as in previous versions of PurseStrings), you must make the Merchant token the *default token* for the character. There are two ways to ensure the Merchant's Inventory remains up-to-date on the character:
- If the Merchant token's name is the same as the character's name, the default token will automatically be updated whenever inventory changes. Be aware that all other token settings will be saved, including rotation, status markers, auras, etc.
- If the names do not match, you will need to update the default token manually using the [`--update-merchant` command](#--update-merchant) **before** removing the token from the VTT.

## Syntax

`!ps <command>`

## Commands:
- **[--help](#--help)**
- **[--config](#--config)**
- **[--setup](#--setup)**
- **[--stock](#--stock)**
- **[--party](#--party)**
- **[--show](#--show)**
- **[--dist](#--dist)** <_coinage_>
- **[--add](#--add)** <_coinage_>
- **[--subt](#--subt)** <_coinage_>
- **[--give](#--give)** <_giver_id_> <_taker_id_> <_coinage_>
- **[--buy](#--buy)** <_buyer_id_> <_seller_id_> <_coinage_> <_item_>
- **[--invlist](#--invlist)** <_merchant_id_>
- **[--update-merchant](#--update-merchant)**

---
### --help
Whispers a PurseStrings help dialog in the chat window. It gives relative commands depending on whether the player is GM. The GM will also get a button to display the [Config Menu](#--config).

`!ps --help`

---
### --config
**GM Only** This gives you a short dialog menu with all of the PurseStrings configuration options so you can customize it for your game. It displays a list of your [Party Members](#--party), links to change the default settings, and a link to add Party Members.

`!ps --config`

#### Merchant Stock
When a merchant's inventory is displayed, you can choose to whether or not to show the quantity of each item. When you show quantities, out-of-stock items (quantity of zero) will display "out of stock." If you are hiding quantities, the out-of-stock items will not be not displayed at all. Any items that have an "infinite availability" such as services ([see above](#merchant-setup)) will always be shown and will never display any quantity regardless of the this setting.

You can also override this value individually on each Merchant token. To do so, simply add "show-stock" or "hide-stock" to the first Bar 1 box on the token.

 Default is to show quantities.

#### Recording Purchases
When a player character purchases an item from a Merchant, PurseStrings can record the purchase to the character's sheet for later reference. For the 5e Shaped sheet, it is recorded in the Miscellaneous Notes field near the bottom. For the 5th Edition OGL sheet, it is recorded in the Treasure field in the Bio tab. This applies only to Inventory items that do not have an infinite availability.

The script will add a line beginning with "PURCHASED ITEMS:" followed by a list of all items purchased. Multiple items will have a number in parentheses corresponding to how many have been purchased. This function ignores all other text in the field.

The default is to record purchases.

---
### --setup
**GM Only** The GM must setup all player characters and relevant NPCs before using PurseStrings. Select each token representing the character(s) you want to setup and run the following command. This command registers each character with the script, ensures the proper currency attributes exist, and adds a token action for calling the `--show` command ([below](#--show)) to view their Purse:

`!ps --setup`

If you wish to add a starting amount to the selected characters, you can optionally pass the coinage parameter along with the `--setup` command. Keep in mind the given amount will be added to every selected character. The following adds 50cp, 20sp, and 10gp to each selected character:

`!ps --setup 50:20:0:10:0`

---
### --party
**GM Only** You may add characters you have already [set up](#--setup) to a Party Members list that persists between sessions. This will allow you to distribute loot ([below](#--dist)) without needing to select player tokens. To do this, select the tokens representing the characters you wish to add and use the following command:

`!ps --party`

You can use this command more than once in case you are in game and cannot select all of them at once. The command will simply add the new token to the list.

If you wish to remove characters from the Party Members list, you will essentially start over with a new group. Select the tokens you wish to keep and pass `--reset` along with the `--party` command:

`!ps --party --reset`

Passing the `--reset` command without tokens selected will *remove all characters from the list.*

---
### --show
To display the contents of a character's Purse, use the `--show` parameter. It will display Purse contents for all selected tokens, and shows the total weight of all coins for encumbrance purposes:

`!ps --show`

You may also send an optional `--whisper` command to make `--show` whisper the results. This is default when [setting up your characters](#--setup) for the first time.

`!ps --show --whisper`

---
### --dist
**GM Only** When the players have discovered treasure, you may use PurseStrings to distribute the coinage portion of the loot evenly among Party Members using `--dist` command. Coinage can be in any format above.

```
!ps --dist 156:280:0:666:0
!ps --dist 146sp, 398gp
```

---
### --add
**GM Only** To add coinage to a character(s) Purse, simply pass it with the `--add` parameter. The following adds 10gp to each selected character:

`!ps --add 10gp`

---
### --subt
**GM Only** To simply remove coinage from a character(s) Purse (without giving it to another character) you use the `--subt` parameter along with the amount of coinage you wish to remove. If the amount of the coinage is more than what that character has in their Purse, the operation will fail. The following subtracts 10gp from each selected character:

`!ps --subt 10gp`

Equivalences are used when determining what coins are removed. For instance, if the Purse has 50sp and 5gp and the amount to subtract is 6gp, the Purse will contain 40sp and 0gp once the subtraction is complete. A Purse with 2cp and 7sp can subtract 5cp and the script will borrow from larger denominations to make up the remainder, in this case leaving the Purse at 7cp and 6sp.

---
### --give
Characters can exchange money between themselves. You must send a token ID that represents both the giving character and the taking (receiving) character along with the amount being given. If you have a character that acts as a "bank" for the Party and does not have a token on the VTT, you can pass the character ID as a *taker only*.

`!ps --give --giver|<giver_id> --taker|<taker_id> --amt|50gp`

As with the `--subt` command ([above](#--subt)), the exchange will fail if the amount of the coinage is more than what the giver has in their Purse, and equivalences will be used for making change.

---
### --buy
Characters can also pay for goods & services as well. PurseStrings handles the monetary transaction along with updating the Merchant's Inventory ([see above](#merchant-setup)). On a successful transaction, a character who has purchased an item will have that item recorded on their sheet if the [record purchases](#--config) option is turned on.

To exchange money for an item or service, you use the `--buy` command along with the IDs of tokens representing both the buyer and the seller. Both IDs must be included as parameters because of the way the API handles targeted tokens. Also required is the amount for which the item is being sold using `--amt|<coinage>`, and the name of the item using `--item|<item_name>`.

`!ps --buy --buyer|<buyer_id> --seller|<seller_id> --amt|50gp --item|Potion of Healing`

You can send a category for the item by separating the name and category with a tilde symbol (~). This will allow you to categorize items that are not already in a Merchant's Inventory. If no category is sent, an "Uncategorized" category will be used.

`!ps --buy --buyer|<buyer_id> --seller|<seller_id> --amt|37gp --item|Greatsword~Weapons`

As with the `--subt` command ([above](#--subt)), the transaction will fail if the price is more than what the buyer has in their Purse, and equivalences will be used for making change.

The Inventory dialog for Merchants ([below](#--invlist)) outputs the proper code for all purchases from a Merchant. Players will simply click the "Buy" link in the Inventory dialog to make a purchase. Once the inventory has been updated, a new inventory list dialog will automatically be generated with any quantity changes.

---
### --invlist
**GM Only** You can display a Merchant's Inventory (see [setup instructions](#merchant-setup) above) using the `--invlist` command followed by the Merchant token's ID:

`!ps --invlist <merchant_id>`

This generates a dialog with the Merchant's inventory/menu and provides the item name, price, and a link the player's can use to make the purchase.

---
### --update-merchant
**GM Only** For characters you wish to use as a Merchant rather than multiple Merchant tokens sharing the same character ([see above](#characters-as-merchants)), you set the character's default token as the one that contains the Merchant's Inventory. In this manner, a Merchant can be quickly moved to the VTT from the Journal. Inventory will be updated automatically if the name on the default token is the same as the character's name. To update manually, use the `--update-merchant` command on the selected Merchant token **before** it is removed from the VTT.

`!ps --update-merchant`

This command is not necessary if the name of the Merchant token is the same as the character's, or if you otherwise have reason not to update inventory on the character.
