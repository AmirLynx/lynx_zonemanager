[![Discord](https://img.shields.io/badge/Discord-Amir__Lynx%239439-orange)](https://discordapp.com/users/440702426765590529)

# lynx_zonemanager


- Add zone when player set in job
- Remove zone when player remove from job
 

## Installation
- Add this in your `server.cfg`:

```
start lynx_zonemananger
```

## Usage

### Add zone
```lua
Citizen.CreateThread(function()
	exports["lynx_zonemanager"]:AddMarkerZone(name, coords.x, coords.y, coords.z, size.x, size.y, size.z, blip)
end)
```

### Remove zone
```lua
Citizen.CreateThread(function()
	exports["lynx_zonemanager"]:RemoveMarkerZone(name)
end)
```

### Enter and exit marker
```lua
local lynxZone 
local waitClick = false
```

```lua
AddEventHandler('lynxmarker:EnterMarker',function(zone)
	if zone == "zone name" then
		lynxZone = zone
		waitClick = true
	else
		return
	end
end)
```

```lua
AddEventHandler('lynxmarker:ExitMarker',function(zone)
	if zone == "zone name" then
		lynxZone = nil
		waitClick = false
		ESX.UI.Menu.CloseAll()
	else
		return
	end
end)
```


## Sample
### This is sample for esx_taxijob vehicle spawn


- Set job event

```lua
local markersActive = false
RegisterNetEvent('esx:setJob')
AddEventHandler('esx:setJob', function(job)
	ESX.PlayerData.job = job
	if ESX.PlayerData.job.name == "taxi" then
		if not markersActive then
			ShowMarkers()
			markersActive = true
			JobActive()
		end
	else
		if markersActive then
			ClearMarkers()
			markersActive = false
		end
	end
end)
```

- player load event

```lua
RegisterNetEvent('esx:playerLoaded')
AddEventHandler('esx:playerLoaded', function(xPlayer)
	ESX.PlayerData = xPlayer
	if ESX.PlayerData.job.name == "taxi" then
		if not markersActive then
			ShowMarkers()
			markersActive = true
			JobActive()
			keyActive()
		end
	else
		if markersActive then
			ClearMarkers()
			markersActive = false
		end
	end
end)
```

- Add zone function

```lua
function ShowMarkers()
	Citizen.CreateThread(function()
		for i,v in ipairs(Config.Areas) do
			exports["lynx_zonemanager"]:AddMarkerZone(v.name, v.coords.x, v.coords.y, v.coords.z, v.size.x, v.size.y, v.size.z, v.blip)
		end
	end)
end
```

- Remove zone function

```lua
function ClearMarkers()
	Citizen.CreateThread(function()
		for i,v in ipairs(Config.Areas) do
			exports["lynx_zonemanager"]:RemoveMarkerZone(v.name)
		end
	end)
end
```

- Enter marker

```lua
AddEventHandler('lynxmarker:EnterMarker',function(zone)
	if ESX.PlayerData.job.name == 'taxi' then
		if zone == "taxiJobSpawnVehicle" then
			lynxZone = zone
			waitClick = true
		else
			return
		end
	end
end)
```

- Exit marker

```lua
AddEventHandler('lynxmarker:ExitMarker',function(zone)
	if ESX.PlayerData.job.name == 'taxi' then
		if zone == "taxiJobSpawnVehicle" then
			lynxZone = nil
			waitClick = false
			ESX.UI.Menu.CloseAll()
		else
			return
		end
	end
end)
```

- This is key control for markers

```lua
function JobActive()
	Citizen.CreateThread(function()
		while markersActive do 
			Wait(0)
			if waitClick and lynxZone ~= nil and IsControlJustReleased(0, 38) then -- E key press
				if lynxZone == "taxiJobSpawnVehicle" then
					OpenVehicleSpawnerMenu()
				end
				lynxZone = nil
				waitClick = false
			end
		end
	end)
end
```
