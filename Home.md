Here the general instructing for **developers** who want to add support of their plugin to ScoreHud via an addon. <br />
The whole process is extremely simple.

# Addon Making

**Step 1**

You start by making a new PHP file. For this tutorial I would be using a file called `EconomyApiAddon.php`. And adding a `namespace`. It could be any of your choice. The `{}` at the end of the namespace are necessary.

```php
namespace JackMD\ScoreHud\Addons{}
```

**Step 2**

Now define the name and main file location of the addon like so.

```php
/**
 * @name EconomyApiAddon
 * @main JackMD\ScoreHud\Addons\EconomyApiAddon
 */
namespace JackMD\ScoreHud\Addons{}
```

The `@name` will be the name of your addon and `@main` will be the `namespace` plus `@name` of the addon.

**Step 3**

Now we will make a class with the same name as in `@name` which will extend `AddonBase`.

```php
/**
 * @name EconomyApiAddon
 * @main JackMD\ScoreHud\Addons\EconomyApiAddon
 */
namespace JackMD\ScoreHud\Addons
{
	use JackMD\ScoreHud\addon\AddonBase;
	use onebone\economyapi\EconomyAPI;
	use pocketmine\Player;

	class EconomyApiAddon extends AddonBase{}
}
```

The extending is necessary. Doing so you will be able to access the methods mentioned and explained [here]().

**Step 4**

I now make a function which gets EconomyAPI plugin by using `getScoreHud()` method to get the servers instance.

```php
/**
 * @name EconomyApiAddon
 * @main JackMD\ScoreHud\Addons\EconomyApiAddon
 */
namespace JackMD\ScoreHud\Addons
{
	use JackMD\ScoreHud\addon\AddonBase;
	use onebone\economyapi\EconomyAPI;
	use pocketmine\Player;

	class EconomyApiAddon extends AddonBase{

		/**
		 * @param Player $player
		 * @return float|string
		 */
		private function getPlayerMoney(Player $player){
			/** @var EconomyAPI $economyAPI */
			$economyAPI = $this->getScoreHud()->getServer()->getPluginManager()->getPlugin("EconomyAPI");

			if($economyAPI instanceof EconomyAPI){
				return $economyAPI->myMoney($player);
			}else{
				return "Plugin not found";
			}
		}
	}
}
```
