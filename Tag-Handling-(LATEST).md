Here are the general instructing for developers who want to add support of their plugin to ScoreHud.
The whole process is extremely simple. No need for addons.

# Tag Handling:

ScoreHud Tag Handling via external plugins'.

### Step 1

In your `Listener` class, import/use this class:

```php
use Ifera\ScoreHud\event\TagsResolveEvent;
```

### Step 2

Handle the `TagResolveEvent` following the [naming standards](https://github.com/CortexPE/HRKChat/wiki/Placeholder-naming-standard) as explained in great detail by @[CortexPE](https://github.com/CortexPE).

I'll be explaining all this by adding some tags called,

- `scorehud.name` which would show the player name
- `scorehud.online` which would show the players online on the server
- `scorehud.money` which would show the players balance. 

Remember this is no longer an addon so add it into your own plugin.

```php 
public function onTagResolve(TagsResolveEvent $event){
	$player = $event->getPlayer();
	$tag = $event->getTag();

	switch($tag->getName()){
		case "scorehud.name":
			$tag->setValue($player->getName());
		break;

		case "scorehud.online":
			$tag->setValue(strval(count($this->getServer()->getOnlinePlayers())));
		break;
	}
};
```

### Step 3

You are done for the most part. If your tag is constant and never updates for as long as the player is online then you are well and good.
But if your tag needs to be updated, as in my case, then there are two events which you can fire based on the type of tag.

- **PlayerTagUpdateEvent**: Fire this event if your tag depends on a player. In my case I will use it for updating `scorehud.money` tag.
- **ServerTagUpdateEvent**: Fire this event if your tag is independent of players. In my case I will use it for updating `scorehud.online` tag.

Imports for these are: 

- `Ifera\ScoreHud\event\PlayerTagUpdateEvent`
- `Ifera\ScoreHud\event\ServerTagUpdateEvent` 

### Step 4

First of, **PlayerTagUpdateEvent**. <br />
Since my tag is related to money I'll fire the event whenever players balance changes. 

```php
public function onMoneyChange(MoneyChangedEvent $event){
	$ev = new PlayerTagUpdateEvent(
		$event->getPlayer(), 
		new ScoreTag("scorehud.money", strval($event->getMoney()))
	);
	$ev->call();
}
```

- Import `Ifera\ScoreHud\scoreboard\ScoreTag` for ScoreTag.
- Parameter 2 of ScoreTag accepts only a string. So remember to cast your value to string beforehand.

### Step 5

For `ServerTagUpdateEvent`, I'll use the following example for `scorehud.online` tag.

```php
public function onJoin(PlayerJoinEvent $event){
	$ev = new ServerTagUpdateEvent(new ScoreTag(
			"scorehud.online",
			strval(count($this->getServer()->getOnlinePlayers())))
	);
	$ev->call();
}

public function onQuit(PlayerQuitEvent $event){
	$ev = new ServerTagUpdateEvent(new ScoreTag(
			"scorehud.online",
			strval(count($this->getServer()->getOnlinePlayers())))
	);
	$ev->call();
}
```

As the online count changes when a player logs in or logs out, I handle it on both the events. 

### Step 6

And that is all there is to it. Hope it wasn't that difficult.

Use the [ScoreHudX](https://github.com/Ifera/ScoreHudX) plugin as an example if you are still confused about stuff.