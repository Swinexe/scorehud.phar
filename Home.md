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

The extending is necessary. Doing so you will be able to access the methods mentioned and explained [here]().

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

To accomplish this we use `getProcessedString(Player $player, string $string): string` method and return the string after editing the value. 

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
		 * @return string
		 */
		public function getProcessedString(Player $player, string $string): string{
			return "{money}" => str_replace("{money}", $this->getPlayerMoney($player), $string);
		}
	}
}
```

Now if your addon uses only one tag you are fine by doing that. If however your addon supports multiple tags then you can do something like this in `getProcessedString()` method.

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
		 * @return string
		 */
		public function getProcessedString(Player $player, string $string): string{
			$data = [
				"{money}" => str_replace("{money}", $this->getPlayerMoney($player), $string)
			];

			if(strpos($string, "{money}") !== false){
				return $data["{money}"];
			}

			return $string;
		}
	}
}
```

I make a array and store the processed string as value to the key I want as tag. Then I check if the original string has that tag by using `strpos()` and if found I return the value stored against the tag in the array.