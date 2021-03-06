# Configuration

Speedcontrol can be used without any configuration, but you will need to do this to get most features to work, such as Twitch integration.

We use the normal [NodeCG bundle configuration](https://nodecg.com/tutorial-bundle-configuration.html) so see that page for some basic details on where the configuration file should go. *tl;dr:* put a JSON file called `nodecg-speedcontrol.json` in your NodeCG installation's `cfg` folder.

If you haven't, I would recommend installing [nodecg-cli](https://github.com/nodecg/nodecg-cli), then you can do either do `nodecg defaultconfig` in the bundle's directory or `nodecg defaultconfig nodecg-speedcontrol` in the NodeCG installation folder and this will create a default configuration file in the correct place with *some* of the settings you can configure already partially filled out, although you will still need to tweak this. See below for what can go in this file in more detail.

As normal with NodeCG, you will need to restart your instance of the NodeCG server when you change the config for them to be applied.

If you are experienced you can also check out the [configschema.json here](../configschema.json).

Below is an example configuration file contents with everything that is available (*do **not** copy this and put it in directly without any modifications; it won't work, sorry*):

```
{
	"live": false,
	"twitch": {
		"enable": true,
		"clientID": "CLIENT_ID",
		"clientSecret": "CLIENT_SECRET",
		"redirectURI": "http://localhost:9090/nodecg-speedcontrol/twitchauth",
		"ffzIntegration": false,
		"streamTitle": "Game: {{game}} - Category: {{category}} - Players: {{players}}",
		"streamDefaultGame": "Games + Demos"
	},
	"schedule": {
		"defaultURL": "https://horaro.org/event/schedule",
		"ignoreGamesWhileImporting": ["Setup"],
		"customData": [
			{
				"name": "Game (Short)",
				"key": "gameShort"
			}
		]
	},
	"tiltify": {
		"enable": true,
		"token": "API_ACCESS_TOKEN",
		"campaign": "CAMPAIGN_ID"
	},
	"speedrunComMarathon": {
		"enable": true,
		"slug": "SRC_SLUG"
	},
	"gaming4Good": {
		"enable": true,
		"twitchChannelID": "TWITCH_ID"
	},
	"api": {
		"enable": true,
		"sharedKey": "TWITCH_ID",
		"hooks": ["HOOK_URL"]
	}
}
```

## Breakdown

```
"live": false
```

By default this boolean is false, changing it to true disables a few dashboard buttons that probably shouldn't be pressed during a live marathon. It also should disable edit mode `<div>`s on graphic bundles generated with [generator-speedcontrol](https://github.com/speedcontrol/generator-speedcontrol), although that hasn't been tested recently.

### Twitch

```
"twitch": {
	"enable": true,
	"clientID": "CLIENT_ID",
	"clientSecret": "CLIENT_SECRET",
	"redirectURI": "http://localhost:9090/nodecg-speedcontrol/twitchauth",
	"ffzIntegration": false,
	"streamTitle": "Game: {{game}} - Category: {{category}} - Players: {{players}}",
	"streamDefaultGame": "Games + Demos"
}
```

This is the part where the Twitch integration configuration is set up. Make sure `enable` is true (it defaults to false), and then you will need to [create a Twitch API app yourself from their developer site](https://glass.twitch.tv/console/apps/create) and put in the supplied client ID and client secret. The redirect URI should be the same as the one above unless you are **not** running your NodeCG server locally and/or you changed the port it uses.

- `streamTitle` is a string that specifies what the title will be updated to when a run is switched. There are some wildcards as seen above that will be replaced with the relevant data if they're specified. Leave it blank if you don't want to have the title updated.
- `streamDefaultGame` is the game in the Twitch directory that will be used if the game name from your schedule cannot be found in the directory.

FrankerFaceZ integration can also be enabled with `ffzIntegration`, this will make it so people who use the browser extension see the runner(s) as a "featured" channel for easy following, and if enabled on your channel, will also add their FrankerFaceZ emoticons for use during their run (you will need to ask the devs of the extension to enable this for you).

Once the Twitch integration settings are fully set up and your NodeCG server has been (re)started, you will see a "Connect with Twitch" button on the dashboard, which can be used to authorise nodecg-speedcontrol with Twitch. Currently, you must login with the channel you wish to have the information updated for.

### Schedule

```
"schedule": {
	"defaultURL": "https://horaro.org/event/schedule",
	"ignoreGamesWhileImporting": ["Setup"],
	"customData": [
		{
			"name": "Game (Short)",
			"key": "gameShort"
		}
	]
}
```

- `defaultURL` is a URL to the schedule on Horaro that will be pre-filled on the dashboard; usually you will only be using your setup for 1 marathon so this means you don't need to keep entering it every time you want to do a (re)import.
- `ignoreGamesWhileImporting` is an array of strings of games on your schedule that will be ignored on import; for example you may have setup blocks you don't want importing. This does partial matches ("Setup Block" will be matched by "Setup").
- `customData` (*for advanced users*) is an array of objects; this is for adding custom data to the run data on import. Once set here, you will be able to select an appropriate column on import for where this data is stored in your schedule. All of this is stored within an objected called `customData` within the run's data object.

### Tiltify

```
"tiltify": {
	"enable": true,
	"token": "API_ACCESS_TOKEN",
	"campaign": "CAMPAIGN_ID"
}
```

Once enabled and an API access token and campaign ID are specified from Tiltify (you can get a token by registering an application on the Tiltify dashboard, the campaign ID is harder to find; look in your browser's inpector in the Network tab), the donation total for your campaign will be stored and frequently updated in the `tiltifyDonationTotal` replicant of this bundle. The source for the Tiltify code can be found in the [./extension/tiltify.js](../extension/tiltify.js) file.

### Speedrun.com Marathons

```
"speedrunComMarathon": {
	"enable": true,
	"slug": "SRC_SLUG"
}
```

Once enabled and a slug is specified of a marathon page on Speedrun.com (this is the part of the URL after the 1st forward slash), if it has donations activated, you will be able to access some information about the marathon's donation information via replicants. No extra README for this yet; see the  [./extension/srcomdonations.js](../extension/srcomdonations.js) file if you're an advanced user and wish to use this functionality in your graphics bundles.

### Gaming4Good

```
"gaming4Good": {
	"enable": true,
	"twitchChannelID": "TWITCH_ID"
}
```

***NOTE: This hasn't been fully tested in a while, it may not work correctly.***

Once enabled and you either have Twitch integration enabled *or* a Twitch channel ID is specified in `twitchChannelID`, the donation total for your event will be stored and frequently updated in the `g4gDonationTotal` replicant of this bundle. The source for the Gaming4Good code can be found in the [./extension/g4g.js](../extension/g4g.js) file.

### API

```
"api": {
	"enable": true,
	"sharedKey": "TWITCH_ID",
	"hooks": ["HOOK_URL"]
}
```

This is a undocumented API that can be used to send data to an external server when runs are completed. It was written and mainly intended for use for the European Speedrunner Assembly events. The source for all this can be found in the [./extension/esacontroller.js](../extension/esacontroller.js) file, if you want to use it for some reason.
