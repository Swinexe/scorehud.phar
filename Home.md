Here the general instructing for **developers** who want to add support of their plugin to ScoreHud via an addon. <br />
The whole process is extremely simple.

# Addon Making

### Step 1

You start by making a new PHP file. For this tutorial I would be using a file called `EconomyApiAddon.php`. And adding a `namespace`. It could be any of your choice. The `{}` at the end of the namespace are necessary.

```php
namespace JackMD\ScoreHud\Addons{}
```

### Step 2

Now define the name and main file location of the addon like so.

```php
/**
 * @name EconomyApiAddon
 * @main JackMD\ScoreHud\Addons\EconomyApiAddon
 */
namespace JackMD\ScoreHud\Addons{}
```

The `@name` will be the name of your addon and `@main` will be the `namespace` plus `@name` of the addon.

### Step 3

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

The extending is necessary. Doing so you will be able to access the methods mentioned and explained [here](https://github.com/JackMD/ScoreHud/blob/master/src/JackMD/ScoreHud/addon/Addon.php).

### Step 4

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

### Step 5

Till now we haven't done anything to make ScoreHud aware of what tag we would be using and what value should appear on the scoreboard.

To accomplish this we use `getProcessedTags(Player $player, string $string): array` method and return the array containing the tags this addon would provide. 

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

		/**
		 * @param Player $player
		 * @param string $string
		 * @return array
		 */
		public function getProcessedTags(Player $player, string $string): array{
			$tags = [];

			if(strpos($string, "{money}") !== false){
				$tags["{money}"] = str_replace("{money}", $this->getPlayerMoney($player), $string);
			}

			return $tags;
		}
	}
}
```

First we make an empty array. Then we using `strpos()` check if the tag we want to provide support for exists in the `$string`. If it does we add it to the array with key pointing to the tag and value pointing to the replaced value as shown in the example above. We then return the tags.

### Step 6

Your addon is now complete. Simple wasn't it? <br />
To test it follow the tutorial [here](https://github.com/JackMD/ScoreHud/tree/master#how-to-use-addons).